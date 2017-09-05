---
layout: post
title: "Pester - Mocking a function with a parameter of type System.Array"
description: ""
category: "powershell"
tags: [pester,powershell]
---

Pester has a very cool Mock function that allows you to replace a function with a Mocked function, which you can then test to see if it's run with certain parameters.

I have been trying to improve the test coverage of [PSForge](http://github.com/devopsguys/PSForge), but I hit a snag when I was trying to create a Mock with a *ParameterFilter* that checks a parameter that contains an array. The following code did not work;

```powershell
Mock Invoke-ExternalCommand { return "/fake-path" } -ParameterFilter { $Command -eq "git" -and $Arguments -eq @("rev-parse", "--show-toplevel"))}
```

This is due to the fact that Powershell's **-eq** operator doesn't support checking if two arrays are equal.

To get around this, I wrote a very quick and dirty function that checks if two sorted arrays are equal;

```powershell
function Compare-Array {
    $($args[0] -join ",") -eq $($args[1] -join ",")
}
```

If the arrays are not sorted prior, they will not match.

# The working Mock

Once I had declared the **Compare-Array** function, the following command successfully mocked the function with the array parameter.

```powershell
Mock Invoke-ExternalCommand { return "/fake-path" } -ParameterFilter { $Command -eq "git" -and (Compare-Array $Arguments @("rev-parse", "--show-toplevel"))}
```

For a more bells-and-whistles implementation of **Compare-Array**, you could try [this version](http://www.indented.co.uk/2014/04/02/compare-array/).
