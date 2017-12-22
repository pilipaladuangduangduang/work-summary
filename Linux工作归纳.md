# Linux工作归纳

标签（空格分隔）： 工作总结

---

## 在该笔记中总结工作开发过程中和Linux相关的内容：

### 查看当前Linux版本

``` linux
cat /etc/issue
``` 

### Linux中对软件包的配置、编译、安装

``` linux
//这三部分别是对软件包的配置、编译、安装
./configure make make install
``` 

### CentOS5之后不支持apt-get命令

``` linux
// centos5之后，要这样下载安装包
$ sudo yum install <packagename>
// centos5之后，就不在支持apt-get下载安装包了
$ sudo aptitude install <packagename>
``` 

### 两台linux主机传输文件

``` 
// 从远程主机拷贝文件到本地主机，输入完后还要输入远程主机的密码
scp root@120.26.4.202:/opt/niezhichun/hello.txt /opt/niezhichun/

// 从本地主机拷贝文件到远程主机， -r 表示传输文件夹
scp -r /opt/niezhichun/AdminXingBook/ root@120.26.4.202:/opt/niezhichun/
``` 

### linux上清空memcache缓存

``` 
// memcache默认端口号为11211
// 打开端口，输入flush_all清空所有缓存，quit关闭端口
telnet localhost 11211
flush_all
quit
``` 

### Linux环境下简单操作redis

 > 和操作memcached差不多

 > 1. 利用redis-cli建立连接： redis-cli -h localhost

 > 2. 一般都会有密码： auth password

 > 3. 可以查看所有的key： keys *

 > 4. 清空所有的key： flushall
 
### linux中查询某一程序所使用的端口

```
1、首先ps -ef | grep sphinx查看指定程序的PID
2、然后netstat -apn | grep PID查看该程序占用的端口号
```

### 修改/etc/profile

 > 修改了/etc/profile之后（因为改错了某些环境变量...）发现很多命令都无法使用了（ls、vi等），需要/bin/vi /etc/profile修复，然后并重启机器~
 
### CentOS7中创建新用户

```
adduser niezhic
passwd niezhic 之后重复输入两次密码
gpasswd -a niezhic wheel 之后niezhic这个用户就有和root一样的权限了
```

### CentOS7添加命令的alias

```
vi ~/.bashrc
# 修改python默认编译的版本
alias python='/usr/local/bin/python3.5'
source ~/.bashrc
```

### Ubuntu操作mysql

```
启动：sudo /etc/init.d/mysql start 
停止：sudo /etc/init.d/mysql stop 
重启：sudo /etc/init.d/mysql restart
```

### 远程登录Ubuntu的MySQL

Ubuntu中默认安装MySQL是允许远程登录的，需要如下操作：

```
vi /etc/mysql/my.cnf
将bind-address设为0.0.0.0
然后重启mysql即可
```

### sudo 出现unable to resolve host 解决方法

修复很简单，直接修改/etc/hosts配置文件中127.0.01对应的名称。

### 查看某个进程中的线程数目

``` bash shell
ps huH p <PID> | wc -l
```