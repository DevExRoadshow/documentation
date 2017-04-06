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
    4. `pipelines github-connect --application-id=ac7762b9-1620-44c1-b290-492d89a3fd7e DevExRoadshow/filenes 4d3a1f609444107cb698c7c69db951e471ebc8f3`
    
<sup>Note1</sup>When I was setting up this demo, blt and blt-project wer broken
with a few different problems. Notably that BLT was forcing an outdated version
of Lightning and BLT Project was failing with a patch problem. Normally you
would use acquia/blt-project as a starter.