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
        s=((const char*)src+size-1);
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
