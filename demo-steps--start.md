# Start a new project
_As team lead I want to start a new project_

1. Create the codebase. Run `composer create-project acquia/blt-project CLOUDSUB_SITEGROUPNAME` <sup>Note1</sup>  
1. Switch into project directory
1. Edit ./blt/project.yml
1. Change:
    1. `machine_name` - should == your cloud site name and should automatically
        be set by BLT during composer install to dir name.
    1. `prefix` - used for git commit message validation.
    1. `human_name`
    1. Nothing else. The git remotes array is only used for blt:deploy and we
       will be using pipelines to deploy. But you can set custom drush aliases,
       targets, etc if you want. But this demo won't get into those.
1. Run `blt vm` (don't boot the VM unless you have something else to demo for
  ~10 minutes)  
  _This will create the vagrant box configuration so that others will be able to
  build the same dev environment._
1. Add an upstream GH.

    ```
    git init
    git add README.md
    git commit -m "first commit"
    git remote add origin git@github.com:DevExRoadshow/filenes.git
    git push -u origin master
    ```
1. Associate this project with an Acquia Cloud Application.
    1. `pipelines list-applications`  
      _Get a list of your Cloud Applications IDs. This assumes you have already
      associate the pipelines client with your Acquia Cloud account using
      `pipelines configure`. Note the application ID of the app you want to use._
    1. `pipelines set-application-id --application-id=ac7762b9-1620-44c1-b290-492d89a3fd7e`
1. Associate the Cloud Application/pipelines with your the GitHub repo.
   _In theory, you could just use the repo provided by cloud, but GH has become
   the de facto standard for forks and PRs etc, and this will allow is to
   seamlessly integrate all of that into our workflow.
    1. Visit https://github.com/settings/tokens/new
    2. Enter a descriptive name and click everything under _repo_, click generate.
    3. Copy and paste the token somewhere
    4. `pipelines github-connect --application-id=ac7762b9-1620-44c1-b290-492d89a3fd7e DevExRoadshow/filenes GITHUB_PERSONAL_ACCESS_TOKEN`
1. Tell pipelines what to do when a commit is made or a PR is filed.  
   _Instructions are stored in an acquia-pipelines.yml file. Cut and paste the
   following:_
   
    ```
    version: 1.0.0
    services:
      - mysql
    
    variables:
      global:
        COMPOSER_BIN: $BUILD_DIR/vendor/bin
        BLT_DIR: $BUILD_DIR/vendor/acquia/blt
    
    events:
      build:
        steps:
            # Install global packages and set global configuration.
            - setup:
                type: script
                script:
                  - cd $BUILD_DIR
                  - composer install
                  - source ${BLT_DIR}/scripts/pipelines/setup_environment
                  - source ${BLT_DIR}/scripts/pipelines/setup_project
                  - composer install-phantomjs
    
            # Install Lightning and copy prod database.
            - install:
                type: script
                script:
                  - cd $BUILD_DIR/docroot
    
                  # Install Lightning from scratch first so our settings file gets
                  # the correct DB connection details for the test env.
                  - /usr/bin/env PHP_OPTIONS="-d sendmail_path=`which true`" ../vendor/bin/drush site-install lightning --db-url=mysql://root:root@127.0.0.1/drupal --yes
    
                  # Then copy the prod database over.
                  - ../vendor/bin/drush --alias-path=../drush sql-sync @speedrun.prod default --yes
                  - ../vendor/bin/drush updatedb --yes
                  - ../vendor/bin/drush cache-rebuild
    
                  # Run a config import to get any new config changes from code.
                  - ../vendor/bin/drush config-import --yes --config=../config/default
    
            # Generate behat config
            - configure:
                type: script
                script:
                  - cd $BUILD_DIR
                  - blt behat-generate
    
            # Execute all testing and validation tasks.
            - run-tests:
                type: script
                script:
                  - cd $BUILD_DIR/docroot
                  - ../vendor/bin/drush runserver --default-server=builtin 8080 &>/dev/null &
                  - ../vendor/bin/phantomjs --webdriver=4444 > /dev/null &
                  - sleep 10
                  # Run a specific Lightning test:
                  # - Panelizer is enabled for landing pages (1e244c89)
                  - ../vendor/bin/behat --config ./sites/default/files/behat.yml --tags=1e244c89
    
            # Generate artifact.
            - build-artifact:
                type: script
                script:
                  - source ${BLT_DIR}/scripts/pipelines/build_artifact
    
            # Deploy the build artifact to a Cloud on-demand environment.
            - deploy:
                script:
                  - pipelines-deploy
    
      # When a GitHub pull request is merged, this deletes the corresponding ODE.
      pr-merged:
        steps:
            - deploy:
                script:
                  - pipelines-deploy
    
      # When a GitHub pull request is closed, this deletes the corresponding ODE.
      pr-closed:
        steps:
            - deploy:
                script:
                  - pipelines-deploy
    ssh-keys:
      drush-key:
        secure: YOUR DRUSH KEY 
    ```
    
   _Describe each section_
1. Push! (or don't - not sure)
    
<sup>Note1</sup>You'll probably already have the codebase built and setup for
the sub that you'll be using. So you'll probably want to use something else for
the name here.