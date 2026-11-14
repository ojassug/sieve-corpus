# Verification notes

## 2026-11-12 — publishing re-applies onto later commits

This commit was pushed to `main` while run 43289738736 was still collecting, to check that
the run's publish step lands its manifest and state on top of a commit that did not exist
when the job started.

The publish fetches `origin/main` and re-applies the run's own files onto whatever it finds,
rather than pushing the branch it checked out hours earlier. So this file should survive the
run, and the run's `corpus: 2026-11-12-3` commit should be a child of this one. Before that
change the run's final `git push` would have been rejected as a non-fast-forward, and the
run's manifest and state would have been lost while its release kept the data.

This push was deliberately made hours ahead of the publish, so it does not force the retry
path: the publish picks it up in its first fetch and succeeds on the first attempt. Pushing
inside the few seconds between that fetch and the push is what exercises the retry, which is
covered by the tests in `tools/corpus/test/publish.test.ts` instead.

Design and reasoning: DECISIONS 22 in trommelci/trommel.
