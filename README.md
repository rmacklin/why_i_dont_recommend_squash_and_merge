# Why I don' t recommend GitHub's squash and merge
Sample repo demonstrating the downsides of GitHub's "squash and merge" compared to creating a merge commit

## Multiple commit authors in a single PR

With a merge commit, the git blame still attributes each change to the right author. With squash and merge, it's all attributed to one author, who may have only contributed a single commit with a very small change.

[Good (merge commit)](https://github.com/rmacklin/why_i_dont_recommend_squash_and_merge/pull/1#issuecomment-244521210) vs [Bad (squash and merge)](https://github.com/rmacklin/why_i_dont_recommend_squash_and_merge/pull/2#issuecomment-244521234)

## Moving/renaming a file

It is frequently necessary to move/rename a file in one commit and then modify it in a second commit in order for git to track it as the same file. With a merge commit, git will still know it's the same file. With squash and merge, it will look like a file deletion and a separate file addition.

[Good (merge commit)](https://github.com/rmacklin/why_i_dont_recommend_squash_and_merge/pull/3#issuecomment-244521443) vs [Bad (squash and merge)](https://github.com/rmacklin/why_i_dont_recommend_squash_and_merge/pull/4#issuecomment-244521380)

## Pre-squash commits won't be included in clones if you delete your feature branches

If you delete your feature branches (which you should, to avoid branch clutter), there's no way to see the pre-squash commits in new clones of the repo.
