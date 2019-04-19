```cpp
int* pi = new int(5);
//上述代码会被编译器转化为
int* pi;
if(pi = new(sizeof(int))){
  *pi = 5;
}
```
