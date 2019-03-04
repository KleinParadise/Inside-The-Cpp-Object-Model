### Bitwise Copy semantice (位逐次拷贝)
如果一个类没有定义拷贝构造函数，通常按照“成员逐一初始化(DefaultMemberwise Initialization)”的手法来解决“一个类对象以另一个同类实体作为初值”——也就是说
把内建或派生的数据成员从某一个对象拷贝到另一个对象身上，如果数据成员是一个对象，则递归使用“成员逐一初始化(Default MemberwiseInitialization)”的手法。
成员逐一初始化具体实现方式就是位逐次拷贝,此时编译器是不需要合成拷贝构造函数的。
```cpp
//位逐次拷贝示例
class Word{
public:
    Word(const char*);
    ~Word(){
        delete [] str;
    };
private:
    int cnt;
    char* str;
};

Word noun("book");
void foo(){
    Word verb = noun;
}
```
将实例noun值作为verb的初始值时,编译器不需要合成拷贝构造函数。只需位逐次拷贝即可实现verb的赋值操作。

### 什么时候Class不能使用位逐次拷贝？
#### 1.当class内含一个member object而后者的class声明有一个copy constructor时(不论是设计者定义还是编译器合成)。
#### 2.当class继承一个base class而后者存在一个copy constructor时(不论是设计者定义还是编译器合成)。
```cpp
class String{
public:
    String(const char*);
    String(const String&);
    ~String();
};

class Word{
public:
    Word(const char*);
    ~Word(){};
private:
    int cnt;
    String str;
};
```
class String声明了显式的拷贝构造函数,当我们使用class Word的一个实例作为同类型一个实例的初始值时,编译器必须为class Word类合成出一个拷贝构造函数。以便调用class String的拷贝构造函数。  
设计者声明或者编译器合成拷贝构造函数最直接的原因是需要在拷贝构造函数中做一些额外的工作或位逐次拷贝无法满足需求。

#### 3.当class声明一个或多个virtual functions时。
对于含有一个或多个virtual functions同类型的实例,位逐次拷贝是可以满足需求的。但是将子类实例作为基类实例的初值,子类实例与基类实例的vptr指针指向的vftb是不同的。位逐次拷贝会造成灾难性的后果。因此在这种情况下,编译器需要通过拷贝构造函数把被初始化的对象的vptr指向正确的虚函数表。
```cpp
class ZooAnimal{
    
};
class Bear: public ZooAnimal{
    
};
Bear yogi;
ZooAnimal franny = yogi;
```
Bear继承ZooAnimal,当把yogi作为franny的初值时,会引起切割行为。yogi中的ZooAnimal部分将作为franny的初值。但是yogi的vptr指针指向的是 class Bear的vftb。如果将其vptr指针赋值给franny将会造成混乱。这是便要借助编译器合成class ZooAnimal的拷贝构造函数,让franny指向class ZooAnimal的虚函数表。

#### 4.当class派生自一个继承串链,有一个或多个virtual base classes时。
使用继承类向基类提供初值,此时使用位逐次拷贝有可能破坏对象中虚基类子对象的位置。因此需要编译器在拷贝构造函数中调整虚基类子对象的位置。

参考:
http://www.roading.org/develop/cpp/%E6%8B%B7%E8%B4%9D%E6%9E%84%E9%80%A0%E5%87%BD%E6%95%B0%EF%BC%88copy-constuctor%EF%BC%89.html
