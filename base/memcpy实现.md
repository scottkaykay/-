## memcpy的c++实现

· 不要破坏传进来的形参，定义新的临时变量来操作\
· 考虑指针类型，不同类型的指针不能直接++赋值\
· 内存重叠的情况，**需要从高地址向低地址赋值**

```C++
void* memcpy(void* dest,const void* src,size_t size)
{
    char* d;
    const char* s;
    
    if(dest==NULL || src==NULL)
        return NULL;
    //分情况，讨论是否有内存重叠
    if((src<dest) && ((const char*)src+size>(char*)dest))
    {
        d=((char*)dest+size-1);
        s=((const char*)src+size-1); //这里 char* s;   s=(char*)src;   这里使用了强制类型转换，将const char*转成了char*, 有了强制转换，编译器就不会报错了
        while(size--)
            *d--=*s--;
        
    }
    else
    {
        d=(char*)(dest);
        s=(const char*)(src);
        while(size--)
            *d++=*s++;
    }
    return dest;
}
```
memcpy将src指向的对象中的size个字符拷贝到dest指向的对象中，返回指向结果对象的指针。\
memove的功能和memcpy一样，但解决了memcpy的内存重叠问题，但这两个函数在处理内存区域重叠的方式不同。


补充：

```C++
#include<stdio.h>
#include<stdlib.h>

void B(char* c)
{
    c[0]='b';
}
int A(const char* c)
{
    B((char*)c);
    return 0;
}

int main(void)
{
    const char* c="abcdefg";
    char d[]="higklmn";
    char* e="opqrst";
    A(c); // 错误
    A(d); // 正确
    A(e); // 错误
    system("pause");
    return 0;
}
```
