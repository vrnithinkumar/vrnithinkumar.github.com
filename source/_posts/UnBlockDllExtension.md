---
layout: post
title: "Unblock Dll Extension"
subtitle: " \"A helper extension remove extension dll from Visual Studio.\""
date: 2017-08-13 14:00:00
author: "Nithin VR"
header-img: "vs.png"
catalog: true
tags:
    - VisualStdio
    - Extension
---
## No Access Error in Visual Studio
The given below error is very common, frustrating and encountered by many Visual Studio users.
```
Unable to copy file "obj\Debug\project.pdb" to "bin\project.pdb". Access to the path 'bin\project.pdb' is denied.
```
It was caused by the pdb or dll file made read only or hidden by the Visual studio itself. If you just remove those attributes from that file this error will go away. But the process is little ve
[StackOverflow question](https://stackoverflow.com/questions/9750101/unable-to-copy-file-access-to-the-path-is-denied/12740768?noredirect=1#comment75731595_12740768)
No Access to the path error look like this:
![Alt text](/2017/08/13/UnBlockDllExtension/NoAccessError.png "No Access Error.")<br />
# Unblock Dll Extension
A tiny helper to remove the ReadOnly and Hidden attribute from dll's and executables which blocking the Visual Studio from building the project. Link to download 
### What does it do
Right click on the Error List window and click the Unblock Files menu item.
![alt text](/2017/08/13/UnBlockDllExtension/UnBlockMenuItem.png "Menu Item in Error Window.")
Result of the action will be shown in message box like this : 
![alt text](/2017/08/13/UnBlockDllExtension/ResultAfterUnblocking.png "Result After Unblocking.")
> - The extension will find all the files causing the error and remove all the Read Only and Hidden attribute.
Please feel free to send the feedback, bug, or any suggestions.
>- [Link to Download](https://marketplace.visualstudio.com/items?itemName=NithinVR.UnBlockDllExtension)
>- [Link to Source](https://github.com/vrnithinkumar/UnblockDllExtension)