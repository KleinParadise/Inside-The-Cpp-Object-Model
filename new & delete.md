```cpp
int* pi = new int(5);
//上述代码会被编译器转化为
int* pi;
if(pi = __new(sizeof(int))){
  *pi = 5;
}

delete pi;
//上述代码会被编译器转化为
if(pi != 0){
 __delete(pi);
}

//通过构造函数配置一个class object
Point3d* origin = new Point3d;
//上述代码会被编译器转化为
Point3d* origin
if(origin = __new(sizeof(Point3d))){
  *origin = Point3d::Point3d(origin);
}

delete origin;
//上述代码会被编译器转化为
if(origin != 0){
 Point3d::~Point3d(origin);
 __delete(origin)
}
```

#### 针对数组的new语意
```cpp
int* p_array = new int[5];
//vec_new()不会被调用,vec_new()的主要作用是把默认构造函数施于数组中的每一个class objects身上。
//上述代码会被编译器转化为
int* p_array = (int*)__new(5 * sizeof(int));
```
