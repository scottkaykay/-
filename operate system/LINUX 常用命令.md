## linux 常用命令

### 常用的10个命令

· ls 查看目录中的文件\
列出根目录下的所有目录： ls /

列出目前工作目录下所有名称为s开头的文件： ls -ltr s*

列出/bin目录以下所有目录及文件详细资料：ls -lR /bin

· cd /home 进入/home目录   cd .. 返回上一级目录   cd ../.. 返回上两级目录 cd /  返回根目录  cd ~进入用户主目录\
· touch 文件名   创建文件\
· mkdir dir1 创建一个叫'dir1'的目录\
· rmdir dir1 删除一个叫'dir1'的目录\
· rm -f file1 删除一个叫'file1'的文件， -f参数表示忽略不存在的文件\
· rm -rf /mulu 删除mulu下面的文件和子目录下的文件\
· cp /test1/file1 /test3/file2  将/test1下的file1复制到/test3目录，并将文件名改为file2\
· mv /test1/file1 test3/file2   将/test1下的file1移动到/test3目录，并将文件名改为file2\
· mv * ../ 当前目录所有文件移动到上一级目录\
· ps -ef|grep xxx 显示进程pid\
· kill -进程id  先使用ps找到进程的id,然后用kill中止进程\
· tar -xvf file.tar  解压tar包   tar -czvf test.tar.gz a.c 压缩a.c文件成test.tar.gz\
· unzip file.zip 解压zip包\
· zip -q -r html.zip /home/html  将/home/html目录下的所有文件和文件夹打包为当前目录下的html.zip
· unrar file.rar  解压rar包\
· free -m  查看服务器内存使用情况\
· top 查看cpu使用情况，用于实时显示进程的动态\
top: 显示进程信息\
top -c: 显示完整命令\
top -b  以批处理模式显示程序信息\
top -S  以累积模式显示程序信息\
top -p 139显示指定的进程信息\
· lsof -i：端口号  查看端口被哪个进程占用（首先用lsof -i显示符合条件的进程情况）\
· netstat -tunlp 用于显示tcp,udp的端口和进程等相关情况（-t 仅显示tcp相关选项，-u 仅显示udp相关选项， -n拒绝显示别名，能显示数字的全部转化为数字，-l仅列出有listen的服务状态，-p显示建立相关链接的程序名）\
· netstat -tunlp | grep 端口号   用于查看指定端口号的进程情况\
· ifconfig 查看网络设备信息\
· 在当前目录中查找后缀有.file字样的文件中包含test字符串的文件，并打印出该字符串的行： grep test \*file

### ps查看进程

· ps -ef | grep java  查看所有Java进程，grep是搜索关键字\
· ps -aux | grep java   aux显示所有状态\
· ps -ef |less  查看进程信息并通过less分页显示

### kill进程

kill -9 [PID]  -9强迫进程立即停止，pid为进程号，通过命令 ps -ef | grep 查询

### 启动与停止服务

./startup.sh      ./shutdown.sh

### 查看日志

一般的测试项目里，会有个logs的目录文件存放日志，有个xxx.out的文件，可使用tail -f动态实时查看后端日志：\
tail -f xxx.out  屏幕上会动态显示当前日志，Ctrl+c停止

### 查看端口

netstat -anp | grep 端口号    查看端口是否被占用，LISTEN表示被占用，不是LISTENING!

netstat -a  显示详细的网络状况

显示当前用户UDP连接状况： netstat -nu

显示UDP端口号的使用情况： netstat -apu

显示监听的套接口 netstat -l

### find查找文件

· 查找一个文件大小超过5M的文件： find . -type f -size +100M    .表示当前目录\ 
· 如果知道文件名称，如何查找其在Linux下哪个目录： find / -name "\*.c"   或者用locate xxx  来找\
· 按文件特征查找：

find / -amin -10 #查找系统中最后10分钟访问的文件\
find / -atime -2 # 查找系统中最后48小时访问的文件\
find / -size +100M #查找系统中大于100M的文件

-amin n              在过去n分钟内被读过\
-anewer file         比file更晚被读取过的文件\
-atime n             在过去n天内被读过\
-cmin n              在过去n分钟内被修改过\
-cnewer file         比文件file更新的文件\
-ctime n             在过去n天内被修改过\
-empty  空文件\
-ipath p    路径名称符合p的文件，ipath会忽略大小写\
-name name , -iname name 文件名称符合name的文件，iname会忽略大小写\
-size n    文件大小是n单位\
-type c    文件类型是c的文件

### 查看文件

· **cat  由第一行开始显示，并将所有内容输出** \
· tac  从最后一行倒序显示内容，并将所有内容输出\
· more 根据窗口大小，一页一页地显示文件内容\
· less 和more类似，但可以往前翻页，而且可以搜索字符, less 1.log 2.log 查看多个文件，使用n切换到2.log,使用p切换到1.log   
· head  只显示头几行\
· tail  只显示最后几行\
· nl    类似于cat -n  显示时输出行号\
· tailf  类似于tail -f 动态显示日志

· cat 将文件从第一行开始连续的内容输出到屏幕上，但cat不常用，因为文件很大时，行数比较多时，屏幕无法容下全部内容，只能看到一部分内容。语法： cat [-n] 文件名\
· more与less：more将文件从第一行开始，根据输出窗口大小，适当地输出内容到屏幕。当一页无法全部输出时，可以用回车键向下翻行，用空格键向下翻页。less支持向前翻页和向后翻页(pageup,pagedown),而且可以
搜索内容，如果内容存在于文本，则会以高亮形式显示。\
· head ，tail:  语法： head [n number] 文件名    number表示显示的行数， head -n 5 runoob.log 显示runoob.log文件的前5行\
·tailf 特别适合于日志文件的跟踪，与tail -f不同的是，如果文件不增长，它不回去访问磁盘文件，减少了磁盘访问，更省能源。

tail -f note.log   跟踪note.log文件的增长情况\
tail +20 bite.log  从第20行至文件末尾\
tail -c 10 note.log  显示文件note.log的最后10个字符

附tail参数表：\
-f  循环读取\
-q  不显示处理信息\
-v  显示详细的处理信息\
-c<数目>  显示的字节数\
-n<行数>  显示文件的尾部n行内容\
--pid=PID  与-f合用，表示在进程ID,PID死掉之后结束

## Linux awk命令

awk是一个强大的文本分析工具。相对于grep的查找， sed的编辑，awk在对数据分析并生成报告时显得尤为强大。简单来说，awk将文件逐行地读入，以空格为默认分隔符将每行切片，切开的部分再进行分析。\
                    使用方法： awk '{ pattern+action }' {filenames}

pattern表示awk在数据中查找的内容，action表示在找到匹配内容时所执行的一系列命令。pattern就是要表示的正则表达式，用斜杠括起来。通常，awk以文件的一行为处理单位，awk每接收文件的一行，然后执行相应的命令。

### 常用命令

· 搜索/etc/passwd有root关键字的**所有行**\
awk '/root/' /etc/passwd

· 搜索.etc/passwd 有root关键字的行，并显示对应的shell\
awk -F: '/root/ {print $7}' /etc/passwd

· 统计.etc/passwd :文件名，每行行号，每行的列数，对应的完整行内容\
· awk -F: '{printf ("filename:%10s, linenumber:%3s,column:%3s,content:%3f\n",FILENAME,NR,NF,$0)}}' /etc/passwd

· 统计/etc/passwd 的第二行信息\
· awk -F: 'NR==2(print "filename: "FILENAME,$0}' /etc/passwd
