---
title: 操作符
date: 2018-10-21 14:57:22
tags:
categories: More Effective C++
---

1. 对定制的 ”类型转换函数“ 保持警觉

   - 两种函数允许编译器执行这样的转换：单自变量 constructors 和隐式类型转换操作符。例如：

     ```c++
     class Name{
       public:
       	Name(const string& s);     //可以把 string 转换为 Name  
     }；
     
     class Rational{
       public:
       	Rational(int num = 0, int den = 1);// 可以把 int 转换为 Rational
     };
     ```

   - 隐式类型转换操作符解决办法比较简单，以功能对等的另一个函数取代类型转换符即可。例如

     ```c++
     class Rational{
       public:
       	operator double() const;//将 Rational 转换为 double
         //替换为下边
         double asDouble() const;//将 Rational 转换为 double
     };
     ```

   - 通过单自变量 constructors 完成的隐式转换较难消除，有两种解决办法：一个是简易法，另一个在编译器不支持简易法时使用。

     - 简易法是使用 C++ 关键字 explicit。只要将 constructors 声明为 explicit，编译器便不能因隐式转换的需要而调用它们。

     - 隐式转换有一条规则：没有任何一个转换程序可以内含一个以上的 ”用户定制转换行为“。可以利用这项规则。例如：

       ```c++
       template<class T>
       class Array{
         public:
           class ArraySize{
             public:
               ArraySize(int n):size(n){}
               int size()const{return size;}
             private:
               int size;
           };
           Array(ArraySize size); //新的构造函数
       };
       ```

       本例对 ArraySize class 的利用，往往被称为 proxy classes，之后再详细学习。

2. 区别 increment/decrement 操作符的前置和后置形式

   - 前置式返回一个 reference，后置式返回一个 const 对象。

3. 千万不要重载 &&，|| 和 ，操作符

   - C++ 对于 “真假值表达式” 采用 “骤死式” 评估方式。重载 && 或 || 将无法提供骤死式语义。
   - 表达式如果内含逗号，那么逗号左侧会先被评估，最后，整个表达式的结果以逗号右侧的值为代表。

4. 了解各种不同意义的 new 和 delete

   - new operator 不但分配内存而且为该对象调用一个 constructor。operator new 只分配内存，不会调用constructor。
   - new 对应 delete，operator new 对应 operator delete。