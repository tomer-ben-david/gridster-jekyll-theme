---
layout: post
title:  "Squash multiple push in git"
date:   2015-11-15 22:18:00
categories: devops,git
comments: true
---
And my way of `squashing` multiple `push` is (perhaps you pushed to your own branch many commits and now you wish to do a pull request and you don't want to clutter them with many commits which you have already pushed).  The way I do that (no other simpler option as far as I can tell is).

1. Create new branch for the sake of `squash` (branch from the original branch you wish to pull request to).
2. Push the newly created branch.
3. Merge branch with commits (already pushed) to new branch.
4. Rebase new branch and squash.
5. Push new branch.
6. Create new pull request for new branch which now has single commit.

Example:

    git checkout branch_you_wish_to_pull_request_to
    git checkout -b new_branch_will_have_single_squashed_commit
    git push -u new_branch_will_have_single_squashed_commit
    git merge older_branch_with_all_those_multiple_commits
    git rebase -i (here you squash)
    git push origin new_branch_will_have_single_squashed_commit

You can now pull request into `branch_you_wish_to_pull_request_to`
