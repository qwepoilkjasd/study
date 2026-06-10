从 **C++ 源代码到程序运行**，整个过程通常可以分为 **编辑 → 预处理 → 编译 → 汇编 → 链接 → 装载运行** 六个阶段。

下面以一个简单程序为例：

```cpp
// main.cpp
#include <iostream>

int main() {
    std::cout << "Hello C++" << std::endl;
    return 0;
}
```

---

# 1. 编辑（Coding）

程序员编写源代码：

```cpp
main.cpp
```

文件中包含：

* 变量定义
* 函数实现
* 类定义
* 头文件引用

此时只是普通文本文件。

---

# 2. 预处理（Preprocessing）

执行预处理器（Preprocessor）。

例如：

```cpp
#include <iostream>
#define PI 3.14
```

会被展开。

### 主要工作

#### ① 头文件展开

```cpp
#include <iostream>
```

变成：

```cpp
// iostream中的内容
...
```

相当于直接复制进去。

---

#### ② 宏替换

```cpp
#define PI 3.14

double r = PI;
```

变成：

```cpp
double r = 3.14;
```

---

#### ③ 条件编译

```cpp
#ifdef DEBUG
    cout << "debug";
#endif
```

根据条件决定是否保留代码。

---

GCC命令：

```bash
g++ -E main.cpp -o main.i
```

得到：

```cpp
main.i
```

预处理后的文件。

---

# 3. 编译（Compilation）

编译器开始分析 C++ 代码。

```cpp
main.i
↓
main.s
```

生成汇编代码。

---

## 编译器主要做什么

### ① 词法分析

把代码拆成 Token

例如：

```cpp
int a = 10;
```

变成：

```text
int
a
=
10
;
```

---

### ② 语法分析

检查语法是否正确。

例如：

```cpp
int = a;
```

报错：

```text
expected identifier
```

---

### ③ 语义分析

检查逻辑是否合法。

例如：

```cpp
int a;
a();
```

报错：

```text
expression cannot be used as function
```

---

### ④ 优化

例如：

```cpp
int a = 2 + 3;
```

优化后：

```cpp
int a = 5;
```

称为：

**常量折叠（Constant Folding）**

---

### ⑤ 生成汇编

得到：

```asm
main:
    push rbp
    mov rbp,rsp
    ...
```

---

命令：

```bash
g++ -S main.cpp -o main.s
```

---

# 4. 汇编（Assembly）

汇编器把汇编代码变成机器码。

```cpp
main.s
↓
main.o
```

生成目标文件（Object File）。

---

命令：

```bash
g++ -c main.cpp -o main.o
```

或者：

```bash
as main.s -o main.o
```

---

目标文件包含：

```text
机器码
符号表
重定位信息
```

但还不能运行。

---

# 5. 链接（Linking）

多个 `.o` 文件和库文件合并。

例如：

```cpp
main.cpp
add.cpp
```

分别生成：

```text
main.o
add.o
```

然后链接：

```text
main.o + add.o
↓
app.exe
```

---

## 为什么需要链接

main.cpp：

```cpp
int add(int,int);

int main() {
    add(1,2);
}
```

add.cpp：

```cpp
int add(int a,int b){
    return a+b;
}
```

编译 `main.cpp` 时：

```cpp
add()
```

只有声明，没有实现。

编译器先记录：

```text
add 这个符号以后再找
```

链接阶段再去：

```cpp
add.o
```

里面找到真正实现。

---

## 静态库链接

例如：

```text
libxxx.a
```

Linux

或者：

```text
xxx.lib
```

Windows

链接时直接把库代码复制到程序中。

---

## 动态库链接

例如：

```text
libxxx.so
```

Linux

```text
xxx.dll
```

Windows

程序运行时再加载。

---

命令：

```bash
g++ main.o add.o -o app
```

生成：

```text
app
```

可执行文件。

---

# 6. 装载（Loading）

当执行：

```bash
./app
```

操作系统开始工作。

---

## OS做什么

### 创建进程

分配：

```text
PID
虚拟地址空间
页表
```

---

### 加载程序到内存

将：

```text
代码段(Text)
数据段(Data)
BSS段
```

映射到内存。

---

典型内存布局：

```text
高地址
│
├─ 栈(Stack)
│
├─ 共享库
│
├─ 堆(Heap)
│
├─ BSS
│
├─ Data
│
├─ Text
│
└────────────
低地址
```

---

### 加载动态库

例如：

```cpp
libstdc++.so
```

```cpp
libc.so
```

```cpp
libpthread.so
```

等等。

---

# 7. 运行（Runtime）

CPU开始执行。

入口并不是：

```cpp
main()
```

而是：

```text
_start
```

---

执行顺序：

```text
_start
↓
C Runtime(CRT)
↓
全局对象构造
↓
main()
↓
全局对象析构
↓
exit()
```

---

例如：

```cpp
class Test{
public:
    Test(){
        cout<<"构造\n";
    }
};

Test t;

int main(){
    cout<<"main\n";
}
```

输出：

```text
构造
main
```

因为全局对象在 `main()` 之前就已经构造完成。

---

# 整个流程图

```text
main.cpp
    │
    ▼
┌─────────┐
│ 预处理   │
└────┬────┘
     ▼
main.i
     │
     ▼
┌─────────┐
│ 编译     │
└────┬────┘
     ▼
main.s
     │
     ▼
┌─────────┐
│ 汇编     │
└────┬────┘
     ▼
main.o
     │
     ▼
┌─────────┐
│ 链接     │
└────┬────┘
     ▼
app.exe
     │
     ▼
┌─────────┐
│ 装载     │
└────┬────┘
     ▼
运行
```

对于实际企业级 C++ 项目（如使用 CMake + GCC/Clang/MSVC），还会涉及：

* CMake 生成构建系统
* 增量编译
* 静态库/动态库管理
* 链接器优化（LTO）
* ELF（Linux）或 PE（Windows）文件格式
* 动态链接器（`ld.so`）
* 运行时库（CRT、libstdc++）

理解这些内容后，你基本就掌握了一个 C++ 项目从源码到运行的完整生命周期。
