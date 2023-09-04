<!-- omit in toc -->
# Git and Github Bootcamp

Collection of notes and exercises for learning how to use Git and Github.

<!-- omit in toc -->
## Table of Contents

- [1. Git Core](#1-git-core)
- [2. Amend, Switching, Branching, and Merging](#2-amend-switching-branching-and-merging)
- [3. Mergin and Resolving Conflicts](#3-mergin-and-resolving-conflicts)
- [4. Comparing Files, Commits, and Branches](#4-comparing-files-commits-and-branches)
- [5. Other Notes](#5-other-notes)
  - [5.1. Git File Restore](#51-git-file-restore)
  - [5.2. Important Commands](#52-important-commands)
  - [5.3. Commands for Local Repositories](#53-commands-for-local-repositories)
  - [5.4. Command for Remote Repositories](#54-command-for-remote-repositories)
  - [5.5. Exercise Notes (Git and GitHub)](#55-exercise-notes-git-and-github)
- [6. Examples](#6-examples)
  - [6.1. Merge Branches, Not Commits](#61-merge-branches-not-commits)
- [7. TODO](#7-todo)

## 1. Git Core

Git is a version control system that tracks and manages changes to files over time. It allows users to review and compare previous file versions, revert changes, and more. For initial setup and configuration of git, refer to the official [git documentation](https://git-scm.com/book/en/v2/Appendix-C%3A-Git-Commands-Setup-and-Config).

One of Git's key concepts is the `HEAD`, a reference to your current location in the repository. It's essentially a pointer to a branch, which in turn is a pointer to a commit. Initially, `HEAD` points to the last commit of the `master` branch, but its position can change as we navigate through the repository.

Let's illustrate this with an example:

1. **First commit:** Both the `master` branch and `HEAD` point to this commit.
2. **Second commit:** The `master` branch and `HEAD` update to point to this new commit.
3. **Create "new-feature" branch:** This branch, now also pointed to by `HEAD`, mirrors the `master` branch's current commit.
4. **Commit in "new-feature":** This new commit updates the `new-feature` branch and `HEAD`, leaving `master` unchanged.
5. **Switch back to master:** `HEAD` updates to point back to the `master` branch, leaving the branch pointers untouched.

We can consider the branch references as "bookmarks" in a book that we can use to jump to a specific page. These bookmarks keep track of different paths in the story. The `HEAD` is the page we are currently reading. At any given time, we're only reading from one page (the `HEAD`), but we can have multiple bookmarks in the book (branches). This way, we can keep track of where we left off in different parts of the story (different lines of development).

Also, it's important to note that we can move to a specific page without using a bookmark (i.e., without using a branch reference) by using the `git switch --detach` (or `git checkout`) command and specifying the commit hash. This is similar to remembering a specific page number and going directly to it. However, without a bookmark, it might be harder to remember where you were, especially if you move to other pages (commits). That's why it's often easier to work with branches: they are like bookmarks that help you keep track of where you've been in the project history.

After branching off from the `master` branch, we can make changes to the code and commit them to the `new-feature` branch. The `master` branch remains unchanged. When we're done with the new feature, we can merge the `new-feature` branch back into the `master` branch. This will combine all the changes made in the `new-feature` branch with the `master` branch since their common ancestor (base) commit.

<!-- adjust link to example -->
It's important to note that we merge branches, not individual commits (see [6.1. Merge Branches, Not Commits](#61-merge-branches-not-commits)) . When we perform a merge operation, Git identifies the common ancestor commit of the two branches and integrates the changes made in both branches since that commit **into** the current `HEAD` branch. If there are conflicting changes, these will need to be resolved manually.

## 2. Amend, Switching, Branching, and Merging

- **Amend the most recent commit**:

   ```shell
   git add <forgotten file>
   git commit --amend -m 'New commit message'
   ```

- **Create a New Branch and Switch to It**

   Creating a new branch and switching to it are often steps performed in sequence when you're starting work on a new feature, bugfix, or experiment. Traditionally, `git checkout` was used for these tasks, but Git has introduced more intuitive, task-specific commands like `git switch` and `git restore` for better clarity and separation of concerns.

   The `git switch` command is more specific to the task of switching branches and makes the action clearer:

   ```shell
   git switch <branch_name>
   ```

   If you want to create a new branch and switch to it in one go, you can do:

   ```shell
   git switch -c <new_branch_name>
   ```

   This is similar to `git checkout -b <new_branch_name>`, but it's clearer in intent.

   Always Stash or Commit Changes Before Switching! Before you switch branches, it's good practice to make sure your working directory is clean. There are two primary ways to do this:

   1. **Committing Changes**: If the changes are complete, and you're ready to save them, commit those to the current branch. This is straightforward:

   2. **Stashing Changes**: If your changes are not complete and you're not ready to commit, you can "stash" them. This takes your changes and stores them in a temporary area so that you can reapply them later:

    ```shell
    git stash
    ```

    After you've switched to the new branch, you can reapply these changes with:

    ```shell
    git stash apply
    ```

   **Why It's Important**: If you try to switch branches with a dirty working directory, i.e., with uncommitted changes, Git will throw an error if those changes conflict with the branch you're trying to switch to. Even if there's no conflict, carrying uncommitted changes across branches can make for a confusing history and can complicate things like code reviews.

- Delete a branch:

   ```shell
   git branch -d <branch name>
   ```

   Note that you can't delete the branch you're currently on. If you want to delete the current branch, you need to switch to another branch first.

   If the branch you want to delete hasn't been merged yet, you can use the `-D` flag instead of `-d` to force the deletion.

- Rename a branch:

   ```shell
   git branch -m <old_branch> <new_branch>
   ```

- Merge a branch into the current (`HEAD`) branch. Let's assume you want to merge changes from a branch named `feature` into the `main` branch. First, switch to the `main` branch:

   ```shell
   git switch main
   ```

   Next, merge the `feature` branch:

   ```shell
   git merge feature
   ```

   If the `main` branch has not diverged from when `feature` was created (meaning, no other changes were made to `main` after branching/`main` is a direct ancestor of `feature`), Git performs a **fast-forward merge**. In this case, Git simply moves the `HEAD` pointer and `main` branch pointer to the latest commit of `feature`.

   However, if changes were made on `main` after `feature` branched off, Git performs a **three-way merge**. This involves finding the common ancestor of `main` and `feature` and creating a new merge commit that combines changes from each branch. This merge commit has **two parents**: the latest commits of `main` and `feature`. Both `HEAD` and `main` branch pointer then move to this new commit.

   If `main` and `feature` have conflicting changes, you'll need to resolve these manually. Once resolved, create the merge commit by running `git commit`. If you decide to abort the merge, run `git merge --abort` to revert your repository to its state before attempting the merge.

## 3. Mergin and Resolving Conflicts

1. First, start the merge process by switching to the `main` branch:

   ```shell
   git switch main
   git merge feature
   ```
  
  If there are any conflicts, Git will let you know in the command output
  
2. Open the file with conflicts in your text editor. You'll see the conflicting changes marked in the following way:

   ```shell
   <<<<<<< HEAD
   [Changes made on the current branch]
   =======
   [Changes made on the other branch]
   >>>>>>> feature
   ```

   Everything between `<<<<<<< HEAD` and `=======` represents the changes on the current branch (`main` in our case). And everything between `=======` and `>>>>>>> feature` are the changes on the `feature` branch.

3. To resolve the conflict, you need to decide if you want to keep the changes from the `main` branch, the `feature` branch, or a combination of both. Edit the file to make it look like the way you want the final code to be. Delete the conflict markers `<<<<<<<`, `=======`, and `>>>>>>>` when you're done editing.

4. After resolving the conflict in a file, you need to add it to the staging area:

   ```shell
   git add <filename>
   ```

5. Repeat the process for all files with conflicts.
  
6. Once you've resolved all conflicts and staged the changes, commit the merge:

   ```shell
   git commit -m "Resolved merge conflicts for merging feature into main"
   ```

Remember, resolving conflicts can sometimes be tricky if the same part of the code has been significantly changed in both branches. It might be necessary to consult with the author of the changes or to understand the context of each change to make an informed decision.

For complex projects with multiple contributors, it's good practice to communicate openly about conflicts and to resolve them collaboratively.

Note that after merging you can delete all branches that are no longer needed. For example, if you've merged the `feature` branch into `main`, you can delete the `feature` branch:

```shell
git branch -d feature
```

Or multiple branches at once:

```shell
git branch -d feature1 feature2 feature3
```

Or all branches but main with one of the following commands:

```shell
git branch -d $(git branch | grep -v main)
# or
git branch | grep -v main | xargs git branch -d
```

## 4. Comparing Files, Commits, and Branches

Note that in the documentation, the **stagging area** is also called **index**.

- **Working Directory** vs. **Staging Area** (only unstaged changes)

   ```shell
   git diff
   ```

    This command highlights what has been changed but is not yet staged for the next commit. Note that you can compare individual files by specifying the file name, i.e., `git diff <filename>`.

- **Staged File** vs. **Last Commit (`HEAD`)** (only staged changes)

   ```shell
   git diff --staged
   # or
   git diff --cached
   ```

- **Working Directory (and Staging Area)** vs. **Last Commit (`HEAD`)** (both staged and unstaged changes)

   ```shell
   git diff HEAD
   ```

   This command will show both staged and unstaged changes combined, as they would look if you committed them right now.

- Compare Branches, Commits, or Files in Different Commits

   ```shell
   git diff <branch1> <branch2>
   git diff <commit1> <commit2>
   git diff <commit1>:<file1> <commit2>:<file1>
   # or
   git diff <commit1> <commit2> <file1>
   ```

## 5. Other Notes

### 5.1. Git File Restore

Git provides different ways to restore your files to a previous state, depending on whether you want to restore the working directory file to its staged version, the last committed version, or to move the staged version of the file back to its last committed version.

- Restore Working Directory File to its Staged Version

   If you made changes to your working directory file and want to discard those changes and restore the file to its staged version, you can do so with the following command:

   ```shell
   git restore <filename>
   ```

   Note that this is equivalent to running `git restore --worktree <filename>`.

- Restore Working Directory File to its Last Committed Version

   If you want to discard all changes to a file (both staged and unstaged) and restore the file to its last committed version, you can do so with the following command:

   ```shell
   git restore --source=HEAD <filename>
   ```

   This command will restore the file to the state it was in the last commit.

- Restore Staged Version of the File to its Last Committed Version

   If you've added changes to the staging area (with `git add`) and want to unstage those changes and restore the file to its last committed version, you can do so with the following command:

   ```shell
   git restore --staged <filename>
   ```

   This command will unstage the changes and leave the file in your working directory untouched.

### 5.2. Important Commands

- `git init`: initialize a git repository in the current directory
- `git add <file>`: add a file to the staging area
- `git commit`: commit the staged files
- `git status`: see the status of the current repository
- `git merge <branch>`: merge the specified branch into the current branch
- `git switch <branch>`: switch to the specified branch and update the working directory
- `git switch -c <branch>`: create and switch to a new branch
- `git branch`: list all the branches in the current repository
- `git branch -d <branch>`: delete a branch`

### 5.3. Commands for Local Repositories

- `git log`: see the commit history
- `git diff`: see the changes between commits, branches, etc.
- `git branch <BRANCH_NAME>`: create a branch
- `git branch -d <BRANCH_NAME>`: delete a branch
- `git merge <BRANCH_NAME>`: merge a branch into the current branch

### 5.4. Command for Remote Repositories

- `git remote add <REMOTE_NAME> <REMOTE_URL>`: add a remote repository
- `git push <REMOTE_NAME> <BRANCH_NAME>`: push a branch to a remote repository
- `git pull <REMOTE_NAME> <BRANCH_NAME>`: pull a branch from a remote repository
- `git clone <REMOTE_URL>`: clone a remote repository
- `git fetch <REMOTE_NAME>`: fetch a remote repository
- `git reset`: reset the staging area
- `git revert`: revert a commit
- `git stash`: stash changes
- `git stash pop`: pop the most recent stash
- `git stash list`: list all stashesexit
- `git stash clear`: clear all stashes
- `git config --global alias.<ALIAS_NAME> <COMMAND>`: create a custom alias
- `git rebase <BRANCH_NAME>`: rebase a branch onto the current branch
- `git tag <TAG_NAME>`: create a tag
- `git tag -d <TAG_NAME>`: delete a tag
- `git reflog`: see the reflog
- `git reflog delete`: delete the reflog
- `git reflog expire --expire-unreachable=now --all`: delete the reflog
- `git reflog expire --expire=now --all`: delete the reflog

### 5.5. Exercise Notes (Git and GitHub)

Once your pull request has been approved and merged into the main branch, you typically don't need the pull request branch anymore. You can delete it to keep your repository tidy. However, depending on your team's workflow and policies, you might want to keep the branch for a while for reference or in case additional changes or fixes are needed.

If you choose to delete the branch, you can do so both locally and on the remote:

1. Delete the branch locally:

   ```shell
   git branch -d <branch-name>
   ```

   If the branch hasn't been merged and you still want to delete it, you can force the deletion:

   ```shell
   git branch -D <branch-name>
   ```

2. Delete the branch on the remote:

   ```shell
   git push origin --delete <branch-name>
   ```

   Before you delete the branch, switch back to the main branch (usually `master` or `main`) or another branch:

   ```shell
   git switch <main-branch-name>
   ```

   Also, update your local main branch with the latest changes:

   ```shell
   git pull origin <main-branch-name>
   ```

Deleting a branch doesn't delete the commits on that branch, it simply removes the branch pointer. The commits are still in the Git repository and can be accessed directly via their commit hashes.

You can use `git switch` followed by a commit hash to move the `HEAD` to a specific commit:

```shell
git switch <commit-hash>
```

Doing so will put you in a "detached HEAD" state, where you're not on any branch. Any new commits you make in this state won't be associated with any branch and will be lost once you switch back to a normal branch, unless you create a new branch while you're in the detached HEAD state:

```shell
git switch -c <new-branch-name>
```

Please note that commits not reachable by any branch or tag may be deleted by Git's garbage collection process. If you want to keep these commits, you should create a new branch to point to them.

## 6. Examples

### 6.1. Merge Branches, Not Commits

Imagine you have a repository with the following history (newest commits at the top):

```sql
* commit D (HEAD -> feature)
* commit C
* commit B (main)
* commit A
```

**Step 1: Switch to the Branch You Want to Merge Into**

First, you'll typically check out to the branch you want to merge the changes into (often this is `main` or `master`).

```shell
git switch main
```

Your `HEAD` is now pointing at `main`, specifically commit `B`.

**Step 2: Perform Merge**

Next, you merge the `feature` branch into `main`.

```shell
git merge feature
```

**What Happens During Merge**

1. **Identify Common Ancestor**: Git identifies the common ancestor of the two branches, which is commit `B` in this case.
2. **Integrate Changes**: Git then looks at commits `C` and `D` on the `feature` branch that occurred after the common ancestor `B`, and applies those changes on top of commit `B` in `main`.
3. **New Merge Commit**: Usually, a new "merge commit" is created to mark the point where the two branches were merged. This commit will have two parent commits: the previous tip of the `main` branch (commit `B`) and the tip of the `feature` branch (commit `D`).

Your history would look something like this if the merge is successful:

```sql
* commit E (HEAD -> main, Merge commit)
|\  
| * commit D (feature)
| * commit C
* | commit B
|/
* commit A
```

- `main` is now at commit `E`, which integrated the changes from the `feature` branch.
- `feature` branch still points to commit `D`.

**Step 3: Handling Conflicts**

If changes in `feature` conflict with changes in `main` since the common ancestor ( `B`), Git will prompt you to resolve these conflicts manually. Once resolved, you can proceed to create the merge commit.

So when the statement says "we merge branches, not individual commits," it means the operation takes into account the series of commits that have occurred on the branches since their common ancestor. The merge operation attempts to integrate all of these changes into the `HEAD` branch ( `main` in this example).

## 7. TODO

- Remove the title format from the lists in section 4 and 5
