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
    1. Add a new media reference field (video) to the Landing Page content type
    1. Adjust the display settings of the two column layout to display the video
1. Show that new feature has been implemented
    1. Create a new landing page
    1. Add a video using the media browsed. Good vid Url: `https://vimeo.com/62005744`
1. Add a test of the feature
1. Export the configuration `drush config-export`
1. Commit all changes and push to remote
1. Open a PR
