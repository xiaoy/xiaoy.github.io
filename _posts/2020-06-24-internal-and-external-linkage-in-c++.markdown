---
layout:     post
title:      "C++ 中的外部和内部链接"
subtitle:   "" 
date:   2020-06-24
author: "武龙飞"
tags:
    - C/C++
    - 2020
---

## C/C++ 编译流程
首先看一下C/C++编译流程图：

![](/img/in-post/2020-06-24-interal-and-external-linkage/build_model.png)

整个流程如下：

1. 预处理器(Preprocess)将头文件内容在cpp文件中展开，展开后的文件这里称作TU(Translation Unit)
2. 编译器(Compile)将TU编译为Object文件
3. 链接器(Link)将Object文件，以及库文件链接生成可执行文件

本文讨论步骤一中展开和步骤三中链接问题。

## 定义和申明
*申明(declaration)*的作用告知编译器*符号(symbol)* 存在，不需要知道变量长度或函数内容。

*定义(definition)* 告知编译器需要为变量分配对应尺寸的内存，以及函数内容。

函数的申明和定义如下：

```C++
int foo();                      // 申明函数
int foo() {return 5;}           // 定义函数
```

变量的申明和定义如下：

```C++
extern int a;                   // 申明变量
int a = 10;                     // 定义变量
```

变量需要注意的两种情况：

```C++
extern int a = 10;             // 此处是变量定义，extern失去效果
int a;                         // 此处也为变量定义，同时变量a的内容未知
```

### 前向申明(ForwardDeclaring)
C++中有前向申明符号的概念。允许C++先申明变量类型或符号，不需要包含具体的实现。好处是当此类型的头文件修改时，使用了此类型的文件不需要编译。

```C++
// foo.h
class Player;               // 这里申明类型Player即可
int foo(Player p);
```

这样当`player.h`文件修改后，引用`Player`的文件不需要重新编译。

### 一次定义规则
在C/C++中同一变量或函数申明不限次数，但定义只能为一次，这称作 [一次定义规则(one difinition rule)](https://en.wikipedia.org/wiki/One_Definition_Rule)。比如在C++中：

```C++
int foo();
int foo();
int foo();
int foo();
int foo(){return 5;}
```

但是定义两次不可以：

```C++
int foo(){return 5;}
int foo(){return 9;}
```

## 预处理器
预处理器执行CPP文件中的宏指令，通过执行指令后得到TS。看一个例子：

```C++
// player.h
#ifdef PLAYER_H_
#define PLAYER_H_
#define DEBUG 1
#ifdef DEBUG
void log_debug();
#else
void log_info(); 
#endif
void log();
#endif
```

```C++
// player.cpp
#include "player.h"
void log_debug(){

}

void log_info(){

}
void log(){
#ifdef DEBUG
    log_debug();
#else
    log_info(); 
#endif
}
```

预处理器处理后的内容为：

```C++
void log_debug();
void log();

void log_debug(){

}

void log_info(){

}
void log(){
    log_debug();
}
```
## 内部链接
符号(symbol)的内部链接指的是符号只在当前TU里可见。这就意味着在头文件定义了内部链接符号，在所有包含此头文件的TU里，都会是独立的符号。缺点是每个TU里都需要为此变量分配内存。

C/C++中使用`static`申明内部链接：

```C++
static int a = 42;
```

这里添加一个例子来说明内部链接：

```C++
// config.h
static int s_max_year = 9999;
```

```C++
// earth.h
void set_max_earth_year();
```

```C++
// mar.h
void set_max_mar_year();
```

```C++
// earth.cpp
#include "config.h"
#include "earth.h"
void set_max_earth_year()
{
    s_max_year = 2999;
}
```

```C++
// mar.cpp
#include "config.h"
#include "mar.h"
void set_max_mar_year()
{
    s_max_year = 1999;
}
```

```C++
// main.cpp
#include <iostream>
#include "config.h"
#include "earth.h"
#include "mar.h"

int main()
{
    set_max_earth_year();
    set_max_mar_year();
    std::cout << "max year:" << s_max_year <<"\n";      // 9999
}
```

在C++中提供了另外一种方式，*匿名命名空间(Anonymous Namespaces)* 来实现内部链接：

```C++
namespace {int variable = 0; }          // static int variable = 0 效果一致
```
因为`config.h`包含在`mar.cpp`和`earth.cpp`后，`s_max_year`在这两个TU里是独立的。虽说内部链接会占用内存，并且让代码变的难以理解，但是通过内部链接TU之间可以隐藏自己的值。

## 外部链接
符号如果是外部链接，那意外这此符号在其他TU可见。此符号在所有包含它的TU里共享，但只允许在一个TU里定义。在C/C++中使用关键字`extern`来申明外部链接，函数默认是`extern`。

```C++
extern int g_cur_year;
extern void set_cur_year();
```

外部链接的符号用在全局变量设置上。C语言可以直接使用宏来定义常量：

```C
#define CLK 10000
```

作为C++程序员，建议使用命名空间：

```C++
// global.h
namespace Global
{
    extern unsigned int g_max_year;
}

// global.cpp
namespace Global
{
    unsigned int g_max_year = 9999;
}
```

## 参考

[internal_and_external_linkage_in_c++](http://www.goldsborough.me/c/c++/linker/2016/03/30/19-34-25-internal_and_external_linkage_in_c++/)