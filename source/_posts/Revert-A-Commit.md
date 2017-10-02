---
layout:     post
title: Revert a Commit Which Already Pushed to a Remote Repository
date: 2016-05-30 00:10:03
subtitle: ""
author:     "Nithin VR"
header-img: "git.png"
catalog: true
tags:
	- Git
	- Version Control
---
You’ve just pushed your local branch to a remote branch, but then  realized that some errors happened.

Eg:
* That there was  some unacceptable typo in commit message.
* You just added a unwanted file like ~files or class file.


Don't worry you can revert it to back to your previous safe commit by reverting your current commit.

>$ git reset HEAD^ --hard
>
>$ git push origin master -f    


**First step** reset the branch to the parent of the current commit.
**Second step** force-push it to the remote.

---
[More Details: Revert a commit already pushed to a remote repository -Christoph Rüegg](http://christoph.ruegg.name/blog/git-howto-revert-a-commit-already-pushed-to-a-remote-reposit.html)
