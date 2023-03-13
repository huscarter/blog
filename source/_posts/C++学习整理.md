---
title: C++学习整理
date: 2022-08-04 20:29:44
categories: 计算机语言
tags: 
    - C
description: 做android开发现在的项目时不时会用到JNI，所以自学下C++对以后android的功能开发会有一定的帮助。
---
## 简介
C++ 是由 Bjarne Stroustrup 于 1979 年在新泽西州美利山贝尔实验室开始设计开发的。C++ 进一步扩充和完善了 C 语言，最初命名为带类的C，后来在 1983 年更名为 C++。C++ 是 C 的一个超集，事实上，任何合法的 C 程序都是合法的 C++ 程序。C++ 被认为是中级语言，它综合了高级语言和低级语言的特点。
## 开发环境
C++的开发需要准备2个东西：一个是编辑器，用来写代码；另一个是编译器，用来编译运行C++代码。编辑器可以用最原始的文本编辑器，但是为了效率建议还是使用Visual Studio或者使用Visual Studio Code，这2个属于ide包含了编辑器和编译器的功能。如果是Mac环境建议使用Visual Studio Code。

## 基本语法
### C++ 程序结构
先看一下C++程序的结构，后续会针对结构中的各个部分分别讲解。
```
#include <iostream> // 预编译引用
using namespace std; // 命名空间

int float f= 0.02; // 全局变量
/**
 * main函数是程序开始执行的地方
 */
int main()
{
  int i = 0; // 局部变量
  cout << "Hello World" << endl; // 输出 Hello World
  return 0;
}
```
### 编译执行
可在当前C++文件目录执行
```
// 编译文件 
g++ [源代码文件] -o [目标文件名]
g++ test.cpp -o test.out

// 执行文件
./test.out
```

### C++数据类型

#### 简单类型

| 类型     | 关键字      | 长度   | 说明(t代码关键字)                                            |
| -------- | ----------- | ------ | ----------------------------------- |
| 布尔型   | bool        | 1byte  |                                                              |
| 字符型   | char        | 1byte  | unsigned t [0,255]，signed t[-128,127]                       |
| 短整型   | short int   | 2byte  | unsigned t [0,65,535]，signed t[-32768,32767]                |
| 整型     | int         | 4byte  | unsigned t [0,2的32次方-1]，signed t[-2的32次方,2的32次方-1] |
| 长整型   | long int    | 8byte  | unsigned t [0,2的64次方-1]，signed t[-2的63次方,-2的63次方-1] |
| 浮点型   | float       | 4byte  |                                                              |
| 双精度   | double      | 8byte  |                                                              |
| 长双精度 | long double | 16byte | 双精度型 16 个字节，可提供18-19位有效数字                    |
| 无类型   | void        |        |                                                              |
| 宽类型   | wchat_t     | 2byte  | typedef short int wchar_t                                    |

可通过C++提供的limits方法打印类型的范围

```
#include<iostream>  
#include <limits>
using namespace std;  
  
int main()  
{
  cout << "char: \t\t" << "所占字节数：" << sizeof(int);  
  cout << "\t最大值：" << (numeric_limits<int>::max)();  
  cout << "\t\t最小值：" << (numeric_limits<int>::min)() << endl;
}
```

#### 数组类型
```
double balance[5] = {1000.0, 2.0, 3.4, 7.0, 50.0};
balance[0] = 99.0
```

#### 字符串类型
C++提供了2种字符串类型的表达方式
1. chat[] 形式（C风格字符串）
```
char site[7] = {'R', 'U', 'N', 'O', 'O', 'B', '\0'};
```
2. string （需要提前引入 cstring 库）
```
#include <cstring>
using namespace std;
char str1[13] = "runoob";
```

### 指针与引用
1. 指针
指针是一个变量，其值为另一个变量的地址，即内存位置的直接地址。
2. 引用
是某个已存在变量的另一个名字，申明初始化之后不可修改。
```
#include <iostream>
using namespace std;

int i = 20;
int& ilink = i; // ilink是初始化为i的整形引用
int* ipoint = &i; // 指针

cout << i << endl; // 20
cout << ilink << endl; // 20
cout << *ipoint << endl; // 20

```

### 日期和时间
C++ 标准库没有提供所谓的日期类型，而是继承了 C 语言用于日期和时间操作的结构和函数。为了使用日期和时间相关的函数和结构，需要在 C++ 程序中引用 <ctime> 头文件。
有四个与时间相关的类型：clock_t、time_t、size_t 和 tm。类型 clock_t、size_t 和 time_t 能够把系统时间和日期表示为整数。结构类型 tm 结构的定义如下
```
struct tm {
  int tm_sec;   // 秒，正常范围从 0 到 59，但允许至 61
  int tm_min;   // 分，范围从 0 到 59
  int tm_hour;  // 小时，范围从 0 到 23
  int tm_mday;  // 一月中的第几天，范围从 1 到 31
  int tm_mon;   // 月，范围从 0 到 11
  int tm_year;  // 自 1900 年起的年数
  int tm_wday;  // 一周中的第几天，范围从 0 到 6，从星期日算起
  int tm_yday;  // 一年中的第几天，范围从 0 到 365，从 1 月 1 日算起
  int tm_isdst; // 夏令时
};
```
#### 时间转字符串
```
#inclue <iostream>
#include <cstring>
using namespace std;

// time 转 字符串封装方法
string date2Str (const time_t* time) {
  tm* tm = localtime(time);
  char buf[128];
  strftime(buf, sizeof(buf), "%Y-%m-%d %H:%M:%S", tm);
  return buf;
}
// main方法
int main( )
{
  time_t now = time(0); // 1970到现在的时间：1503564157
  cout << "当前时间:" << date2Str(&now) << endl;
}
```
#### 字符串转时间
```
#inclue <iostream>
#include <cstring>
using namespace std;

// 字符串转 time 封装方法
time_t str2Time (string str) {
  tm dateTimeHold;
  int year, month, day, hour, minute, second;
  year = atoi(str.substr(0, 4).c_str());
  month = atoi(str.substr(5, 7).c_str());
  day = atoi(str.substr(8, 10).c_str());
  hour = atoi(str.substr(11, 13).c_str());
  minute = atoi(str.substr(14, 16).c_str());
  second = atoi(str.substr(17, 19).c_str());
  dateTimeHold.tm_year = year - 1900;
  dateTimeHold.tm_mon = month - 1;
  dateTimeHold.tm_mday = day;
  dateTimeHold.tm_hour = hour;
  dateTimeHold.tm_min = minute;
  dateTimeHold.tm_sec = second;
  dateTimeHold.tm_isdst = 0;
  time_t t = mktime(&dateTimeHold); // 已经减了8个时区
  return t;
}

// main方法
int main()
{
  cout << "当前时间:" << str2Time("2022-08-05 17:04:45") << endl;
}
```

### 输入输出
字节流是从设备（如键盘、磁盘驱动器、网络连接等）流向内存，这叫做输入操作。字节流是从内存流向设备（如显示屏、打印机、磁盘驱动器、网络连接等），这叫做输出操作。
#### 控制台输入
```
char name[50];
cout << "请输入您的名字" <<endl;
cin >> name;
cout << "您的名字:" << name << endl;
```

#### 控制台输出
```
char name[] = "weihh"
cout << "您的名字:" << name << endl;
```

### 面向对象编程
#### 类
C++类的概念同java，区别就是一般C++对公开的类都会写头文件。
```
class Box
{
  public:
    double length;   // 盒子的长度
    double breadth;  // 盒子的宽度
    double height;   // 盒子的高度
    // 成员函数声明
    double get(void);
    void set( double len, double bre, double hei );
};
// 成员函数定义
double Box::get(void)
{
  return length * breadth * height;
}
 
void Box::set( double len, double bre, double hei)
{
  length = len;
  breadth = bre;
  height = hei;
}
```
#### 继承
C++可以继承多个类，这一点和java的单继承不同。
```
/ 派生类
class Rectangle: public Shape, public PaintCost
{
  public:
    int getArea()
    { 
      return (width * height); 
    }
};
```
### 重载
C++ 允许在同一作用域中的某个函数和运算符指定多个定义，分别称为函数重载和运算符重载。
#### 函数重载
```
class printData
{
  public:
    // 函数重载需要它们的参数列表和定义（实现）不相同
    void print(int i) {
      cout << "整数为: " << i << endl;
    }

    void print(double  f) {
      cout << "浮点数为: " << f << endl;
    }

    void print(char c[]) {
      cout << "字符串为: " << c << endl;
    }
};
```

#### 运算符重载
```
// 重载 + 运算符，用于把两个 Box 对象相加
class Box
{
  public:
  ... // 其他代码
  Box operator+ (const Box& b)
  {
    Box box;
    box.length = this->length + b.length;
    box.breadth = this->breadth + b.breadth;
    box.height = this->height + b.height;
    return box;
  }
}
```

## C++实践
[计算器头文件](Calculator.h)
[计算器主代码](Calculator.cpp)

## 一些注意事项
### endl 和 /n的区别
endl会刷新缓存，/n不会。

## 链接
1. [runboom C++学习](https://www.runoob.com/cplusplus/cpp-pointers.html)