---
layout: post
title: "Github will soon support Git Large File Storage"
description: ""
category: Version Control
tags: [git,github]
---
By default, git will compress and store all subsqeuent full versions of binary assets. This can cause poor performance of git if you're regularly updating pictures or 3D model files.


[Git Large File Storage](https://git-lfs.github.com) (LFS) is an open source solution to this issue.

According to the project website;

>Git Large File Storage (LFS) replaces large files such as audio samples, videos, datasets, and graphics with text pointers inside Git, while storing the file contents on a remote server like GitHub.com or GitHub Enterprise.

## How do I use it?

Git LFS extends Git's functionality by adding the ```lfs``` command.

Some of the subcommands included with ```git lfs```:

- **init:** Initialise git-lfs
- **logs:** View git-lfs erros logs
- **track:** Add a file to git-lfs
- **untrack:** Remove a file from git-lfs
- **push:** Push tracked files to the git-lfs server
- **status:** List modified git-lfs objects.

An example workflow:

``` bash
git lfs track "*.psd"
git add file.psd
git commit -m "Add large file"
git push origin master
```

This will push the large file using the Git LFS API. 

This means that the git server will need to support Git LFS. 

As of now (2015-07-05) you can only use Git LFS with your Github account if you [request access](https://github.com/early_access/git-lfs). I'm currently waiting for my invite!