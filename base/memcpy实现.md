## memcpy的c++实现

· 不要破坏传进来的形参，定义新的临时变量来操作\
· 考虑指针类型，不同类型的指针不能直接++赋值\
· 内存重叠的情况，**需要从高地址向低地址赋值**

```C++
void* memcpy(void* dest,const void* src,size_t count)
{
    char* d;
    const char* s;
    //分情况，讨论是否有内存重叠
    if(dest>(src+size) || (dest<src))
    {
        d=dest;
        s=src;
        while(count--)
            *d++=*s++;
        
    }
    else
    {
        d=(char*)(dest+count-1);
        s=(char*)(src+count-1);
        while(count--)
            *d--=*s--;
    }
    return dest;
}
```
