---
layout: post
title: "Atlassian svn-git migration scripts are not creating branches and tags"
description: ""
category: "git"
tags: [git,svn]
---

Atlassian have some really useful scripts to help you to convert a Subversion repository over to Git. I encountered a problem with the Atlassian helper script's *clean-git* command, in that the local Git branches and Git tags weren't being generated from the imported subversion branches.

I had a google around, and found that the problem was a backwards-compatibility issue with Git 2.x

Since v2.x the clone command uses the prefix "origin" which was empty by default in Git 1.x. You can specify the prefix during the clone with;

``` bash
git svn clone --prefix="" --std-layout
```

Now that the branch prefixes match what the script expects, this command will create the branches and tags without error;

``` bash
java -Dfile.encoding=utf-8 -jar ~/svn-migration-scripts.jar clean-git --force
```

The scripts that Atlassian have produced haven't been updated since 2014 (at the time of writing), which is why the Git 2.x behaviour isn't accounted for.

Atlassian have a great tutorial that explains how use their helper scripts to migrate from SVN to Git [link](https://www.atlassian.com/git/tutorials/svn-to-git-prepping-your-team-migration).
