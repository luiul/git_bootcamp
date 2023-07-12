<!-- omit in toc -->
# Git and Github Bootcamp

Collection of notes and exercises for learning how to use Git and Github. 

<details>
<summary>Course Curriculum</summary>

1. Git Core
   1. Commiting
   2. Branching
   3. Merging
2. Git Advanced 1
   1. Diffing
   2. Stashing
   3. Undoing (revert, reset, restore, etc.)
3. GitHub and collaboration
   1. Fetching and Pulling
   2. Odds and Ends
   3. Collaborative workflows
4. Git Advanced 2
   1. Rebasing
   2. Tagging
   3. Reflog
   4. Custom Aliases

</details>

<!-- Don't forget to update section numbers! -->
<!-- omit in toc -->
## Table of Contents

- [1. Git Core](#1-git-core)
- [Important commands](#important-commands)

## 1. Git Core

Git is a version control system. Version control is software that tracks and manages changes to files over time. Version control systems generally allow users to review previous versions of files, compare changes between versions, revert changes, etc.


## Important commands

- `git init`: initialize a git repository in the current directory
- `git status`: see the status of the current repository
- `git add <FILE>`: add a file to the staging area
- `git commit`: commit the staged files
- `git log`: see the commit history
- `git diff`: see the changes between commits, branches, etc.
- `git checkout <COMMIT_HASH>`: checkout a commit
- `git checkout <BRANCH_NAME>`: checkout a branch
- `git branch <BRANCH_NAME>`: create a branch
- `git branch -d <BRANCH_NAME>`: delete a branch
- `git merge <BRANCH_NAME>`: merge a branch into the current branch
- `git remote add <REMOTE_NAME> <REMOTE_URL>`: add a remote repository
- `git push <REMOTE_NAME> <BRANCH_NAME>`: push a branch to a remote repository
- `git pull <REMOTE_NAME> <BRANCH_NAME>`: pull a branch from a remote repository
- `git clone <REMOTE_URL>`: clone a remote repository
- `git fetch <REMOTE_NAME>`: fetch a remote repository
- `git reset`: reset the staging area
- `git restore`: restore a file to a previous commit
- `git revert`: revert a commit
- `git stash`: stash changes
- `git stash pop`: pop the most recent stash
- `git stash list`: list all stashes
- `git stash clear`: clear all stashes
- `git config --global alias.<ALIAS_NAME> <COMMAND>`: create a custom alias
- `git rebase <BRANCH_NAME>`: rebase a branch onto the current branch
- `git tag <TAG_NAME>`: create a tag
- `git tag -d <TAG_NAME>`: delete a tag
- `git reflog`: see the reflog
- `git reflog delete`: delete the reflog
- `git reflog expire --expire-unreachable=now --all`: delete the reflog
- `git reflog expire --expire=now --all`: delete the reflog