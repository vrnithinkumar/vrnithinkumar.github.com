---
layout: post
title: "Stand Up Script"
subtitle: " \"Powershell script to remind you stand up and take break while you work continuously \""
date: 2016-06-06 14:00:00
author: "Nithin VR"
header-img: "StandUp_760_348.png"
catalog: true
tags:
    - Powershell
    - Scripting
---

# Stand Up Script

Powershell script that will alert at each hour by Desktop notification as well as using beep sound. To avoid continues sitting and working this script will help to remind to stand up or relax each hour. It will lock the PC at each hour too :)

### What does it do

> - It will beep according to the time.
> - Show Desktop notification with time and message.
> -  Lock the the computer automatically after the notification.

*Screen Shot of Notification:*
![alt text](/img/Notifiaction.png "")

### To Do

> - Get time interval for notification from user as command line parameter.
> - Change the busy waiting to sleep.
> - Re-factor the code.

### How to Run it

>  - Download the script from [github](https://github.com/vrnithinkumar/powershell/blob/master/StandUpAlarm.ps1).
>  -  Run `powershell StandUpAlaram.ps1`

Please feel free to send the feedback, bug, or any suggestions.
