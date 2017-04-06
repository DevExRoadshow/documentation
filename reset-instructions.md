# Reset Instructions
There are several steps taken during the demo that will need to be undone before
they can be demoed again.

1. Disassociate the filenes repo with the github repo.

    ```
    pipelines github-disconnect --application-id=ac7762b9-1620-44c1-b290-492d89a3fd7e DevExRoadshow/filenes GITHUB_PERSONAL_ACCESS_TOKEN
    ```

1. Delete the GH personal access token created for the filenes repo
1. Hard reset the speedrun repo to the `reset` upstream and push the reset to origin

    ```
    git reset --hard upstream
    git push -f origin master
    ```
