# Community toolkit for VSCT files used in Visual Studio extensions

Part of the [VSIX Community](https://github.com/VsixCommunity)

[![Build Status](https://dev.azure.com/flexviews/Community.VisualStudio.VSCT/_apis/build/status/CZEMacLeod.Community.VisualStudio.VSCT?branchName=main)](https://dev.azure.com/flexviews/Community.VisualStudio.VSCT/_build/latest?definitionId=71&branchName=main)
[![NuGet](https://img.shields.io/nuget/v/Community.VisualStudio.VSCT)](https://nuget.org/packages/Community.VisualStudio.Toolkit/)

## Summary

A development time MSBuild extension to [Microsoft.VSSDK.BuildTools](https://www.nuget.org/packages/Microsoft.VSSDK.BuildTools/)

Adds a new item type `VSCTInclude` which ensures that the items path is added to `VSCTIncludePath` and gives a warning if you do _not_ include it in any `VSCTCompile` file.

It also contains the VSGlobals.vsct file originally from the [ExtensibilityTemplatePack](https://github.com/madskristensen/ExtensibilityTemplatePack).

It means the file can be versioned, and updated after the project is created from the template.

It lights up via the `VSCTIncludePath` item group used by the `VSCTCompile` target/task.

## Purpose
This package attempts to solve some issues with the current extensibility model, specifically around VSCT files.

### The API is dated and has lots of ugly COM legacy noise
The most common symbols in VSCT files have unintelligable IDs based on the old complex COM nature. New symbols are available which alias these to more readable and discoverable names.

### Only Microsoft can update the build tools and that doesn't scale
This is a living project where the whole community can contribute helpers on top of the official VS SDK. There is no need to wait for Microsoft to make an update, since this projet gives the ability to continue the work in a separate work stream.

## Templates
For both project- and item templates that utilizes this NuGet packages, download the [Extensibility Template Pack](https://marketplace.visualstudio.com/items?itemName=MadsKristensen.ExtensibilityItemTemplates).

## How to use

### VSGlobals 
1) Remove any local copy of `VSGlobals.vsct` you may have in your local project.
   i) It may be hidden/nested under VSCommandTable.vsct
2) Install the package into your VSIX project
3)
    i)  If you started with a project created from the [ExtensibilityTemplatePack](https://github.com/madskristensen/ExtensibilityTemplatePack) using the `VSIX Project w/Command (Community)` template, you don't need to do anything. :joy:
    ii) If you used an older template, or a different template you can now include and use the globals.

Ensure you have the following in your `VSCommandTable.vsct` file
```xml
  <Include href="VSGlobals.vsct"/>
```

Replace items like
```xml
  <Parent guid="guidSHLMainMenu" id="IDM_VS_MENU_TOOLS"/>
```
with
```xml
  <Parent guid="VSMainMenu" id="View.DevWindowsGroup.OtherWindows.Group1"/>
```

The top level groups are 
- VSMainMenu
- VSNuGet
- VSTerminal
- VSFeedback
- VSEditor
- VSEditor2

If you have the latest [ExtensibilityTemplatePack](https://github.com/madskristensen/ExtensibilityTemplatePack) (V2.1.11 or higher) installed you will get intellisense and popups to show you where your item will be added.
See [Writing Visual Studio Extensions with Mads - Improving our community templates](https://www.youtube.com/watch?v=Gzpntvp0yzo&t=1s) for a demonstration and more information.

## Warnings

If you set an item as `VSCTInclude`, but then do not reference it from a `VSCTCompile` item you will get two warnings.

- CVSTK001 No VSCTCompile file includes this VSCTInclude
  - This is shown for each VSCTInclude and jumps you to the include file.
- CSVTK002 Add &lt;Include href="xxxx.vsct"/&gt;
  - This is shown for each VSCTCompile and jumps you to the compile file
  - xxxx.vsct is the name of the VSCTInclude file.
  - Here you can add the relevant snippet
```xml
  <Include href="xxxx.vsct"/>
```


## Properties

You can set these properties in your project or Directory.Build.props files.
### Simple
| Property | Default | Description |
|-|-|-|
| `EnableDefaultVSCTIncludeItems` | true | Enable marking all non-`VSCTCompile` items of type *.vsct with the `VSCTInclude` Build Action. |
| `EnableVSGlobalsVSCT` | true | Add the `VSGlobals.vsct` file to the `VSCTCompile` tasks include search paths.<br/>Allows you to add <pre lang="xml"> <code>&lt;Include href="VSGlobals.vsct"/&gt;</code></pre> |
### Advanced
| Property | Default | Description |
|-|-|-|
| `ShowVSGlobalsVSCT` | false | Show the VSGlobals.vsct file in your project. |
| `VSGlobalsVSCTDependentUpon` | `VSCommandTable.vsct` | If `ShowVSGlobalsVSCT` is set to true, this is the name of the file under which the `VSGlobals.vsct` will be nested. |
| `VSGlobalsVSCTPath` | The directory of the package's copy of the `VSGlobals.vsct` | This can be used to override the location with a personalized copy of the file, while keeping the logic from the package. |
| `VSCTIncludeExclude` |  | Semi-colon separated list of files (wildcards allowed) to exclude from being included as VSCTInclude if `EnableDefaultVSCTIncludeItems` is set to true.

