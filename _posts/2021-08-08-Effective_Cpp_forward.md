---
layout: post
title: "Effective Cpp: Forward/Move[WIP]"
description: "C++ new feature forward "
comments: true
keywords: "C++, forward"
---



### lval/rval
. In concept (though not always in practice), rvalues correspond to temporary objects returned from functions, while lvalues correspond to objects you can refer to, either by name or by following a pointer or lvalue reference.

A useful heuristic to determine whether an expression is an lvalue is to ask if you can take its address. 

Here, it’d be perfectly valid to take rhs’s address inside Widget’s move constructor, so rhs is an lvalue, even though its type is an rvalue reference. (By similar reasoning, all parameters are lvalues.)




```C++
class Annotation {
public:
  explicit Annotation(const std::string text)
  : value(std::move(text))  // "move" text into value; this code
  { … }                     // doesn't do what it seems to!
  
  …

private:
  std::string value;
};

class string {            // std::string is actually a 
public:                   // typedef for std::basic_string<char>
  …
  string(const string& rhs);    // copy ctor
  string(string&& rhs);         // move ctor
  …
};

```


std::move(text) : cast **left val const std::string -> right val const std::string**


 rvalue of type const std::string


 the move ctor takes right value reference to non-cast string[1]
 the copy ctor takes left value reference to const string[2]

 const rvalue -x-> [1]
 const rvalue ---> [2]

 Moving a value out of an object generally modifies the object, so the language should not permit const objects to be passed to functions (such as move constructors) that could modify them.


There are two lessons to be drawn from this example. First, don’t declare objects const if you want to be able to move from them. Move requests on const objects are silently transformed into copy operations. Second, std::move not only doesn’t actually move anything, it doesn’t even guarantee that the object it’s casting will be eligible to be moved. The only thing you know for sure about the result of applying std::move to an object is that it’s an rvalue.



