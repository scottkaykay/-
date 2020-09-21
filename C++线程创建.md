## C++线程创建

### 通过初始函数创建一个线程

```C++
#include<iostream>
#include<thread>
using namespace std;

void print1()
{
    cout<<"print1_1线程执行"<<endl;
    cout<<"print1_2线程执行"<<endl;
    cout<<"print1_3线程执行"<<endl;
}
int main()
{
    thread mythread1(print1); //print1作为线程mythread1的初始函数
    mythread1.join();   //join阻塞主线程，等待mythread1这个线程执行完毕再继续执行
    cout<<"主线程执行"<<endl;
    return 0;
}
```

### 用类对象创建一个线程

```C++
#include<iostream>
#include<thread>

class T
{
public:
    int it;
    T(int m_it):it(m_it)
    {
        cout<<"构造函数被执行"<<endl;
    }
    T(const T &t):it(t.it)
    {
        cout<<"拷贝构造函数执行"<<endl;
    }
    ~T()
    {
        cout<<"析构函数被执行"<<endl;
    }
    void operator()()
    {
        cout<<"it值:"<<it<<endl;
    }
};
int main()
{
    int itm=8;
    T t(itm);
    thread mythread2(t);
    mythread2.join();
    cout<<"主线程执行"<<endl;
    return 0;
}
```

### 用lambda表达式创建一个线程

```C++
#include<iostream>
#include<thread>
using namespace std;
int main()
{
    auto mylamthread=[]{
    cout<<"我的线程开始执行了"<<endl;
    //......
    cout<<"我的线程执行结束了"<<endl;
    };
    thread mythread3(mylamthread);
    mythread3.join();
    cout<<"主线程执行结束"<<endl;
    return 0;
}
```
