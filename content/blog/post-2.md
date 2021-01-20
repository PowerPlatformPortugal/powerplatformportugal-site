---
title: "Source code files for Canvas apps"
date: 2020-01-20T12:14:34+06:00
image: "images/blog/powerapps-canvas.gif"
description: "Imagine using GitHub and Visual Studio Code with the text formulas of a Canvas app.  Teams can collaborate on apps: they can work on private branches, diff changes, create pull requests for review, and merge into master. We are very pleased to announce the experimental release of a tool that enables these modern miracles.  It is but the first step as we make application lifecycle management easier for formulas and Canvas apps."
draft: false
---

Imagine using GitHub with a Canvas app.  And I mean using GitHub as it was meant to be used, with text diffs between versions, and not just storing opaque .msapp blobs.   Teams can collaborate on apps: they can work on private branches, diff changes, create pull requests for review, and merge into master.

Imagine using Visual Studio Code with a Canvas app, a full screen editor with search and replace.

We are very pleased to announce the experimental release of a tool that enables these modern miracles.  It is but the first step as we make application lifecycle management easier for formulas and Canvas apps.

To illustrate, in this short video we will change the Icon property of an Icon control with these steps:

Export an app as an .msapp file with Save As.
Unpack the .msapp to individual source files using our new tool.
Make a simple change to a formula using Visual Studio Code.
Pack the modified source files back into an .msapp file.
Open the modified .msapp file in Studio and see the result of my change.


This new tool enables the source code of a Canvas app to be effectively managed in GitHub or Azure DevOps.  Diffs, pull requests, and comments can be based on lines of formula text rather than .msapp blob files.

In this next example, we’ll make the same change to the Canvas app stored in GitHub as text source code files.  We’ll go through all the steps required to manage the change, have it reviewed, commented on, merged in to master, and the result tested.



We’ll work with this GitHub repo in Visual Studio Code.  First we’ll create a new branch, make modifications there (change the icon from “Person” to “People”), and push the changes to our new branch on GitHub.



Back on GitHub, we’ll create a pull request for this new branch to merge into master.  We’ll have an opportunity to review the one line change, to comment on the change, and to approve the merge.  Canvas apps become a part of your existing application lifecycle management workflow.



And after the merge has been made, we sync changes back to our client and recreate the .msapp file.  We can now load the file into Power Apps Studio and see our change.



Excited?  These examples only scratches the surface of what is possible.  By supporting text files, all the tools that you love and all the unique workflows that you have created can now be used with Canvas apps too.

Limitations
But before you get too excited, let’s reiterate that this is experimental.  We are making this available now for your feedback and to find issues.

Please do not convert all your .msapp files to source files and throw away the originals.  We are working toward a day when you can do this, but that is not today.   By far, Power Apps Studio is still the best place to edit a Canvas app.  We are introducing a new way to manage and edit Canvas apps, a companion to Studio, not a replacement.

There are some known limitations and gotchas:

Outside of Studio, don’t add a control with the same name as one that already exists.  This can happen easily if two different authors use Power Apps Studio to add a button control, resulting in two different Button1 controls.  When collaborating with others, we recommend renaming controls to something appropriate and hopefully unique within the app.
The source code file may expose options that Studio does not.  For example, the ZIndex property is exposed on controls.  In Studio, this isn’t a full fledged property and can only be moved up or down with a static value.  If you put a formula here, the app may not operate properly.
Distribution
So, how do you get the cool PASopa tool used above?   To download and use:

Install .NET Core 3.1.x (x64).
Clone the repo at https://github.com/microsoft/PowerApps-Language-Tooling
Run build.cmd in the root directory.
You can find PASopa.exe and needed dependencies in the bin\Debug\PASopa directory.
More details on building are available in the GitHub readme.

I did say this was experimental. 🙂 This isn’t going to be for everyone at this time and that is OK.  At this early juncture we are looking for savvy early adopters that can give us good feedback on the directory structure and file format.  Eventually we will be integrating this directly with the Power Apps CLI and it can be run by anyone without needing to do a build.  PASopa is just a test harness that we are using in the meantime.

We are releasing this tool as open source, and that will still be true even after we integrate with the Power Apps CLI.  We’d love your feedback and issue reports directly through GitHub.  Eventually, we will also welcome your pull requests, but are limiting outside contributions until we are better established.

YAML File Format
We adopted a subset of YAML as our file format.  Our first priority was a format that was super easy to read and write by humans.  But we also wanted something that could be embedded in other places within the Power Platform, and many of them use YAML already or are considering this move.   We wanted to leverage existing libraries and tools and not create something completely new.

There are three unique aspects to how we embed within YAML:

All formulas start with a leading =.   Just like Excel, the = introduces a formula rather than a static value.   We don’t make you write the leading equals in Power Apps Studio but if you notice the formula bar it is always there.  The leading = also helped us avoid the normal data type interpretation that YAML does for static value, that is not appropriate for formulas.
Some formulas must be expressed in YAML multi-line.   Using the leading = helps us a great deal, but it doesn’t address everything.  If you have a formula that contains a string with an embedded #, it is interpreted by YAML as the start of a comment, and the rest of the line will be truncated.  In these cases, the formula must be expressed using YAML’s multi-line facilities, most likely being a variant of the | syntax.
Controls are typed with the As keyword.  Normally YAML has only a single name to the left of the : when providing a property value.  But there is nothing to say that left hand side can’t be more complex and we are taking advantage of this to not only name the property but also provide its type.  We know our use of As here is different from how we use it in ForAll and other record scope functions, and we considered lots of other alternatives, but the resounding feedback from early reviews was to stick with what Visual Basic used.
Full details on the file format are available in docs included in the GitHub repo.
