## 智能指针 shared_ptr

### 用法：

原本我们使用指针是这样的：

```cpp
    A* a;
    a = new A(...);
```

而智能指针则是这样写:

```cpp
    shared_ptr<A> a;
    a = make_shared<A>(...);
```

### shared_ptr的特点：

+ 当即将指向其它内存时，首先会将自身引用计数--
+ 当引用计数为0时，就会释放掉原本指向的内存
+ 接下来指向新的内存，引用计数赋值为`原本指向该内存的智能指针数+1`

这样就保证了所有`没有被指针指向的内存都会被顺利的释放`

我们需要做的就是`删除掉那些不再需要的shared_ptr元素`

### 多个对象共享底层数据时：

+ 可以通过智能指针来管理共享内存
比如a对象用智能指针成员在底层动态的创建了数据D，这时b对象拷贝a，同时也拷贝了a的智能指针；在a被销毁时，a中的智能指针销毁，但仍有b中拷贝的智能指针指向底层数据D，并不会影响b对底层数据D的引用，当b也销毁时，没有指针指向这块内存，D顺利释放

show me the code:
```cpp
class Base{
public:
       Base(std::string s) {
              this->data = std::make_shared<std::string>(s);
       }
       std::string getdata() {
              return *data;
       }
private:
       std::shared_ptr<std::string> data;
};


int main() {
       Base* a = new Base("D");
       Base b(*a);
       delete a;
       std::cout << b.getdata() << std::endl;
       return 0;
}

```
+ 既保证了对象们谁也`不能单方面的销毁共享内存`，也保证了内存在不再需要的时候能顺利释放
