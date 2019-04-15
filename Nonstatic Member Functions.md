### 非静态成员函数的调用方式
nonstatic member function至少必须和一般的nonmember function有相同的效率,这是c++的设计准则之一。  
通过编译器内部将'member函数实例'转化为对等的'nonmember函数实例'来实现这一准则。  
转换步骤如下:
1. 改写函数的signature,以安插一个额外的参数到member function中,用以提供一个存取管道,使class object得以将此函数调用,该额外参数被称为this指针。
2. 将每一个'对nonstatic data member的存取操作'改为经由this指针来存取。
3. 将member function重新写成一个外部函数，函数名称经过'mangling'处理，使其在程序中称为独一无二的语汇。

```cpp
Point3d Point3d::normalize() const{
    register float mag = magnitude();
    Point3d normal;
    normal._x = _x/mag;
    normal._y = _y/mag;
    normal._z = _z/mag;
    return normal;
}

//上述函数将被编译器转化为
normalize_7Point3dFv(register Point3d* this,Point3d& _result){
    register float mag = this->magnitude();
    _result.Point3d::Point3d();
    _result._x = this->_x/mag;
    _result._y = this->_y/mag;
    _result._z = this->_z/mag;
    return ;
}
```

#### 函数名称的特殊处理
1. member function的名称前面会被加上class的名称
2. 加上改函数的参数链表和参数类型
通过上面两步便可造出独一无二的结果。
