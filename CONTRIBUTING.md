# Contributions Guidelines

Contributions are always welcome. However, to make this a smooth collaboration experience for everyone and to maintain the quality of the code, here is a few things to consider before and after making a pull request:

## Branching strategy

* `master` always holds released, working code.
* `develop` is the integration branch where new work lands first.
* Both branches are protected: nobody can push to them directly, force-push them or delete them. Every change arrives through a pull request, which must pass the CI checks (`audit` and `test` on Ubuntu, macOS and Windows) with all conversations resolved.
* Start a feature or fix from `develop` (`git switch -c feature/<name> develop`), open a pull request into `develop`, and merge it once CI is green.
* To release, open a pull request from `develop` into `master`. Use **Create a merge commit** (not squash or rebase) so the two branches keep a common history. After it is merged, tag the release on `master`, for example `git tag -a v2.7.0 -m "v2.7.0" && git push origin v2.7.0`.
* Run `make audit` before opening a pull request.

## Consistency

There are already +80 operators and +250 unit tests. Please don't try necessarily to reinvent the wheel and make sure to check first how the current implementation solves the most common problems.

## Edge Case

When we develop a new operator, there are a lot of edge cases to handle (eager/lazy observation, sequential vs parallel, on error strategy, etc.). The utility functions `observable()`, `single()` and `optionalSingle()` are there to help. Yet, it is not always possible to use them (observation of multiple Observables, etc.). In this case, you may want to take a look at existing operators like `WindowWithTime` to see an exhaustive implementation.

## Unit Tests

Make sure to include unit tests. Again, consistency is key. In most of the unit tests, we use the RxGo assertion API.

## Duration

If an operator input contains a duration, we should use `rxgo.Duration`. It allows us to mock it and to implement deterministic tests whenever possible using `timeCausality()`.

## Write Nice Code

Try to write idiomatic code according to [Go style guide](https://github.com/golang/go/wiki/CodeReviewComments). Also, see this project style guide for project-specific idioms (when in doubt, consult the first).

## Code Formatting

Before to create a pull request, make sure to format your code using:

* Run `go fix ./...` and `gofmt -s -w .`.
* Optionally, [gofumpt](https://github.com/mvdan/gofumpt):
    * Install: `go install mvdan.cc/gofumpt@latest`
    * Execute: `gofumpt -w .`

## Run the tests

The project requires Go 1.27.1 or later. Before opening a pull request, run:

```
make test
```

This runs the unit tests with the race detector and the goroutine leak tests. Tests that start goroutines should call `defer goleak.VerifyNone(t)` first and cancel their context with `defer cancel()` (not `t.Context()`, which is only cancelled after the test function returns).

## Open an issue

This is to encourage discussions and reach a sound design decision before implementing an additional feature or fixing a bug. If you're proposing a new feature, make it obvious in the subject.
