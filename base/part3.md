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

