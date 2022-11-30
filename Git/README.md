# 1 Intro

The Book on Git Guide: https://git-scm.com/book/en/v2 .

# 2 Squeeze Commits

```sh
# make several commits into one
$ git reset HEAD^^^ (reset head to the 3rd last commit)
$ git add (then add all modifications again)
$ git commit (commit changes)

# way two:
$ git rebase -i HEAD~#{number of commits} 
# e.g. "git rebase -i HEAD~5". Then within the next page, pickup the last commit you want, and squash other commit if we need, then complete rebase with new commit, after that all needed commits should be in one commit.
$ git log (check the latest commit)

# show one commit changed files
$ git show --pretty=""  --name-only f7748e47a1a70f004068be4642d04166f47cc692
```

# 3 Revert/Rollback Commits

```sh
# This will create three separate revert commits:
git revert a867b4af 25eee4ca 0766c053

# It also takes ranges. This will revert the last two commits:
git revert HEAD~2..HEAD

#Similarly, you can revert a range of commits using commit hashes (non inclusive of first hash):
git revert 0d1d7fc..a867b4a

# Reverting a merge commit
git revert -m 1 <merge_commit_sha>

# To get just one, you could use `rebase -i` to squash them afterwards
# Or, you could do it manually (be sure to do this at top level of the repo)
# get your index and work tree into the desired state, without changing HEAD:
git checkout 0d1d7fc32 .

# Then commit. Be sure and write a good message describing what you just did
git commit
```

For original answer, please check [Stack Overflow](https://stackoverflow.com/questions/4114095/how-do-i-revert-a-git-repository-to-a-previous-commit
https://git-scm.com/docs/git-revert)

# 4 Git Alias

```sh
# have shortcut "git s" for "git status"
$ git config --global alias.s status
```

refer to https://git-scm.com/book/en/v2/Git-Basics-Git-Aliases for details.