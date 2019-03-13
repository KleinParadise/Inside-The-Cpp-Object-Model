### Class 加上多态
#### Class支持多态,势必给其带来空间和存取时间的额外负担,主要表现在以下四点：
1. 导入一个与Class有关的virtual table,用来存放该Class声明的每一个virtual function的地址。virtual table中元素的个数一般是该Class中virtual function
的数量加上支持运行前获取该class的类型的一两个slot。
2. 在每个class object中导入一个vptr,用以在执行期使该object能找到对应的virtual table。
3. 加强构造函数,指定vptr的初值,使其指向正确的virtual table。
4. 加强的析构函数，使它能够抹消指向Class之相关virtual table的vptr。即完成子类析构再到基类析构的操作。

#### 将vptr放在class object头尾,有什么差异？
将vptr放在class object的尾端,这种方式可以保留base Class C struct的空间布局,可以使其支持C struct的继承类型。  
将vptr放在class object的前端,这种布局会为多重继承带来帮助,但是丧失了C语言的兼容性。
