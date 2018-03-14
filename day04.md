### cpp的对象模型
#### 1.CPP的对象模型和c的结构模型没有任何区别,包括成员的布局,以及对齐补齐的规则
#### 2.对象中只有成员变量,没有成员函数,类的成员变量,在该类的每个对象中都有一份独立的拷贝.但是类的成员函数只有一份,且为该类的所有对象共享.
#### 3.为了在一个函数中区分出不同的调用对象,编译器会为每个成员函数提供一个隐藏的指针参数--this指针--指向调用改成员函数的对象,在成员函数中对所有成员变量的访问,以及怼其它成员函数的调用,实际上是通过this指针完成的.
#### 4.类的构造函数中同样具有this指针参数,指向这个正在被构造的对象.
#### 5.显示的使用this指针的场合
  *1)解决成员变量的名字冲突
  *2)从成员函数中返回调用对象的自引用
  *3)通过成员函数实现对象间的交互
  *4)在成员函数,函数销毁调用对象自身.


```
class A{
  B m_b;
};
class B{
  A m_a;
}

编译器编译的话,要首先知道 sizeof(A),用来分配内存,但是分配不了,所以不正确

class A{
  B* m_b;
};
class B{
  A* m_a;
}

以上方法就对了,改成指针,占四个字节,可以申请内存了,这样就正确了
```

code demo

```
#include<iostream>
using namespace std;
class A
{
public:
    A(int* data,int numb) {
        for (int i=0;i<numb;++i)
        {
            this->data[i]=data[i];
        }
        this->numb=numb; //使用this指针,避免参数和类的成员出现重名的问题
    }
    int data[1024];
    int numb;
};
class B
{
public:
    B(void) :m_counter(0){}
    B& inc(void){
        ++m_counter;
        return * this;   //返回自引用,返回类型是B的引用 B&  返回自指针
    }
    int m_counter;
};


int main(void){
    int data[5]={10,20,30,40,50};
    A a(data,5);
    B b;
    b.inc().inc().inc();//实现级调用,一级一级的调用
    cout<<b.m_counter<<endl;
    return 0;
}

```
code demo

```
#include<iostream>
using namespace std;
class Student; //类的短声明
class Teacher
{
public:
    void educate(Student* student);
    void reply(char const* answer){
        m_answer=answer;
    }
private:
    string m_answer;
};

class Student
{
public:
     void ask(char const* question,Teacher* teacher){
        cout<<"问题:"<<question<<endl;
        teacher->reply("我不知道");
    }
};
//把这个函数放到student后面去
void Teacher::educate(Student* student){
    student->ask("什么是this指针",this);
    cout<<"回答"<<m_answer<<endl;
}

int main(void){
    Teacher t;
    Student s;
    t.educate(&s);
    return 0;
}


```
以上有两点:1.一个类的短声明, 2,一个将成员函数放到外边,另一个类之后
