### Class Data Members 类的成员变量
1. 一个类的data members用来表现这个class在程序执行时的某种状态。nostatic data members 放置的是个别class object感兴趣的数据。static data members放置的
则是整个class感兴趣的数据。

2. c++对象模型以空间优化和存取速度优化来表现nostatic data members,并保持与c struct数据的兼容。nostatic data members直接存放在每一个class object中。
对于集成而来的nostatic data members不管是virtual还是novirtual的base class,也会存放在每一个class object中。

3. class中的static data members(静态成员变量)会被放置到程序的global data segment中。不会影响class object的大小。不管该class存在产生多少份实例,
static data members只存在一份实例

4. 每个class object都有足够的容量来容纳它所有的nostatic data members。当时改class object的大小并不是所有nostatic data members的大小,它往往比想象中
的大,一是因为编译器为支持语言特性如virtual,而为class object添加额外的data member。二是由于边界调整的需要。


### The Binging of a Data Member 成员变量的绑定
```cpp
extern int x;

class Point3d{
public:
    float X(){
        return x;
    };
private:
    float x;
};
//编译器的语法分析 会在这里执行。
```
在c++ 2.0之前 float X()函数返回的是int x。2.0之后对成员函数的分析,会直到整个class的声明都出现了开始。因此上述写法在2.0正确,它会返回class Point3d的
float x。  
但是对于成员函数的参数列表的类型,并不会整个class的声明都出现了才开始决议。他们会在第一次遇到时被适当的决议。
```cpp
typedef int length;

class Point3d{
public:
    void mumble(length var){
        _var = var;
    }
    length mumble(){
        return _var;
    }
private:
    typedef float length;
    length _var;
};
```
mumble函数的参数length用的就是typedef int length,而没有用到class内部的typedef float length。为了避免混淆,我们需要使用防御性编程,将class内部的typedef float lengt
放在mumble函数之前。

```cpp
typedef int length;

class Point3d{
private:
  typedef float length;
  length _var;
public:
    void mumble(length var){
        _var = var;
    }
    length mumble(){
        return _var;
    }    
};
```

