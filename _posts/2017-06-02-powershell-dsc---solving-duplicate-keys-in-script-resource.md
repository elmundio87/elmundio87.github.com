---
layout: post
title: "Powershell DSC: Solving duplicate keys in Script resource"
description: ""
category: 
tags: []
---
Recently I came across a puzzling issue while writing a custom Powershell DSC resource - when I tried to use it more than once in a single configuration, I would get this error message;


>Add-NodeKeys : The key properties combination '{$yourModuleCode}' is duplicated for keys 'GetScript,SetScript,TestScript' of resource 'Script' in node 'localhost'. Please make sure key properties are unique for each resource in a node


After a bit of googling, I found a [very informative article](https://www.briantist.com/how-to/use-duplicate-dsc-script-resources-in-loop/) by Brian Scholer that explained to me what was going on.

Basically, Powershell DSC doesn't like it when you have exact duplicates of a Script resource in the same `Configuration` block, which will happen if you place a `Script` within a `ForEach-Object` loop.

Powershell doesn't take the value of `$variable` values into account until the MOF file is actually applied to a machine, so a `ForEach-Object` loop will generate identical `Script` blocks. 

``` powershell
ForEach ($number in 1,2,3){
    Script example {
        TestScript  = { return $False}
        SetScript   = { Write-Output $Using:number}
        GetScript   = { @{ Result = "Hardcoded"}}
    }
}
```

*An example configuration that would trigger the error*

The differences would only become apparent at runtime, but by this point the MOF checker has already thrown an Exception and halted the MOF generator.

![Keyboard Cat](/assets/images/keyboard_cat.gif)

*This problem was sufficiently frustrating*


## A workaround

Brian described an interesting solution - using a custom function, he substituted the value of `$Using:variables` before the uniqueness check happened. This means that the MOF checker will treat each Script block as being slightly different, as they have been modified to include some uniqueness.

``` powershell
ForEach ($number in 1,2,3){
    $v = @{ Result = $number }
    Script MyScript
    {   
        ...
        GetScript = { $Using:v } | Replace-Using
    }
}
```

*Piping the GetScript value into Replace-Using to introduce uniqueness*

## A simpler workaround with GUID strings

Building on top of Brian's findings, I believe I have found a much simpler solution for introducing uniqueness into Script blocks.

`[guid]::NewGuid())` will generate a random GUID string, that will look like this;

    Guid
    ----
    882fb2c8-e9cd-4c9f-83f4-27fe34a55934


Running the `NewGuid()` function should always generate random strings with a low chance of duplicate collision.

By generating a GUID value in the  `GetScript` section of the Script block, a unique string being placed into the hashtable that it returns.

``` powershell
GetScript =  "@{ Result = $([guid]::NewGuid()) }"
```

If you `ForEach-Object` loop over the Script block multiple times now, the `GetScript` return value will be unique for each iteration. This should prevent the `Please make sure key
properties are unique for each resource in a node` error.

GUID generation takes a fraction of a second, so should not affect the performance of your DSC configuration.