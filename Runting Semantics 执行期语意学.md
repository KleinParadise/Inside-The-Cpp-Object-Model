### Object Construction and Destruction 执行期对象的构造与析构
编译器函数中的安插 constructor和destructor
```cpp
{
  Point point;
  //point.Point::Point() 构造函数
  ...
  //point.Point::~Point() 析构函数
}
```
switch函数中,destructor必须放置在每一个离开点
```cpp
{
  Point point;
  //constructor 构造函数
  switch(int(point.x())){
    case -1:
    ...
    //destructor
    return;
    case 0:
    ...
    //destructor
    return;
    case 1:
    ...
    //destructor
    return;
    default :
    ...
    //destructor
    return;
  }
  //destructor
}
```
#### Global Objects 全局对象
```cpp
Matrix identity;
main(){
  //identity 在此初始化
  Matrix m1 = identity;
  ...
  return 0;
}
```
在main()函数中第一次用到identity之前，会将identity构造出来，在main()函数结束之前把identity摧毁掉。  
c++程序中的所有global objects都放置在程序的data segment中。如果有明确的指定它一个值，object将以该值为初值。否则object所配置到内存内容为0.  
编译器构造，析构全局对象的流程:
1. 为每一个需要静态初始化的档案产生一个_sti()函数，内带必要的contructor调用操作或inline expansions.
2. 在每一个需要静态内存释放操作(static deallocation)的文件中，产生一个_std的函数，内带必要的destructor调用操作或inline expansions.
3. 提供一组'funtime library' munch函数：一个_main()函数以调用可执行文件中的所有_sti()函数，以及一个exit函数，以调用可执行文件中的所有_std()函数。  

通过unix的nm命令来收集程序中各个object files的_sti()函数和_std()函数。


#### Local Static ObjectS 局部静态对象
```cpp
const Matrix& identity(){
  static Matrix mat_identity;
  ...
  return mat_identity;
}
```
mat_identity的constructor必须只能施行一次,虽然identity()可能被调用多次  
mat_identity的destructor必须只能施行一次,虽然identity()可能被调用多次  
编译器如何保证上述操作语意  
策略1：无条件在程序程序启动构造出对象，这会导致所有的local static object都在程序起始被初始化，即使它们所在的函数不曾被调用过。这种策略浪费了时间和内存。  
策略2：在identity()被调用时才把mat_identity构造出来。导入一个临时对象来保护mat_identity的初始化操作。第一次处理identity()函数，这个临时对象被评估为false,当其构造函数被调用，将该临时对象改为true。如此一来解决了构造问题。析构函数也要有条件的作用于mat_identity上，只有mat_identity被构造即临时对象为true才能调用其析构函数。但是mat_identity相对于函数是local,因此很难在identity()外拿到mat_identity变量。解决方法是,由于mat_identity是static 变量，其地址会被转到程序的global object的data segment中。在identity()函数外声明一个临时全局变量来保存mat_identity的地址。编译器通过该临时全局变量实现对mat_identity的操作。

#### Array of Objects 对象数组
```cpp
Point knots[10];
```
如果Point既没有定义一个构造函数也没定义一个析构函数,上述代码编译器只需配置足够的内存以存储10个连续的Point元素即可。  
如果Point定义了一个默认构造函数，那么这个构造函数必须轮流施于数组中每一个元素之上。  
编译器使用名为'vec_new()'的函数，产生出以class objects构造而成的数组。
```cpp
void* vec_new(
  void *array,//数组的起始地址
  size_t elem_size,//每个class object的大小
  int elem_count,//数组中元素的数目
  void (*constructor)(void*),//class 默认构造函数的函数指针
  void (*destructor)(void*,char),//class 默认析构函数的函数指针
)

Point knots[10];

```
