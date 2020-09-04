## slect poll epoll解释

参考：https://blog.csdn.net/sszgg2006/article/details/38664789?utm_medium=distribute.pc_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-2.channel_param&depth_1-utm_source=distribute.pc_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-2.channel_param

网卡接收数据，经过硬件电路的传输，把数据写入内存，向CPU发送信号，调用网卡中断处理程序处理数据。

### 进程阻塞为什么不占用CPU资源

简单说，被阻塞的进程会进入到操作系统的等待队列中，不会被CPU执行，CPU会分时执行工作队列中的进程，所以阻塞的进程不会占用CPU资源。\
select，poll，epoll都是阻塞方法，进程阻塞是进程调度的重要一环，指的是进程在等待某事件发生前的等待状态。

操作系统添加等待队列只是添加了对这个等待中进程的引用，以便在接收到数据时获取进程对象并将其唤醒。

### 如何高效地监视多个socket？ 使用EPOLL

预先传入一个socket列表，如果列表中的socket都没有数据，则挂起进程，直到有一个socket收到数据，唤醒进程。这是select的设计思想。

准备一个数组fds,让fds存放所有要监视的socket，然后调用select，如果fds中的所有socket都没有数据，select会阻塞，直到有一个socket接收到数据，select返回，唤醒进程。具体是哪个socket收到了数据，可以通过**遍历的方式**。
，注意这里的遍历，一旦socket数量很多，开销是相当大的。

### select流程

假如进程A运行的是服务端的socket程序，建立连接后，调用rev准备接收数据。如果程序监视了3个socket，在调用select后，操作系统把进程A 的引用加入socket1,socket2,
socket3的等待队列，如果socket2收到了数据，中断程序会唤醒进程A，将进程A**从所有的等待队列溢出，加入到工作队列里面。**
虽然简单，缺点显而易见：\
· 每次调用select都需要将进程加入到所有监视socket的等待队列，每次唤醒都要从等待队列中移出。总共涉及两次遍历，每次都需要将整个fds列表传给内核。正因为遍历的开销大，所以select规定了
socket的最大监视数量，默认为1024个\
· 进程被唤醒后，程序不知道是哪个socket收到了数据，还要遍历一次\

当程序调用select时，内核会先遍历一遍socket，如果有一个以上的socket缓冲区有数据就直接返回，不会阻塞。

```C++
int s=socket(AF_INET,SOCK_STREAM,0);
bind(s,...);
listen(s,...);
int fds[]=存放需要监听的socket
while(1)
{
    int n=select(...,fds,...);
    //遍历
    for(int i=0;i<fds.count;i++)
    {
        if(FD_ISSET(fds[i],...))
        {
            //fds[i]的数据处理
        }
    }
}
```

### epoll设计思路

i. 功能分离

每次select调用，都是维护等待队列+阻塞进程，   epoll将这两个操作分开，每次调用使用epoll_ctl维护等待队列，epoll_wait阻塞进程，大大地提升了效率。
```C++
int s =socket(AF_INET,SOCK_STREAM,0);
bind(s,...);
listen(s,...);
int epfd=epoll_create(...); //使用epoll_create创建一个epoll对象epfd
epoll_ctl(epfd,...);//通过epoll_ctl将需要监视的socket加入epfd中，内核会将epfd对象添加到所有监视的socket的等待队列中

while(1)
{
    int n=epoll_wait(...); //调用epoll_wait等待数据
    for(接收到数据的socket)
    {
        //数据处理
    }
}
```

socket收到数据后，中断程序会操作epfd对象，而不是直接操作进程。收到数据后，中断程序会给epfd的就绪列表添加socket引用。epfd对象相当于socket和进程间的中介，
socket数据接收不影响进程，而是通过改变epfd镀锡的就绪列表来改变进程状态。当调用epoll_wait时，如果就绪列表已经引用了socket，呢么epoll_wait直接返回，如果就绪列表为空，
则阻塞进程。

ii. 就绪列表

select低效的原因之一是程序不知道是哪个socket收到数据，只能一个个遍历。如果内核维护一个就绪列表，引用收到数据的socket，即可避免遍历。如前文中的例子，
如果是socket2和socket3 收到了数据，就会被就绪列表rdlist引用。进程被唤醒后，只要获取rdlist的内容，就能知道哪些socket收到了数据。

iii.epoll的实现细节

就绪列表引用就绪的socket，应该能快速地插入数据。程序可能随时调用epoll_ctl添加删除socket，删除socket时，如果socket已经存放在就绪列表中，那也应该同时删除。所以，
就绪列表应该具备快速插入删除的能力。双向链表就是符合要求的数据结构。

利用红黑树保存监视的socket，epoll使用了红黑树作为索引结构。

## 总结

· select,poll的实现需要自己不断轮询所有fd集合，直到设备就绪，期间可能要睡眠和唤醒多次交替。epoll也需要调用epoll_wait不断轮询就绪链表，期间也可能多次睡眠和唤醒交替，但是其只需遍历就绪链表，不用遍历所有的文件描述符。\
· slect,poll每次调用都要把文件描述符集合从用户态往内核态拷贝一次，并且把current往设备等待队列中挂一次，epoll只需要一次拷贝，把current挂上epoll内部定义的等待队列),也能节省不少开销。\
· select受文件句柄fd的数量限制，默认是1024，岁可以修改系统参数，但还是有数量限制\
· 就绪数据需要内核态到用户态的拷贝\
· poll类似于slect,用一个阻塞函数来同时监听多个文件描述符，为解决select的句柄数量限制问题，poll引入了一个单独的数据结构pollfd,用来存放每个线程的文件描述符，一旦线程文件描述符注册到pollfd后，就能关闭掉fd,缺点是仍需要扫描pollfd里的所有fd,判断哪些fd已就绪。就绪数据需要从内核拷贝到用户态。
