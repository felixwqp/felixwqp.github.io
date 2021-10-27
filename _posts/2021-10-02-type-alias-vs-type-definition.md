---
layout: post
title: "[go programming]  type alias v.s. type definition"
description: "new jounery to go"
comments: true
keywords: "go, programming language"
---


the difference is the type definition defines a new type, which can have new function with it. basically, type definition is a new type. 

```go
type T1 int
var x T1 = 2
var y int = 0
y = x // will not work need to convert to int
y = int(x)

for alias
type T2 = int
var x2 T2 = 2
var x3 int = x2 // this works
```

> So, alias types literally ARE THE SAME. In all ways, T2 just is an int. (there's almost no reason to actually make an alias of a built-in... but that's beside the point). Aliases are completely interchangeable with the original. You also can't define new methods on an alias.
> T1 is a new type with the same memory structure as int, but because you gave it a new name, Go will not let you implicitly convert from a regular int to T1.
> There's really only three reasons to define a named type
> 1.) Methods - this is the main reason for defining a named type - that's the way you can add methods to a type.
> 2.) Documentation - if multiple functions take the same type of argument that needs to follow some specific rules, you can document that on a named type, even if it otherwise doesn't need to be a separate type.
> 3.) Type differentiation - your T1 up above is *not* an int. Making it a separate type makes it so that it's hard$$er for developers to accidentally send random numbers to your function that takes a T1. Saying something is a T1 indicates it shouldn't be any random old int, it should be created in a specific way (usually returned from a function of some sort) and means something other than just "some integer between 0 and +-maxint". Now, nothing *stops* a developers from doing `var foo T1 = T1(len(s))` ... but that should be super rare and wo$$$$uld be a red flag in any developer's head, whether writing or reviewing that code.