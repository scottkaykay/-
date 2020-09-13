## fork进程创建函数

一个进程包括代码，数据和分配给进程的资源。fork()函数通过系统调用创建一个与原来进程几乎完全相同的进程，即这两个进程可以做完全相同的事情，如果初始参数或者
传入的变量不同，两个进程也可以做不同的事。

进程调用fork()函数后，系统先给新的进程分配资源，如存储数据和代码的空间。然后把原来进程的所有值复制到新的进程中，只有少数值和原来的进程不同。类似于克隆。

父进程中返回子进程的进程号，在子进程中返回0

```C++
int main(int argc, char *argv[])
{
    pid_t pid; 
    int cnt = 0;

    pid = fork();

    if (pid == -1) {
        perror("fork error");
        exit(1);
    } else if (pid == 0) {
        printf("The returned value is %d\nIn child process!!\nMy PID is %d\n",
        pid, getpid());
        cnt++;
    } else {
        printf("The returned value is %d\nIn father process!!\nMy PID is %d\n",
        pid, getpid());
        cnt++;
    }
    printf("cnt = %d\n", cnt);

    return 0;
}
//运行结果是：
     //The returned value is 20473
    // In father process!!
    // My PID is 20472
    // cnt = 1
    // The returned value is 0
    // In child process!!
    // My PID is 20473
    // cnt = 1
```
在语句pid=fork()之前，只有一个进程在执行，这条语句之后就有两个进程在执行了，这两个进程几乎完全相同，将要执行的下一条语句都是if(pid==-1),两个进程的进程号不同，主要与fork的函数特性有关，fork被调用一次，却能返回两次，可能有三种不同的返回值\
· 父进程中，fork返回创建子进程的进程ID\
· 子进程中，fork返回0\
· 如果出现错误，fork返回-1

可以通过fork返回值判断是父进程还是子进程。进程号的值为什么不同，其实就相当于链表，进程形成了链表，父进程的进程号指向子进程的进程号，由于子进程没有子进程，所以其进程号为0

fork出错的原因：\
· 当前进程数已经达到了系统规定的上限，这时errno的值被置为EAGAIN\
· 系统内存不足，这时errno的值被设置为ENOMEM

创建新进程成功后，系统中出现两个基本完全相同的进程，子进程从父进程处继承了整个进程的地址空间，包括进程上下文，代码段，进程堆栈，内存信息，打开的文件描述符，当前工作目录等等。父子进程不共享存储空间的部分，父子进程共享正文段，两个进程执行没有固定的先后顺序，要看系统的进程调度策略。

父子进程中cnt变量是独立的，互不影响
