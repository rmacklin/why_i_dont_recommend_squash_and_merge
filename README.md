# Why I don't recommend GitHub's squash and merge

A sample repository demonstrating the downsides of GitHub's "squash and merge"
compared to a regular merge

## Multiple commit authors in a single PR aren't respected

With a merge commit, the git blame still attributes each change to the right
author. With squash and merge, it's all attributed to one author, who may have
only contributed a single commit with a very small change.

[Good (merge commit)](https://github.com/rmacklin/why_i_dont_recommend_squash_and_merge/pull/1#issuecomment-244521210)
vs
[Bad (squash and merge)](https://github.com/rmacklin/why_i_dont_recommend_squash_and_merge/pull/2#issuecomment-244521234)

Update: GitHub made this a bit better with co-author attribution on squash
merges:
https://github.blog/changelog/2019-12-19-improved-attribution-when-squashing-commits.
However, it still doesn't attribute specific changes (modified lines) to their
respective individual authors, so it's still less useful than the regular merge
commit history.

## Moving/renaming a file can disconnect its history

It is frequently necessary to move/rename a file in one commit and then modify
it in a second commit in order for git to track it as the same file. With a
regular merge these commits are preserved, so git will still know it's the same
file and preserve its history. With squash and merge, it will look like a file
deletion and a separate file addition, so `git blame` will no longer show the
original author of each line (before it was renamed/moved).

[Good (merge commit)](https://github.com/rmacklin/why_i_dont_recommend_squash_and_merge/pull/3#issuecomment-244521443)
vs
[Bad (squash and merge)](https://github.com/rmacklin/why_i_dont_recommend_squash_and_merge/pull/4#issuecomment-244521380)

## Pre-squash commits won't be included in clones

If you delete your feature branches (which you should, to avoid branch
clutter), there's no way to see the pre-squash commits in new clones of the
repo. This means those clones can't be used to view the diff of an individual
commit nor can they revert an individual commit. The
[rmacklin/squash_and_merge_example](https://github.com/rmacklin/squash_and_merge_example)
repo demonstrates this. It's the same as this repo but with the
`squash-and-merge` branch set to `master` and the feature branches deleted (so
it resembles a real repo that only uses squash and merge).

## Git bisect is less granular, so you have to do more manual bug hunting

Suppose a pull request has 10 commits, each with a diff stat of around +50/-30
but each one only modifying 1 to 3 files. The combined diff for the entire PR
might be around +500/-300 across up to 30 files. If it ends up squashed into a
single commit with that diff, `git bisect` becomes significantly less valuable
when it lands on that commit as the culprit for a regression: The bug could be
in any of those files! On the other hand, if the original history of atomic
commits was preserved with a regular merge, `git bisect` will drop you on one
of those +50/-30 commits that only touched a couple files, making it much
easier to quickly spot the offending code.

# You can have your cake and eat it, too!

Now comes the good news! First of all, let me be clear that I absolutely
support squashing commits when you realize you made a mistake in an earlier
commit. Many people know about `git commit --amend` to correct the most recent
commit. Fewer people know how to quickly amend a commit that's further back in
the history, though. But git actually makes this quite efficient with
`git commit --fixup` and
[auto-squashing](https://robots.thoughtbot.com/autosquashing-git-commits)! So,
you can (and should) squash away typos/syntax errors/etc. before it's time to
merge your PR, which will result in a more useful/concise/atomic history
(without all the drawbacks of GitHub's squash and merge button).

More importantly, even when you merge pull requests using regular merge
commits, you'll still have access to the condensed history that you would've
had if those PRs had been "squash and merged". While `git log` will give you
access to all of the granular commits from each PR, `git log --first-parent`
will give you the history of each merge into the branch, omitting the
individual commits from each PR, just like the history you would've had with
squash and merge! The `--first-parent` flag can also be passed to other
commands such as `git bisect --first-parent`, `git blame --first-parent`, and
`git show --first-parent` to have them operate as if you had a history of
"squash and merged" PRs. Also, the unsquashed history affords you the ability
to revert both an individual commit (`git revert SHA`) and an entire PR
(`git revert -m 1 MERGE_SHA`) in one go. You really get the best of both worlds
when you use regular merge commits!

So, go ahead and say "No" to GitHub's "squash and merge" button - you won't
regret it :)
