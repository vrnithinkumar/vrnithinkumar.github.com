---
layout: post
title: "F# Mentorship Program : Week-2"
subtitle: " \"F# Software Foundation’s Mentorship Program. Week-2\""
date: 2017-10-29 20:41:49
author: "Nithin VR"
header-img: "FSharpInViolate.png"
catalog: true
tags:
    - F#
---

### Summary:
I updated my progress in both sharp for fun and profit and hacker rank, we discussed one of my solution in hacker rank. Oleg suggested to try Fibonacci in different ways. We tried solving the a problem from hackerrank - [Compute the Perimeter of a Polygon](https://www.hackerrank.com/challenges/lambda-march-compute-the-perimeter-of-a-polygon).
#### My initial code
```CSharp
//Enter your code here. Read input from STDIN. Print output to STDOUT
let distance (x1:int,y1:int) (x2:int,y2:int) =
    let xDiff = abs x1-x2
    let yDiff = abs y1-y2
    let sqrSum = (pown xDiff 2)+( pown yDiff 2)
    sqrt (double sqrSum)
    
let getPoint (s:string) =
    let va = 
        s.Split(' ') 
        |> Array.map System.Int32.Parse
    (va.[0], va.[1])

[<EntryPoint>]
let main argv = 
    let t = System.Console.ReadLine()|> int
    let values = 
        Seq.initInfinite(fun _ -> System.Console.ReadLine())
        |> Seq.takeWhile(isNull >> not)
        |> Seq.map getPoint
        |> Seq.toList

    let first, rest  = values.[0], List.tail values
    
    let foldFunc (perimeter, prevPoint) nxtPoint =
        perimeter+(distance prevPoint nxtPoint), nxtPoint

    let (finalPerimeter , last) = List.fold foldFunc (0.0, first) rest
    
    printfn "%f" (finalPerimeter + (distance first last))
    0
```
#### Cleaned up code
```CSharp
//Enter your code here. Read input from STDIN. Print output to STDOUT
let distance ((x1:int,y1:int), (x2:int,y2:int)) =
    let xDiff = abs x1-x2
    let yDiff = abs y1-y2
    let sqrSum = (pown xDiff 2)+( pown yDiff 2)
    sqrt (double sqrSum)

let getPoint (s:string) =
    let [| x ; y |] = 
        s.Split(' ') 
        |> Array.map System.Int32.Parse
    x, y

[<EntryPoint>]
let main argv = 
    let testCases = System.Console.ReadLine()|> int
    let mutable firstPoint = (0,0)
    let values = 
        Seq.init testCases (fun i -> System.Console.ReadLine())
        |> Seq.map getPoint
        |> Seq.toList
        
    let lines = Seq.pairwise (values.[0]::(List.rev values))
    
    let perimeter = 
        lines
        |> Seq.map distance 
        |> Seq.sum
    
    printfn "%f" perimeter
    0
```
### New concepts I learned
- **Seq.pairwise**
Seq.pairwise method will take a sequence and returns a sequence of tuple with element in the input sequence and its predecessor.
eg: `Seq.pairwise [1..4]` returns `[(1, 2); (2, 3); (3, 4)]`
- **Seq.init**
Generates a new sequence which, when iterated, will return successive elements by calling the given function, up to the given count. eg :Seq.init count initializer
eg : `Seq.init 4 (fun n -> n * 2)` returns `[0, 2, 4, 6]`

- **yield** **VS** **yield!(yield bang)**
```csharp
let simpleYield = seq { for i in 1..5 do yield i}
\\ returns {1, 2, 3, 4, 5}

let simpleYieldBang = seq { for i in 6..10 do yield i; yield!  simpleYield}
\\ returns {6, 7, 8, 9, 10, 1, 2, 3, 4, 5} 
```
`yield!` will allow to yield a seq as elements not just as seq. It will yield each elements in the sequence.

### Here are the few questions we discussed
1. *Is None is same as null of C#?*
Yes. When we convert the F# to C# it is similar to null. It is used to represent a value that may not exist or invalid.

2. *Is there any types for None or Some?*
Yes. It is called Option type a union type of two case `None` and `Some`. eg : `int option` is a option type which wraps a int value. It is used with pattern matching for handling cases like where valid value not exists. [More Details](https://docs.microsoft.com/en-us/dotnet/fsharp/language-reference/options)

3. *Is List.fold is recursive calling or for loop inside implementation?*
Analyze a recursive data structure, fold recombine the results of recursively processing its constituent parts, building up a return value.

4. *Is Seq.unfold similar to lazy list in C#? Is it storing any state internally?*
Internal structure for iterator and we a calculating on the fly. Elements in the stream are generated on-demand by applying the element generator, until a None value is returned by the element generator. Each call to the element generator returns a new residual state.

5. *Partial application, is the parameter passing always follow from the left to right?*
Yes. It follows parameter passing order always follow from the left to right.

6. *While finding the type by type inference do we actually handle the runtime case?* 
For example : 
    `let x = 2147483647 + 1` **No error** 
    `let y = 2147483648 ` **Shows error FS1147: This number is outside the allowable range for 32-bit signed integers.**
Compiler will check only the current values is in the set of supported values of the type. It will not do any operation to verify the type.
