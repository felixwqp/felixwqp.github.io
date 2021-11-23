---
layout: post
title: "Golang Memory Management notes"
description: ""
comments: true
keywords: ""
---



#### where to allocate?
1. 尽量放在stack上
2. 每个goroutine有各自的stack
3. 根据escape analysis的结果（can deermine the lifetime），如果不确定，object才会放到heap上
```md
Go prefers to allocate memory on the stack, so most memory allocations will end up there. This means that Go has a stack per goroutine and when possible Go will allocate variables to this stack. The Go compiler attempts to prove that a variable is not needed outside of the function by performing **escape analysis** to see if an object “escapes” the function. If the compiler can determine a variables lifetime, it will be allocated to a stack. However, if the variable’s lifetime is unclear it will be allocated on the heap. Generally if a Go program has a pointer to an object then that object is stored on the heap. Take a look at this sample code:
```

#### GC
 non-generational concurrent, tri-color mark and sweep garbage collector. 