# cpp从入门到放弃
## 名字空间
> * 避免名字冲突  
* 区分逻辑，每个功能一块命名空间

```
namespace{
  名字空间成员
}

namespace ns{
  int var=0;
  void fun(void){};
}
```
### 名字空间合并
```
namespace 名字空间名{
  名字空间成员1;
  名字空间成员2：
}
namespace 名字空间名{
  名字空间成员3;
}
```
```
#include<iostream>
namespace ns{
  int var=0;
  void fun(void){
    std::cout<<"ns的fun函数"<<std::endl;
  }
}
namespace ns{
  struct type{
    int x;
    char y[256];
  }
}
```
###使用名字空间中的名字

1,通过作用域限定操作符“：：”  
2,名字空间指令  
using namespace ns;
3,名字空间声明-针对某一个成员  
using 名字空间名：：成员名  using ns：：

### 名字空间 全局变量
小作用域隐藏大作用域  
名字空间相对于全局域是小作用域
```
int y=8888;
namespace nv{
  int x=5678;
  int y=9999;
  void foo(void){
    cout<<y<<endl;
    cout<<::y<<endl
  }
}
```
以上打印出是的是9999和 8888

### 无名名字空间
将无家可归的名称放到无名名字空间中。  
对于无名空间中的名字，直接通过 ：：访问

### 名字空间是可以嵌套的
```
namespace ns1{
  int a=1;
  namespace ns2{
    int a=2;
  }
}
```
## 结构体 联合 枚举
### 声明的时候可以省略关键词
结构体和联合的区别，就是内存布局不一样，联合是共用
```
struct student{
  ....
};
struct student s1={...}  //c
student s1={...} //C++
```
### c++的结构体中可以定义函数，函数可以直接访问结构体的成员
```
#include <iostream>
using namespace std;
struct Student {
  char name[128];
  int age;
  int score;
  void who(void) {
    cout << "i am " << name << "i am " << age << "yeas old" << endl;
  }
};
int main(void) {
  Student s = {"zhangfei", 11, 99}, *p = &s; // p指针指向s的地址
  cout << s.name << s.age << s.score << endl;
  cout << p->name << p->age << p->score << endl;
  s.who();
  return 0;
}

```
### c++中定义匿名联合
```
int main(void){
  union{
    int n;
    char c[4];//未初始化的局部变量，不知道里面是什么  
  };
  n=0x12345678
  for (int i=0;i<4;++i)
    print("%#x ",c[i]);
  cout<<endl;
  return 0;
}
```
以上输出：0x78 0x56 0x34 0x12
n 和c[4]共用了一块内存，所以对n的赋值，相当于对c[4]初始化

### c++中的枚举不能和整形
```
#include <cstdio>
#include <iostream>
using namespace std;

enum Color { RED, GREEN, BLUE };

int main(void) {

  cout << RED << GREEN << BLUE << endl;
  //Color c=0;  error 尝试将整形赋值给枚举型，不可以
  Color c=RED；
  return 0;
}
```
输出是012，c++是强类型  
c++把枚举看作是有范围的整数集合。枚举限定了值域

### 字符串 string
```
#include <iostream>
#include<string>
using namespace std;
int main(void){
  string s1="hello";
  s1+=" ";
  string s2="world";
  cout<<s1+s2<<endl;
}
```
### 布尔类型  bool
```
#include <iostream>
#include <string>
using namespace std;
int main(void) {
  bool b = true;
  cout << boolalpha << b << endl; // boolalpha 以bool字符的形式输出
  b = !b;
  cout << boolalpha << b << endl;
  cout << sizeof(b) << endl;
  return 0;
}

```
bool是一个字节


## 重载 overload
>* 同一个作用域中，<b>函数名相同，参数表不同的函数，构成重载关系<b>  
* 调用函数时，根据实参与形参的类型匹配情况，选择一个确定的版本，这个过程成为重载解析   
* 通过函数指针指向具有重载关系的函数，所选择的重载版本由该函数的指针的类型决定
* c++换名：c++编译器会按照一定的规则将函数的参数表信息与函数的原始名混合编码，形成一个新的函数名，因此具有重载关系的函数，虽然其原始名一样，但是因为其参数表的不同，最后编译生成的实际函数名是不同的
* 通过extern“c” 可以要求c++编译器按照c的方式处理函数接口，即不换名字，当然也就重载
```
extern“c”{
int add (int a,int b);
int sub(int a,int b);
}
```
如果包含extern “c”声明指定复的文件也需要被c编译器处理，为了防止c编译器无法识别extern“c”而导致报错误
```
#ifdef _cplusplus
extern"c"{
#endif
....
#ifdef_cplisplius
}
#endif
```
只有在c++编译器中 _cplusplus宏才有定义，在c编译器中无意义。

code：
```
#include <iostream>
using namespace std;

void foo(void) { cout << "1" << endl; }

void foo(int n) { cout << "2" << endl; }

void foo(int *n) { cout << "3" << endl; }
void foo(int n, double d) { cout << "4" << endl; }
void foo(double n, int d) { cout << "5" << endl; }
// int foo(void){} 函数的重载与返回值没关系
int main(void) {
  foo();    // 1
  foo(100); // 2
  int *p;
  int n;
  foo(p);  // 3
  foo(&n); // 3 地址就是指针，指针就是地址
  return 0;
}
```
```
#include <iostream>
using namespace std;

void funa(char *p) { cout << 1 << endl; }
void funa(char const *p) { //常量指针，指向一个常量的指针。目标不可修改，指针可修改
                     //const只要是在*号前边就
  //不会修饰*号
  cout << 2 << endl;
}
void funa(char const* p,char c){
  cout<<3<<endl;
}
void funa(char*p,int n){
  cout<<2<<endl;
}
int main(void) {
  char *p; //
  funa(p); //输出1，1是完美匹配，完美匹配优于常量转换
  char c;
  funa(p,c)//输出3,常量转换优于升级转换
  return 0;
}

```
```
#include <iostream>
using namespace std;
double foo(int n) { cout << 1 << endl; }
char *foo(float f) { cout << 2 << endl; }
int main(void) {
  double (*p1)(int) =
      foo; //定义函数指针，在定义的时候，函数重载已经开始了，带有整形参数，1
  char *(*p2)(float) = foo;          //定义函数指针 2
  cout<<(void*)p1<<（void*)p2<<endl; //打印指针地址
  p1(3.14f);  // 1 在初始化的时候就已经重载了
  p2(10);     // 2
  foo(3.13f); // 2
  foo(10);    // 1
}
```
## C++调用C
```
// add.h文件
#ifndef _ADD_H
#define _ADD_H
int add(int, int);
#endif
//add.c文件
#include <add.h>
int add(int a, int b) { return a + b; }

// main.cpp文件
#include <iostream>
using namespace std;

//用c的接口生成 ####重要  
extern "C"{
#include "add.h”
}

int main(void){
  cout<<add(100,200)<<endl;
  return 0;
}

```
* 纯c编译：gcc -c add.c  生成add.O
* cpp编译：g++ -c main.cpp  生成main.O
* 联合：g++ add.o main.o
* 生成a.out
## C调用C++
同理

## 缺省参数
* 可以为函数的参数指定缺省值，调用函数时若为指定实参，则取与该实参相对应的形参缺省默认值
```
#include <iostream>
using namespace std;
void foo(int a, int b = 1000) { cout << a << b << endl; }
int main(void) {
  foo(123, 356);
  foo(123);
  return 0
}
```
* 函数的缺省值参数实在编译阶段解决的，因此只能用常量，常量表达式或者全局变量等非局部画数值作为缺省参数。
* 参数的匹配性检查不看定义，只看声明
* 如果函数的某一个参数带有缺省值，那么该参数后面的所有参数必须带有缺省值
* 不要因为使用缺省参数而导致重载歧义

## 哑元
* 只指定类型而不指定名称的函数参数，成为哑元。
* 哑元主要应用于版本升级过程中的向下兼容和特殊的操作符重载等场合。
 ```
#include <iostream>
using namespace std;
int add(int a,int ,int c)
{
  return a+c;
}
int main(void){
  cout<<add(100,200,300)<<endl;
  return 0;
}
```

##内联 inline
* 避免函数频繁调用过程中的时间

