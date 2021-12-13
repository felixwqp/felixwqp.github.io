---
layout: post
title: "C++ exception notes"
description: ""
comments: true
keywords: ""
---

https://harttle.land/2015/08/27/effective-cpp-29.html

异常处理并不意味着需要写显式的 try 和 catch。异常安全的代码，可以没有任何 try 和 catch。




异常安全是指当异常发生时，
1) 不会泄漏资源，
2) 也不会使系统处于不一致的状态。 
   
   
通常有三个异常安全级别：基本保证、强烈保证、不抛异常（nothrow）保证。

- 基本保证: 抛出异常后，对象仍然处于合法（valid）的状态。但不确sa定处于哪个状态。data might modified
- 强烈保证: 如果抛出了异常，程序的状态没有发生任何改变。就像没调用这个函数一样。
- 不抛异常保证。这是最强的保证，函数总是能完成它所承诺的事情。

1. example: 
```C++
void Menu::changeBg(istream& src){
    lock(&mutex);
    delete bg;
    ++ changeCount;
    bg = new Image(src);
    unlock(&mutex);
}
```

比如申请内存总是可能失败的，如果内存不够就会抛出"bad alloc"异常。加入new Image(src)抛出异常， 那么异常安全的两个条件都会破坏：

mutex资源被泄露了。没有被unlock。
Menu数据一致性被破坏。首先bg变成了空，然后changeCount也错误地自增了。

2. when to set state? 
  一个好的状态变更策略是：只有当某种事情（比如背景变更）已经发生了，才去改变某个状态来指示它发生了。


3. copy & swap? 
```C++
class Menu{
    ...
private:
    Mutex m;
    std::shared_ptr<MenuImpl> pImpl;
};
Menu::changeBg(std::istream& src){
    using std::swap;            // 见 Item 25
    Lock m1(&mutex);

    std::shared_ptr<MenuImpl> copy(new MenuImpl(*pImpl));
    copy->bg.reset(new Image(src));
    ++copy->changeCount;

    swap(pImpl, copy);
}
```
只有改好了之后我们才swap它们。swap应当提供不抛异常的异常安全级别


3. when no exception, the turn on/off of compiler for exception has similar proformance. but the binary size has 10/20 difference
because the unwinding of stack, is decided by the exception position.

llvm:
```
In an effort to reduce code and executable size, LLVM does not use RTTI (e.g. dynamic_cast<>;) or exceptions.
```


problems:
1. 异常违反了“你不用就不需要付出代价”的 C++ 原则。只要开启了异常，即使不使用异常你编译出的二进制代码通常也会膨胀。
   - 目前的主流异常实现中，都倾向于牺牲可执行文件大小、提高主流程（happy path）的性能


2. 异常比较隐蔽，不容易看出来哪些地方会发生异常和发生什么异常。


从 C++17 开始，C++ 甚至完全禁止了以往的动态异常规约，你不再能在函数声明里写你可能会抛出某某异常。你唯一能声明的，就是某函数不会抛出异常——noexcept、noexcept(true) 或 throw()。
Dynamic exception specification(until c++17): 
If a function is declared with type T listed in its exception specification, the function may throw exceptions of that type or a type derived from it.
```C
void f() throw(int); // OK: function declaration
void (*pf)() throw (int); // OK: pointer to function declaration
void g(void pfa() throw(int)); // OK: pointer to function parameter declaration
typedef int (*pf)() throw(int); // Error: typedef declaration

```


Yes, throwing an exception from the failed constructor is the standard way of doing this
no exception from destructor, has to be 


no throw for destructor: 
 The C++ rule is that you must never throw an exception from a destructor that is being called during the "stack unwinding" process of another exception. For example, if someone says throw Foo(), the stack will be unwound so all the stack frames between the throw Foo() and the } catch (Foo e) { will get popped. This is called stack unwinding.







vector 会在元素类型没有提供保证不抛异常的移动构造函数的情况下，在移动元素时会使用拷贝构造函数。这是因为一旦某个操作发生了异常，被移动的元素已经被破坏，处于只能析构的状态，异常安全性就不能得到保证了。

1. calling code needs to recognize error condition and handle, o.w. terminate
2. jumps to exception handler through call stack, 
3. exception stack-unwinding mechanism destroys all objs(RAII), in the scope after thrown
4. An exception enables a clean separation between the code that detects the error and the code that handles the error.



exception v.s. std::optional: 
- exception: have error msg, 
- std::optional: lose error message, less overhead(no stack unwinding needed)


error handling in c++:
- error code
- exception


how to write exception for a class? 



all exception should derived from ```std::exception```
```C++
#include <stdexcept>
#include <limits>
#include <iostream>

using namespace std;

void MyFunc(int c)
{
    if (c > numeric_limits< char> ::max())
        throw invalid_argument("MyFunc argument too large.");
    //...
}

int main()
{
    try
    {
        MyFunc(256); //cause an exception to throw
    }

    catch (invalid_argument& e)
    {
        cerr << e.what() << endl;
        return -1;
    }
    //...
    return 0;
}
```

1. exception v.s. assert:
Use asserts to check for errors that should never occur. Use exceptions to check for errors that might occur,
2. Throw exceptions by value, catch them by reference. Don’t catch what you can't handle.
3. Don't allow exceptions to escape from destructors or memory-deallocation functions.



use shared_ptr to manage resource that can have exception
1. RAII for handling
2. some problem with shared point construction: https://stackoverflow.com/questions/20895648/difference-in-make-shared-and-normal-shared-ptr-in-c

```C++
void F(const std::shared_ptr<Lhs> &lhs, const std::shared_ptr<Rhs> &rhs) { /* ... */ }

F(std::shared_ptr<Lhs>(new Lhs("foo")),
  std::shared_ptr<Rhs>(new Rhs("bar")));

```
Because C++ allows arbitrary order of evaluation of subexpressions, one possible ordering is:
```C++

new Lhs("foo"))
new Rhs("bar"))
std::shared_ptr<Lhs>
std::shared_ptr<Rhs>
```
> Now, suppose we get an exception thrown at step 2 (e.g., out of memory exception, Rhs constructor threw some exception). We then lose memory allocated at step 1, since nothing will have had a chance to clean it up. The core of the problem here is that the raw pointer didn't get passed to the std::shared_ptr constructor immediately.









