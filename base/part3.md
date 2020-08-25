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
· HashTable的方法是同步的，HashMap未经同步，在多线程场合要手动同步hashmap \
· hashtable不允许null值，hashmap允许null值\
· hashtable 有一个容器，功能和containsValue功能一样\
· hashtable使用enumeration\
· hashtable中hash数组默认大小为11，增加的方式是2*old+1, hashmap中hash的默认大小是16，而且一定是2的指数
