## std::move原理

std::move执行一个无条件的转化，将左值转化为右值引用，通过右值引用使用该值，用于移动语义，完成对象状态或所有权的转移。
可以减少不必要的临时对象复制(临时对象的创建销毁对性能有较大的影响)，提高效率。比如vector的push_back操作，会对参数对象进行复制，造成对象内存的额外创建（每加入一个元素都会创建并复制临时对象，结束后销毁）。
对指针类型的标准库对象并不需要这样做。

为了实现移动语义，需要定义转移构造函数或转移赋值操作符：

```C++
class MyString { 
 private: 
  char* _data; 
  size_t   _len; 
  void _init_data(const char *s) { 
    _data = new char[_len+1]; 
    memcpy(_data, s, _len); 
    _data[_len] = '\0'; 
  } 
 public: 
  MyString() { 
    _data = NULL; 
    _len = 0; 
  } 

  MyString(const char* p) { 
    _len = strlen (p); 
    _init_data(p); 
  } 

  MyString(const MyString& str) { 
    _len = str._len; 
    _init_data(str._data); 
    std::cout << "Copy Constructor is called! source: " << str._data << std::endl; 
  } 

  MyString& operator=(const MyString& str) { 
    if (this != &str) { 
      _len = str._len; 
      _init_data(str._data); 
    } 
    std::cout << "Copy Assignment is called! source: " << str._data << std::endl; 
    return *this; 
  } 

  virtual ~MyString() { 
    if (_data) free(_data); 
  } 
 }; 

 int main() { 
  MyString a; 
  a = MyString("Hello"); 
  std::vector<MyString> vec; 
  vec.push_back(MyString("World")); 
 }
 
 //转移构造函数的定义
 MyString(MyString&& str) { 
    std::cout << "Move Constructor is called! source: " << str._data << std::endl; 
    _len = str._len; 
    _data = str._data; 
    str._len = 0; 
    str._data = NULL; 
 }
 //转移赋值操作符的定义
 MyString& operator=(MyString&& str) { 
    std::cout << "Move Assignment is called! source: " << str._data << std::endl; 
    if (this != &str) { 
      _len = str._len; 
      _data = str._data; 
      str._len = 0; 
      str._data = NULL; 
    } 
    return *this; 
 }
```

## explicit, constexpr, noexcept

参考：https://www.cnblogs.com/gklovexixi/p/5622681.html

explict: c++中，有时可以将构造函数用作自动类型转换函数，但这种特性不是总是合乎要求的，有时会导致意外的类型转换。explicit关键字的作用就是禁止自动的隐式转换，只能显式地进行类型转换。**注意：只有一个参数的构造函数，或者构造函数有n个参数，但有n-1个参数提供了默认值，这样的情况才能进行类型转换。**

参考：https://blog.csdn.net/qq_42914703/article/details/103357355

constexpr:c++11标准规定，允许将变量声明为constexpr类型，以便**由编译器来验证变量的值是否是一个常量表达式。**

参考：https://blog.csdn.net/zkreats/article/details/50550786?utm_medium=distribute.pc_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-2.channel_param&depth_1-utm_source=distribute.pc_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-2.channel_param

noexcept:等同于throw,保证程序不抛出任何异常。使用noexcept时，std::teminate()函数会被立即调用，而不是调用std::unexpected();
因此，在异常处理的过程中，编译器不会回退栈，这为编译器的优化提供了更大的空间。

## 迭代器失效

序列式容器进行元素删除的时候，后面的元素会前移，导致后面所有元素的迭代器都会失效，因为序列式容器使用了连续分配的内存，删除一个元素导致后面所有的元素会向前移动一个位置，不能使用erase(iter++)的方式，**但erase方法可以返回下一个有效的迭代器！**

```C++
int main()
{
   vector<int> ivec;
   int n, val;
   cin >> n;
   for (int i = 0; i < n; i++)
   {
      cin >> val;
      ivec.push_back(val);

   }
   int count = 0;
   for (vector<int>::iterator iter = ivec.begin(); iter != ivec.end(); )
   {

      if(count==3)
         iter = ivec.erase(iter);
      cout << *iter << endl;
      count++;
      iter++;
   }
   return 0;
}
```

### 32位机和64位机不同数据类型的大小

32：\
char: 1
short: 2
int:  4
long: 4
long long :8
float: 4
double:8
void*: 4


64：\
char: 1
short: 2
int:  4
long: 4  (这是windows平台上的， linux平台上是8字节)
long long :8
float: 4
double:8
void*: 8

