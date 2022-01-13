[Table of Contents](index.md)
## Developing long-running features

The easiest way to work on long-running features is to not put
your feature in a branch!

Instead, try to build the new
feature in a way that allows you to add it to the master
branch in a disabled state. This can be done through a build-time or
runtime switch.

It is almost always possible to add new features this way. Though it
may require more work or a modified design approach for an
individual developer, working this way typically increases the
efficiency of the entire team, due to the following advantages:

- Code is immediately accessible to other developers for collaboration.

- Tests related to the new code can easily be turned on or off
  as necessary.

- Tests for the current code must still pass and cannot be discarded,
  so there is no loss in test coverage.

- It is easy to browse code for the new feature to see how the new
  architecture / strategy differs from the current approach. This is
  especially important in cases where features need to be added to the
  current system that will be replaced. By knowing how things will
  eventually change, it is often possible to devise a solution that
  works with both the new and the old.

- Encourages making multiple rounds of smaller, more focused changes
  to lay the ground work for the feature. For example, it may be necessary
  to create new, sometimes unused, interfaces in order to expose info
  that will eventually be used by a new system. Doing this in a
  separate step is typically easier to understand and review.

- It is easy to 'turn on' the new feature for QA, either at runtime
  or via a separate build in order to get additional test feedback
  during development.


## Maintaining and merging long-running feature branches

Despite best-efforts to avoid them, sometimes long-running feature
branches are required. They can speed up development for a small
team or reduce the complexity of work required for large scale
changes at the interfaces between systems.

The key to maintaining long-running features is to frequently
merge in changes from the master branch. This will seem daunting
at first, but gets easier if performed more frequently, since the
change sets will be smaller. This strategy also ensures that any
incompatibilities or breaking changes are detected quickly.

When it comes time to merge a long-running feature branch, there
are no magic bullets. If you were merging in code from master
frequently, then it will be relatively straightforward.

### General strategy for merging a long-running feature branch

1. Merge master into feature branch.

2. Make sure it is stable.
  
    A. Code builds and tests pass.

    B. The app passes basic smoke tests on devices.
    
    C. The QA team has verified that feature branch builds function 
    correctly with no known major issues.

3. Merge feature branch into master    

#### Example: merging prototype/master -> master

At one point in the history of OverDrive, we attempted to build
a 'prototype app' in a branch of the repo. This ultimately failed
and we needed to merge most of the prototype code into master.
Here's the integration process I used:

1. Start with basestation and refactor all prototype changes so that
they could be turned off or ignored. Then merge master and make sure
that tests pass and that we can still play prototype games.  Keep
doing this to update from basestation/master until the overdrive repo
is also ready.

2. Merge overdrive code from master -> prototype/master and make sure
the game works. Add a blank scene to the UI that just runs the game
the way it used to work in master.  Point to the updated basestation
prototype/master branch and hook up the new API to conditionally play
the game different ways.  Use the app & test scene to prove that
switching "game type" paths at runtime works.

3. Get help to refactor old OverDev UI to get it working with all the
latest Unity architecture changes.  Make sure it's possible to play
games both ways.

4. At this point prototype/master has everything integrated and is
ready to be merge back into master.

5. Submit PR, make sure it builds, all tests pass, and get other devs
to build it to check that all of the Unity plugin features still work.

6. Give a build to QA to test overnight to get the green light
before merging.

