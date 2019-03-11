### Static Data Members 静态成员变量
1. 静态成员变量,编译器将其提出于class之外。每一个静态变量只有一个实例,存放在程序的data segment之中。被视为一个global变量，但只在class生命范围内可见。
2. 如果该静态变量是从其他基类继承而来,程序对该静态变量还是只有唯一的一个实例,其存储路径还是那么直接。
3. 如果取一个静态成员变量的地址,会得到一个指向其数据类型的指针。因为该静态变量并不含在class object之中,因此不会得到一个指向其class member的指针。
4. 两个class都声明了相同静态成员,当他们都放在data segment之中时,会导致名称冲突。这时编译器会暗中对每个静态成员变量编码,确保他们有独一无二的标识。
5. 如果通过一个函数给静态变量赋值。如 ```foobar().chunkSize = 23;```那么编译器会将其转化为```(void) foobar(); Point3d..chunkSize = 23;```

### NonStatic Data Members 非静态成员变量
1. 非静态成员变量存放在每一个class object之中,只能通过显示或隐式的class object来存取它们。
2. 通过成员函数来处理非静态成员变量,隐式的class object就会调用。即this指针
3. class object的起始地址加上data member的偏移位置(offset),即可对非成员变量进行存取操作。而非静态成员的偏移位置在编译器可获知。
```cpp
origin._y = 0;
//编译器内部将转化为
&origin + (&Point3d::_y -1)
//指向data member的指针,其偏移位置总是会被加1。这样做的目的是使编译器区分
//一个指向data member的指针,用以指出class的第一个member
//一个指向data member的指针,没有指出任何member
```
4. 以下对非静态成员变量的操作,在存取效率上有什么差异？
```cpp
Point3d origin;
*pt = &origin;

origin.x = 0;
pt->x = 0 
```
当Point3d是一个子类,继承结构中有一个virtual base class并且x是从virtual base class继承而来。此时只有在执行期我们才能知道pt的具体类型,因此无法确定变量
x的偏移位置(offset),这时候对x的存取效率会比较低。


