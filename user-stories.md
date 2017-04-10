# User Stories Covered

## As team lead I want to start a new project.
**Requirements:**
* Use GH for VCS

**Things that should be easy:**
* Building the codebase
* Deploying app to Acquia Cloud

**In this demo, we will:**
* Create a new Drupal codebase that includes Lightning and BLT
* Create a shared VM config for local development
* Use GH as our VCS
* Associate the project with an Acquia Cloud application
* Connect the application to pipelines
* Give pipelines instructions on what to do on commit/PR

[Demo steps](demo-steps--start.md)

## As a developer, I want to contribute a feature.
**Requirements:**
* Use GH for filing pull request

**Things that should be easy:**
* Setting up my local environment
* Writing functional tests
* Test locally

**In this demo, we will:**
* Pull the codebase from GitHub and use the contained config to build our local
  developement environement
* Add a feature (subprofile)
* Write a test of the feature and test locally
* Create a pull request

[Demo steps](demo-steps--contribute.md)

## As a senior developer I want to review and merge contributions.
**Requirements:**
* Code review happens on GH

**Things that should be easy:**
* Viewing results of test suite
* Manual testing (I don’t want have to build the app)
* Merging and deploying the contribution

**In this demo, we will**
* Review the PR code on github
* View the results of the existing and added functional test
* Manually test the new feature in an ODE
* Merge and deploy the new feature

[Demo steps](demo-steps--review.md)