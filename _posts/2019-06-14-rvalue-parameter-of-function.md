---
title: C++函数声明中要不要用右值引用？
layout: single
guid: ad0a2d66d5304d848adfba2d1b1a6a94
date: 2019-06-14 08:57:49
categories:
  - C++
tags:
  - 右值引用
---

在一次代码评审时，看到一个成员函数使用了``const T&``，不假思索地就建议使用``T``传值，在函数内用``std::move``进行移动。之后有同事问我，不是应该用``func(T&&)``吗？一时间感觉脑子不好用，难道一段时间不用，哪里搞错了？赶紧上网搜索答案。

## 应该使用传值吗？

在STL中，可以看到很多地方都同时提供``const T&``和``T&&``两种重载，分别对应参数为左值和右值两种情况。看来这样做是标准做法吧。但这样一来，基本相同的逻辑要重复写两次。说好的``DRY``（``Don't Repeat Yourself``）原则呢？

在[链接1][ref1]、[链接2][ref2]中，都看到有答主推荐如果函数内需要保存参数的一个拷贝，就用传值。

```cpp
void foo(T t)
{
    // ...
    t_ = std::move(t);
    // ...
}
```

这样，调用者可以决定是否使用``std::move``放弃传入参数的所有权。

1. 如果调用者放弃所有权，整个过程中不发生拷贝；
2. 如果调用者不放弃所有权，无论用什么方式，还是会发生一次拷贝。

这样看来，因此传值并没有引入额外的开销，还避免了同时提供``const T&``和``T&&``类型参数的两个重载的麻烦，完美！

```cpp
T lval;
foo(lval); // copy from lvalue
foo(T {}); // (potential) move from prvalue
foo(std::move(lval)); // (potential) move from xvalue
```

## 还有什么选择？

传值看上去很美。不过，STL是怎么回事？难道标准委员会的人看不到这点？我们还是继续找答案吧。

在CppCon 2014上，Herb Sutter发表了题为“Back to the Basics! Essentials of Modern C++ Style”的[演讲][BackToBasics]（[视频在此][BackToBasicsVideo]），谈到了我们关心的问题。

在演讲中，Herb Sutter谈到

1. 对于输出参数或者输入/输出的参数，用函数返回值或者用左值引用参数``T&``即可；
1. 对于小对象（如基本数值类型）和不能拷贝的对象，传值即可；
1. 对于大的输入参数：
   * 如果函数内不需要保留，直接用``const T&``即可；
   * 如果函数内需要保留一份，则：
     1. 和C++ 98一样，优先使用``const T&``；
     2. 为了增加优化机会，可以在``const T&``基础上，增加右值引用``T&&``的重载；
     3. 如果可以接受代码必须放在头文件中，并且不能作为虚函数，也可以考虑用``forward reference``，即``universal reference``。

前面几点都没有什么问题，和C++ 98是一样的。分歧在于在对于大的输入参数，且函数内需要在保存一份时为什么不直接传值？

## 传值有什么问题？

传值在很多时候是可以的，但Herb Sutter指出了重要的一点，也是最重要的一点：对于要进行内存分配、而调用者又不希望转移所有权的参数，传值意味着**100%**会进行一次内存分配。初看起来，这不是很正常吗？既然调用者不转移所有权，而被调的地方又需要保留一份，难道不是**必须**进行内存分配？

不是的！无论是``std::string``、``std::vector``还是其他对象，在目标对象有足够空间保存新的值时，是不需要重新进行内存分配的。比如原来的``std::string``对象能容纳100字节，赋给这个对象50字节的新值时，并不会先释放原来100字节的内存再分配50字节的新内存，而是会直接复制到原来100字节的内存中，并标记其长度 为50字节。这时候是不需要内存分配的。

Herb Sutter在演讲中提到，现实情况中，远低于50%的对象拷贝会触发内存分配。因此在这样的情况下，把``常量引用 + 复制``改为``传值 + 移动``，其效率不是不变而是降低，还降低很多。

## 结论

* 一般情况下用常量引用``const T&``
* 确实有优化必要时可以增加一个接受右值引用``T&&``的重载
* 在构造函数等不涉及利用目标对象原有空间（必须从零开始构造）的情况下，可以传值，以避免在多参数函数中，每个参数都可能是``const T&``和``T&&``造成的组合爆炸

回头看看，我遇到的那个场景类似于构造函数，传值是可以的。But, however，这个场景基本上只在初始化时调用，并没有优化的必要，直接用最简单的``const T&``也是没问题的，看来是自己想多了 ╮（﹀＿﹀）╭

[ref1]: https://stackoverflow.com/questions/7592630/is-pass-by-value-a-reasonable-default-in-c11
[ref2]: https://stackoverflow.com/questions/18673658/should-i-always-move-on-sink-constructor-or-setter-arguments
[BackToBasics]: https://github.com/CppCon/CppCon2014/tree/master/Presentations/Back%20to%20the%20Basics!%20Essentials%20of%20Modern%20C%2B%2B%20Style
[BackToBasicsVideo]: http://www.youtube.com/watch?v=xnqTKD8uD64
