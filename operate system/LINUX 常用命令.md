## linux 常用命令

### 常用的10个命令

· ls 查看目录中的文件\
· cd /home 进入/home目录   cd .. 返回上一级目录   cd ../.. 返回上两级目录 cd /  返回根目录  cd ~进入用户主目录\
· mkdir dir1 创建一个叫'dir1'的目录\
· rmdir dir1 删除一个叫'dir1'的目录\
· rm -f file1 删除一个叫'file1'的文件， -f参数表示忽略不存在的文件\
· rm -rf /mulu 删除mulu下面的文件和子目录下的文件\
· cp /test1/file1 /test3/file2  将/test1下的file1复制到/test3目录，并将文件名改为file2\
· mv /test1/file1 test3/file2   将/test1下的file1移动到/test3目录，并将文件名改为file2\
· mv * ../ 当前目录所有文件移动到上一级目录\
· ps -ef|grep xxx 显示进程pid\
· kill -进程id  先使用ps找到进程的id,然后用kill中止进程\
· tar -xvf file.tar  解压tar包\
· unzip file.zip 解压zip包\
· unrar file.rar  解压rar包\
· free -m  查看服务器内存使用情况

### ps查看进程

· ps -ef | grep java  查看所有Java进程，grep是搜索关键字\
· ps -aux | grep java   aux显示所有状态

### kill进程

kill -9 [PID]  -9强迫进程立即停止，pid为进程号，通过命令 ps -ef | grep 查询

### 启动与停止服务

./startup.sh      ./shutdown.sh

### 查看日志

一般的测试项目里，会有个logs的目录文件存放日志，有个xxx.out的文件，可使用tail -f动态实时查看后端日志：\
tail -f xxx.out  屏幕上会动态显示当前日志，Ctrl+c停止

### 查看端口

netstat -anp | grep 端口号    查看端口是否被占用，LISTEN表示被占用，不是LISTENING!

### find查找文件

· 查找一个文件大小超过5M的文件： find . -type f -size +100M    .表示当前目录\ 
· 如果知道文件名称，如何查找其在Linux下哪个目录： find / -name xxx  或者用locate xxx  来找\
· 按文件特征查找：

find / -amin -10 #查找系统中最后10分钟访问的文件\
find / -atime -2 # 查找系统中最后48小时访问的文件\
find / -size +100M #查找系统中大于100M的文件

### 查看文件

· **cat  由第一行开始显示，并将所有内容输出** \
· tac  从最后一行倒序显示内容，并将所有内容输出\
· more 根据窗口大小，一页一页地显示文件内容\
· less 和more类似，但可以往前翻页，而且可以搜索字符\
· head  只显示头几行\
· tail  只显示最后几行\
· nl    类似于cat -n  显示时输出行号\
· tailf  类似于tail -f 动态显示日志

· cat 将文件从第一行开始连续的内容输出到屏幕上，但cat不常用，因为文件很大时，行数比较多时，屏幕无法容下全部内容，只能看到一部分内容。语法： cat [-n] 文件名\
· more与less：more将文件从第一行开始，根据输出窗口大小，适当地输出内容到屏幕。当一页无法全部输出时，可以用回车键向下翻行，用空格键向下翻页。less支持向前翻页和向后翻页(pageup,pagedown),而且可以
搜索内容，如果内容存在于文本，则会以高亮形式显示。\
· head ，tail:  语法： head [n number] 文件名    number表示显示的行数\
·tailf 特别适合于日志文件的跟踪，与tail -f不同的是，如果文件不增长，它不回去访问磁盘文件，减少了磁盘访问，更省能源。
