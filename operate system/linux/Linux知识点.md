## SSH与FTP服务权限修改

允许下载，拒绝登录ssh:\
vi /etc/passwdzhang1:x:500:500::/home/zhang1:/sbin/nologin

允许ftp拒绝ssh: usermod -s /sbin/nologin name

允许ftp与ssh： usermod -s /bin/bash name

不允许ssh与ftp: vim /etc/ssh/sshd_config

修改配置文件：\
# 禁止用户user1登陆，多个用户空格分隔： DenyUsers user1

禁止用户组group1的所有用户登录，多个空格分离： DenyGroups group1

## 文件权限类型 chmod

linux下文件权限类型一般包括读，写，执行。读(r=4),写(w=2),执行(x=1). 于是，可读可写可执行(4+2+1=7)

chmod 755  三位数字分别代表文件所有者，文件所有者所在用户组的其他用户，其他用户组的用户

u 代表用户， g 代表用户组 ， o代表其他， a代表所有  +x表示应用于所有标签

系统管理员编写扫描临时文件的shell程序tmpsc.sh, 测试该程序时提示拒绝执行，解决的方法有：\
chmod 755 tmpsc.sh

chmod a+x tmpsc.sh

chmod u+x tmpsc.sh

