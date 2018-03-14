### 常函数
#### 1.在类成员函数的参数表之后,函数体的左花括号之前,加上const关键字,该成员函数的this指针具有常属性,这样的成员函数被成为常函数.
#### 2.在常函数内部,因为this指针被声明为常量指针(即目标只读的指针),所以无法修改成员变量的值,除非该成员变量被mutable关键字修饰.
code demo

```
#include<iostream>
using namespace std;
class Student
{
public:
    Student(string const&name="",int age=0,){
        m_name=name;
        m_age=age;
        m_times=0;
    }
    string m_name;
    int m_age;
    mutable int m_times;//方法2,加上关键字mutable 去除常属性
    //成员函数,这个防止修改的话,在参数表后边左花括号前面加const,表示这个指针,this指针是一个常指针,
    void print(void) const {
        cout<<m_name<<":"<<m_age<<endl;
        m_name="修改了";
        ++const_cast<Student*>(this)->m_times;//方法1:用去常指针去掉const属性

    }
    //void print(Student const* this) //以上相当于这样.就是常函数
};

//外部函数调用,防止误改变,则应该加const,对象的常引用,只读.
void print(Student const &student){
    cout<<student.m_name<<":"<<student.m_age<<endl;
}

int main(void){
    Student a("张飞",12);
    print(a);
    a.print();
}


```
#### 3.常对象只能调用常函数,非常对象既可以调用非常函数也可以调用常函数,原型相同的常函数和非常函数可以构成重载关系.

```
#include<iostream>
using namespace std;
class A{
   public:
    void foo(void){}
    //void foo(A* this){};
    void bar(void)const{}
    //void bar(A const* this){};
    //hum函数-常函数和非常函数构成重载
    void hum(void){
        cout<<"hum函数的非常版本"<<endl;
    }
    void hum(void) const{
        cout<<"hum函数的常版本"<<endl;
    }
};

int main(void){
    A a;//非常对象
    a.foo();// foo(&a);//非常对象调用非常函数
    a.bar();//bar(&a); 访问权限收窄,非常对象a既调用常函数
    A const& r=a;//r是常对象
    //r.foo();//error,常对象,不能调用非常函数
    r.bar();
    A const* p=&a;//常对象,指针同样适用.
    return 0;
}

```
### 析构函数

#### 1.析构函数是类的特殊的成员函数
* (1)析构函数的函数名就是在类名前面加"~"
* (2)析构函数没有返回类型;
* (3)析构函数没有参数
* (4)析构函数不能重载
#### 2.析构函数在对象被销毁的时候自动调用
* (1)局部对象的析构函数由其所在的最小作用域的右花括号调用;
* (2)堆对象通过delte运算符调用
* (3)全局对象的析构函数,被进程加载其调用

#### 3.缺省析构函数
如果一个类没有定义析构函数,编译器提供一个缺省析构函数,该函数对于基本类型的成员变量什么也不做,而对于类类型的成员变量,则会调用其相应类型的析构函数.


```
#include<iostream>
using namespace std;
class Array{
public:
    Array(size_t size):m_array(new int[size]),m_size(size){
        cout<<"构造函数"<<this<<endl;
    }
    ~Array(){
        delete[] m_array;
        cout<<"析构函数"<<this<<endl;

    }
    int& at(size_t i){
        if (i>=m_size)
            throw string("下标溢出");
        return m_array[i];
    }
    int const& at(size_t i)const{
        return const_cast<Array*>(this)->at(i);
    }
private:
    int* m_array;
    size_t m_size;
};

int main(void){
    Array a(5);
    for(size_t i=0;i<5;++i){
        a.at(i)=i+1;
    }
    for (size_t i=0;i<5;++i){
        cout<<a.at(i)<<endl;
    }
    return 0;
}

```
再解读

```
Array* a=new Array(10);
//a=malloc(sizeof(Array));
//Array::Array(a);
delete a;
//Array::~Array(a);
//free(a);

```
### 拷贝构造和拷贝赋值
#### 1.缺省方式的拷贝构造和拷贝赋值,对包括指针在内的基本类型成员变量按字节复制,导致浅拷贝问题.
#### 2.为了获得完整意义上的对象副本,必须自己定义拷贝构造函数和拷贝赋值运算符函数.针对指针型的成员变量做深度拷贝
#### 3.拷贝赋值运算符函数的实现
* 避免自赋值
* 分配新资源
* 释放旧资源
* 拷贝新内容
* 返回自引用
