### Static Data Members 静态成员变量
1. 静态成员变量,编译器将其提出于class之外。每一个静态变量只有一个实例,存放在程序的data segment之中。被视为一个global变量，但只在class生命范围内可见。
2. 如果该静态变量是从其他基类继承而来,程序对该静态变量还是只有唯一的一个实例,其存储路径还是那么直接。
3. 如果取一个静态成员变量的地址,会得到一个指向其数据类型的指针。因为该静态变量并不含在class object之中,因此不会得到一个指向其class member的指针。
4. 两个class都声明了相同静态成员,当他们都放在data segment之中时,会导致名称冲突。这时编译器会暗中对每个静态成员变量编码,确保他们有独一无二的标识。
5. 如果通过一个函数给静态变量赋值。如 ```foobar().chunkSize = 23;```那么编译器会将其转化为```(void) foobar(); Point3d..chunkSize = 23;```

### NonStatic Data Members 非静态成员变量
