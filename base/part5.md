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

1. 当size()==capacity()，插入元素时vector会被重新分配内存，指向元素的迭代器都会失效。\
2. 当size()<capacity()，插入元素时vector不会被重新分配内存。此时，若迭代器指向插入位置之前的元素，它仍有效；若迭代器指向插入位置之后的元素，它将会失效。

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

### 传入一个char*,如何给它分配内存

```C++
void fun(char** c)
{
	*c = new char[1];
	
}

int main()
{
   char* c = NULL;
   fun(&c);
   *c = '1';
   cout << *c;
   return 0; //改变指针的内容，需要使用指针的指针
}
```

### 写一个string类，支持拷贝赋值，允许放入容器

```C++
#define _CRT_SECURE_NO_WARNINGS //忽略strcpy报错

#include<iostream>
#include<vector>
#include<stack>
#include<string.h>
using namespace std;

class Mystring
{
public:
	Mystring() :data_(new char[1])
	{
		*data_ = '\0';
	}
	Mystring(const char* str) :data_(new char[strlen(str) + 1])
	{
		strcpy(data_, str);
	}
	Mystring(const Mystring& rhs) :data_(new char[rhs.size() + 1])
	{
		strcpy(data_, rhs.c_str());
	}
	Mystring& operator=(Mystring rhs)
	{
		newswap(rhs);
		return *this;
	}
	size_t size() const
	{
		return strlen(data_);
	}
	const char* c_str() const
	{
		return data_;
	}
	void newswap(Mystring rhs)
	{
		swap(data_, rhs.data_);
	}
	~Mystring()
	{
		delete[] data_;
	}
private:
	char* data_;
};



void foo(Mystring x)
{

}

void bar(const Mystring& x)
{

}

Mystring baz()
{
	Mystring ret("world");
	return ret;
}

int main()
{
	Mystring s0;
	Mystring s1("hello");
	Mystring s2(s0);
	Mystring s3 = s1;
	s2 = s1;
	foo(s1);
	bar(s1);
	foo("temporary");
	bar("temporary");
	Mystring s4 = baz();

	vector<Mystring> svec;
	svec.push_back(s0);
	svec.push_back(s1);
	svec.push_back(baz());
	svec.push_back("good job");
	return 0;
}
```

### C与C++的字符串

C++把字符串封装成了一种数据类型string,可以直接声明变量并进行赋值等字符串操作。区别：\
				C字符串				c++ string对象
	
所需的头文件名称        	<string>或<string.h>               <string>或<string.h>
	
需要头文件原因		 	为了使用字符串函数 		     为了使用string类

声明方式               	 char name[20]                        string name

初始化方式		  	 char name[20]="nihao"               string name="nihao"

必须声明字符串长度吗       	   是                                     否

使用一个null字符吗         	      是                                     否

字符串赋值的实现方式     	        strcpy(name,"john")                name="john"

优点                                更快                           更易于使用，优选方案

可以赋一个比现有字符更长的字符串吗     不能                                 可以

### C中定义变长结构体

```C++
#include<iostream>
#include<vector>
#include<stack>
#include<string.h>
#include<string>
using namespace std;

struct Mydata {
	int len;
	char data[0];
};

int main()
{
	int len = 10;
	char str[10] = "123456789";
	
	Mydata* mydata = (Mydata*)malloc(sizeof(Mydata) + 10);
	memcpy(mydata->data, str, 10);
	cout << mydata->data << endl;
	free(mydata);
	return 0;
}
```

### 求解结构体各数据成员的偏移量

```C++
#include<iostream>
#include<stddef.h>

using namespace std;

struct A{
	int a;
	char b;
	long c;
};
int main()
{
	cout<<(unsigned long)offsetof(A,a)<<endl;
	cout<<(unsigned long)offsetof(A,c)-(unsigned long)offsetof(A,a)<<endl;
	
	A instance;
	cout<<(unsigned long)(&mydata.c)<<endl;
	cout<<(unsigned long)(&mydata.c)-(unsigned long)(&mydata.a)<<endl;
	return 0;
}
```
