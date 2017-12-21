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

### 为git用户分配/opt/gitrepo/目录的所有权限

```
# 这样一来git用户就可以在该目录下创建删除文件了
chown -R git:git /opt/gitrepo/
```

### Ubuntu中git用户不能使用sudo

今天在使用git用户执行sudo命令时遇到这个：git is not in the sudoers file.  This incident will be reported.

```
# 首先切到root用户
su root
# 查看 sudoers文件在哪里
whereis sudoers
sudoers: /etc/sudoers.d /etc/sudoers /usr/share/man/man5/sudoers.5.gz
# 更改/etc/sudoers的权限
chmod u+w /etc/sudoers
# 编辑/etc/sudoers文件
vi /etc/sudoers
# 找到root    ALL=(ALL:ALL) ALL并在下面添加git     ALL=(ALL:ALL) ALL然后保存退出
# 最后再次更改/etc/sudoers文件的权限
chmod u-w /etc/sudoers
```

### sudo 出现unable to resolve host 解决方法

修复很简单，直接修改/etc/hosts配置文件中127.0.01对应的名称。

### ubuntu16.04 install redis

ubuntu16.04 不能直接 apt-get install redis，所以就需要手动下载安装启动了~

To install redis follow these steps:

Set up a non-root user with sudo privileges
Install build and test dependencies:
 - sudo apt update
 - sudo apt full-upgrade
 - sudo apt install build-essential tcl
Set up redis:
Download latest copy via this link or with this
 - curl -O http://download.redis.io/redis-stable.tar.gz
Create a temporary folder for it in say your /home/username/redis-stable directory
Move into created folder and extract it
 - tar xzvf redis-stable.tar.gz
Change into the folder cd redis-stable and build it with
 - make
 - make test
 - sudo make install
Configure redis:
Create configuration directory:
 - sudo mkdir /etc/redis
Move sample redis configuration file:
 - sudo cp /home/george/redis-stable/redis.conf /etc/redis
Edit the file:
 - sudo nano /etc/redis/redis.conf # or with any other text editor
Make two changes there:
supervised no to supervised systemd
dir to dir /var/lib/redis # for persistent data dump
Set up the systemd unit file:

 - sudo nano /etc/systemd/system/redis.service
Add the text:
[Unit]
Description=Redis In-Memory Data Store
After=network.target

[Service]
User=redis
Group=redis
ExecStart=/usr/local/bin/redis-server /etc/redis/redis.conf
ExecStop=/usr/local/bin/redis-cli shutdown
Restart=always

[Install]
WantedBy=multi-user.target
Set up redis user, groups and directories:

create redis user and group with same ID but no home directory:
sudo adduser --system --group --no-create-home redis   
sudo mkdir /var/lib/redis   # create directory
sudo chown redis:redis /var/lib/redis   # make redis own /var/lib/redis
sudo chmod 770 /var/lib/redis   # adjust permission
Test redis:
Start redis service:
 - sudo systemctl start redis
Check status:

 - systemctl status redis
Result of status if started successfully:
Output
● redis.service - Redis Server
 Loaded: loaded (/etc/systemd/system/redis.service; enabled; vendor preset: enabled)
 Active: active (running) since Wed 2016-05-11 14:38:08 EDT; 1min 43s ago
 Process: 3115 ExecStop=/usr/local/bin/redis-cli shutdown (code=exited, status=0/SUCCESS)
 Main PID: 3124 (redis-server)
 Tasks: 3 (limit: 512)
 Memory: 864.0K
 CPU: 179ms
 CGroup: /system.slice/redis.service
          └─3124 /usr/local/bin/redis-server 127.0.0.1:6379
Test instance:
Connect:
redis-cli
Test connectivity at prompt:
127.0.0.1:6379> ping   # result PONG
Check ability to set keys:
127.0.0.1:6379 set test "It's working!"  # result ok
Get the key just set:
127.0.0.1:6379 get test  # result "It's working!"
Exit redis:
127.0.0.1:6379 exit
Restart redis and then re-run steps 1, 4, and 5:
sudo systemctl restart redis
Enable redis to start at boot:
sudo systemctl enable redis

### 查看某个进程中的线程数目

``` bash shell
ps huH p <PID> | wc -l
```