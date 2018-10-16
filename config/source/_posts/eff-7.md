---
title: 模板与泛型编程
date: 2018-10-16 23:27:26
tags:
categories: Effective C++
---

1. 了解隐式接口和编译期多态

   - classes 和 templates 都支持接口和多态。

   - 对 classes 而言接口是显式的，以函数签名为中心。多态则是通过 virtual 函数发生于运行期。
   - 对 templates 参数而言，接口是隐式的，奠基于有效表达式。多态则是通过 template 具现化和函数重载解析发生于编译期。

2. 了解 typename 的双重意义

   - 声明 template 参数时，前缀关键字 class 和 typename 可互换

   - 请使用关键字 typename 标识嵌套从属类型名称；但不得在 base class lists（基类列）或 member initialization list（成员初值列）内以它作为 base class 修饰符。

   - - 下面的例子，在我们知道 C 是什么之前，没有办法知道 C::const_iterator 是否为一个类型，在缺省情况下 C++ 将其视为变量而不是类型。这时必须在它前面放置关键字 typename。

       ```c++
       template<typename C>//C是一个 STL 兼容容器。
       void print2nd(const C& container){
           C::const_iterator* x;//C::const_iterator实际上是嵌套从属类型//被视为乘法操作
       }
       ```

     - 上述规则的例外是，typename 不可以出现在 base classes list 内的嵌套从属类型名称之前，也不可在 member initialization list 中作为 base class 修饰符。例如：

       ```c++
       template<typename T>
       class Derived: public Base<T>::Nested{//base class list 中不允许 typename
       public:
           explicit Derived(int x):Base<T>::Nested(x)//初始化列表不允许 typename
           {
               typename Base<T>::Nested temp;
               //既不在基类列也不再初始化列表，可以加typename
           }
       }；
       ```

3. 学习处理模板化基类内的名称

   - 可在 derived class template 内通过 this-> 指向 base class template 内的成员名称，或一个明白写出的 base class 资格修饰符（using 或 Base::)。

   - 模板全特化。例如：

     ```c++
     template<>
     class MsgSender<CompanyZ>{};
     ```

4. 将与参数无关的代码抽离 templates

   - templates 生成多个 classes 和多个函数，所以任何 template 代码都不该与某个造成膨胀的 template 参数产生相依关系。

   - 因非类型模板参数而造成的代码膨胀，往往可消除，做法是以函数参数或 class 成员变量替换 template 参数。
   - 因类型参数而造成的代码膨胀，往往可降低，做法是让带有完全相同二进制表述的具现类型共享实现码。

5. 运用成员函数模板接受所有兼容类型

   - 请使用 member function template 生成可接受所有兼容类型的函数。

   - 如果你声明 member template 用于「泛化 copy 构造」或「泛化 assignment 操作」你还是需要声明正常的 copy 构造函数和 copy assignment 操作符。

6. 需要类型转换时请为模板定义非成员函数

   - 当我们编写一个 class template，而他所提供之 “与此 template 相关的” 函数支持 “所有参数之隐式类型转换” 时，请将那些函数定义为 “class template 内部的 friend 函数”。对比条款24 。

7. 请使用 traits classes 表现类型信息

8. 认识 template 元编程

   - 将工作由运行期移往编译期，得以实现早期错误检测和更高的执行效率。
