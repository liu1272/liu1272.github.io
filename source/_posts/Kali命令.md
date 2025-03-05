title: Kali常用命令
author: liuGuobin
tags:
  - 附录
categories:
  - 附录
date: 2022-11-21 13:12:00
---
# kali的常用命令
### 1.基础命令
```
passwd 修改密码

passwd root 修改root用户密码

date 显示系统日期

sudo 后面加命令 就可以调用管理权限

apt-get update 更新软件列表
访问源列表里的每个网址，并读取软件列表，然后保存在本地电脑。我们在新立得软件包管理器里看到的软件列表，都是通过update命令更新的。

apt-get upgrade 更新软件
把本地已安装的软件，与刚下载的软件列表里对应软件进行对比，如果发现已安装的软件版本太低，就会提示你更新。

apt-get dist-upgrade 更新软件  

--+--+--+--+--+--+--+--+--+--+--+--+--+--+--+--+--+--+--+--+--
【apt-get upgrade 与 apt-get dist-upgrade 的区别】
upgrade:系统将现有的Package升级,如果有相依性的问题,而此相依性需要安装其它新的Package或影响到其它Package的相依性时,此Package就不会被升级,会保留下来.

dist-upgrade:如果有相依性问题,需要安装/移除新的Package,就会试着去安装/移除它.
(所以通常这个会被认为是有点风险的升级)

apt-get upgrade 和 apt-get dist-upgrade 本质上是没有什么不同的，只不过dist-upgrade会识别出当依赖关系改变的情形并作出处理，而upgrade对此情形不处理。

例如软件包 a 原先依赖 b c d，但是在源里面可能已经升级了，现在是 a 依赖 b c e。这种情况下，dist-upgrade 会删除 d 安装 e，并把 a 软件包升级，而 upgrade 会认为依赖关系改变而拒绝升级 a软件包。
--+--+--+--+--+--+--+--+--+--+--+--+--+--+--+--+--+--+--+--+--

clear 清屏

sudo gpedit 特权调用gpedit编辑器

reboot 重启

apt-get install XXXXX 安装缺少的依赖包，（XXX）里面输入缺少依赖包的名字

apt-get autoremove –purge 软件名 删除包及其依赖的软件包+配置文件等

apt-get install +模块名 这种方法也可以安装模块,或者apt-get install python-模块名

firefox 浏览器打开

shutdown -h now 关闭系统(1)

init 0 关闭系统(2)

telinit 0 关闭系统(3)

shutdown -h hours:minutes & 按预定时间关闭系统

shutdown -c 取消按预定时间关闭系统

shutdown -r now 重启(1)

reboot 重启(2)

su -l 切换登陆用户

logout 注销用户登陆

pwd 当前路径
```

### 2.文件夹或文本操作
```
cd .. 返回上一级目录

cd ../.. 返回上级两级目录

cd - 返回上次所在的目录

cd 文件夹名  进去文件夹

cd ~ 进去当前用户主目录

pwd 显示当前工作路径

touch 文件名  常见文件

cat 文件名 读取文档内容

mkdir 文件名 创建文件夹

mkdir -p 文件夹 递归创建文件夹

cp 复制命令

cp -r moon moons 复制moon 文件夹到当前 moons 如果有里面有文件 使用-r 

mv 移动命令

mv moon moons 也可以说是改名

mkdir 文件名 文件名 创建多个文件夹

ls 显示文件和文件夹名

ls -a 显示所有的文件和文件夹 包含隐藏文件

ls -l 显示文件和目录的详细资料

rm -f 文件  删除文件

rm 文件夹名 删除文件夹

rm -rf  文件/文件夹  删除文件夹 及其目录下的文件

bzip2 file1 压缩一个叫做 ‘file1’ 的文件

gunzip file1.gz 解压一个叫做 ‘file1.gz’的文件

gzip file1 压缩一个叫做 ‘file1’的文件

gzip -9 file1 最大程度压缩

rar a file1.rar test_file 创建一个叫做 ‘file1.rar’ 的包

rar x file1.rar 解压rar包

unrar x file1.rar 解压rar包

dpkg -i package.deb 安装/更新一个 deb 包

dpkg -r package_name 从系统删除一个 deb 包

dpkg -l 显示系统中所有已经安装的 deb 包

dpkg -l | grep httpd 显示所有名称中包含 “httpd” 字样的deb包

dpkg -s package_name 获得已经安装在系统中一个特殊包的信息

dpkg -L package_name 显示系统中已经安装的一个deb包所提供的文件列表

dpkg –contents package.deb 显示尚未安装的一个包所提供的文件列表

tar zcvf +压缩文件名 压缩文件

tar zxvf +解压包名 解压文件

tar -jcvf renwolesshel.tar.bz2 打包

tar jxvf renwolesshel.tar.bz2 解压

zip -q -r renwolesshel.zip renwolesshel/ 解压zip格式的压缩包

unzip renwolesshel.zip

which 加软件名 可以查出软件目录在哪

./+文件名或者apt-get +文件名 安装文件
```

### 3.系统信息
```
arch 显示机器的处理器架构(1)

uname -m 显示机器的处理器架构(2)

uname -r 显示正在使用的内核版本

cat /proc/version 显示内核的版本

cat /proc/net/dev 显示网络适配器及统计

cat /proc/cpuinfo 显示CPU info的信息

cat /proc/mounts 显示已加载的文件系统
```

### 4.vi 常用命令
vi编辑器分为三种状态，分别是：
1.命令模式（command mode）
控制屏幕光标的移动，字符、字或行的删除，移动复制某区段及进入Insert mode下，或者到 last line mode。
2.插入模式（Insert mode）
只有在Insert mode下，才可以做文字输入，按「ESC」键可回到命令行模式。
3.底行模式（last line mode）
将文件保存或退出vi，也可以设置编辑环境，如寻找字符串、列出行号……等。
```
**打开**
vi filename :打开或新建文件，并将光标置于第一行首
vi +n filename ：打开文件，并将光标置于第n行首
vi + filename ：打开文件，并将光标置于最后一行首

**编辑**
i 在当前位置生前插入
I 在当前行首插入
a 在当前位置后插入
A 在当前行尾插入
o 在当前行之后插入一行
O 在当前行之前插入一行
编辑结束，按Esc返回命令模式

**基本查找**
/text　　查找text，按n健查找下一个，按N健查找前一个。
?text　　查找text，反向查找，按n健查找下一个，按N健查找前一个。
查找很长的词，如果一个词很长，键入麻烦，可以将光标移动到该词上，按*或#键即可以该单词进行搜索，相当于/搜索。而#命令相当于?搜索。

**删除**
删除当前行 dd
删除2行 2dd

**拷贝和粘贴**
yy 拷贝当前行
nyy 拷贝当前后开始的n行，比如2yy拷贝当前行及其下一行。
p  在当前光标后粘贴
区块拷贝
1. 将光标移动到要复制的文本开始的地方，按 v进入可视模式。
2. 将光标移动到要复制的文本的结束的地方，按y复制。此时vim会自动将光标定位到选中文本的开始的地方，并退出可视模式。
3. 我移动光标到文本结束的地方，按 p粘贴。

**退出命令**
:wq 保存并退出
:q! 强制退出并忽略所有更改
:e! 放弃所有修改，并打开原来文件
:w保存修改

**显示行号**
set number
set nu
输入:n，代表跳转到第n行，如:100，就跳转到第100行。

撤销操作
u
——> vim与vi的区别
——> vim是vi的延申 vim编辑源码会高亮 vim的命令对vi是完全兼容的
```

### 5.文件搜索
```
find / -name file1 从 ‘/’ 开始进入根文件系统搜索文件和目录

find / -user user1 搜索属于用户 ‘user1’ 的文件和目录

find /home/user1 -name *.bin 在目录 ‘/ home/user1’ 中搜索带有’.bin’ 结尾的文件

find /usr/bin -type f -atime +100 搜索在过去100天内未被使用过的执行文件

find /usr/bin -type f -mtime -10 搜索在10天内被创建或者修改过的文件

find . -name '*.php' -mmin -30  查找最近30分钟修改的当前目录下的.php文件

find . -name '*.php' -mtime 0  查找最近24小时修改的当前目录下的.php文件

find . -name '*.inc' -mtime 0 -ls  查找最近24小时修改的当前目录下的.php文件，并列出详细信息

find . -type f -mtime 1  查找当前目录下，最近24-48小时修改过的常规文件。

find . -type f -mtime +1  查找当前目录下，最近1天前修改过的常规文件。

-atime 文件访问时间

-ctime 文件常见时间

-mtime文件修改时间

-mtime n : n为数字，意思为在n天之前的“一天之内”被更改过内容的文件

-mtime +n : 列出在n天之前（不含n天本身）被更改过内容的文件名

-mtime -n : 列出在n天之内（含n天本身）被更改过内容的文件名

-type f 代表一个普通格式的文件 二进制文件

-type d 代表文件夹

find / -name *.rpm -exec chmod 755 ‘{}’ ; 搜索以 ‘.rpm’ 结尾的文件并定义其权限
--+--+--+--+--+--+--+--+--+--+--+--+--+--+--+--+--+--+--+--+--
文件中r w x -的含义:
r是只读权限，w是写的权限，x是可执行权限,-是没有任何权限。
目录中r w x的含义:
r具有ls权限，w是在目录上可以增加、删除、创建权限，x是具有ls –l权限。

权限     读    写   执行        读    写   执行      读   写   执行
字符     r     w     x           r     w    x         r    w    x
数字     4     2     1           4     2    1         4    2    1
分配     对象文件所有者          文件所属组用户         其他用户

chmod 777 +文件名 给单独文件赋所有权限
chmod -R 777 /home/user  表示将整个/home/user目录与其中的文件和子目录的权限都设置为rwxrwxrwx

常用权限
-rw------- (600) 只有所有者才有读和写的权限
-rw-r--r-- (644) 只有所有者才有读和写的权限，组群和其他人只有读的权限
-rwx------ (700) 只有所有者才有读，写，执行的权限
-rwxr-xr-x (755) 只有所有者才有读，写，执行的权限，组群和其他人只有读和执行的权限
-rwx--x--x (711) 只有所有者才有读，写，执行的权限，组群和其他人只有执行的权限
-rw-rw-rw- (666) 每个人都有读写的权限
-rwxrwxrwx (777) 每个人都有读写和执行的权限

也可以使用字母模式
chmod +x 和chmod a+x 是一样的，一般没有明确要求，可以就用chmod +x
u代表用户   g代表用户组   o代表其它   a代表所有

创建一个文件只有用户具有完全控制的权限
touch 文件名
chmod u+rwx 文件名 当前用户加全部的权限
chmod g-r 文件名   群组把读取权限去掉
chmod o-r 文件名   其他用户读取权限去掉

--+--+--+--+--+--+--+--+--+--+--+--+--+--+--+--+--+--+--+--+--

find / -xdev -name *.rpm 搜索以 ‘.rpm’ 结尾的文件，忽略光驱、捷盘等可移动设备

locate *.ps 寻找以 ‘.ps’ 结尾的文件 - 先运行 ‘updatedb’ 命令

whereis halt 显示一个二进制文件、源码或man的位置

which halt 显示一个二进制文件或可执行文件的完整路径

grep kali /etc/passwd grep关键词搜索

find / -name moonsec 2>/dev/null   搜索某个文件/文件夹 屏蔽出错信息
```
### 6.用户和群组
```
groupadd group_name 创建一个新用户组

groupdel group_name 删除一个用户组

cat /etc/group | grep group_name 

usermod -a -G moontea k1把用户k1附加其他用户组

useradd moonsec

passwd moonsec

useradd -r -m -s /bin/bash moonsec

参数的意思：
-r 建立系统账号
-m 自动建立用户的登入目录
-s /bin/bash 指定用户登入后所使用的shell

su - root 切换到root用户

su 用户名 切换到普通用户
```

### 7.系统管理
```
top  查看正在指向的程序信息

ps -ef 查看所有进程信息

ps -ef | grep tomcat 查找指定进程

kill -9 id 强制杀死进程

env 当前的变量

查看系统变量

echo $PATH

hostname 查看主机名

history 查看历史命令
```

### 8.查看磁盘信息
```
df   默认不带单位，挂载点、总容量、已用、可用

df  -h   友好显示（带单位），很多命令的友好显示都是-h
```

### 9.查看内存信息
```
free   默认以KB为单位，总容量、已用、可用、缓存大小

free  -m   默认以MB为单位

free  -h   上面2种都不带单位、只显示数值，这种是友好显示的，会带单位G、M等
```

### 10.查看环境变量
```
echo  $JAVA_HOME
echo $PATH | grep jdk
```

### 11.开机自启
有很多程序往往都需要设置开机自启，比如tomcat、mysql、nginx等。
```
vim /etc/rc.d/rc.local   在/etc/rc.d/rc.local文件中加入开机要自动执行的程序

mount -t smbfs -o username=user,password=pass //WinClient/share /mnt/share   挂载一个windows网络共享
```

### 12.网卡配置
```
vi /etc/network/interfaces

auto eth0                     //指定网卡（根据ifconfig结果修改）

iface eth0 inet static        //启动静态ip

address 192.168.0.66          // 设置静态ip

netmask 255.255.255.0         //子网掩码

gateway 192.168.0.1           //指定网关

service networking restart    //重启网络

systemctl restart networking  //重启网络

ifconfig eth0 192.168.0.33    //设置临时ip
```

### 13网卡DNS服务器
```
vi /etc/resovl.conf

nameserver 114.114.114.114

重启网卡

service networking restart

systemctl restart networking

注意有时候重启才生效
```

### 14.服务相关  
```
apache2服务器
打开：/etc/init.d/apache2 start 
重启：/etc/init.d/apache2 restart
关闭：/etc/init.d/apache2 stop

/etc/init.d/network start  启动网卡

echo 1 > /proc/sys/net/ipv4/ip_forward  开启路由转发
```

### 15.linux常用网络命令
网络和监控命令类似于这些： hostname, ping, ifconfig, iwconfig, netstat, nslookup, traceroute, finger, telnet, ethtool 
用于查看 linux 服务器 ip 地址，管理服务器网络配置，通过 telnet 和 ethernet 建立与 linux 之间的网络链接，查看 linux 的服务器信息等。
```
hostname 查看本机名

ping  一般用于测试网络是否通 内网icmp

ping www.baidu.com -c 3 执行3次

ifconfig 网卡信息

iwconfig 用于配置或获取无线网络设备信息

netstat 
-a (all)显示所有选项，默认不显示LISTEN相关
-t (tcp)仅显示tcp相关选项
-u (udp)仅显示udp相关选项
-n 拒绝显示别名，能显示数字的全部转化成数字。
-l 仅列出有在 Listen (监听) 的服務状态
-p 显示建立相关链接的程序名
-r 显示路由信息，路由表
-e 显示扩展信息，例如uid等
-s 按各个协议进行统计
-c 每隔一个固定时间，执行该netstat命令。

提示：LISTEN和LISTENING的状态只有用-a或者-l才能看到

netstat -ano
-an 查看当前所有已连接端口
-nlpt 查看当前所有tcp端口
-nlpu 查看当前所有udp端口
-nlpt | grep 80 查看所有80端口使用情况

netstat -lntup 查看所有的tcp和upd端口

nslookup 
查询其他记录
直接查询返回的是A记录，我们可以指定参数，查询其他记录，比如AAAA、MX等。

nslookup -qt=type domain [dns-server]
其中，type可以是以下这些类型：
A 地址记录
AAAA 地址记录
AFSDB Andrew文件系统数据库服务器记录
ATMA ATM地址记录
CNAME 别名记录
HINFO 硬件配置记录，包括CPU、操作系统信息
ISDN 域名对应的ISDN号码
MB 存放指定邮箱的服务器
MG 邮件组记录
MINFO 邮件组和邮箱的信息记录
MR 改名的邮箱记录
MX 邮件服务器记录
NS 名字服务器记录
PTR 反向记录
RP 负责人记录
RT 路由穿透记录
SRV TCP服务器信息记录
TXT 域名对应的文本信息
X25 域名对应的X.25地址记录

traceroute
-d 使用Socket层级的排错功能。
-f 设置第一个检测数据包的存活数值TTL的大小。
-F 设置勿离断位。
-g 设置来源路由网关，最多可设置8个。
-i 使用指定的网络界面送出数据包。
-I 使用ICMP回应取代UDP资料信息。
-m 设置检测数据包的最大存活数值TTL的大小。
-n 直接使用IP地址而非主机名称。
-p 设置UDP传输协议的通信端口。
-r 忽略普通的Routing Table，直接将数据包送到远端主机上。
-s 设置本地主机送出数据包的IP地址。
-t 设置检测数据包的TOS数值。
-v 详细显示指令的执行过程。
-w 设置等待远端主机回报的时间。
-x 开启或关闭数据包的正确性检验。
(traceroute是用来检测发出数据包的主机到目标主机之间所经过的网关数量的工具。)
(traceroute的原理是试图以最小的TTL（存活时间）发出探测包来跟踪数据包到达目标主机所经过的网关，然后监听一个来自网关ICMP的应答。发送数据包的大小默认为38个字节。)

telnet
Linux telnet命令用于远端登入。
执行telnet指令开启终端机阶段作业，并登入远端主机。
现在多数用于检测某个端口是否开放

Finger
Finger是一种用户信息分享服务。它工作在TCP 79端口，可以用来公开用户的特定信息。Nmap的finger的脚本可以向Finger服务器发送请求，查询并获取用户的相关信息，如登录名、用户名、TTY类型、登录时间等。

ethtool 是用于查询及设置网卡参数的命令
```

### 16.防火墙iptables
```
iptalbes -L  查看防火墙规则

iptables -F   清除规则

/etc/init.d/iptables stop   关闭防火墙

service iptables stop

安装：apt-get install ufw

关闭：ufw disable # To disable the firewall

开启：ufw enable # To enable the firewall
```