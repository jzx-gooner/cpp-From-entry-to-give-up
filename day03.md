# 显式(强制)类型转换
## 1.C风格的类型转换
```
(目标类型)源类型变量
int i=1234;
char c=(char)i;
```  

## 2,cpp风格的类型转换

### 1.静态类型转换  static_cast
****static_cast<目标类型>(源类型变量)****  
编译器会对源类型和目标类型做相容性检查,检查不通过则报错.
#### A.在源和目标之间存在一个方向可以做隐式类型转换,那么这两个类型就是相容类型.

```
short x=10;
void *v=&x;
short* p=static_cast<short*>(v);
----------------------------
short x;
short* v=&x;
int *p =static_cast<int*>(v);//会报错,指向目标的字节长度不一样
```

#### B.若从源类型到目标类型存在自定义的转换规则(类型转换构造函数/类型转换运算符函数),那么它们也可以被称作相容类型.--操作符重载

### 2.动态类型转换
dynamic_cast<目标类型>(源类型变量)

用于具有多态性的父子类型的指针或者引用

### 3.去常类型转换
const_cast<目标类型>(源类型变量)

去除指针或者引用的常属性.

```
int x=10;
int const* p=&x;
int *p =const_cast<int*>(cp); // 去除掉const属性

```
示例:

```
#include <iostream>
using namespace std;
int main(void) {
  int const x = 100;
  int &r = const_cast<int &>(x); // 强制转换
  return 0;
}
```

```
#include <iostream>
using namespace std;
int main(void) {
  int volatile const x =
      100; // volatile关键词的使用避免了const使用的时候编译器提前进行的替换操作
  int &r = const_cast<int &>(x); // 强制转换
  r = 200;
  cout << r << endl;
}

```

### 4.重解释类型转换
reinterpret_cast<目标类型>(源类型变量)

转换任意类型的指针或引用//转换任意类型的指针和整形之间的转换

```
#include <iostream>
using namespace std;
int main(void) {
  char c[] = {0x78, 0x56, 0x34, 0x12};
  //把char型的数组重解释为整数
  int *n = reinterpret_cast<int *>(c);
  cout << hex << showbase << *n << endl; // 0x12345678
  double x=3.14;
  double* p=&x;
  int h=reinterpret_cast<int>(p);//把指针当作整数来看
  char* c=reinterpret_cast<char*>(h);//把整形转换成指针
  return 0;
}

```

# 面向对象
## 1.为什么要面向对象
### (1)相比于分而治之的结构化设计,强调大处着眼的面向对象程序设计思想,更适合于开发大型软件
### (2)得益于数据抽象,代码复用等面向对象的固有技术,软件开发的效率获得了极大的提升,成本却大幅降低
### (3)面向对象技术在数据库,网络通信,图像界面等领域的广泛应用,已催生出各种设计模式和应用框架.

## 2.什么是面向对象
### (1)万物皆对象
### (2)把大型软件堪称一个由对象组成的社会
### (3)对象拥有足够的智能,能够理解来自其它对象的信息,并以适当的方式作出反应
### (4)对象能够从高层对象继承某些特性,并允许低层对象从自己继承某些特性
### (5)编写程序的过程就是一个描述对象的过程,最终是问题域和解域获得完美的统一.
### (6)面向对象的三大要素(四大) :封装,继承,多态,抽象

# 类和对象
## 1.通过属性和行为描述具体的对象,其中属性表示对象的静态特征,而行为则表示对象的动态特性
## 2.拥有相同属性和行为的对象被分为一组,即一个类
## 3.类即逻辑抽象
### a.简单类型:只能表示一个属性(变量)
### b.数组类型:可以表示多个属性(元素),但是类型必须相同
### c.结构体类型:可以多个类型不同的属性,但却少对行为的描述.
### d.类类型:既可以表示多个不同类型的属性(成员变量),同时也可以表示多个不同的行为(成员函数).

# 1 类的定义与实例化
## 类的主要内容
### 1.类的主要内容
#### (1)成员变量:描述对象的各种属性
#### (2)成员函数:描述对象的各种行为
#### (3)构造函数:对对象做初始化
#### (4)析构函数:对对象做终结化
#### (5)访控属性:决定成员的访问特性
* public:公有属性 (struct缺省就是公有属性)
* private:私有属性,只有自己可以访问.(class缺省就是私有属性.)
* protected:保护的,只有自己和自己的子类可以访问

#### (6)继承方式与基类:继承

```
class Student{
  //吃饭
  void eat(string const& food){....}
  //睡觉
  void sleep(int time){....}
  //学习
  void learn(string const& course){...}
  string m_name; //姓名
  int m_age;//年龄
  int m_no;//学号

};

```
Code Demo:
```
#include <iostream>
using namespace std;
class Student {
public:
  void eat(string const food) {
    cout << "我叫" << m_name << ",正在吃" << food << endl;
  }
  void sleep(int time) {
    cout << "我今年" << m_age << "岁,睡了" << time << "小时" << endl;
  }
  void learn(string const &course) {
    cout << "我的学好是" << m_no << ",现在学" << course << endl;
  }
  string m_name;
  int m_age;
  int m_no;
};
int main(void) {
  Student s1; // 对象的实例化
  s1.m_name = "张飞";
  s1.m_age = 25;
  s1.m_no = 1001;
  s1.eat("面条");
  s1.sleep(1);
  s1.learn("c++");
  return 0;
}

```

改进demo

```
#include <iostream>
using namespace std;
class Student {
public:
  void eat(string const food) {
    cout << "我叫" << m_name << ",正在吃" << food << endl;
  }
  void sleep(int time) {
    cout << "我今年" << m_age << "岁,睡了" << time << "小时" << endl;
  }
  void learn(string const &course) {
    cout << "我的学好是" << m_no << ",现在学" << course << endl;
  }
  void setname(string const &name) {
    if (name == "二")
      cout << "拒绝接受此名字" << endl;
    else
      m_name = name;
  }
  void setage(int age) {
    if (age < 0)
      cout << "拒绝接受该年龄" << endl;
    else
      m_age = age;
  }
  void setno(int no) {
    if (no < 0 || no > 1000)
      cout << "学号不存在" << endl;
    else
      m_no = no;
  }

private:
  string m_name;
  int m_age;
  int m_no;
};
int main(void) {
  Student s1; // 对象的实例化
  s1.setname(张飞);
  s1.setage(78);
  return 0;
}


```
未初始化的,会胡乱来了,所以引出构造函数,对类进行初始化
## 2 构造函数
### (1)函数名与类名相同,且没有返回类型;
### (2)构造函数在创建对象时被系统自动调用;
        * A 直接定义变量,栈对象
        * B 用new操作符创建对象,堆对象
### (3)构造函数在对象整个生命期内,一定会被调用,切仅被调用一次.
### (4)构造函数负责对成员变量进行初始化,分配必要的资源,设置对象的初始状态.

```
#include <iostream>
using namespace std;
class Student {
public:
  //构造函数 用来初始化,每创建一个对象,调用一下构造函数
  Student(string const &name, int age, int no) {
    cout << "我是构造函数" << endl;
    m_name = name;
    m_age = age;
    m_no = no;
  }

  void eat(string const food) {
    cout << "我叫" << m_name << ",正在吃" << food << endl;
  }
  void sleep(int time) {
    cout << "我今年" << m_age << "岁,睡了" << time << "小时" << endl;
  }
  void learn(string const &course) {
    cout << "我的学好是" << m_no << ",现在学" << course << endl;
  }
  void setname(string const &name) {
    if (name == "二")
      cout << "拒绝接受此名字" << endl;
    else
      m_name = name;
  }
  void setage(int age) {
    if (age < 0)
      cout << "拒绝接受该年龄" << endl;
    else
      m_age = age;
  }
  void setno(int no) {
    if (no < 0 || no > 1000)
      cout << "学号不存在" << endl;
    else
      m_no = no;
  }

private:
  string m_name;
  int m_age;
  int m_no;
};

int main(void) {
  Student s1("张飞", 25,1001); //创建对象,同时传入构造参数!!!!!  直接定义变量,栈对象
  s1.setname("王二");
  s1.setage(78);
  Student *s2 = new Student("赵云", 22, 1002); //用new操作符创建对象,堆对象
  delete s2;
  return 0;
}

```
以上的实现方式是隐式内联的方式,如果类特别big的话,可以分开写:
* student.cpp   //实现学生类,在里面声明函数,加上作用域的限制

```
#include<iostream>
#include"stdent.h"
Student::Student(string const&name,int age,int no){balabala}
void Student::eat(string const& food){''''}
```

* student.h   //一个标准的类的声明,只函数声明

```
#ifndef _STUDENT_H
#define _STUDENT_H
//声明学生类
class Student{
  //函数声明
  void eat(string const& food);
};
#endif  
```
* main.cpp  

```
#include "student.h"
int main(void){
  "balabala 主函数"
}

```
编译的话,直接编译两个  g++ main.cpp student.cpp 
