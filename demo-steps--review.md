# Review and merge a contribution
_As a senior developer I want to review and merge contributions._

1. Notification of pending PR is sent via normal channel (e.g. email or GutHub
   interface)
1. View the PR in normal GitHub interface
1. Review the code changes, and, given no issues...
1. Note link to Pipelines job and click link
1. Note added test, and, given no issues...
1. Note the new ODE and click on the provided URL
1. Manually test new feature in ODE, and, given no issues...
1. Merge the PR though GitHib's interface into the master branch
1. Pipelines updates the pipelines-build-master branch with a new artifact
1. That branch is checked out on test
1. Test manually again. Given no issues...
1. Tag pipelines-build-master branch and deploy to prod3