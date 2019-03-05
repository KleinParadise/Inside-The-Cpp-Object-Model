## Program Transformation Semantics 程序转化语意学

### Explicit Initialization 显式的初始化操作
```cpp
class X{    
};

X x0;

void foo_bar(){
    X x1(x0);
    X x2 = x0;
    X x3 = X(x0);
}
```
函数foo_bar()中将有两个阶段的程序转化:  
1. x1,x2,x3定义被重写,其初始化操作被剥除。
2. class X的拷贝构造函数调用操作会被安插进去。
以下是上述两阶段过程的C++伪码
```cpp
void foo_bar(){
    X x1;
    X x2;
    X x3;
    //编译器安插拷贝构造函数的调用操作 X::X(const X& xx)
    x1.X::X(x0);
    x2.X::X(x0);
    x3.X::X(x0);
}
```

### Argument Initialization 参数的初始化
```cpp
void foo(X x0)
//调用foo
X xx;
foo(xx);
```
将xx传给函数foo,编译器有一种策略就是产生临时object,然后调用class X的拷贝构造函数将xx作为临时object的初值。此时函数foo的声明也会被转化为void foo(X& x0)
来避免将临时object位逐次拷贝给x0。在函数完成之后,调用class X的析构函数对临时object进行析构。


### Return Value Initialization 返回值的初始化
```cpp
X bar(){
    X xx;
    //处理xx
    return xx;
}
```
函数bar的返回值如何从局部变量xx中复制出来?  
1. 首先给函数bar加一个参数,类型是class X object的引用,该引用将作为该函数的返回值。
2. 在函数bar的return之前,调用class X的拷贝构造函数,将xx作为第一步新增参数引用的初值。
以下是上述过程的伪码:
```cpp
void bar(X& _result){   //加上了额外的参数，改变了返回值类型
    X xx;
    xx.X::X();//初始化xx 调用xx的构造函数
    //处理xx
    _result.X::X(xx); //使用copy constructor
    return;
}
```
上述转化操作调用了拷贝构造函数,一定程度上降低了效率。使用_result代替xx，能避免构造函数的调用，从而提高效率。这种优化技术称为iNamed Return Valie(NVR)
bar函数优化为:
```cpp
void bar(X& _result){   //加上了额外的参数，改变了返回值类型
    //调用_result的构造函数
    _result.X::X();
    //直接处理_result
    return;
}
```



