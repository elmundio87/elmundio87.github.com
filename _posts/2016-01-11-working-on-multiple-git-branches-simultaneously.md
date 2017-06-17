---
layout: post
title: "Working on multiple Git branches simultaneously"
description: "Using git-worktree for easy multi-branch work"
category: Version Control
tags: [git,scm]
---
Working on multiple branches at the same time has been a staple part of traditional source control tools such as CVS, SVN and Perforce, due to the fact that each branch is kept in a separate folder. Multiple branch versions of a single file can be opened in text editors and modified alongside each other, and this was taken for granted.

|*Filesystem of a SVN repository*|
|--------------------------------|
|![Filesystem diagram of a SVN repo](/assets/images/svn-filesystem.png)|

### Blob-based storage

Git came along, with a new way of doing things. Each commit is treated as a snapshot of the repository filesystem, a branch is simply a lightweight moveable pointer to a single commit. Every time you commit, the branch pointer is moved forward automatically. 

Branch and commit data is kept in the hidden .git folder, found in the root of every Git repository. If you open this folder, you will see that it's stored as a set of binary blobs. When a Git commmand is run (eg. **git reset --hard HEAD**), Git will render the current state of the workspace based on the contents of these blobs.

This means that a workspace can only show one branch at a time on the filesystem,  switching to a different branch will result in files being replaced or removed.

|*Filesystem of a Git repository*|
|--------------------------------|
|![Filesystem diagram of a Git repo](/assets/images/git-filesystem.png)|

Developers that are used to SVN or TFS will often raise this as a reason not to switch to Git. Before Git version 2.5, the only way to view 2 branches side by side on the filesystem was to clone 2 instances of the remote repository, which is hacky at best.

### git-worktree

Thankfully, with version 2.5 of Git came [git-worktree](https://git-scm.com/docs/git-worktree), which provides official support for working on multiple branches.

When you run **git worktree add \<path\> \<branch\>**, Git will create a folder (a worktree) that Git will render the contents of the specified branch into. If you change directory into this new worktree, you can work on this branch without affecting the project files in the root of the workspace.

[Git 2.7 adds the git worktree list subcommand](https://developer.atlassian.com/blog/2016/01/git-2.7-release/?q=s) to display your repository's worktrees (and their associated branches). This makes it easier to track any worktrees that you have created.

``` bash
$ git worktree list
/Users/edmundd/Development/scripts                  55ace27 [master]
/Users/edmundd/Development/scripts/JIRA-628         2ddba67 [JIRA-628]
/Users/edmundd/Development/scripts/JIRA-629         55ace27 [JIRA-629]
```

### Clean up after yourself

Branches are cheap in Git, designed to be easy to create and destroy. Worktrees are no exception. When you're finished with a worktree, simply delete the folder from the filesystem and run ```git worktree prune```

Easy as that!
