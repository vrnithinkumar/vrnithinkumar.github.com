---
layout: post
title: "RegEx in Visual Studio"
subtitle: " \"Swap parameters in Visual Studio using regular expression.\""
date: 2017-08-19 14:00:00
author: "Nithin VR"
header-img: "vs.png"
catalog: true
tags:
    - VisualStudio
    - RegEx
---
## Swap parameters using regular expression
This issue is occurred to me many times while sending review request, for example when I use Assert.AreEqual() method and the parameters are not in the order ( expected value, actual value) is got swapped. It's boring and long vexing job to correct all the Assert.AreEqual() parameter order. While searching on Internet found this easy way to swap the parameters using Regular Expression.
```csharp
public static void AreEqual(object expected,object actual)  
```
For example, I wrote code like :
```csharp
Assert.AreEqual("ActualString1", CretedString1);  
Assert.AreEqual("ActualString2", CretedString2);  
Assert.AreEqual("ActualString3", CretedString3);  
```
The parameter is reversed order, it's not right and For sure I will get a review comment to change this. To swap this parameter we can use find and replace with regular expression.
{% blockquote %}
Note : Regular Expression in Visual Studio is bit different.
{% endblockquote %}

##### Add this as Search Term
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

- Be careful to select code part you want to swap parameter. Don't apply for whole Document or Solution, It might do some harmful effects.
- You can tweak the RegX for other use cases where parameter pattern is different.
- There exist some shortcuts to reorder the parameter in VS but ii didn't work for me and even if it work, We need to select each AreEqual method and apply those shortcut.

##### More Reference:
>- [Using Regular Expressions in Visual Studio](https://msdn.microsoft.com/en-us/library/2k3te2cs.aspx)
>- [Assert.AreEqual Method (Object, Object) (Microsoft.VisualStudio.TestTools.UnitTesting)](https://msdn.microsoft.com/en-us/library/ms243413.aspx)
>- [shortcut to swap/reorder parameters in vs?](https://stackoverflow.com/questions/3292292/is-there-a-shortcut-to-swap-reorder-parameters-in-visual-studio-ide)
