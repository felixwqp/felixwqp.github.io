---
layout: post
title: "cpp inline"
description: ""
comments: true
keywords: ""
---

#### Take Away about inline on optimizer: 
1. compiler will inline function itself to optimize. Specifically, compiler will calculate a threshold to whether to inline or not. And based on the actual content of function call cost, decide whether to inline. 

2. -O1:  never inline
3. -O2: can inline


> [How inline Might Affect The Optimizer](https://www.youtube.com/watch?v=GldFtXZkgYo)
> -- <cite>Jason Turner</cite>


#### About the ODR: 

> It specifies the scope of the function to be that of a translation unit. So, if an inline function appears in foo.cpp (either because it was written in it, or because it #includes a header in which it was written, in which case the preprocessor basically makes it so). Now you compile foo.cpp, and possibly also some other bar.cpp which also contains an inline function with the same signature (possibly the exact same one; probably due to both #includeing the same header). When the linker links the two object files, it will not be considered a violation of the ODR, as the inline directive made each copy of the file local to its translation unit (the object file created by compiling it, effectively). This is not a suggestion, it is binding.
> -- <cite>https://stackoverflow.com/questions/39652884/one-definition-rule-multiple-definition-of-inline-functions</cite>

Not accurate understanding of inline when compile about ODR: 
- each cpp and corresponding h file is a translation unit, they are compiled independently, probably, and inline function only exist in that translation object, because no function call make, so no need to worry about signature duplication. 

note: my current understanding does be incomplete and even errorous. but due to time limit, temporarily leave the understanding to inline as this. 


TODO: understand the translation unit. 

