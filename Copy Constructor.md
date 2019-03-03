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
#### 1.当class内含一个member object而后者的class声明有一个copy constructor时。
#### 2.当class继承一个base class而后者存在一个copy constructor时。
#### 3.当class声明一个或多个virtual functions时。
#### 4.当class派生自一个继承串链,有一个或多个virtual base classes时。

参考:
http://www.roading.org/develop/cpp/%E6%8B%B7%E8%B4%9D%E6%9E%84%E9%80%A0%E5%87%BD%E6%95%B0%EF%BC%88copy-constuctor%EF%BC%89.html
