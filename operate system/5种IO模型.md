## 5种IO模型

配合钓鱼案例说明：https://blog.csdn.net/ZWE7616175/article/details/80591587?utm_medium=distribute.pc_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-3.nonecase&depth_1-utm_source=distribute.pc_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-3.nonecase

IO主要分为两步：等，数据搬迁。为了提高效率，应尽可能降低等的时间。5种IO模型包括：阻塞IO,非阻塞IO，IO多路复用，信号驱动IO，异步IO。

### 阻塞IO

在内核将数据准备好之前，系统调用会一直等待所有的套接字，默认的是阻塞方式。

### 非阻塞IO

每隔一段时间，客户轮询内核是否准备好数据（文件描述符缓冲区是否就绪），如果准备好了，就进行拷贝数据的操作，如果还没有准备好，也不阻塞程序，内核返回未准备好的信号，
等待用户程序的下一次轮询。这对CPU来说是较大的浪费。

### 信号驱动IO

用户进程告诉内核，当数据准备好时给用户进程发送信号，用户捕捉SIGIO信号，调用信号处理函数获取数据报。

### IO多路复用

IO多路复用使用了select,poll,epoll的方法，可以同时监听多个文件描述符，当有文件描述符就绪时，就将这个文件描述符进行处理。

### 异步IO

当应用程序调用aio_read时，内核一方面去取数据报返回，一方面把控制权还给应用进程，应用进程继续处理其他事情，是一种非阻塞的状态。\
当内核中有数据报就绪时，内核将数据报拷贝到应用程序中，返回aio_read定义好的函数处理程序。
