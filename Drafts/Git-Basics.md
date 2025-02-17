---
layout: post
title: "Git Basics "
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

##### Git Work Flow with GitHub
- Forking the repo
- Cloning the forked repo

`git clone https://github.com/vrnithinkumar/Hexo_Static_Site.git`
- Setting up stream

`git remote add upstream git@bitbucket.org:some-gatekeeper-maintainer/some-project.git`
- Creating a new branch

`git checkout-b MyNewBranch`
- Committing changes to the new branch

`git push origin MyNewBranch`
- Creating a pull request 
- If reviewer asks for any change, making changes and committing to branch
- Deleting the branch after merging

`git branch -D MyNewBranch`
- Pruning all branches

`git remote prune origin`
- Fetching changes from the upstream 

`git fetch upstream`

- Merging our master with upstream
`git merge upstream/master`

##### More Reference:
>- [GitHub for the Roslyn Team](https://channel9.msdn.com/Blogs/dotnet/github-for-the-roslyn-team)
>- [Git Training for the .NET Team](https://channel9.msdn.com/Series/NET-Framework/Git-Training-for-the-NET-Team)
parameters-in-visual-studio-ide)
