---
layout: post
title: "Fixing the git-clean command in the Atlassian SVN to Git migration scripts"
description: ""
category: "git"
tags: [git,svn]
---

Atlassian have some really useful scripts to help you to convert a Subversion repository over to Git. I encountered a problem with the `git-clean` command, in that the local git branches and git tags weren't being created from the imported subversion branches.

I had a google around, and found that the problem was a backwards-compatibility issue with Git 2.x

Since v2.x the clone command uses the prefix "origin" which was empty by default in Git 1.x. You can specify the prefix during the clone with `git svn clone --prefix=""`

Now that the branch prefixes match what the script expects, running the `git-clean` command will create the branches and tags without error.

The Atlassian scripts haven't been updated since 2014 (at the time of writing), which is why the 2.x behaviour isn't accounted for.