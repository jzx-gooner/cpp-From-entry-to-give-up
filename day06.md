### 静态成员
#### 1.属于类而非对象
* 静态成员变量不包含与对象实例中,具有进程级的声明周期
* 静态成员函数没有this指针,也没有常属性
* 静态的成员函数只能访问静态成员(变量或者函数),非静态成员函数既可以访问静态成员,也可以访问非静态成员

#### 2.静态成员也受访控属性的约束
#### 3.静态成员变量必须在类的外部定义或初始化,静态成员函数既可以在类的外部也可以在类的内部定义

```
#include<iostream>
using namespace std;
//static int g=100; //静态全局变量
void foo(void){    //static void foo()  //静态全局函数,仅能在这里使用
    static int i=10;//静态局部变量
    int j=10;
    cout<<++i<<" "<<++j<<endl;
}

class A
{
public:
    static int m_semb; //静态成员变量 ,这是声明
    int m_memb;
    static void foo(void){//静态成员函数
        cout<<m_semb<<endl;
        //cout<<m_memb<<endl;//错误,静态成员函数没有this指针,所以访问不了
        //cout<<this<<endl;//错误,静态成员函数不能使用this
    }
};
int A::m_semb=200;//定义静态成员变量,要在类的外部定义
int main(void){
    foo();//11,11
    foo();//12,11
    foo();//13,11
    A a1,a2;
    cout<<&a1.m_semb<<a1.m_memb<<endl;//
    cout<<&a2.m_semb<<a2.m_memb<<endl;//a1 a2的静态成员变量地址是一个的,静态成员是类的一部分,与对象无关
}


```

```
#include<iostream>
using namespace std;
class Account
{

public:
    Account(double balance):m_balance(balance) {}
    double settle(void){
        m_balance*=(1+m_inter);
        return m_balance;
    }
    static void setInter(double inter){ //修改利率
        m_inter=inter;
    }
private:
    double m_balance;
   static double m_inter;//利率 是静态的 每个账户对象共用一个 利率可以修改 类的所有对象都可以共享这个属性,牵一发动全身
};
double Account::m_inter=0.01; //静态成员变量定义!!!
int main(void){
    Account a1(1000),a2(10000);
    cout<<a1.settle()<<" "<<a2.settle()<<endl;
    Account::setInter(0.05)//调息
    return 0;
}


```
#### 4.构造函数私有化,通过私有让只能生成一个对象,通过静态保持其唯一性 ===就是单例模式

```
class A{
public:
  void work(void){
    cout<<"功能""<<endl;}
  static A& getInstance(void){
    return m_a;
  }
private:
  A(void){};
  A(const A&){};
  static A m_a;
};

int main(void){
  A& a=A::getInstance;
  a.work();
  return 0;
}
```

### 成员指针
通过类型的约束,表达指向类特定类型成员的指针.

### 操作符(运算符)重载
