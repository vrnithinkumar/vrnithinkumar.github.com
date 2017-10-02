---
layout: post
title: "Git Basics"
subtitle: " \"Enough basics oo get started with Git.""
date: 2017-08-21 14:00:00
author: "Nithin VR"
header-img: "Git_Basics.png"
catalog: true
tags:
    - Git
---
## Introduction to Git
[Git](https://git-scm.com/), a version control created by [Linus](https://en.wikipedia.org/wiki/Linus_Torvalds), creator of Linux. It was created for managing contributions to linux code base.
##### Git Terms
Remote : 
Upstream :


```csharp
\((".*"),([^\)]*)  
```
##### Add this as Replace Term
```csharp
($2, $1  
```

##### How its works
![alt text](/2017/08/19/vs-regex/RegEx.png "How RegEx works.")
For information about regular expressions that are used in replacement patterns, see [Substitutions in Regular Expressions](https://docs.microsoft.com/en-us/dotnet/standard/base-types/substitutions-in-regular-expressions). To use a numbered capture group, the syntax is $2 to specify the numbered group and (x) to specify the group in question. 
For example, the grouped regular expression (\d)([a-z]) finds four matches in the following string: 1a 2b 3c 4d. 
The replacement string z$1 converts that string to z1 z2 z3 z4.

##### Example Screenshots
Before :
![alt text](/2017/08/19/vs-regex/before.png "Before changes.")

After :
![alt text](/2017/08/19/vs-regex/after.png "After changes.")

- Be careful to select code part you want to swap parameter.Dont apply for whole Document or Solution, It might do some harmfull effects.
- You can tweak the RegX for other use cases where parameter pattern is different.
- There exist some shortcuts to reorder the parameter in VS but ii didn't work for me and even if it work, We need to select each AreEqual method and apply those shortcut.

##### More Reference:
>- [GitHub for the Roslyn Team](https://channel9.msdn.com/Blogs/dotnet/github-for-the-roslyn-team)
>- [Git Training for the .NET Team](https://channel9.msdn.com/Series/NET-Framework/Git-Training-for-the-NET-Team)
>- [shortcut to swap/reorder parameters in vs?](https://stackoverflow.com/questions/3292292/is-there-a-shortcut-to-swap-reorder-parameters-in-visual-studio-ide)