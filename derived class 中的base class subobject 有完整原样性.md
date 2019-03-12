```cpp
class Concrete{
public:
private:
    int val;
    char c1;
    char c2;
    char c3;
};
```
在32位机器中,每一个Concrete object的大小都是8byte。
1. val占了4byte
2. c1,c2,c3各占了1byte
3. alignment(边界调整)需要1byte.

将Concrete类分裂为三层结构,Concrete1,Concrete2,Concrete3的大小是多少呢？
```cpp
class Concrete1{
public:
private:
    int val;
    char c1;
};

class Concrete2 : public Concrete1{
public:
private:
    char c2;
};

class Concrete3 : public Concrete2{
public:
private:
    char c3;
};
```
Concrete1包含val和c1共5byte,加上边界调整3byte 大小为8byte。  
Concrete2包含Concrete1和c2共9byte,加上边界调整3byte 大小为12byte。  
Concrete3包含Concrete2和c3共13byte,加上边界调整3byte 大小为16byte。 
结构图如下:
出现该结果是为了让derived class 中的base class subobject有其完整原样性。使得基类赋值给子类时,保持子类中基类数据的原样性,同时也不会覆盖子类中的数据。
