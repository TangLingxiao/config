---
title: 资源管理
date: 2018-10-16 23:06:01
tags:
categories: Effective C++
---

1. 以对象管理资源(资源取得时机便是初始化时机RAII)

   - 为防止资源泄漏,请使用RAII对象,它们在构造函数中获得资源并在析构函数中释放资源.常用的RAII classes是tr1::shared_ptr和std::auto_ptr

2. 在资源管理类中小心copying行为

   - 复制RAII对象必须一并复制它所管理的资源,所以资源的copying行为决定RAII对象的copying行为

   - 普遍而常见的RAII class copying行为是:抑制copying(noncopy),施行引用计数法(shared_ptr)

3. 在资源管理类中提供对原始资源的访问

4. 成对使用new和delete时要采取相同形式

   - 如果在new中使用[],必须在相应的delete也是用[]

5. 以独立语句将newed对象置入智能指针

   - 如果不这样做,一旦异常被抛出,有可能导致难以察觉的资源泄漏

