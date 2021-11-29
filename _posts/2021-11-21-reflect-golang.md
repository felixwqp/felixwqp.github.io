---
layout: post
title: "Reflection"
description: ""
comments: true
keywords: ""
---


reflection is a key component of this service register feature.
reflect has two key type: **reflect.Type** and  **reflect.Value**
### reflect.Type && reflect.Value
-> just a Go Type, this is an interface with many methods
, implmentation is type descriptor
1. discriminate among types
2. inspect the components
```go
t := reflect.TypeOf(3)  // a reflect.Type
fmt.Println(t.String()) // "int"
fmt.Println(t)          // "int"
```
**3 -> interface{}**: 
an assignment from a concrete value to an interface type performs an **implicit interface** conversion, which creates an interface value consisting of two components:
- its dynamic type is the operand’s type (int)
- its dynamic value is the operand’s value (3).


Typeof and Valueof expose these dynamic Type:
- Typeof return reflect.Type. an interface value’s dynamic type, it always returns a concrete type
- Valueof return reflect.Value,  containing the interface’s dynamic value.


The inverse operation to reflect.ValueOf is the reflect.Value.Interface method. It returns an interface{} holding the same concrete value as the reflect.Value:

```go
v := reflect.ValueOf(3) // a reflect.Value
x := v.Interface()      // an interface{}
i := x.(int)            // an int
fmt.Printf("%d\n", i)   // "3"
```


### reflect.Value() v.s. interface{}
A reflect.Value and an interface{} can both hold arbitrary values. 
- an empty interface hides the representation and intrinsic operations of the value it holds and exposes none of its methods, so unless we know its dynamic type and use a type assertion to peer inside it (as we did above), there is little we can do to the value within. 
- In contrast, a Value has many methods for inspecting its contents, regardless of its type.
```go
s.rcvr.Method(0).String()
result = {string} "<func(geerpc.Args, *int) error Value>"

s.rcvr.Interface()
result = {interface {} | github.com/felixwqp/geerpc.Foo}
Random1 = {int} 0
Random2 = {int} 0
```


