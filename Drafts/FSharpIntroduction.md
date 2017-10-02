---
layout: post
title: "Introduction to F#"
subtitle: " \"Getting started with F#""
date: 2017-08-22 14:00:00
author: "Nithin VR"
header-img: "FSharp_Basics.png"
catalog: true
tags:
    - Fsharp
---
# FSharp Introduction

## Hello World Explained
```fsharp
[<EntryPoint>]    
let main argv =
    printfn "Hello World" 
    0 // return an integer exit code
```
##### Program to calculate the milage.
```fsharp
open System

[<EntryPoint>]    
let main argv =
    Console.Write("Please enter how far you traveled: ")
    let distance = Console.ReadLine()
    let distance = float distance
    Console.Write("Please enter how much fuel you used: ")
    let fuel = Console.ReadLine()
    let fuel = float fuel
    let consumption = distance / fuel
    Console.WriteLine("Your car does a distance of " + (string consumption) + "  per single unit of fuel ")
    Console.ReadKey()
    0 // return an integer exit code
```