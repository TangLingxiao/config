---
title: 继承与面向对象设计（下）
date: 2018-10-16 23:22:27
tags:
categories: Effective C++
---

1. 绝不重新定义继承而来的 non-virtual 函数

2. 绝不重新定义继承而来的缺省参数值
   - 因为缺省参数值都是静态绑定，而 virtual 函数是动态绑定

3. 通过复合塑模出 has-a 或 “根据某物实现出”

4. 明智而审慎地使用 private 继承
   * private 继承意味着 “根据某物实现出“。当 derived class 需要访问 protected base class 的成员，或需要重新定义继承而来的 virtual 函数时，这么设计是合理的。
   * private 继承可以造成 empty base 最优化。
   * derived classes 可以重新定义 virtual 函数，但是不得调用它们。“重新定义” 表示某些事 “如何” 被完成，“调用” 则表示 “何时” 被完成。这是各自独立互不相干的。

5. 明智而审慎地使用多重继承

   * virtual 继承会增加大小、速度、初始化（及赋值）复杂度等等成本。如果 virtual base classes 不带任何数据，将是最具实用价值的情况。

   * 多重继承可能导致新的歧义性，以及对 virtual 继承的需要。

   * 多重继承的确有正当用途。其中一个情节涉及 “public 继承某个 Interface class” 和 “private 继承某个协助实现的 class” 的两相组合。

     ```c++
     class IPerson{
     public:
         virtual ~IPerson();
         virtual std::string name()const = 0;
         virtual std::string birthday()const = 0;
     };
     
     class DatabaseID;
     class PersonInfo{
     public:
         explicit PersonInfo(DatabaseID pid);
         virtual ~PersonInfo();
         virtual const char* theName() const;
         virtual const char* theBirthDate()const;
         virtual const char* valueDelimOpen()const;
         virtual const char* valueDelimClose()const;
     };
     
     class CPerson: public IPerson, private PersonInfo{
     public:
         explicit CPerson(DatabaseID pid):PersonInfo(pid){}
         virtual std::string name()const{
             return PersonInfo::theName();
         }
         virtual std::string birthday()const{
             return PersonInfo::theBirthDate();
         }
     private:
         const char* valueDelimOpen()const;
         const char* valueDelimClose()const;
     };
     
     ```
