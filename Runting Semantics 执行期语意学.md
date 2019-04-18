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
