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

### 3线程打印26个字母，如1A,2B,3C,1D,2E,3F...

```C++
#include<iostream>
#include<thread>
#include<mutex>

using namespace std;

mutex mymutex;
int ct = 1;
int num = 0;
char c = 'A';
void thread1()
{
	while (1)
	{
		if (num >= 26)
			break;
		lock_guard <mutex> lock(mymutex);
		if (ct == 1)
		{
			c ='A'+num;
			cout << ct << c << " ";
			++num;
			++ct;
		}
	}
}

void thread2()
{
	while (1)
	{
		if (num >= 26)
		{
			cout << "ok" << endl;
			break;
		}
		lock_guard <mutex> lock(mymutex);
		if (ct == 2)
		{
			c = 'A'+num;
			cout << ct << c << " ";
			++num;
			++ct;
		}
	}
}

void thread3()
{
	while (1)
	{
		if (num >= 26)
			break;
		lock_guard <mutex> lock(mymutex);
		if (num >= 26)
			break;
		if (ct == 3)
		{
			c ='A'+num;
			cout << ct << c << " ";
			++num;
			ct = 1;
		}
	}
	
}

int main()
{
	thread t1(thread1);
	thread t2(thread2);
	thread t3(thread3);

	t1.join();
	t2.join();
	t3.join();
	return 0;
}
```
