---
title: 实现
date: 2018-10-16 23:08:05
tags:
categories: Effective C++
---

1. 尽可能延后变量定义式的出现时间

   - 这样做可增加程序的清晰度并改善程序效率

2. 尽量少做转型动作

   - 尽量避免转型,特别是dynamic_cast

   - 宁可使用C++-style转型,不要使用旧式转型

3. 避免返回handle(包括reference,指针,迭代器)指向对象内部.

   - 帮助const成员函数的行为像个const,并将发生"虚吊号码牌"(dangling handles)的可能性降至最低.

4. 为异常安全而努力是值得的

   - 异常安全函数即使发生异常也不会泄漏资源或允许任何数据结构败坏。这样的函数区分为三种可能的保证：基本型、强烈型、不抛异常型。

   - 「强烈保证」往往能够以 copy-and-swap 实现出来，但”强烈保证“并非对所有函数都可实现或具备现实意义。
   - 函数提供的“异常安全保证”通常最高只等于其所调用的各个函数的“异常安全保证”中的最弱者。

5. 透彻了解 inlining 的里里外外

   - 将大多数 inling 限制在小型、被频发调用的函数身上。

   - 不要因为 function templates 出现在头文件，就将它们声明为 inline。

6. 将文件间的编译依存关系降至最低

   - “声明的依存性” 替换 “定义的依存性”，这正是编译依存性最小化的本质：现实中让头文件尽可能自我满足，万一做不到，则让它与其他文件内的声明式（而非定义式）相依。例如下例：Person 只内含一个指针成员，指向其实现类 PersonImpl。这般设计被称为 pimpl idiom。这样的设计下，Person 的客户完全与 Dates，Addresses 以及 Persons 的实现细目分离了。那些 classes 的任何实现修改都不需要 Person 客户端重新编译。真正实现了“接口与实现分离”。

     ```c++
     #include <string>
     class PersonImpl; //Person实现类的前置声明
     class Date; //Person接口用到的classes的前置声明
     class Address;
     class Person{
     public:
     	Person(const std::string& name, const Data& birthday, const Address& addr);
     	std::string name()const;
     	std::string birthDate()const;
     	std::string address()const;
     private:
     	std::shared_ptr<PersonImpl> pImpl;//指向实现的指针
     };
     ```

   - 如果使用 object reference 或 object pointers 可以完成任务，就不要使用 objects。

   - 尽量以 class 声明式替换 class 定义式。

   - 为声明式和定义式提供不同的头文件。

     ```c++
     #include "Person.h"
     #include "PersonImpl.h"
     Person::Person(const std::string& name, const Data& birthday, const Address& addr): pImpl(new PersonImpl(name, birthday, addr)){
     }
     std::string Person::name()const{
     	return pImpl->name();
     }
     ...
     ```

     像 Person 这样使用 pimpl idiom 的 class 被称为 Handle classes。它将所有函数转交给相应的实现类完成。

   - 另一种实现 Handle classes 的方法是 Interface class（虚基类）。通过工厂函数返回指针。

   - 支持 “编译依存性最小化” 的一般构想是：相依于声明式，不要想依与定义式。两个手段是：Handle classes 和 Interface classes。

   - 程序库头文件应该以“完全且仅有声明式”的形式存在，无论是否涉及templates。

