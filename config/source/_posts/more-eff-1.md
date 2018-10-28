---
title: 基础议题
date: 2018-10-18 00:28:14
tags:
categories: More Effective C++
---

1. 仔细区别 pointers 和 references

   - reference 必须有初值，reference 一定代表某个对象不可能是 null
   - 当确定指向某个东西而且绝不会改变指向其他东西，或是当你实现一个操作符而其语法需求无法由 pointers 达成，则选择 references。任何其他时候，采用 pointers。

2. 最好使用 C++ 转型操作符

   - static_cast 基本上拥有与 C 旧式转型相同的威力与意义，以及相同的限制。

   - const_cast 仅用来改变表达式中的常量性（constness）或易变性（volatileness）。

   - dynamic_cast 用来执行继承体系中 ”安全的向下转型或跨系转型动作“ 。也就是说你可以利用 dynamic_cast，将 ”指向 base class objects 的 pointers 或 references“ 转型为 ”指向 derived（或 sibling base）class objects 的 pointers 或 references“，并得知转型是否成功。如果失败，会以一个 null 指针（当转型对象是指针）或一个 exception（当转型对象是 reference）表现出了。

   - reinterpret_cast 转换结果几乎总是与编译平台息息相关，不具移植性。最常用的用途是转换函数指针类型。例如：

     ```c++
     typedef void (*FuncPtr)();
     FuncPtf funcPtrArray[10];
     
     int doSomething();//不转型无法放进funcPtrArray
     funcPtrArray[0]=&dosomething;//报错
     funcPtrArray[0]=reinterpret_cast<FuncPtr>(&dosomething);//这样即可通过编译
     ```

3. 绝对不要以多态方式处理数组

   - 通过 base class 的 pointers 和 references 来操作 derived class objects 所形成的数组，结果几乎绝不会如预期般地运作。

     例如：

     ```c++
     class BST;
     class BalancedBST: public BST;
     
     void printBSTArray(ostream& s, const BST array[], int numElements){
         for (int i = 0; i < numElements; ++i){
             s << array[i];//假设BST 重载了 << 运算符
         }
     }
     
     BST BSTArray[10];
     printBSTArray(cout, BSTArray, 10);//right
     
     BalancedBST bBSTArray[10];
     printBSTArray(cout, bBSTArray, 10);//???
     ```

     我们知道 array[i]  其实是 *(array + i)。array 和 array + i 之间的距离一定是 i * sizeof(BST)。但 BalancedBST 大小比 BST 大，因此上述结果不可预期。

     再例如，通过一个 base class 指针，删除一个 derived class objects 组成的数组：

     ```c++
     void deleteArray(ostream& s, BST array[]){
         s << "Deleting array at address "
           << static_cast<void*>(array) << '\n';
         delete [] array;
     }
     
     BalancedBST *balTreeArray = new BalancedBST[50];
     deleteArray(cout, balTreeArray);//结果未定义
     ```

     虽然没有看到，但其中一样有 ”指针算数表达式“ 存在。当数组被删除，其中每个元素的 destructor 都必须被调用。于是编译器由 delete [] array; 产生如下代码：

     ```c++
     for(int i = the number of elements in the array - 1; i >= 0; --i){
         array[i].BST.~BST();
     }
     ```

     于是产生和上一个例子同样的问题。

4. 非必要不提供 default constructor

   - 如果 default constructor 无法确保对象的所有字段都被正确的初始化，就避免让 default constructor 出现。
   - 一个类显式地声明了任何构造函数，编译器不生成公有的默认构造函数。在这种情况下，如果程序需要一个默认构造函数，需要由类的设计者提供。
   - 默认构造函数（default constructor）就是在没有显式提供初始化式时调用的构造函数。它由不带参数的构造函数，或者为所有的形参提供默认实参的构造函数定义。
