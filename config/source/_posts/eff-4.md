---
title: 设计与声明
date: 2018-10-16 23:07:24
tags:
categories: Effective C++
---

1. 让接口容易被正确使用,不易被误用

2. 设计class犹如设计type

3. 宁以pass-by-reference-to-const 替换 pass-by-value

   - 前者通常比较高效,并可避免切割问题

   - 以上规则并不适用于内置类型,以及STL迭代器和函数对象

4. 必须返回对象时,别妄想返回其reference

   - 绝不要返回pointer或reference指向一个local stack对象,或返回reference指向一个heap-allocated对象,或返回pointer或reference指向一个local static对象而有可能同时需要多个这样的对象

5. 将成员变量声明为private

   - 这可赋予客户访问数据的一致性,可细微划分访问控制,允诺约束条件获得保证,并提供class作者以充分的实现弹性

   - protected并不比public更具封装性

6. 宁以non-member non-friend替换member函数

7. 若所有参数皆需类型转换,请为此采用non-member函数

8. 考虑写出一个不抛异常的swap函数

   - 调用swap时应针对std::swap使用using声明式,然后调用swap并且不带任何命名空间修饰