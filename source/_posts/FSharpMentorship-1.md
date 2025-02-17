---
layout: post
title: "F# Mentorship Program : Week-1"
subtitle: " \"F# Software Foundation’s Mentorship Program.\""
date: 2017-10-02 19:32:15
author: "Nithin VR"
header-img: "FSharpInViolate.png"
catalog: true
tags:
    - F#
---
# F# Software Foundation’s Mentorship Program
It is my pleasure to share that I have got selected in [F# Software Foundation’s Mentorship](http://fsharp.org/mentorship/about.html) Program. I got a great mentor, Oleg Golovin. As per the discussion we decided to meet one hour in every weekend using Skype. For the first 30 minutes will to clearing my doubts regarding F# and next 30 minutes will be used to do solving challenges in HackerRank via pair programming. I am totally excited about this. Hoping I will fully utilize this opportunity.
First week meet up held on 10-Sep-2017, we had a Skype call. We started off with a general introduction to F#. I discussed about how I am learning F# now and which all materials I am following. Oleg suggested me to refer the [F# for fun and profit](https://fsharpforfunandprofit.com) web site and suggested on practicing more questions from the [HackerRank Functional Programming](https://www.hackerrank.com/domains/fp/intro).
**My setup:** Mac OS X, VS Code, Ionide.
### New concepts I learned
- **map**
map method on a list, will apply the passing function to each element and create a new list. `map` is supported with Array, List, Seq , etc.
```FSharp
let list1 = [1; 2; 3; 4; 5; 6; 7; 8; 9; 10]
let list2 = 
    list1 
    |> List.map (fun x -> x * 2)
// list2 = [2; 4; 6; 8; 10; 12; 14; 16; 18; 20]
```
- **fold**
A "fold" operation applies given function to each element in a list and pass around the accumulator which is initialized. Returns the accumulator as the result of the fold operation. `fold` is supported with Array, List, Seq, Set and Map.
```FSharp
let list1 = [1; 2; 3; 4; 5; 6; 7; 8; 9; 10]
let sum =
    list1
    |> List.fold (fun elmnt sum -> elmnt + sum) 0 // we are initializing
// return value of the function will be passed as accumulator(sum) in next iteration.
// sum = 55
```
- **How calling function is different**
```FSharp
let foo() =
    printfn "Hello world"
foo   // this won't call the foo it will return the function
foo() // this will call the actual foo since we are passing `()` unit type
```
In F# every function accept a single parameter we have to pass the unit type `()` even if the function accepts nothing. 

- **unit type**
unit type means absence of any values. It is just a placeholder to use the when no value is available or required. Its value is `()`. For example functions like printf will return nothing but a unit type. TLDR : F# unit = C# void
### Here are the few questions we discussed.
1. *Type representation is little confusing, like tuple is (a, b) but in type highlighter will show it as a * b , Is there a good place look for more details to understand about this type representation?*
I think this is done in part to avoid confusion with multi-argument generics like 'Dictionary<string, int>'. Imagine that dictionary values is tuples of two integers '(int, int)'. So, 'Dictionary<string, int, int>'? 'Dictionary<string, (int, int)>'? To me, 'Dictionary<string, int * int>' is cleaner, because '*' is easily recognized as tuple mark, where for '()' you have to look more carefully into type definition.

2. *Why do we need to explicitly specify a function is recursive using rec?*
There is excellent answer here: [Stack Overflow](https://stackoverflow.com/questions/900585/why-are-functions-in-ocaml-f-not-recursive-by-default) Basically, that's just historical choice.

3. *Won't the immutability cause to use more memory, since every time we create a new change we are creating a new object/value?*
Yes, immutability would cause to use a lot more memory... if you are careless with collections and object passing. Also, FSharp structures is heavily optimized. So, for example, when you add new element to the list, it doesn't create new copy of a list with all copies of its elements. FSharp just creates one new list element, marks it as head for 'new' list, and attaches old list as tail. Arrays are not that way, they actually copy all of their contents.

4. *I read some where using mutable variable is not a functional way, in F# do we always try or prefer to not use mutable variables?*
It's better when your function always returns the same result with the same inputs. But if we declare and use mutable variable somewhere inside that function - the 'same result' guarantee is lower. If we use mutable variable that's declared elsewhere - there's no guarantee, that some other function hasn't changed it, so we wouldn't get 'same result'. Of course going strict 'no mutables' is not a good way. I find it preferable to not use public mutables - when you absolutely need to have some state that changes over time on long-living entity, it's totally okay to use private mutable.

5. *Why arrays are mutable while lists are not?*
Array in F# has the same base as in C# - System.Array. So, naturally, they behave the same as in C#. Lists, on the other hand, is immutable special 'FSharpList'. When you add an element, you actually create new list as I described above. If you try to mutate the element in the list - the head that previously was pointing to that element is now pointing into corrupted memory, because the list is singly-linked. You change the element - and the tail gets disconnected from former head.