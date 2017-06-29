---
layout: post
title: "[Reblog] Refactoring a monolithic Powershell DSC project using PSForge"
description: ""
category: "powershell"
tags: [powershell]
---

*Over the last couple of years, we have been managing environment configuration of a Windows-based production environment with Powershell DSC. It’s been working rather nicely so far – any configuration drift is fixed within 15 minutes, and Windows-literate engineers are quick to get used to the Powershell-based configuration syntax.*

*We have a great Continuous Delivery pipeline set up to promote any new configuration, so any new DSC can be rolled out from Dev to Production with relative ease.*

![Pipeline](https://www.devopsguys.com/wp-content/uploads/2017/06/continuous_delivery_pipeline.png)

*All DSC configuration and dependencies are stored in one Git repository – Teamcity packages the repository using Nuget, then Octopus Deploy unpacks and runs the DSC configuration on each environment in the pipeline.*

You can read the rest on the [DevOpsGuys blog](https://www.devopsguys.com/wp-content/uploads/2017/06/continuous_delivery_pipeline.png)!