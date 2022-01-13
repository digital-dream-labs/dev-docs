[Table of Contents](index.md)
## Checklist for merging a pull request

The goal of these steps is to maintain efficiency across the
entire development and QA teams.  It is not practical for each 
developer to test all aspects of the entire app on every
platform for each of their changes. However, these basic steps should help
ensure that proposed changes _do not break_ other developers from doing work, or
the QA team from testing other features.

Before merging a pull request, the developer submitting the request
should ensure that the following steps take place:

1. Build server reported a successful build & test.

2. For non-trivial changes, code was reviewed online or in-person.

3. The app passes a minimal "smoke test" on an iOS or Android device.

### Minimal Smoke Test

- App launches without crashing.

- It is possible to start and complete a game.

- It is possible to rematch or start another game after the first one.

