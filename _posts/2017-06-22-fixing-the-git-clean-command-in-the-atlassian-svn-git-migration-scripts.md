---
layout: post
title: "Atlassian svn-git migration scripts are not creating branches and tags"
description: ""
category: "git"
tags: [git,svn]
---

Atlassian have some really useful scripts to help you to convert a Subversion repository over to Git. The **clean-git** script is supposed to generate all the local branches and Git tags from the imported SVN branches and tags, however when I tried running the script it didn't actually do anything!

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

Alternatively, you could revert back to a version of Git prior to 2.0.

Atlassian have a great tutorial that explains how use their helper scripts to migrate from SVN to Git [link](https://www.atlassian.com/git/tutorials/svn-to-git-prepping-your-team-migration).
