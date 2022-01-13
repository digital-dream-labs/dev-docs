[Table of Contents](index.md)
## Life cycle of a basic feature

These are the steps that we use to introduce a new feature or fix a bug.

01. Update `master` to pull the latest changes with `git fetch -p`

02. Create a feature branch with a short, meaningful name
(see [git-etiquette](git-etiquette.md)).  For example,
`git checkout -b od-9999-fix-voice-over --track origin/master`

03. Develop a feature (or fix a bug)!
    - Write tests & code.
    - Test the app to make sure you didn't break anything.
    - Repeat if necessary.
    
04. Rebase your changes against the latest master with `git pull --rebase`

    - Test app to make sure your changes work the latest code.

05. Use interactive rebase (`rebase -i`) to squash irrelevant commits
(see [git-etiquette](git-etiquette.md)).

06. Push your branch to github with `git push`.

07. Open Pull Request.

08. Let the build-server build & test your Pull Request branch.

09. If the build failed or changes are necessary:

    - Make changes and push new commits to the branch.
    - Merge `master` into your branch (if it can no longer be automatically merged, for example).
    - Let the build-server build & test.

10. If the review is complete and the build succeeds:

    - Merge the branch into master.
    - Delete the branch on github.

