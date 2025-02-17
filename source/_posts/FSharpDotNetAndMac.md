---
title: "F# in Mac OS."
subtitle: " \"Setting up F# with .NET Core, VSCode and Ionide.\""
date: 2017-10-02 14:00:00
author: "Nithin VR"
header-img: "fsharporg.png"
catalog: true
tags:
    - F#
    - Mac
    - .NET Core
---
# Introduction to F# in Mac OS
In short we will be setting up in the below order.
- Install .NET Core.
- Install VS Code.
- Install Ionide.
### Installing .NET Core
Download and install .NET Core SDK from [.NET Core for Mac](https://www.microsoft.com/net/core#macos). 
### Installing VS Code
[Download Visual Studio Code](https://code.visualstudio.com) for Mac and Install.
### Installing Ionide
Ionide is a plugin to support F# language features for VS Code. Open VS Code, press `Cmd+P` and enter the  command `ext install Ionide-fsharp` to install the Ionide package.
Or search ionide in VS Code extensions and install from there.
### Hello World
1. **Create a solution to have multiple projects.**
{% codeblock %}
dotnet new sln --name Everything
{% endcodeblock %}
If we did not specify  the `--name` it will take the folder name as the solution name.
2. **Create F# console project and add it to the solution.**
{% codeblock %}
dotnet new console -lang f# -o hwFSharpApp
{% endcodeblock %}
In above command `-o hwFSharpApp` sets an output directory of hwFSharpApp and creates hwFSharpApp.fsproj. `console -lang F#` will create a console app in F# language.
{% codeblock %}
dotnet sln add hwFSharpApp/hwFSharpApp.fsproj 
{% endcodeblock %}
This will add project `hwFSharpApp/hwFSharpApp.fsproj` to the solution.
3. **Build and run.**
The below command will build the solution with all the projects. 
{% codeblock %}
dotnet build Everything.sln 
{% endcodeblock %}
To run the console application use the below command with `dotnet run` which specifies the projects to run.
{% codeblock %}
dotnet run --project hwFSharpApp/hwFSharpApp.fsproj 
{% endcodeblock %}
![alt text](/2017/10/02/FSharpDotNetAndMac/run_app.png "Running .NET project")
4. **Use VS Code to edit.**
Using VS Code open the folder with solution(Everything.sln) we created. We can use the F# Project Explorer to Build Run and Debug the F# Projects by setting it as startup project.
![alt text](/2017/10/02/FSharpDotNetAndMac/VSCode.png "VS Code with loaded solution.")
Use `--help` to explore more options in .NET CLI. 
### More Details
1) [Use F# on Mac OSX](http://fsharp.org/use/mac/)
2) [Get started with F# and .NET Core](https://channel9.msdn.com/Events/dotnetConf/2017/T318)
