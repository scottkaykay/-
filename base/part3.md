## 一个空类包含什么？

· 空的构造函数\
· 拷贝构造函数（浅拷贝）\
· 重载赋值操作符函数（浅拷贝）\
· 析构函数\
· 取址运算符\
· 取址运算符const

所有的这些默认函数，只有代码中调用了才会生成，否则也不会生成

```C++
class Test1{

};
class Test2
{
public:
    Test2();
    Test2(Test2& t);
    Test2& operator=(const Test2& other);
    Test2* operator & ();
    const Test2* operator & () const;
    ~Test2();
```

## hash_map,hash_table

hash_map的用法和map是一样的，提供了insert,size,count等操作，里面的元素也是以pair类型来存储的，其底层是用哈希表来实现的。
总体来说，hash_map查找速度会比map快，而且查找速度基本和数据量大小无关，属于常数级别，map的查找速度属于log(n)级别。但两者谁快不是绝对的，数据量很大时，log(n)可能比常数还小。
hash_map对空间的要求比map要高很多，是空间换时间的做法，但如果哈希函数选的不好也会影响效果。

hashtable的应用非常广泛，HashMap是新框架中用来代替HashTable的类，尽量使用hashmap.它们的区别：\
· 二者的父类不同：HashTable继承自Dictionary类， HashMap继承自AbstractMap类 \
· hashtable不允许null值，hashmap允许null值\
· hashtable 比hashmap多提供了elements()和contains()两个方法,elements返回hashtable中value的枚举，contain判断是否包含此元素\
· hashtable是线程安全的，它的每个方法都加入了Synchronize方法，HashMap不是线程安全的，在多线程并发的环境下，可能会产生死锁等问题，氮气效率比hashtable高不少，在多线程操作时使用ConcurrentHashMap\
·遍历方式不同，二者都使用了Iterator,Hashtable还使用了Enumeration\
· hashtable中hash数组默认大小为11，增加的方式是2\*old+1, hashmap中hash的默认大小是16，而且一定是2的指数

hash_map基于哈希表，哈希表最大的优点，就是把数据存储和查找消耗的时间大大降低，几乎可以看成是常数时间，代价是消耗较多的内存。另外，编码比较容易也是它的特点之一。

基本原理：使用一个下标范围比较大的数组来存储元素，设计一个哈希函数，使每个元素的关键字都与一个函数值（哈希计算得到的数组下标）相对应，再用这个数组单元来存储这个元素。但是，不能保证每个元素的关键字与函数值是一一对应的，不同的元素可能会对应到相同的函数值，产生冲突。

hash_map首先分配一大片内存，形成许多桶，再利用哈希函数对key进行计算，映射到不同位置保存：\
· 得到key\
· 通过哈希函数计算得到哈希值\
· 得到桶号（一般为hash值对桶数取模）\
· 存放key和value在桶内

取值过程：\
· 得到key\
· 通过哈希函数得到hash值\
· 得到桶号（哈希值对桶数取模）\
· 比较桶的内部元素是否与key相等，若都不相等则没有找到\
· 取出相等的记录的value

如果每个桶内部只有一个元素，那么查找的时候就只有一次比较。要实现哈希表，和用户相关的是：哈希函数和比较函数。

定义自己的哈希函数：\
```C++
struct str_hash{
    size_t operator()(const string& str) const
    {
        unsigned long __h=0;
        for(size_t i=0;i<str.size();i++)
            __h=5*__h+str[i];
        return size_t(__h);
    }
};
```

如果有自己的数据类型，需要重新定义比较函数，重载operator==函数：\
```C++
struct mystruct{
    int iID;
    int len;
    bool operator==(const mystruct& my) const
    {
        return (iID==my.iID) && (len==my.len);
    }
};
```

使用方法：hash_map<string ,string,hash<string>,equal_to<mystruct>> mymap;
    
hash_map和map的区别：\
· 构造函数： hash_map需要哈希函数，比较函数，map只需要比较函数\
· 存储结构：hash_map采用哈希表存储，map采用红黑树存储|
选择标准：权衡查找速度，数据量，内存使用

## 哈希函数的构造方法

· 数字分析法\
· 平方取中法\
· 分段叠加法\
· 除留余数法\
· 伪随机数法

## 友元函数

· 友元函数不是类的成员函数\
· 友元函数不受类中的访问权限关键字限制，可以把它放在类的公有，私有，保护部分，但结果一样\
· 某类的友元函数的作用域并非该类的作用域，如果该友元函数是另一类的成员函数，则作用域为另一类的作用域，否则与一般函数相同。\

为什么要用友元函数？\
实现类之间的数据共享，减少系统开销，提高效率。其他类的成员函数可以访问该类的私有变量和保护变量，从而使两个类共享同一函数。应用场景：\
· 运算符重载的某些场合需要使用友元\
· 两个类要共享数据的时候

·优点：能实现类间的数据共享，提高效率\
·缺点：友元函数破坏了封装机制，尽量减少友元函数的使用

几个注意点：\
· 友元函数没有this指针：要访问非static 成员时，需要对象做参数,访问static成员或全局变量时，则不需要对象做参数。\
· 友元函数可以直接调用，不需要通过对象或指针\
· 友元函数是类外的函数，所以它的声明可以放在类的私有段或公有段且没有区别。

友元函数的分类：\
· 普通友元函数\
声明：  friend + 普通函数声明\
实现位置：可以在类外或类中\
调用： 类似普通函数，直接调用

· 类Y的所有成员函数都为类X的友元函数，提供一种类之间的合作方式，使类Y的对象可以具有类X和类Y的功能\
· 声明位置：类中public或private均可，常写在private \
· 声明： friend + 类名\
· 调用：普通调用

· 类Y的一个成员函数为类X的友元函数，则类Y中的这个成员函数可以访问类X的私有变量\
· 声明位置： public中\
· 声明： friend+成员函数声明\
· 调用：先定义Y的对象y,使用y调用自己的成员函数，自己的成员函数中使用了友元机制\

友元函数和类的成员函数的区别：\
· 成员函数有this指针，友元函数没有this指针\
· 友元函数不能被继承


## STL中的Allocator(空间配置器)

参考：https://zhuanlan.zhihu.com/p/34725232

```C++
//main函数里的容器定义：
std::vector<int> v;

//STL中
template<class T,class Alloc=allocator<T>>  //默认参数allocator<T>,上面将T替换成了int,当然，也可以自己实现一个allocator传入
class vector{
  Alloc data_allocator; //每个vector内部实例一个allocator   //为什么allocator也要接受类型T? 因为allocator除了负责内存分配和释放，还要负责对象的构造和析构，调用对象的构造和析构函数需要知道类型，另外，allocator中有为n个T类型对象分配内存的批量操作，只有知道对象类型才能计算出对象需要的空间。
};
```

Allocator中最重要的四个函数： allocate,deallocate, construct,destroy,可以简单理解为 ::operator new , ::operator delete, 构造函数，析构函数。一个最简单的allocator就可以理解为对new,delete的简单封装，以及对构造函数和析构函数的直接调用。

SGI STL的allocator包含两部分，一个是标准的allocator,一个是特殊配置器(默认使用的，在memory中实现，下面对其展开)：\
· <stl_construct.h> 定义了全局函数construct和destroy,负责对象构造和析构\
· <stl_alloc.h> 内存配置和释放在此处实现，其内部有两级配置器，第一级结构简单，封装malloc()和free()，第二级实现了自由链表和内存池，用于提升大量小额内存配置时的性能。\
· <stl_uninitialiezed.h> 一些用于填充和拷贝大块内存的全局函数

对象的构造/析构，与内存的分配和释放是分离实现的

用户代码实例化一个vector对象，vector对象调用alloc的接口，此时不需要实例化一个空间配置器，只要调用alloc的静态函数就行了。

std::alloc的接口与标准非常类似：\
· static T* allocate() 函数负责空间配置，返回一个T对象大小的空间\
· static T* allocate(size_t) 函数负责批量空间配置\
· static void deallocate(T*)函数负责空间释放\
· static void deallocate(T*,size_t) 函数负责批量空间释放

第一级空间配置器进行大空间的配置：\
```C++
template<int inst>
class __malloc_alloc_template{...};
// allocate()直接使用malloc()
//deallocate直接使用free()
//模拟c++的set_new_handler()以处理内存不足的情况
```

第二级空间配置器，程序多次进行小空间配置时，可以从自由链表和内存池中获取空间，减少系统调用，提升性能。
```C++
template<bool threads,int inst>
class __default_alloc_template{..};
//维护16个自由链表,负责16中小型区块的次配置能力，内存池以malloc()配置而得，如果内存不足，转调用第一级配置器
//如果需求大于128Byte，转调用第一级配置器
```

## 编译过程：预处理，编译，汇编，链接

· 预处理：展开头文件/宏替换/去掉注释/条件编译   Test.i  main.i \
· 编译： 检查语法，生成汇编代码                Test.s  main.s \
· 汇编：将汇编代码转换为机器码                 Test.o  main.o             \
· 链接：链接到一起生成可执行程序               a.out

· 预处理包含的操作\
#define  宏定义，简单易用，但是只是简单替换，不做类型安全检查，可读性不好，运行时可能在内存中存在多个内容备份\
#undef   撤销已定义过的宏名\
#include 使编译程序将另一源文件嵌入到带有#include 的源文件中|
#if #endif      #if 后面的表达式如果为true,则编译它与#endif之间的代码，中间可以有#else  #elif  形成多级判断\
#ifdef    用#ifdef 与#ifndef命令分别表示“如果有定义”及“如果无定义”，属于条件编译\
#line     改变当前行数和文件名称\
#error    编译程序时，只要遇到#error 就会生成一个编译错误提示信息，并停止编译\
#pragma   它允许向编译程序传送各种指令，例如#pragma once可以保证文件只被编译一次，防止头文件多次包含。\
#pragma pack(n)编译器将按照n各字节对齐\
#pragma pack() 编译器取消自定义字节对齐方式

· 编译期间需要注意的问题\
.的优先级高于\*， ->可消除这个问题\
· []优先级高于*, ()优先级高于*

·链接\
静态链接：链接器在链接时，将库的内容加入到可执行程序中去。链接器是一个可独立程序，将一个或多个库，目标文件链接到一块生成可执行程序。静态链接是把要调用的函数或者过程链接到可执行文件中。\
动态链接：动态链接所调用的代码并没有被拷贝到应用程序的可执行文件中去，而是在其中加入了所调用函数的描述信息（往往是一些重定位信息），仅当应用程序被装入内存开始运行时，在windows的管理下，才在应用程序与相应的DLL之间建立链接，当要执行所调用DLL中的函数时，根据链接产生的重定位信息，windows才转去执行DLL中相应的函数代码。

## 编译原理

有一语法制导翻译如下所示：\
S→bAb {print”1”} A→(B {print”2”} A→a {print”3”} B→Aa) {print”4”}\
若输入序列为b(((aa)a)a)b，且采用自底向上的分析方法，则输出序列为

                                  S
                                / | \
                               b  A  b          1    (((aa)a)a)
                                 / \
                                (   B           2    ((aa)a)a)
                                   /|\
                                  A a ）        4    ((aa)a)
                                 / \
                                (   B           2    (aa)a)
                                   /|\
                                  A a )         4    (aa)
                                 / \
                                （  B           2    aa)
                                   /|\
                                  A a )         4    a
                                  |
                                  a             3 
                                 
## 设计模式的6大原则

· 开放封闭：对扩展开放，对修改关闭。在程序需要扩展的时候不能去修改原来的代码，而是要利用接口和抽象类。\
· 里氏替换原则：任何父类可以出现的地方，子类一定可以出现，LSP原则是继承复用的基石，只有子类可以替换掉父类，且软件单位的功能不受影响时，父类才能真正被复用。里氏替换原则是实现抽象画的具体步骤的规范。\
· 依赖倒转原则：针对接口编程，依赖于抽象而不是具体。\
·  接口隔离：使用多个隔离的接口，比使用单个接口要好。可以降低类间的耦合度。\
· 迪米特法则：一个实体应尽量少地与其他实体间发生相互作用，使得系统功能模块相对独立\
· 合成复用原则：尽量使用合成/聚合的方式，而不是使用继承

## 磁盘调度算法

FCFS调度， SSTF调度， SCAN 调度， C-SCAN调度， LOOK调度
