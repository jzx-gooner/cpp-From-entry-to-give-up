# 引用-- &
* 1.引用即别名 :声明一个标识符为引用,即表示该标识符可作为另一个有名或者无名对象的别名
* 2.常引用的目标不可修改,只能读取 int const& d=c; 不能修改
* 3.c++中的无名对象(字面值常量,临时变量)都被视为右值,只能通过常引用来引用.int const&a=10; //纯右值  int const&a=x+y;
* 4.引用必须初始化!!!!!
* 5.无法定义一个什么都不引用的引用
* 6.引用已经初始化便不能再引用其它对象
## 引用型参数

1.函数的形参是实参的别名.可以将函数的形参声明为引用形式,该形参在参数传递过程中由对应的实参初始化,并成为该实参的别名

2.在函数中修改实参的值
除了用返回值,指针的方法,又多了另一种方法,引用
通过引用型参数,可以在函数体内部修改调用者实参的值,成为除了返回值和指针参数以外,第三种由函数内部向函数外部输出数据的途径.

3.使用引用,避免了复制对象的开销,使用常引用.  
通过引用传递参数,形参只是实参的别名而非其副本,这就避免了从实参到形参的对象复制,这对于具有复杂数据结构的参数而言可以提高其传参性能.
如果函数只是读取引用型参数的目标对象,那么最好通过常引用接收实参,防止在函数中意外的修改变量的值
```
#include <cmath>
#include <iostream>
using namespace std;
void foo(int a) {
  cout << "foo:" << &a << endl; //地址a
  ++a;
  cout << a << endl; // 124
}

//函数,通过指针返回周长,通过引用返回面积,通过返回值,返回对角线长度
double rect(double w, double h, double *c, double &s) {
  *c = (w + h) * 2;
  s = w * h;
  return sqrt(w * w + h * h);
}

struct Student{
  char name[64];
  char addr[256];
  char mail[128];
};
//使用引用,省去了复制对象的开销
void insert(Student const &student){
  cout<<student.name<<endl;
  cout<<student.addr<<endl;
  cout<<student.mail<<endl;

}

void bar(int &a) {
  cout << "bar:" << &a << endl;
  ++a;
  cout << "a=" << a << endl;
}

int main(void) {
  int x = 123;
  cout << "main:" << &x << endl; //地址b
  foo(x);                        //这个的输入是x的副本,是拷贝
  cout << x << endl;             // 123
  bar(x);                        //是别名,就是x
  cout << x << endl;             // 124
  double w = 3, h = 4, c, s, t;
  t = rect(w, h, &c, s);
  cout << "周长:" << c << endl;
  cout << "面积" << s << endl;
  cout << "对角线" << t << endl;
  Student student={"zhangfei","jinan","jia6120@163.com"};
  insert(student);
  return 0;
}

```
## 引用型的返回值
1.返回左值  
2.函数的局部变量只具有函数级设置块或语句级的声明周期,函数一旦返回,所有的局部变量即刻销毁,即使通过返回值获得了对它们的引用,其目标也将是未定义的.因此不要从函数中返回对局部变量的引用,而返回全局,静态,成员,堆变量的引用是安全的.

```
#include <iostream>
using namespace std;
struct A {
  int data;
  int &foo(void) { return data; }
};
int main(void) {
  A a = {100};
  cout << a.data << endl; // 100
  a.foo() = 200;
  cout << a.data << endl; // 200
  return 0;
}

```

3.虽然引用是通过指针实现的,但是在高级语言层面,引用和指针还是具有若干不同的特性.
* (1)指针可以不初始化,其目标可以在初始化后随意变更;但是引用必须初始化,一旦初始化就无法变更其目标
* (2)可以定义空指针,即什么也不指向的指针.但是不能定义空引用,引用必须有所引用,要不干嘛去
* (3)可以定义二级指针,指向指针的指针,但是不能定义引用引用的引用.
```
int x=10;
int* p=&x;// 一级指针
int**p=&p;//二级指针
```

* (4)可以对指针引用
int x=10;
int* p=&x;
int*&q=p;//q是p的别名
* (5)指向引用的指针,不可能!
* (6)有指针数组,没有引用数组!!!,可以给数组起个别名
```
int a=10,b=20,c=30;
int* p[3]={&a,&b,&c};
int(&r)[3]=a;
```
* (7)函数引用,代表函数的别名

```
int func(double);//函数
int(*pfunc)(double)=&func;//函数指针  取地址
inf(&rfunc)(double)=func;//函数引用
//函数调用

pfunc(3.14) ======(*pfunc)(3.14);
rfunc(3.14);

```

```
#include <iostream>
using namespace std;
int add(int x, int y) { return x + y; }
int main(void) {
  //古典写法
  int (*pfunc)(int, int) = &add;
  cout << (*pfunc)(100, 200) << endl;
  //现代写法
  int (*pfunc2)(int, int) = add;
  cout << pfunc2(100, 200) << endl;
  //函数引用
  int (&rfunc)(int, int) = add;
  cout << rfunc(100, 200) << endl;
  return 0;
}
```
交换两个变量的值

```
#include <iostream>
using namespace std;
//错误的
void swap1(int x, int y) {
  int z = x;
  x = y;
  y = z;
}
//指针
void swap2(int *x, int *y) {
  int z = *x;
  *x = *y;
  *y = z;
}
//引用
void swap3(int &x, int &y) {
  int z = x;
  x = y;
  y = z;
}
//指针字符串交换
void swapp(char const **x, char const **y) {
  char const *z = *x;
  *x = *y;
  *y = z;
}
//引用字符串交换
void swapr(char const *&x, char const *&y) {
  char const *z = x;
  x = y;
  y = z;
}

int main(void) {
  int x = 100, y = 200;
  swap1(x, y);
  cout << x << " " << y << endl;
  swap2(&x, &y);
  cout << x << " " << y << endl;
  swap3(x, y);
  cout << x << " " << y << endl;
  char const *p = "hello";
  char const *q = "world";
  swapp(&p, &q);
  cout << p << " " << q << endl;
  swapr(p, q);
  cout << p << " " << q << endl;
  return 0;
}


```  
