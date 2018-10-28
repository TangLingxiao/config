---
title: 异常处理
date: 2018-10-28 16:04:36
tags:
categories: More Effective C++
---

1. 利用 destructors 避免泄漏资源
   - 把资源封装在对象内，通常便可以在 exceptions 出现时避免泄漏资源。
2. 在 constructors 内阻止资源泄漏
   - 如果 constructors 中抛出异常，则对象没有完全构造好，C++ 拒绝为其调用 destructor。
   - 通常只需将所有可能的 exceptions 捕捉起来，执行某种清理工作，然后重新抛出 exception，使它继续传播出去。
   - 使用智能指针代替 pointer class members 也可免除 constructor 内因 exceptions 的出现而发生的资源泄漏。
3. 禁止异常流出 destructors 之外
   - 它可以避免 terminate 函数在 exception 传播过程中的栈展开机制中被调用。
   - 他可以协助确保 destructors 完成其应该完成的所有事情。
4. 了解 “抛出一个 exception” 与 “传递一个参数” 或 “调用一个虚函数” 之间的差异
   - 一个对象被抛出作为 exception 时，总是会发生复制。因为对象可能离开了命名空间调用了 destructor。
   - 一般而言，必须使用 throw; 语句才能重新抛出当前的 exception，它不会改变被传播的 exception 的类型。此外，它也比较有效率，因为不需要产生新的 exception object。
   - “抛出一个 exception” 与 “传递一个参数” 相比，前者会多构造一个 “被抛出物” 的副本。
   - 千万不要抛出一个指向局部对象的指针，因为该局部对象会在 exception 离开其 scope 时被销毁。
   - exception 与 catch 字句相匹配过程中，仅有两种转换可以发生。一种是继承架构中的类转换，一个针对  base class exception 而编写的 catch 字句，可以处理类型为 derived class 的 exceptions。第二个允许的转换是从一个 “有型指针” 转为 “无型指针”，例如对 const void* 指针设计的 catch 字句，可捕捉所有指针类型的 exception。
   - 与调用虚函数不同，虚函数采用 best fit 策略，而 exception 处理遵循 first fit 策略。catch 子句总是依出现的顺序做匹配，因此针对 derived class 设计的 catch 子句如果出现在 base class 的 catch 子句之后，则不会被调用。
5. 以 by reference 方式捕捉 exceptions
   - catch by pointer 会面临捕捉的指针指向不存在对象的问题。
   - catch by value 则会面临复制两次的问题，一次是必定会产生的 exception object，一次是传递对象的拷贝。另一个问题是引起切割问题，derived class 被视为 base class，丧失其派生成分。
   - catch by reference 可以避免对象删除问题，也可以避开切割问题，也约束了 exception object 需被复制的次数。
6. 明智运用 exception specifications
   - 它们对希望函数抛出怎样的 exception 提供了说明，但在违反 exception specification 时会导致调用默认的unexpected，默认的 unexpected 会调用 terminate 进而调用 abort 导致程序停止。
   - 它们很容易被违反，可能会妨碍更上层的 exception 处理函数。
7. 了解异常处理的成本
   - 为了能够在运行期处理 exceptions，需要成本，即使你从未使用关键词 try，throw 或 catch。
   - 空间成本，放置某些数据结构，记录哪些对象已被完全构造妥当。
   - 时间成本，随时保持那些数据结构的正确性。

