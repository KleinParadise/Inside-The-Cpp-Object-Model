### Member Initialization List 成员初始化队伍

当我们写出一个构造函数时,就有机会设置成员变量的初值。在构造函数设置成员的初值有两种方式:  
1. 通过Member Initialization List设置初值。
```cpp
class Word{
    //通过Member Initialization List
    public Word():_name(""),_ctn(0){
    };
private:
    String _name;
    int _ctn;
};
```
2. 通过构造函数函数体设置初值。
```cpp
class Word{
    //通过构造函数函数体
    public Word(){
        _name = 0;
        _ctn = 0;
    };
private:
    String _name;
    int _ctn;
};
```
有以下四种情况,使用Member Initialization List初始化成员变量比在构造函数函数体中初始化成员变量效率更高。
1. 当初始化一个 reference member时
2. 当初始化一个const member时
3. 当调用一个base class的构造函数,他拥有一组参数时
4. 当调用一个member class的构造函数,他拥有一组参数时  

上述四种情况,使用函数体初始化成员变量,会产生一个临时object导致程序效率降低。而使用Member Initialization List初始化,直接调用该成员的拷贝构造函数来设定
其初值,避免产生了临时object,从而效率较高。 

使用构造函数函数体初始化成员变量产生临时object的c++伪码
```cpp
Word::Word()
{
	//调用String的default constructor
	_name.String::String();
	
	//产生临时对象
	String temp = String(0);
	
	//“memberwise”地拷贝_name
	_name.String::operator=(temp);
	
	//摧毁临时对象
	temp.String::~String();
	
	_cnt = 0;
}
```
使用Member Initialization List的c++伪码
```cpp
Word::Word(){
  _name.String::String();
  _cnt = 0 ;
}
```

#### 注意Member Initialization List 与 构造函数函数体中成员变量的初始化顺序
成员变量在class中的声明顺序决定了Member Initialization List初始化成员变量的顺序。以下代码将会带来不可预知的bug
```cpp
class X{
public:
    //编译器会为此产生一个警告
    X(int val):j(val),i(j){
        
    };
private:
    int i;
    int j;
};
```
依照class声明的顺序,i将比j早初始化,因此将j作为i的初值是错误的。正确的做法应该如下
```cpp
class X{
public:
    X(int val):j(val){
        i = j;
    };
private:
    int i;
    int j;
};
```
Member Initialization List会在构造的函数体之前执行。  

可以通过构造函数调用一个member function来初始化某一个成员变量。考虑到多态的情况，最好将其放在构造函数函数体内,而不是放在Member Initialization List中。

