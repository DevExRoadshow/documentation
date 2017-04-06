# Start a new project
_As team lead I want to start a new project_

1. Create the codebase. Run `composer create-project devexroadshow/project-starter --stability=dev --no-interaction filenes` <sup>Note1</sup>  
   _(3m 40s)_
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
                  - set -x
                  - composer self-update
                  - composer validate --no-check-all --ansi
                  - composer install
                  - source ${BLT_DIR}/scripts/pipelines/setup_environment
                  - source ${BLT_DIR}/scripts/pipelines/setup_project
                  - composer install-phantomjs
    
            # Install Lightning and setup behat config.
            - configure-behat:
                type: script
                script:
                  - set -x
                  - cd ./docroot
                  # Install Lightning.
                  - ../vendor/bin/drush site-install lightning --db-url=mysql://root:root@127.0.0.1/drupal --yes
                  # Lightning Dev will automatically scan for feature files in
                  # enabled extensions and generate a behat configuration file based
                  # on the results.
                  - ../vendor/bin/drush pm-enable lightning_dev --yes
    
            # Execute all testing and validation tasks.
            - run-tests:
                type: script
                script:
                  - set -x
                  - ../vendor/bin/drush runserver --default-server=builtin 8080 &>/dev/null &
                  - ../vendor/bin/phantomjs --webdriver=4444 > /dev/null &
                  - sleep 10
                  # Run two specific Lightning tests:
                  # - Panelizer is enabled for landing pages (1e244c89)
                  # - Uploading an image to be ignored by the media library (b23435a5)
                  - ../vendor/bin/behat --config ./sites/default/files/behat.yml --tags=1e244c89,b23435a5
    
            # Generate artifact.
            - build-artifact:
                type: script
                script:
                  - set -x
                  - source ${BLT_DIR}/scripts/pipelines/build_artifact
    ```
    
   _Describe each section_
1. Push! (or don't - not sure)
    
<sup>Note1</sup>When I was setting up this demo, blt and blt-project wer broken
with a few different problems. Notably that BLT was forcing an outdated version
of Lightning and BLT Project was failing with a patch problem. Normally you
would use acquia/blt-project as a starter.