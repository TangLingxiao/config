---
title: 构造、析构、赋值运算
date: 2018-10-16 23:04:51
tags:
categories: Effective C++
---

1. 了解C++默默编写并调用那些函数

2. 若不想使用编译器自动生成的函数,就该明确拒绝

   - 为驳回编译器自动提供的机能,可将相应的成员函数声明为private并且不予实现.如noncopy

3. 为多态基类声明virtual析构函数

   - polymorphic base classes应该声明一个virtual析构函数.如果class带有任何virtual函数,他就应该拥有一个virtual析构函数

   - classes的设计目的如果不是作为base classes使用,或不是为了具备多态性(polymorphically),就不该声明virtual析构函数

4. 别让异常逃离析构函数

   - 析构函数绝对不要吐出异常.如果一个被析构函数调用的函数可能抛出异常,析构函数应该捕捉任何异常,然后吞下它们或结束程序

   - 如果客户需要对某个操作函数运行期间抛出的异常做出反应,那么class应该提供一个普通函数执行该操作

5. 绝不在构造和析构过程中调用virtual函数

   - 因为这类调用从不下降至derived class

6. 令operator= 返回一个reference to* this

7. 在operator=中处理"自我赋值"

   - 其中技术包括比较"来源对象"和"目标对象"的地址,精心周到的语句顺序,以及copy-and-swap

8. 复制对象时勿忘其每一个成分

   - copying函数应该确保复制"对象内的所有成员变量"及"所有base class成分"

   - 不要尝试以某个copying函数实现另一个copying函数,应该将共同机能放进第三个函数中,并由两个copying函数共同调用

