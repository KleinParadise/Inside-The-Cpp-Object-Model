### Default Consturctor 默认构造函数
在以下四种情况下默认构造函数会被编译器合成出来:
#### 1.带有"Default Consturctor"的Member Class Object
```cpp
class Foo{
public:
    Foo(){};
};

class Bar{
public:
    Foo foo;
    char* str;
};
Bar bar //Bar::foo在此处初始化
```
Class Bar没有构造函数,在使用Class Bar类时编译器为其合成一个默认构造函数调用class Foo默认构造函数来初始化成员变量foo。Bar::foo初始化是编译器的责任,Bar::str初始化是程序员的责任。

#### 2.带有"Default Consturctor"的Base Class
一个没有任何构造函数的class派生于一个带有default Consturctor的Base Class。那么该derived class的构造函数需要被合成出来。它将调用它上一层Base Class的默认构造函数。如果derived class的设计者提供了多个构造函数,但其中都没默认构造函数,此时编译器会扩张提供的每个构造函数,将其必要调用的Base Class的默认构造函数加进去。

#### 3.带有"Virtual Function"的Class
分为两种情况,需为其合成默认构造函数
##### a.class 声明或继承一个virtual function
##### b.class 派生自一个继承串链,其中有一个或多个 virtual base classes
```cpp
class Widget{
public:
    virtual void flip() = 0;
};
void flip(const Widget& widget){
    widget.flip();
}
Bell,Whistle都继承Widget
void foo(){
    Bell b;
    Whistle w;
    
    flip(b);
    flip(w);
}
```
以上有两个扩张在编译期间发生:
1. 一个 virtual function table(vtbl）会被编译器合成出来,内放class的virtual function地址和class的类型
2. 在每个class object中一个额外的pointer member(vptr)会被编译器合成出来,内含class vtbl的地址。  

为了该机制发挥功效,正确的为object的vptr设定初值,放置适当的virtual table。对于class所定义的每一个构造函数,编译器都会安插一些代码来处理vptr和vtbl。当class没有任何构造函数,编译器就会合成默认构造函数,来来处理vptr和vtbl。


#### 4.带有"Virtual Base Class"的Class
virtual base class的实现在各个编译器之间有差异。但是其实现的共同点必须使virtual base class在其每一个derived class object的位置在执行期都准备妥当。因此对于class所定义的每一个构造函数,编译器都会安插容许每一个virtual base class的执行期存取操作的代码。如果该class没有声明任何构造函数,编译器会为它合成一个默认构造函数,来执行上述安插操作。

