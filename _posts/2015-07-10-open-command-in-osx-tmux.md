---
layout: post
title: "Unable to use 'open' command in OSX tmux"
description: "A fix is posted"
category: osx
tags: [homebrew,tmux]
redirect_from:
  - /osx/2015/07/10/open-command-in-osx-tmux/
---
I recently installed **tmux** on my laptop running OSX 10.10.4. I'd not had any issues until I tried to use the 'open' command in order to install a .pkg file manually.

When I ran **open package.pkg**, I got the following outputl

    The window server could not be contacted. open must be run with a user logged in at the console, either as that user or as root.

I found the fix on a [chinese blog](http://blog.csdn.net/yhcharles/article/details/43225443)


``` bash
brew update
brew install reattach-to-user-namespace
brew upgrade reattach-to-user-namespace
echo "set -g default-command \"reattach-to-user-namespace -l ${SHELL}\"" >> ~/.tmux.conf
```

If you're in tmux, exit and start a new session.


You can now use the ```open``` command without any issues inside tmux.
