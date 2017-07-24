---
layout: post
title: "Display a VSTS dashboard on a TV screen with custom CSS"
description: ""
category: "vsts"
tags: [css,vsts]
---

I have a tablet set up at work that always shows me my VSTS dashboard, which I've configured with widgets just for my consumption.

One feature that VSTS is lacking, is a "fullscreen" experience for Team Dashboards - there's no built-in way of hiding the top menu bar, and the "customise the dashboard button".

# My solution

I have created a [custom userstyle](https://userstyles.org/styles/145627/hide-vsts-dashboard-menu-bar) for VSTS that does the trick. It removes the menubar, shifts the content up 100 pixels and removed the (+) button.

You will need the Stylish extension (or similar) to install it.

![screenshot](https://github.com/elmundio87/hide-vsts-dashboard-menubar-css/raw/master/screenshot.jpeg)

I've created a Github repo for the custom stylesheet, which you can find [here](https://github.com/elmundio87/hide-vsts-dashboard-menubar-css)