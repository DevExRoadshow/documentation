# Contribute to an existing project
_As a developer, I want to contribute a feature_

The following steps are done already. We will be using the same repo to create
the feature branch as we do to review. This is because I can't boot two
virtual machines with the same config on the same machine. So we're effectively
playing the role of both developer (feature contributor) and senior developer
(feature reviewer/committer) on the same machine.
1. Clone the GitHub repo
1. Boot the VM
1. Install the app locally. Run `blt:local:setup`
1. Synchronize your codebase with the test environment. Run `blt local:refresh`

The following steps will be done live.
1. Find an feature request issue in GitHub that you want to contribute.  
1. Create a feature branch.
1. Do whatever is necessary to to add the feature.
    1. Require the new module `composer require drupal/paragraphs`
    1. Enable the module `drush @speedrun.local en -y paragraphs`
    1. Add a new paragraph type (text + media)
    1. Adjust the display of the media to rendered: embedded
    1. Add that paragraph type to the top of Basic Page
    1. Export the config `drush @speedrun.local csex -y`
    1. Add a test
    
    ```
    @api @top-stuff
    Feature: Editors should be able to add text and media to the top of pages.
    
      Scenario: Make sure Top Stuff text and media fields are present and visible to basic page creators.
        Given I am logged in as a user with the "Basic page creator" role
        When I am on "/node/add/page"
        Then I should see "Text + Image"
    
      Scenario: Make sure Top Stuff is correctly rendered on display.
        Given I am an anonymous user
        And page content:
          | title     | path       | moderation_state |
          | Top Stuff | /top-stuff | published        |
        When I visit "/top-stuff"
        Then I should see "Top Stuff"
        
    ```
    
    1. Run the test locally:
        1. SSH in to the VM: `drush @speedrun.local ssh`
        1. Generate local behat config `blt behat-generate -Dhost=local.speedrun.com`
        1. Execute behat with the local config and the paragraphs tag: `./vendor/bin/behat --config ./docroot/sites/default/files/behat.yml --tags=paragraphs`
        1. Exit out of the VM
    
    1. Add the top-stuff tag to the pipelines buildsteps behat command
    1. Commit everything to the feature branch and push to GH
    1. Technically, the contributor would open a PR at this point which would
       create the CDE on the projects main repo. But since we're working from
       the main repo already, Cloud CD will automatically create a feature
       branch CDE once the Pipeline job passes.
