###### ##作业簿册<br>by RoyceJentest

# Day01 C++提升篇01

## 第一题

扩展阅读（阅读，加深对面向对象的理解）
[OO真经]：https://www.cnblogs.com/leoo2sk/archive/2009/04/09/1432103.html

## 第二题

类与类之间的关系有哪几种？各自的特点怎样的？

> 类与类之间的关系有五种：继承（泛化）、关联、聚合、组合、依赖
>
> 1. 继承（泛化）：基类部分会成为派生类的一部分，在语义层面：A is B。
>
> 继承与泛化的区别
>
> 继承：先有基类，然后再有派生类。泛化：先有派生类，然后将具有相同的属性的抽象为基类。
>
> 2. 关联：
>
> 双向的关联关系：彼此知道对方的存在，但是并不负责对方的生命周期。在语义层面：A has B。在代码层面上，可以使用指针或者引用。
>
> 单向的关联关系：A知道B的存在，但是并不负责对方的生命周期。在语义层面：A has B。在代码层面上，可以使用指针或者引用。
>
> 3. 聚合：一种强一点的关联关系，有整体与局部的关系，但是整体并不负责局部的生命周期。在语义层面：A has B。在代码层面：使用的是指针或者引用。
>
> 4. 组合：是一种更强的关联关系。有整体与局部的关系，但是整体会负责局部的生命周期。在语义层面：A has B。在代码层面：使用的子对象。
>
> 5. 依赖：是两个类之间的一种不确定的关系，语义上是一种A use B的关系，这种关系是**偶然的，临时的，并非固定**的。在类图的画法：使用虚线的箭头，从A指向B 。在代码上表现为：
>
>    - B作为A的成员函数参数；
>
>    - B作为A的成员函数的局部变量（B作为A的成员函数的返回值）；
>
>    - A的成员函数调用B的静态方法。
>
>    总结：
>
>    1、从方向来看，继承是垂直关系，其它四种是横向关系。
>
>    2、从耦合程度来看，依赖 < 关联 < 聚合 < 组合 < 继承
>
>    3、从语义来看，继承 is，依赖 use， 关联、聚合、组合 has
>
>    4、依赖关系主要在成员函数。其他的四种主要在数据成员的角度。

## 第三题

面向对象设计有哪些原则？各自的特点是什么？

> 面向对象的设计原则设计了七种， 包括：
> 单一职责原则 (Single Responsibility Principle)、开闭原则(Open Closed Principle)、里氏替换原则
> (Liscov Substitution Principle)、接口分离原则 (Interface Segregation Principle)、依赖倒置原则
> (Dependency Inversion Principle)、迪米特法则（Law of Demeter -> Least Knowledge Principle）、
> 组合复用原则 (Composite/Aggregate Reuse Principle)
>
> 1. 单一职责原则
>
> 核心思想：一个类，最好只做一件事，只有一个引起它变化的原因
>
> 2. 开闭原则
>
> 核心思想就是对抽象编程，而不对具体编程，因为抽象相对稳定
>
> 3. 里氏替换原则
>
> 核心思想是：派生类必须能够替换其基类。
> 可以表现为：
> 派生类可以实现基类的抽象方法，体现多态（正好就是C++中多态的含义）
> 派生类可以增加新的个性/功能（C++中的新增功能的特点）
> 派生类不能覆盖基类中的非虚函数（C++中的隐藏）（基类与派生类中对于非虚函数而言，不要同
> 名）
>
> 4. 接口分离原则
>
> 核心思想：使用多个小的专门的接口，而不要使用一个大的总接口
>
> 5. 依赖倒置原则
>
> 核心思想：面向接口编程，依赖于抽象(抽象是稳定的，具体的是在变化的)
>
> 在大多数情况下，开闭原则、里氏代换原则和依赖倒置原则会同时出现，开闭原则是目标，里氏代换原则是基础，依赖倒置原则是手段
>
> 6. 最少知识（知道）原则
>
> 减少类与类、模块与模块之间的耦合程度。
>
> 7. 组合复用原则
>
> 使用关联、聚合取代继承关系。

## 第四题

运用所学的UML类图的知识，画出“文本查询程序的扩展”作业的类图。【C++ Primer 15.9节作业】（可以贴图）

## 第五题

设计模式可以分为几类？分别有哪些？

> 设计模式通常可以分为以下几类：
>
> 1. 创建型模式（Creational Patterns）：
>    - 单例模式（Singleton Pattern）
>    - 工厂方法模式（Factory Method Pattern）
>    - 抽象工厂模式（Abstract Factory Pattern）
>    - 建造者模式（Builder Pattern）
>    - 原型模式（Prototype Pattern）
>
> 2. 结构型模式（Structural Patterns）：
>    - 适配器模式（Adapter Pattern）
>    - 桥接模式（Bridge Pattern）
>    - 装饰器模式（Decorator Pattern）
>    - 组合模式（Composite Pattern）
>    - 外观模式（Facade Pattern）
>    - 享元模式（Flyweight Pattern）
>    - 代理模式（Proxy Pattern）
>
> 3. 行为型模式（Behavioral Patterns）：
>    - 策略模式（Strategy Pattern）
>    - 观察者模式（Observer Pattern）
>    - 模板方法模式（Template Method Pattern）
>    - 命令模式（Command Pattern）
>    - 迭代器模式（Iterator Pattern）
>    - 责任链模式（Chain of Responsibility Pattern）
>    - 备忘录模式（Memento Pattern）
>    - 状态模式（State Pattern）
>    - 访问者模式（Visitor Pattern）
>    - 中介者模式（Mediator Pattern）
>    - 解释器模式（Interpreter Pattern)
>    
> 4. 并发模式（Concurrency Patterns）：
>    - 读写锁模式（Read-Write Lock Pattern）
>    - 生产者-消费者模式（Producer-Consumer Pattern）
>    - 信号量模式（Semaphore Pattern）
>    - 线程池模式（Thread Pool Pattern）
>    - 观察者模式（Observer Pattern）的并发版本
>
> 这些设计模式提供了在软件设计中常见问题的解决方案，并且经过了广泛的实践验证，在不同的场景中被反复应用和证明。使用设计模式可以提高代码的可读性、可维护性和可扩展性，并且有助于降低代码的耦合度。

## 第六题

什么是工厂模式？它有哪些特点？

工厂模式是一种创建型设计模式，旨在提供一种创建对象的接口，但允许子类决定实例化的类是哪一个。它将对象的创建过程封装在一个单独的方法中，从而可以轻松地为系统提供灵活性和可维护性。

工厂模式通常包括以下几种变体：

1. 简单工厂模式（Simple Factory Pattern）：通过一个工厂类来创建对象，客户端通过传递不同的参数给工厂类来获得不同的对象实例。

2. 工厂方法模式（Factory Method Pattern）：定义一个创建对象的接口，但让子类决定实例化哪个类。这使得一个类的实例化延迟到了子类中进行。

3. 抽象工厂模式（Abstract Factory Pattern）：提供一个接口，用于创建相关或依赖对象的家族，而不需要明确指定具体类。它将一组工厂方法组织在一起，以便在需要时可以方便地交换产品家族。

工厂模式的主要特点包括：

1. 封装对象创建过程：将对象的创建过程封装在一个单独的方法或类中，客户端不需要直接创建对象，而是通过工厂来获取所需的对象实例。

2. 提供灵活性和可维护性：工厂模式使得系统更加灵活，因为客户端代码不依赖于具体的对象类，而是依赖于工厂接口或抽象类。这使得在需要修改对象创建过程时可以更加方便地进行扩展和维护。

3. 支持多态性：工厂模式通过将对象的创建过程推迟到子类中，实现了多态性。客户端代码可以使用统一的接口来获取不同的对象实例，从而提高了代码的可扩展性和可维护性。

4. 降低耦合度：工厂模式将对象的创建过程从客户端代码中解耦出来，使得客户端代码与具体对象类之间的耦合度降低，提高了系统的灵活性和可维护性。

## 第七题

实现工厂模式，并画出其类图

![](C:\Users\任永昌\AppData\Roaming\Typora\typora-user-images\Snipaste_2024-04-03_21-21-15.png)

<img src="http://ryctypora.oss-rg-china-mainland.aliyuncs.com/img/Snipaste_2024-04-03_21-21-15.png" style="zoom: 67%;" />

Factory.h

```c++
#ifndef _FACTORY_H
#define _FACTORY_H

#include"Product.h"
// 使用前向声明
class Product;

class Factory 
{
public: 
    
virtual Product * createProduct() = 0;
    
};

#endif //_FACTORY_H
```

FactoryA.h

```c++
#ifndef _FACTORYA_H
#define _FACTORYA_H


#include "Factory.h"
#include"ProductA.h"
class FactoryA
: public Factory 
{
public: 
    Product * createProduct() override;
};

#endif //_FACTORYA_H
```

FactoryA.cpp

```c++
#include "FactoryA.h"
/**
 * FactoryA implementation
 */

/**
 * @return Product *
 */
Product * FactoryA::createProduct()
{
    return new ProductA();
}
```

FactoryB.h

```c++
#ifndef _FACTORYB_H
#define _FACTORYB_H

#include "Factory.h"
#include "ProductB.h"


class FactoryB
:public Factory 
{
public: 
    Product * createProduct() override;
};

#endif //_FACTORYB_H
```

FactoryB.cpp

```c++
#include "FactoryB.h"

/**
 * FactoryB implementation
 */


/**
 * @return Product *
 */
Product * FactoryB::createProduct()
{
    return new ProductB();
}
```

Product.h

```c++
#ifndef _PRODUCT_H
#define _PRODUCT_H

#include "Factory.h"
#include<iostream>
using namespace std;
class Product {
public: 
    
virtual void show() = 0;
    
};

#endif //_PRODUCT_H
```

ProductA.h

```c++
#ifndef _PRODUCTA_H
#define _PRODUCTA_H

#include "Product.h"


class ProductA
:public Product 
{
public: 
    
    void show();
};

#endif //_PRODUCTA_H
```

ProductA.cpp

```c++
#include "ProductA.h"

/**
 * ProductA implementation
 */


/**
 * @return void
 */
void ProductA::show() {
    cout << "this is ProductA" << endl;
    return;
}
```

ProductB.h

```c++
#ifndef _PRODUCTB_H
#define _PRODUCTB_H

#include "Product.h"


class ProductB
: public Product
{
public: 

    void show();
};

#endif //_PRODUCTB_H
```

ProductB.cpp

```c++
#include "ProductB.h"

/**
 * ProductB implementation
 */


/**
 * @return void
 */
void ProductB::show() {
    cout << "this is ProductB" << endl;
    return;
}
```

test.cpp

```c++
#include "Factory.h"
#include "Product.h"
#include "FactoryA.h"
#include "FactoryB.h"
#include <memory>
int main(int argc, char * argv[]){
    cout << "hello,world" << endl;
    //生产产品A
    unique_ptr<Factory> factoryA(new FactoryA());
    unique_ptr<Product> productA(factoryA->createProduct());
    productA->show();

    //生产产品B
    unique_ptr<Factory> factoryB(new FactoryB());
    unique_ptr<Product> productB(factoryB->createProduct());
    productB->show();
    return 0;
}
```

![image-20240404230425585](http://ryctypora01.oss-cn-hangzhou.aliyuncs.com/img/image-20240404230425585.png)

# Day02 

test

无

# Day03 C++提升篇03

编程题：

实现生产者消费者问题, 采用基于对象的方式（重点掌握TaskQueue的封装）



# Day05

## 第一题

面试常考题（下面几个题目，大家在面试时候能够搞清楚即可，八股文）

1、TCP链接建立的过程需要三次握手，为什么？

​    

2、TCP链接断开的过程需要四次挥手，为什么？

 

3、 关闭链接时,服务器端可不可以主动断开链接?为什么？

 

4、为什么需要TIME_WAIT状态，该状态可以删除吗？

 

5、TCP协议和UDP协议有啥区别？

6、网络IO复用模型有哪些？它们之间的异同是什么？

7、网络IO模型有哪些？

8、什么是同步、异步？什么是阻塞、非阻塞？

9、Reactor模型 vs Proactor模型对比，各自的特点是？

## 第二题

简答题

什么是大端模式，什么是小端模式？大端模式和小端模式有什么区别？

## 第三题

简答题

TCP协议的服务器和客户端的基本通信流程是怎样的？其中有哪些函数是阻塞的？

## 第四题

编写一个程序，判断自己的机器是大端模式还是小端模式
