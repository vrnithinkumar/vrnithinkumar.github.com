---
layout:     post
title: Generics in .NET
date: 2018-01-30 00:10:03
subtitle: "\" Design and Implementation of Generics in .NET \""
author:     "Nithin VR"
header-img: "dotnet.jpg"
catalog: true
tags:
	- .NET
	- C#
---
# Introduction
Recently I gave talk in my office related to generics in .NET. How it got introduced and how it work behind the scenes. I mainly referred the [Design and Implementation of Generics for the .NET Common Language Runtime](https://www.microsoft.com/en-us/research/wp-content/uploads/2001/01/designandimplementationofgenerics.pdf) by **Andrew Kennedy** and **Don Syme**.
## What is generics?
Generics is methodology to write programs or logic, without specialising to any type. As the program loging will be generic and it can accept type as a parameter and specialize/instantiate it for that. It is also known as [Parametric Polymorphism](https://en.wikipedia.org/wiki/Parametric_polymorphism). It is commonly used to avoid code duplication and keep the logics independent of types in single place.
## Generics in .NET
### Initial Design Goals
- Safety : Bugs are caught at compile time.
- Expressivity : Different specialization using type parameter.
- Clarity : Less casting between types.
- Efficiency : Reduced or no need for run-time checks.
### Before generics
Before Generics was introduced in C# the object is used as its the top most in class hierarchy. But it was not type safe and there was an overhead of boxing and unboxing for primitive element types.
*Example : Boxing and unboxing IL instructions*
![alt text](/2018/01/30/GenericsInDotNet/boxing.png "Boxing and unboxing.")
*Example : Object Stack vs Generic Stack*
![alt text](/2018/01/30/GenericsInDotNet/BeforeGenerics.png "Generics vs Object")
### IL code
## BenchMarking
## Reference
