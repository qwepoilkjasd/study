下面我用比较系统的方式讲一下 **C++ 中的 `this` 指针**。它是理解类、成员函数、链式调用、运算符重载、const 成员函数时非常重要的概念。

---

# 1. `this` 指针是什么？

在 C++ 中，**每一个非静态成员函数内部，都隐含地拥有一个特殊指针：`this`**。

它指向：

> **调用当前成员函数的那个对象本身**

例如：

```cpp
class Student {
public:
    string name;
    int age;

    void print() {
        cout << name << " " << age << endl;
    }
};
```

当你这样调用：

```cpp
Student s;
s.name = "Tom";
s.age = 18;

s.print();
```

看起来是：

```cpp
s.print();
```

但编译器可以理解成类似这样：

```cpp
Student::print(&s);
```

也就是说，在 `print()` 内部，隐藏着一个指针：

```cpp
this == &s
```

所以成员函数里的：

```cpp
cout << name << age;
```

本质上可以理解为：

```cpp
cout << this->name << this->age;
```

---

# 2. `this` 指针的基本用法

## 2.1 访问当前对象的成员变量

```cpp
class Person {
public:
    string name;

    void show() {
        cout << this->name << endl;
    }
};
```

这里的：

```cpp
this->name
```

表示当前对象的 `name` 成员。

一般情况下，`this->` 可以省略：

```cpp
cout << name << endl;
```

但是有些场景必须或者建议写出来。

---

# 3. 解决成员变量和参数同名问题

这是 `this` 最常见的用途之一。

比如：

```cpp
class Person {
private:
    string name;
    int age;

public:
    void setInfo(string name, int age) {
        name = name;
        age = age;
    }
};
```

这段代码有问题。

在函数内部，参数 `name` 会遮蔽成员变量 `name`。

所以：

```cpp
name = name;
```

其实是参数给参数赋值，成员变量没有被修改。

正确写法：

```cpp
class Person {
private:
    string name;
    int age;

public:
    void setInfo(string name, int age) {
        this->name = name;
        this->age = age;
    }
};
```

这里：

```cpp
this->name
```

表示当前对象的成员变量；

右边的：

```cpp
name
```

表示函数参数。

完整例子：

```cpp
#include <iostream>
#include <string>
using namespace std;

class Person {
private:
    string name;
    int age;

public:
    void setInfo(string name, int age) {
        this->name = name;
        this->age = age;
    }

    void show() {
        cout << "name = " << this->name << endl;
        cout << "age = " << this->age << endl;
    }
};

int main() {
    Person p;
    p.setInfo("Alice", 20);
    p.show();

    return 0;
}
```

输出：

```cpp
name = Alice
age = 20
```

---

# 4. `this` 指针的类型

假设有一个类：

```cpp
class Person {
public:
    void func() {
    }
};
```

在普通成员函数中，`this` 的类型是：

```cpp
Person* const this
```

注意这里有两个点：

```cpp
Person* const this
```

意思是：

> `this` 是一个指针常量，指向 `Person` 对象。

也就是说：

```cpp
this = nullptr;
```

这种操作是不允许的，因为 `this` 本身不能被重新赋值。

但是：

```cpp
this->成员变量 = 新值;
```

是可以的，因为普通成员函数可以修改当前对象的数据。

---

# 5. `const` 成员函数中的 `this`

如果成员函数后面加了 `const`：

```cpp
class Person {
private:
    int age;

public:
    void show() const {
        cout << age << endl;
    }
};
```

那么在这个函数中，`this` 的类型变成：

```cpp
const Person* const this
```

含义是：

1. `this` 指针本身不能改；
2. `this` 指向的对象也不能改。

所以在 `const` 成员函数中不能修改普通成员变量：

```cpp
class Person {
private:
    int age;

public:
    void show() const {
        age = 20; // 错误
    }
};
```

因为 `show()` 承诺不会修改当前对象。

正确用法：

```cpp
class Person {
private:
    int age;

public:
    void show() const {
        cout << age << endl;
    }
};
```

---

# 6. 返回 `*this` 实现链式调用

`this` 是指针，指向当前对象。

所以：

```cpp
*this
```

表示当前对象本身。

这在链式调用中非常常见。

例如：

```cpp
#include <iostream>
using namespace std;

class Counter {
private:
    int value;

public:
    Counter(int value = 0) {
        this->value = value;
    }

    Counter& add(int x) {
        this->value += x;
        return *this;
    }

    Counter& sub(int x) {
        this->value -= x;
        return *this;
    }

    void show() {
        cout << value << endl;
    }
};

int main() {
    Counter c;

    c.add(10).sub(3).add(5).show();

    return 0;
}
```

执行过程：

```cpp
c.add(10)
```

返回当前对象 `c` 的引用；

然后继续调用：

```cpp
.sub(3)
```

再返回当前对象引用；

最后：

```cpp
.add(5).show()
```

结果是：

```cpp
12
```

为什么返回类型要写成引用？

```cpp
Counter& add(int x)
```

如果写成：

```cpp
Counter add(int x)
```

返回的是对象副本，链式调用后面可能操作的是临时对象，不是原对象，效率也差。

所以一般链式调用返回：

```cpp
return *this;
```

并且返回类型写成：

```cpp
ClassName&
```

---

# 7. `this` 指针和构造函数

构造函数中也可以使用 `this`。

```cpp
class Person {
private:
    string name;
    int age;

public:
    Person(string name, int age) {
        this->name = name;
        this->age = age;
    }
};
```

不过 C++ 中更推荐使用 **成员初始化列表**：

```cpp
class Person {
private:
    string name;
    int age;

public:
    Person(string name, int age)
        : name(name), age(age) {
    }
};
```

这里初始化列表里的：

```cpp
name(name)
```

左边是成员变量，右边是参数。

虽然看起来有点绕，但这是 C++ 中非常常见、也更高效的写法。

---

# 8. `this` 指针和析构函数

析构函数中也有 `this`。

```cpp
class Person {
public:
    ~Person() {
        cout << "析构对象地址: " << this << endl;
    }
};
```

例如：

```cpp
int main() {
    Person p;
    cout << "对象地址: " << &p << endl;
}
```

你会发现：

```cpp
this
```

和：

```cpp
&p
```

是同一个地址。

---

# 9. 静态成员函数中没有 `this`

这是一个很重要的点。

```cpp
class Person {
public:
    static void func() {
        cout << this << endl; // 错误
    }
};
```

为什么？

因为静态成员函数属于类本身，不属于某个具体对象。

调用方式可以是：

```cpp
Person::func();
```

此时根本没有对象，所以没有 `this` 指针。

普通成员函数调用时需要对象：

```cpp
Person p;
p.func();
```

静态成员函数调用时不需要对象：

```cpp
Person::func();
```

所以：

> **静态成员函数中没有 `this` 指针，也不能直接访问非静态成员变量。**

例如：

```cpp
class Person {
private:
    int age;

public:
    static void func() {
        age = 10; // 错误
    }
};
```

因为 `age` 属于对象，不属于类。

如果想访问，需要显式传入对象：

```cpp
class Person {
private:
    int age;

public:
    static void func(Person& p) {
        p.age = 10;
    }
};
```

---

# 10. `this` 指针和运算符重载

`this` 在运算符重载中非常常见。

例如重载赋值运算符：

```cpp
class Person {
private:
    int age;

public:
    Person(int age = 0) {
        this->age = age;
    }

    Person& operator=(const Person& other) {
        if (this == &other) {
            return *this;
        }

        this->age = other.age;
        return *this;
    }
};
```

这里：

```cpp
if (this == &other)
```

用于判断是否是自我赋值。

比如：

```cpp
Person p;
p = p;
```

这时候：

```cpp
this
```

和：

```cpp
&other
```

其实是同一个地址。

所以可以直接返回：

```cpp
return *this;
```

这能避免一些资源释放类对象中的严重错误。

---

# 11. `this == nullptr` 可以吗？

这是一个容易踩坑的问题。

理论上你可以写：

```cpp
class Test {
public:
    void func() {
        if (this == nullptr) {
            cout << "this is null" << endl;
        }
    }
};
```

然后这样调用：

```cpp
Test* p = nullptr;
p->func();
```

但这是 **未定义行为**。

虽然有些编译器可能真的输出：

```cpp
this is null
```

但不能依赖它。

原因是：

> 通过空指针调用非静态成员函数，本身就是未定义行为。

所以不要写这种代码。

正确做法是调用前判断：

```cpp
Test* p = nullptr;

if (p != nullptr) {
    p->func();
}
```

---

# 12. `this` 指针是否占对象内存？

不占。

比如：

```cpp
class A {
public:
    int x;
};
```

对象：

```cpp
A a;
```

只存储成员变量 `x`。

`this` 指针不是对象的一部分，它是成员函数调用时由编译器隐式传入的参数。

可以粗略理解成：

```cpp
void A_func(A* const this);
```

所以：

```cpp
sizeof(A)
```

不会因为 `this` 指针而变大。

例子：

```cpp
#include <iostream>
using namespace std;

class A {
public:
    int x;

    void func() {
        cout << this << endl;
    }
};

int main() {
    cout << sizeof(A) << endl;
    return 0;
}
```

通常输出：

```cpp
4
```

而不是：

```cpp
12
```

或者：

```cpp
16
```

当然，具体大小还可能受内存对齐影响。

---

# 13. `this` 指针和对象地址

看这个例子：

```cpp
#include <iostream>
using namespace std;

class A {
public:
    int x;

    void printAddress() {
        cout << "this = " << this << endl;
    }
};

int main() {
    A a;
    cout << "&a   = " << &a << endl;
    a.printAddress();

    return 0;
}
```

输出类似：

```cpp
&a   = 0x7ffeedcc9abc
this = 0x7ffeedcc9abc
```

可以看到：

```cpp
this == &a
```

这说明 `this` 保存的就是当前对象的地址。

---

# 14. 多个对象调用同一个成员函数

看这个例子：

```cpp
class Person {
public:
    int age;

    void show() {
        cout << this->age << endl;
    }
};
```

调用：

```cpp
Person p1;
Person p2;

p1.age = 10;
p2.age = 20;

p1.show();
p2.show();
```

成员函数代码只有一份，但为什么能区分是 `p1.age` 还是 `p2.age`？

因为每次调用时传入的 `this` 不同。

```cpp
p1.show(); // this 指向 p1
p2.show(); // this 指向 p2
```

所以第一次输出：

```cpp
10
```

第二次输出：

```cpp
20
```

---

# 15. `this` 和引用返回的区别

有些人会疑惑：

```cpp
return *this;
```

为什么不是：

```cpp
return this;
```

因为二者类型不同。

假设：

```cpp
class A {};
```

那么：

```cpp
this
```

类型是：

```cpp
A*
```

而：

```cpp
*this
```

类型是：

```cpp
A
```

如果函数返回对象引用：

```cpp
A& func()
```

应该返回：

```cpp
return *this;
```

如果函数返回指针：

```cpp
A* func()
```

才返回：

```cpp
return this;
```

例子：

```cpp
class A {
public:
    A& f1() {
        return *this;
    }

    A* f2() {
        return this;
    }
};
```

调用方式不同：

```cpp
A a;

a.f1().f1();     // 返回引用，用点号继续调用
a.f2()->f2();    // 返回指针，用箭头继续调用
```

---

# 16. 在成员函数内部删除自己：`delete this`

C++ 中可以写：

```cpp
delete this;
```

但这是非常危险的写法。

例如：

```cpp
class A {
public:
    void destroy() {
        delete this;
    }
};
```

只有当对象是通过 `new` 创建的，才有可能合法：

```cpp
A* p = new A;
p->destroy();
```

但如果对象是栈对象：

```cpp
A a;
a.destroy(); // 错误，不能 delete 栈对象
```

会导致未定义行为。

即使是堆对象，`delete this` 后也不能再访问任何成员：

```cpp
p->destroy();
p->someFunc(); // 错误，对象已经被销毁
```

所以实际开发中应尽量避免 `delete this`，除非你非常清楚对象生命周期，比如某些引用计数框架或 COM 风格对象。

---

# 17. `this` 指针常见面试点总结

## 17.1 `this` 指针存在于哪里？

存在于非静态成员函数内部，由编译器隐式传入。

---

## 17.2 静态成员函数有没有 `this`？

没有。

因为静态成员函数不属于某个对象。

---

## 17.3 `this` 指针能不能修改？

不能。

普通成员函数中可以理解为：

```cpp
ClassName* const this
```

`this` 本身不能指向别的对象。

---

## 17.4 `const` 成员函数中 `this` 的类型是什么？

普通成员函数：

```cpp
ClassName* const this
```

`const` 成员函数：

```cpp
const ClassName* const this
```

---

## 17.5 `this` 占对象内存吗？

不占。

它不是对象的数据成员，而是成员函数调用时隐式传入的参数。

---

## 17.6 `this` 能不能是空指针？

正常情况下不应该是空指针。

通过空指针调用非静态成员函数是未定义行为。

---

# 18. 一个综合例子

```cpp
#include <iostream>
#include <string>
using namespace std;

class Person {
private:
    string name;
    int age;

public:
    Person(string name, int age) {
        this->name = name;
        this->age = age;
    }

    Person& setName(string name) {
        this->name = name;
        return *this;
    }

    Person& setAge(int age) {
        this->age = age;
        return *this;
    }

    void show() const {
        cout << "name = " << this->name << endl;
        cout << "age = " << this->age << endl;
    }

    bool isSameObject(const Person& other) const {
        return this == &other;
    }
};

int main() {
    Person p1("Alice", 18);
    Person p2("Bob", 20);

    p1.setName("Tom").setAge(25).show();

    cout << boolalpha;
    cout << p1.isSameObject(p1) << endl;
    cout << p1.isSameObject(p2) << endl;

    return 0;
}
```

输出：

```cpp
name = Tom
age = 25
true
false
```

这里涉及了：

```cpp
this->name
```

解决同名问题；

```cpp
return *this;
```

支持链式调用；

```cpp
void show() const
```

说明 `const` 成员函数中的 `this` 不能修改对象；

```cpp
this == &other
```

判断两个引用是否指向同一个对象。

---

# 19. 一句话总结

`this` 指针就是：

> **非静态成员函数中，指向当前调用对象的隐藏指针。**

普通成员函数里：

```cpp
ClassName* const this
```

`const` 成员函数里：

```cpp
const ClassName* const this
```

常见用途：

```cpp
this->成员变量
```

用于区分成员变量和参数；

```cpp
return *this;
```

用于返回当前对象，实现链式调用；

```cpp
this == &other
```

用于判断是否是同一个对象。




`this` 指针的生命周期可以这样理解：

> **`this` 指针本身只在非静态成员函数调用期间存在；它指向的对象生命周期由对象本身决定。**

也就是说，`this` 有两层含义要分开看：

```cpp
this        // 指针本身
*this       // this 指向的对象
```

它们的生命周期不是一回事。

---

## 1. `this` 指针本身什么时候产生？

当你调用一个非静态成员函数时，编译器会隐式传入当前对象的地址，这个地址在函数内部表现为 `this`。

例如：

```cpp
class A {
public:
    void func() {
        // this 在这里可用
    }
};

int main() {
    A a;
    a.func();
}
```

调用：

```cpp
a.func();
```

可以粗略理解为：

```cpp
A::func(&a);
```

所以在 `func()` 执行期间：

```cpp
this == &a
```

成立。

函数执行结束后，`this` 这个“隐式参数”也就结束了。

---

## 2. `this` 指针本身的生命周期

在一个成员函数中：

```cpp
class A {
public:
    void func() {
        cout << this << endl;
    }
};
```

`this` 的生命周期大致是：

```cpp
进入成员函数
    ↓
编译器提供 this
    ↓
函数体内可以使用 this
    ↓
函数返回
    ↓
this 这个隐式参数消失
```

所以：

> **`this` 指针本身的生命周期等于当前成员函数调用的生命周期。**

它不是对象的数据成员，不存在对象里，也不会跟着对象一起存活。

---

## 3. `this` 指向的对象生命周期

`this` 指向的是当前对象。

对象生命周期取决于对象是怎么创建的。

---

### 3.1 栈对象

```cpp
class A {
public:
    void show() {
        cout << this << endl;
    }
};

int main() {
    A a;
    a.show();
}
```

这里对象 `a` 是栈对象。

它的生命周期是：

```cpp
进入 main 中 a 的作用域
    ↓
构造 a
    ↓
调用 a.show()，this 指向 a
    ↓
离开作用域
    ↓
析构 a
```

所以在 `show()` 里：

```cpp
this == &a
```

但是 `show()` 结束后，`this` 不存在了。

对象 `a` 仍然存在，直到离开作用域。

---

### 3.2 堆对象

```cpp
A* p = new A;
p->show();
delete p;
```

调用：

```cpp
p->show();
```

时：

```cpp
this == p
```

对象生命周期是：

```cpp
new A
    ↓
对象存在
    ↓
p->show()，this 指向堆对象
    ↓
show() 结束，this 消失
    ↓
对象仍然存在
    ↓
delete p
    ↓
对象销毁
```

注意：

```cpp
delete p;
p->show(); // 错误，未定义行为
```

因为对象已经被销毁了，再通过这个地址调用成员函数，`this` 指向的是无效对象。

---

### 3.3 静态对象 / 全局对象

```cpp
class A {
public:
    void show() {
        cout << this << endl;
    }
};

A globalA;

int main() {
    globalA.show();
}
```

`globalA` 是全局对象，生命周期贯穿整个程序运行期间。

调用成员函数时，`this` 临时出现，指向 `globalA`。

```cpp
globalA.show(); // this 指向 globalA
```

函数结束后 `this` 消失，但 `globalA` 继续存在。

---

## 4. 构造函数中的 `this` 生命周期

构造函数也是非静态成员函数，所以里面也有 `this`。

```cpp
class A {
public:
    A() {
        cout << "constructor this = " << this << endl;
    }
};
```

当对象开始构造时，`this` 就可以在构造函数中使用。

```cpp
A a;
```

过程大概是：

```cpp
为对象 a 分配内存
    ↓
调用构造函数
    ↓
构造函数中 this 指向这块内存
    ↓
构造完成
    ↓
对象生命周期正式开始/可正常使用
```

不过要注意：

> 在构造函数执行期间，对象还没有完全构造完成。

例如：

```cpp
class A {
public:
    A() {
        func();
    }

    virtual void func() {
        cout << "A::func" << endl;
    }
};

class B : public A {
public:
    void func() override {
        cout << "B::func" << endl;
    }
};
```

```cpp
B b;
```

构造 `B` 时，会先构造 `A` 部分。

在 `A` 的构造函数里调用虚函数：

```cpp
func();
```

调用的是：

```cpp
A::func
```

不是：

```cpp
B::func
```

因为此时 `B` 部分还没有构造完成。

所以构造函数中的 `this` 是可用的，但对象还处在“半成品”状态。

---

## 5. 析构函数中的 `this` 生命周期

析构函数里也有 `this`。

```cpp
class A {
public:
    ~A() {
        cout << "destructor this = " << this << endl;
    }
};
```

当析构函数执行时，对象还没有完全消失，`this` 仍然指向当前对象。

过程：

```cpp
对象生命周期即将结束
    ↓
调用析构函数
    ↓
析构函数中 this 仍然有效
    ↓
析构函数结束
    ↓
对象内存被释放或生命周期结束
```

但是析构函数中也要注意虚函数问题：

```cpp
class A {
public:
    virtual ~A() {
        func();
    }

    virtual void func() {
        cout << "A::func" << endl;
    }
};

class B : public A {
public:
    ~B() {
    }

    void func() override {
        cout << "B::func" << endl;
    }
};
```

销毁 `B` 对象时，先执行 `B::~B()`，再执行 `A::~A()`。

在 `A::~A()` 中调用：

```cpp
func();
```

调用的是：

```cpp
A::func
```

不是：

```cpp
B::func
```

因为此时派生类 `B` 的部分已经析构完了。

---

## 6. 能不能保存 `this` 指针？

可以保存，但要非常小心。

例如：

```cpp
class A {
public:
    A* getThis() {
        return this;
    }
};
```

使用：

```cpp
A a;
A* p = a.getThis();
```

此时：

```cpp
p == &a
```

是成立的。

只要对象 `a` 还活着，`p` 就有效。

```cpp
p->getThis(); // 可以
```

但如果对象生命周期结束：

```cpp
A* p;

{
    A a;
    p = a.getThis();
}

p->getThis(); // 错误，p 已经悬空
```

这里 `a` 离开作用域后被销毁，`p` 还保存着原来的地址，但那个地址已经不再代表一个有效对象。

这叫：

> **悬空指针。**

---

## 7. 返回 `this` 或 `*this` 是否安全？

要看对象是否还能继续活着。

### 返回 `this`

```cpp
class A {
public:
    A* self() {
        return this;
    }
};
```

```cpp
A a;
A* p = a.self(); // 安全，a 还活着
```

但：

```cpp
A* makePtr() {
    A a;
    return a.self();
}

int main() {
    A* p = makePtr();
    p->self(); // 错误，p 悬空
}
```

因为 `makePtr()` 返回后，局部对象 `a` 已经销毁。

---

### 返回 `*this`

常见于链式调用：

```cpp
class A {
public:
    A& set() {
        return *this;
    }
};
```

```cpp
A a;
a.set().set(); // 安全
```

因为返回的是当前对象引用，而 `a` 还活着。

但下面就危险：

```cpp
A& bad() {
    A a;
    return a.set();
}
```

函数返回后 `a` 已经销毁，返回引用悬空。

---

## 8. `delete this` 对生命周期的影响

有些代码会写：

```cpp
class A {
public:
    void destroy() {
        delete this;
    }
};
```

这表示：

> 在成员函数内部销毁当前对象。

它只有在当前对象确实是通过 `new` 创建时才可能合法。

```cpp
A* p = new A;
p->destroy(); // 可能合法
```

但调用之后：

```cpp
p->destroy();
p->destroy(); // 错误，对象已经没了
```

而且在 `delete this` 后，成员函数里也不能再访问成员变量：

```cpp
class A {
public:
    int x = 10;

    void destroy() {
        delete this;
        cout << x << endl; // 错误，对象已经销毁
    }
};
```

对象如果是栈上创建的，更不能 `delete this`：

```cpp
A a;
a.destroy(); // 错误，delete 栈对象，未定义行为
```

所以一般不推荐 `delete this`，除非你在写非常明确的生命周期管理逻辑，比如引用计数对象。

---

## 9. 空指针调用成员函数时 `this` 的生命周期？

例如：

```cpp
class A {
public:
    void show() {
        cout << this << endl;
    }
};

int main() {
    A* p = nullptr;
    p->show();
}
```

有些环境下你可能看到输出：

```cpp
0
```

但这不是合法行为。

因为：

```cpp
p->show();
```

要求 `p` 指向一个有效对象。

`p == nullptr` 时没有对象，所谓的 `this` 也不是一个有效的当前对象指针。

所以这是：

> **未定义行为。**

不要依赖它。

---

## 10. 一个非常关键的区别

下面这句话很重要：

> **成员函数执行结束，不代表对象销毁；对象销毁，才代表以后不能再使用指向它的 `this` 地址。**

比如：

```cpp
class A {
public:
    A* getThis() {
        return this;
    }
};

int main() {
    A a;

    A* p1 = a.getThis();
    A* p2 = a.getThis();

    cout << p1 << endl;
    cout << p2 << endl;
}
```

`getThis()` 调用两次，产生了两次 `this` 隐式参数。

但它们的值相同，都是 `&a`。

```cpp
p1 == p2 == &a
```

因为它们都指向同一个对象。

不过每一次函数调用里的 `this` 指针本身都是那次调用的隐式参数。

---

## 11. 生命周期总结表

| 内容                      | 生命周期                       |
| ----------------------- | -------------------------- |
| `this` 指针本身             | 当前非静态成员函数调用期间              |
| `this` 指向的栈对象           | 从定义处开始，到离开作用域结束            |
| `this` 指向的堆对象           | 从 `new` 成功开始，到 `delete` 结束 |
| `this` 指向的全局对象          | 程序启动后构造，程序结束时析构            |
| 构造函数中的 `this`           | 构造函数执行期间有效，但对象尚未完全构造       |
| 析构函数中的 `this`           | 析构函数执行期间有效，但对象正在销毁         |
| 保存下来的 `this` 指针         | 只有在对象仍然存活时有效               |
| `delete this` 后的 `this` | 不能再安全使用                    |

---

## 12. 最核心的一句话

`this` 指针本身只是成员函数调用时的一个隐式参数，**随着函数调用开始而出现，随着函数返回而消失**；但它指向的对象可以比这次函数调用活得更久，也可能在函数中被销毁。

可以记成：

```cpp
this 的生命 = 一次成员函数调用
*this 的生命 = 当前对象的生命
```
