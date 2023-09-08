<!-- omit in toc -->
# Git and Github Bootcamp

Collection of notes and exercises for learning how to use Git and Github.

<!-- omit in toc -->
## Table of Contents

- [1. Git Core](#1-git-core)
- [2. Amend, Switching, Branching, and Merging](#2-amend-switching-branching-and-merging)
- [3. Mergin and Resolving Conflicts](#3-mergin-and-resolving-conflicts)
- [4. Comparing Files, Commits, and Branches](#4-comparing-files-commits-and-branches)
- [5. Stashing Changes](#5-stashing-changes)
  - [5.1. Introduction](#51-introduction)
  - [5.2. Stashing Commands](#52-stashing-commands)
- [6. Reset (Unstage and Discard) and Revert](#6-reset-unstage-and-discard-and-revert)
- [7. Restore: Unstaging and Unmodifying](#7-restore-unstaging-and-unmodifying)
  - [7.1. Working with Staged Changes](#71-working-with-staged-changes)
  - [7.2. Working with Unstaged Changes](#72-working-with-unstaged-changes)
- [8. Clean Working Directory](#8-clean-working-directory)
- [9. GitHub Basics](#9-github-basics)
  - [9.1. Pushing Changes to Remote](#91-pushing-changes-to-remote)
- [10. Examples](#10-examples)
  - [10.1. Merge Branches, Not Commits](#101-merge-branches-not-commits)
- [11. Other Notes](#11-other-notes)
  - [11.1. Working Directory vs. Staging Area](#111-working-directory-vs-staging-area)
  - [11.2. Git Workflow Diagram Explained](#112-git-workflow-diagram-explained)
  - [11.3. Avoid Git Checkout](#113-avoid-git-checkout)
  - [11.4. Remove Sensitive Information from Git History](#114-remove-sensitive-information-from-git-history)
  - [11.5. Important Commands](#115-important-commands)
  - [11.6. Commands for Local Repositories](#116-commands-for-local-repositories)
  - [11.7. Command for Remote Repositories](#117-command-for-remote-repositories)
  - [11.8. Exercise Notes (Git and GitHub)](#118-exercise-notes-git-and-github)
- [12. TODO](#12-todo)

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

   1. **Committing Changes**: If the changes are complete, and you're ready to save them, commit those to the current branch.

   2. **Stashing Changes**: If your changes are not complete and you're not ready to commit, you can "stash" them. This takes your changes and stores them in a temporary area so that you can reapply them later.

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
# or git branch | grep -v main | xargs git branch -d
```

## 4. Comparing Files, Commits, and Branches

Note that in the documentation, the **stagging area** is also called **index**.

- **Working Directory vs. Staging Area** (only unstaged changes)

   ```shell
   git diff
   ```

    This command highlights what has been changed but is not yet staged for the next commit. Note that you can compare individual files by specifying the file name, i.e., `git diff <filename>`.

- **Staged File vs. Last Commit (`HEAD`)** (only staged changes)

   ```shell
   git diff --staged
   # or git diff --cached
   ```

- **Working Directory (and Staging Area) vs. Last Commit (`HEAD`)** (both staged and unstaged changes)

   ```shell
   git diff HEAD
   ```

   This command will show both staged and unstaged changes combined, as they would look if you committed them right now.

- Compare Branches, Commits, or Files in Different Commits

   ```shell
   git diff <branch1> <branch2>
   git diff <commit1> <commit2>
   git diff <commit1>:<file1> <commit2>:<file1>
   # or git diff <commit1> <commit2> <file1>
   ```

## 5. Stashing Changes

### 5.1. Introduction

Stashing is a way to temporarily store both staged and unstaged changes that you don't want to commit immediately. This is particularly useful when you need to quickly switch context and work on a different branch, but you're in the middle of making code changes that aren't ready to be committed.

1. **Changes Come With You**: If you switch branches without stashing, your changes (both staged and unstaged) will come with you to the new branch, as long as they don't conflict with files in the destination branch. This is the default behavior of Git and works well for quick context switches, assuming no conflicts.
2. **Potential Conflicts**: Git will prevent you from switching branches if it detects that the changes you have (staged or unstaged) would conflict with the destination branch. This is where git stash becomes particularly useful. You can stash your changes, switch branches, and then later apply the stash to continue where you left off.

So, stashing is a way to sidestep both of these issues: it allows you to cleanly switch branches without committing unfinished work, and it eliminates the issue of conflicts when switching branches.

When you stash your changes, it results in a "clean working tree." A **clean working tree** means that there are no changes that are staged (in the staging area) or unstaged (in the working directory). Having a clean working tree is often necessary for various Git operations, like pulling from a remote repository or switching to a different branch that has conflicting changes.

### 5.2. Stashing Commands

- **Stash Changes**: This command takes all uncommitted changes (both staged and unstaged) and stores them in a temporary area, leaving you with a clean working tree.

   ```shell
   git stash
   ```

   By default, git stash will stash both staged and unstaged changes. If you want more control over what to stash, you can use the following flags:
  - --keep-index: Stash only unstaged changes and keep staged changes in the staging area.
  - --include-untracked: Stash both staged and unstaged changes, as well as any untracked files.
  - --all: Stash all changes, including ignored files.

- **List Stashes**: This command lists all stashes in the order they were created. The most recent stash is at the top.

   ```shell
   git stash list
   ```

- **Apply or Pop Stash**: The apply command reapplies the most recent stash to your working directory without removing it from the stash list. On the other hand, pop both reapplies and removes the most recent stash from the stash list.

   ```shell
   git stash apply
   # or
   git stash pop
   ```

   If you want to apply or pop a specific stash, you can specify its index like so:

   ```shell
   git stash apply stash@{<stash_number>}
   # or
   git stash pop stash@{<stash_number>}
   ```

- **Drop a Specific Stash or Clear All Stashes**: The drop command deletes a specified stash from the stash list. The clear command removes all stashes from the stash list.

   ```shell
   git stash drop stash@{<stash_number>}
   # or
   git stash clear
   ```

## 6. Reset (Unstage and Discard) and Revert

- **Unstage Changes from the Staging Area**: If you've staged changes with `git add` and want to unstage those changes while leaving your working directory unaffected, use:

    ```shell
    git reset
    ```

    ⚠️ **Warning**: This command only affects the staging area, not your working directory. The changes you unstaged will move from the staging area back to your working directory.

    💡 **Note**: You can also use `git reset` to move the HEAD and branch pointer to a specific past commit, effectively allowing you to create a new branch that starts at that commit.

- **Discard All Uncommitted Changes (Destructive)**: If you wish to discard all changes in your working directory and staging area, reverting them to the state of the last commit, use:

    ```shell
    git reset --hard
    ```

    ⚠️ **Warning**: This is a destructive operation that will permanently discard all unstaged and staged changes. Your working directory and staging area will be reset to match the last committed state, based on `HEAD`.

- **Revert a Commit**: To create a new commit that undoes the changes from a specific past commit, use:

    ```shell
    git revert <commit_hash>
    ```

    This will create a new commit that reverses the changes made in the specified commit. This is often the preferred method for undoing changes when collaborating with others, as it doesn't rewrite commit history.

    **Note for Collaboration**: Using `git revert` is particularly useful when collaborating with others because it adds a new commit to reverse the changes, rather than altering existing commits. This makes it safer and more transparent when working on shared branches.

## 7. Restore: Unstaging and Unmodifying

### 7.1. Working with Staged Changes

- **Unstage Changes**: If you've staged changes with `git add` and want to unstage those changes, use:

    shell

    ```shell
    git restore --staged <filename>
    ```

    ⚠️ **Warning**: Unstaging changes won't affect the changes in your working directory. Note that `git reset` can also be used to unstage changes, but it's a more general command that can also be used to reset the state of your working directory to match a specific commit.

### 7.2. Working with Unstaged Changes

- **Remove Unstaged Changes and Match the Staged or Last Committed Version**: If you've made changes to a file but haven't staged them, and you want to undo these changes, you can:

    shell

    ```shell
    git restore <filename>
    ```

    ⚠️ **Warning**: Using this command will remove any unstaged changes, making the file in your working directory match the staged version. **Be cautious, as any unsaved changes will be lost**. This could revert the file to the last committed version or a previously staged but uncommitted change.

- **Restore File to a Specific Past Commit**: To restore a file in the working directory to the state it was in at a specific past commit, use:

    shell

    ```shell
    git restore --source=<commit_hash> <filename>
    ```

    ⚠️ **Warning**: Exercise caution when using this command. It will restore the file in the working directory to the state it was in at the specified commit, discarding all unstaged changes made to the file since then. This operation is irreversible and can lead to data loss. Note that this does not affect any changes that have been staged.

## 8. Clean Working Directory

A **clean working directory** means that there are no changes that are staged (in the staging area) or unstaged (in the working directory). Having a clean working directory is often necessary for various Git operations, like pulling from a remote repository or switching to a different branch that has conflicting changes.

If you want to remove untracked files (files that are not yet tracked by Git), you can use the `git clean` command.

- **Dry Run:** Before actually removing any files, you can do a dry run to see which files would be removed:

    bash

    ```bash
    git clean -n
    ```

- **Remove Untracked Files:** Once you're sure about which files to remove, you can use the following command to remove all untracked files:

    bash

    ```bash
    git clean -f
    ```

- **Remove Untracked Directories:** If you want to remove untracked directories as well, you can use:

    bash

    ```bash
    git clean -f -d
    ```

**Caution:** `git clean` is a destructive operation. Once you remove untracked files/directories using this command, you can't recover them unless you have a backup elsewhere. Always double-check and maybe even back up important data before using this command.

## 9. GitHub Basics

GitHub is a hosting platform for Git repositories. It provides a web-based graphical interface and tools for collaboration. It also provides additional features like issue tracking, pull requests, and more.

### 9.1. Pushing Changes to Remote

The `git push` command is used to update a remote repository with your local commits. It takes your local repository changes and "pushes" them to a remote repository that you have write access to. This is how team members share changes with each other in a distributed version control system like Git.

- **Basic Syntax**: The basic syntax for `git push` is as follows:

    ```shell
    git push <remote> <local_branch>
    ```

  - `<remote>`: This is the name of the remote repository. By default, the original remote is named `origin`.

  - `<local_branch>`: This is the name of the local branch that you want to push to the remote repository. By default, the remote branch will have the same name, but you can specify a different name if you wish (see below).

- **Naming the Remote Branch**: If you want to push your local branch to a remote branch with a different name, you can specify it using the following syntax:

    ```shell
    git push <remote> <local_branch>:<remote_branch>
    ```

- **Default Behavior**: If you simply run `git push` without specifying the remote and branch, Git will use the configuration that was set up when you cloned the repository or added the remote. Typically, it will push the current branch to the corresponding remote branch that `git pull` would pull from (this is often the same-named branch on the `origin` remote).

- **Set Upstream**: You can also set an "upstream" branch, which configures which remote branch corresponds to the local branch. Once you've set an upstream branch, you can use `git push` without any arguments while on that branch:

    ```shell
    git push -u <remote> <local_branch>
    ```

    The `-u` flag sets the upstream for the branch, so future calls to `git push` or `git pull` can be done without specifying the branch.

## 10. Examples

### 10.1. Merge Branches, Not Commits

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

## 11. Other Notes

### 11.1. Working Directory vs. Staging Area

The term "working directory" refers to the *set of files and directories* in your local file system that is associated with a Git repository. These files represent the current state of your project and may include changes that are either staged or unstaged.

Here's a breakdown to help conceptualize:

- **Staged Changes**: These are changes that have been marked for inclusion in the next commit. Staging changes doesn't affect your working directory; it affects the staging area, which is a layer between the working directory and the repository.
- **Unstaged Changes**: These are changes in your working directory that have not yet been staged. These could be modifications to existing files or new files that haven't been added to the staging area yet.
- **Clean Working Directory**: This means that there are no changes in either staged or unstaged state. The working directory matches the latest commit in the current branch.

To conceptualize, consider your working directory as your "workspace" where you do your regular work—creating, editing, deleting files, etc. The staging area is like a "preparation area," a place where you gather all the changes that you want to commit. Finally, the Git repository itself is the "record book" or "database" that keeps a history of all the commits (sets of changes) made over time.

In summary, the working directory contains both staged and unstaged changes, but it's helpful to think of the staged changes as being in a separate "staging area," awaiting to be committed to the repository.

### 11.2. Git Workflow Diagram Explained

```mermaid
graph TD;
  A[Working Directory] -->|git add| B[Staging Area];
  B -->|git commit| C[Local Repository];
  C -->|git push| D[Remote Repository];
  D -->|git pull| C;
  C -->|git switch| A;
```

The above diagram illustrates the key areas of a Git workflow:

1. **Working Directory**: This is where your actual files live. When you make changes to your files, those changes are initially unstaged and exist only in your working directory.

    - **Transition to Staging Area**: You stage these changes by running `git add`, which moves the changes to the staging area.

2. **Staging Area**: This is an intermediate area where changes are collected before being permanently stored in the commit history of the local repository.

    - **Transition to Local Repository**: After staging, you run `git commit` to store the changes from the staging area into the local repository.

3. **Local Repository**: This is the `.git` folder in your project directory. It contains the entire history of your project.

    - **Transition to Remote Repository**: You can push commits from your local repository to a remote repository using `git push`.
  
    - **Transition to Working Directory**: You can update the working directory to reflect a specific commit or branch using `git switch`.

4. **Remote Repository**: This is a version of your project that is hosted on a remote server, typically platforms like GitHub, GitLab, or Bitbucket.

    - **Transition to Local Repository**: You can update your local repository to match the remote repository by running `git pull`, which fetches changes from the remote and merges them into your local repository.

### 11.3. Avoid Git Checkout

Avoiding git checkout in favor of more specialized commands is a good idea for clarity and specificity.

1. **Switching branches**: Use `git switch <branch_name>` instead of `git checkout <branch_name>`.

    ```bash
    git switch feature-branch
    ```

2. **Creating a new branch and switching to it**: Use `git switch -c <new_branch_name>` instead of `git checkout -b <new_branch_name>`.

    ```bash
    git switch -c new-feature-branch
    ```

3. **Restore a file to the last committed state**: Use `git restore <file>` instead of `git checkout -- <file>`.

    ```bash
    git restore some-file.txt
    ```

4. **Unstaging changes**: Use `git restore --staged <file>` instead of `git checkout HEAD -- <file>` to unstage changes.

    ```bash
    git restore --staged some-file.txt
    ```

5. **Applying changes from another branch**: Use `git apply` or `git cherry-pick` to bring in changes from another branch without switching to it. `git checkout` used to do this with the `-p` flag, but it's not the primary way to do it anymore.

6. **Detaching HEAD**: While `git checkout` could be used to detach the HEAD, you can do it explicitly using `git switch` and `git reset`:

    ```bash
    git switch --detach
    git reset --hard <commit_hash>
    ```

7. **Checking out submodules**: If you are working with submodules, instead of `git checkout` you might use a combination of `git submodule update` and other submodule commands.

### 11.4. Remove Sensitive Information from Git History

If you've accidentally committed a file containing sensitive information (like a file containing credentials), and you want to remove it from your Git history while retaining your latest changes, you can follow these steps:

**⚠️ WARNING: This will rewrite history, which can be dangerous if you're working on a shared branch. Make sure to consult with your team and proceed with caution.**

1. **Backup Your Repository**: Before doing anything, backup the current state of your repository.

    ```shell
    cp -R your_repo your_repo_backup
    ```

2. **Untrack the File**: First untrack the file from Git.

    ```shell
    git rm --cached <filename>
    ```

3. **Commit this change**: Commit this change to your history.

    ```shell
    git commit -m "Untrack sensitive file"
    ```

4. **Add File to `.gitignore`**: Add the filename to your `.gitignore` to ensure it won't be accidentally committed again.

    ```shell
    echo '<filename>' >> .gitignore
    ```

5. **Commit `.gitignore` Change**: Stage and commit this change.

    ```shell
    git add .gitignore
    git commit -m "Ignore sensitive file"
    ```

6. **Rewrite History**: Use the `filter-branch` command or `filter-repo` to actually remove the file from your entire commit history. Here's how you can do it with `filter-branch`:

    ```shell
    git filter-branch --force --index-filter \
      "git rm --cached --ignore-unmatch <filename>" \
      --prune-empty --tag-name-filter cat -- --all
    ```

    Note: `git filter-branch` is now a "deprecated" command, but it's still available for tasks like this. The recommended alternative is `git filter-repo`, which is more performant and flexible but has to be installed separately.

7. **Force Push to Remote**: Once your local history is cleaned up, force-push the changes to your remote repository.

    ```shell
    git push origin --force --all
    ```

    **⚠️ CAUTION**: This is a destructive operation and will overwrite changes on the remote that you don't have locally. Make sure nobody has pushed to the remote repository after your last fetch.

8. **Contact Collaborators**: If anyone else had pulled the repository while the sensitive data was in it, make sure they also remove their local history to prevent the data from being pushed again.

9. **Rotate Credentials**: Since the credentials were exposed, it's good practice to consider them compromised and rotate them for new ones.

Remember, once you've pushed a commit that contains sensitive information, you should consider that information compromised. Even if you remove the commit from your Git repository, GitHub keeps a log of recent changes, and there's no guarantee that someone hasn't cloned your repo and still has a copy of the sensitive data.

This should help you remove the sensitive file from your Git history. Follow these steps carefully and consult your team when executing such tasks, as they have ramifications on everyone's local repositories.

### 11.5. Important Commands

- `git init`: initialize a git repository in the current directory
- `git add <file>`: add a file to the staging area
- `git commit`: commit the staged files
- `git status`: see the status of the current repository
- `git merge <branch>`: merge the specified branch into the current branch
- `git switch <branch>`: switch to the specified branch and update the working directory
- `git switch -c <branch>`: create and switch to a new branch
- `git branch`: list all the branches in the current repository
- `git branch -d <branch>`: delete a branch`

### 11.6. Commands for Local Repositories

- `git log`: see the commit history
- `git diff`: see the changes between commits, branches, etc.
- `git branch <BRANCH_NAME>`: create a branch
- `git branch -d <BRANCH_NAME>`: delete a branch
- `git merge <BRANCH_NAME>`: merge a branch into the current branch
  - - `git reset`: reset the staging area
- `git revert`: revert a commit
- `git stash`: stash changes
- `git stash pop`: pop the most recent stash
- `git stash list`: list all stashesexit
- `git stash clear`: clear all stashes

### 11.7. Command for Remote Repositories

- `git remote add <REMOTE_NAME> <REMOTE_URL>`: add a remote repository
- `git push <REMOTE_NAME> <BRANCH_NAME>`: push a branch to a remote repository
- `git pull <REMOTE_NAME> <BRANCH_NAME>`: pull a branch from a remote repository
- `git clone <REMOTE_URL>`: clone a remote repository
- `git fetch <REMOTE_NAME>`: fetch a remote repository
- `git config --global alias.<ALIAS_NAME> <COMMAND>`: create a custom alias
- `git rebase <BRANCH_NAME>`: rebase a branch onto the current branch
- `git tag <TAG_NAME>`: create a tag
- `git tag -d <TAG_NAME>`: delete a tag
- `git reflog`: see the reflog
- `git reflog delete`: delete the reflog
- `git reflog expire --expire-unreachable=now --all`: delete the reflog
- `git reflog expire --expire=now --all`: delete the reflog

### 11.8. Exercise Notes (Git and GitHub)

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

## 12. TODO

- Remove the title format from the lists in section 4 and 5
