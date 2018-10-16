---
title: 继承与面向对象设计（上）
date: 2018-10-16 23:12:44
tags:
categories: Effective C++
---

1. 确定你的 public 继承塑模出 is-a 关系

2. 区分接口继承和实现继承

   - derived classes 内的名称会遮掩 base classes 内的名称。

   - 为了让被遮掩的名称在见天日，可使用 using 声明式或转交函数（forwarding functions）。

     ```c++
     class Base{
     public:
     	virtual void mf1() = 0;
     	virtual void mf1(int);
     };
     
     class Derived: public Base{
     public:
     	virtual void mf1();
     };
     
     Derived d;
     d.mf1();//right
     d.mf1(x);//wrong
     
     //----------------using-----------
     class Derived1: public Base{
     public:
     	using Base::mf1;
     	virtual void mf1();
     };
     Derived1 d1;
     d1.mf1();//right
     d1.mf1(x);//right
     
     //假设Derived只想继承mf1（）的无参数版本。using 声明式则排不上用场，这时可以用转交函数
     //----------------forwarding functions
     class Derived2: private Base{
     public:
     	virtual void mf1(){
     		Base::mf1();
         }
     };
     ```

3. 区分接口继承和实现继承

   - 声明一个 pure virtual 函数的目的是为了让 derived classes 只继承函数接口。

   - 声明普通的 virtual 函数是为了让 derived classes 继承接口和缺省实现。
   - 声明 non-virtual 函数是为了令 derived classes 继承接口和强制性实现。

4. 考虑 virtual 函数以外的其他选择

   - 使用 non-virtual-interface(NVI) 手法，那是 Template Method 设计模式的一种特殊形式。它以 public non-virtual 成员函数包裹较低访问性（private 或 protected）的 virtual 函数。

     ```c++
     class GameCharacter{
     public:
     	int healthValue() const{
     		...
     		int retVal = doHealthValue()
     		...
     		return retVal;
         }
     private:
     	virtual int doHealthValue()const{
     		...
         }
     };
     ```

   - 将 virtual 函数替换为 “函数指针成员变量” ，这是 Strategy 设计模式的一种分解表现形式。

     ```c++
     class GameCharacter;
     int defaultHealthCalc(const GameCharacter& gc);
     class GameCharacter{
     public:
     	typedef int (*HealthCalFunc)(const GameCharacter&);
     	explicit GameCharacter(HealthCalcFunc hcf = defaultHealthCalc)
             :healthFunc(hcf){}
         int healthValue()const{
             return healthFunc(*this);
         }
     private:
         HealthCalcFunc healthFunc;
     };
     
     class EvilBadGuy: public GameCharacter{
         ...
     };
     
     int loseHealthQuickly(const GameCharacter&);
     int loseHealthSlowly(const GameCharacter&);
     EvilBadGuy ebg1(loseHealthQuickly);
     EvilBadGuy ebg2(loseHealthSlowly);
     ```

   - 以 std::function 成员变量替换 virtual 函数，因而允许使用任何可调用物（callable entity）搭配一个兼容于需求的签名式。这也是 Strategy 设计模式的某种形式。

     ```c++
     class GameCharacter;
     int defaultHealthCalc(const GameCharacter& gc);
     class GameCharacter{
     public:
     //和上边只有这一处不同//
         typedef std::function<int （const GameCharacter&）> HealthCalcFunc;
         explicit GameCharacter(HealthCalcFunc hcf = defaultHealthCalc)
             :healthFunc(hcf){}
         int healthValue()const{
             return healthFunc(*this);
         }
     private:
     	HealthCalcFunc healthFunc;
     };
     
     class EvilBadGuy: public GameCharacter{
         ...
     };
     
     short calcHealth(const GameCharacter&);
     struct HealthCalculator{
         int operator()(const GameCharacter&)const{...}
     };
     
     class GameLevel{
     public:
         float health(const GameCharacter&)const;
     };
     
     EvilBadGuy ebg1(calcHealth);
     EvilBadGuy ebg2(HealthCalculator());
     
     GameLevel currentlevel;
     //注意成员函数有个this指针
     EvilBadGuy ebg3(std::bind(&GameLevel::health, currentlevel, _1));
     EvilBadGuy ebg4(
         [&](GameCharacter&)->int
     	{
     		...
     	});
     ```

   - 传统的 Strategy设计模式实现，将继承体系内的 virtual 函数替换为另一个继承体系内的 virtual 函数。

     ```c++
     class GameCharacter;
     class HealthCalcFunc{
     public:
         virtual int calc(const GameCharacter& )const;
     };
     HealthCalcFunc defaultHealthCalc;
     class GameCharacter{
     public:
         explicit GameCharacter(HealCalcFunc* phcf = &defaultHealthCalc)
             :pHealthCalc(phcf){}
         int healthValue()const{
             return pHealthCalc->calc(*this);
         }
     private:
         HealthCalcFunc* pHealthCalc;
     };
     ```

