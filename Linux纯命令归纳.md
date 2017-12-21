# Linux纯命令归纳

标签（空格分隔）： 工作总结

---

## 在该笔记中只记录Linux中纯命令的技巧：

### cd命令

```
// cd不带指定目录，跳转到家目录(~)
$ cd
```

### ls命令

ls 可能是用户最常使用的命令

```
// ls 可以针对置顶目录从而查看指定目录中的文件信息，并且可以同时指定多个
$ ls /var /opt
/opt:
banzhuan.zip  gitrepo  rabbitmq-py  redis
/var:
backups  cache  lib  local  lock  log  mail  opt  run  spool  tmp

// ls -lht查看文件的大小
$ ls -lht jdk-8u112-linux-x64.tar.gz 
-rw-r--r-- 1 root root 175M Sep 24 07:36 jdk-8u112-linux-x64.tar.gz

// ls -a 查看所有文件，包括以.开头的文件
$ ls -a
.          .bash_history  .config     .mysql_history  .profile           .ssh
..         .bashrc        .gitconfig  .nano_history   .pydistutils.cfg   .viminfo
.aptitude  .cache         .local      .pip            .rediscli_history
```

### date命令

```
// 当前时间命令
$ date
Wed Dec 28 15:54:58 CST 2016

// 修改系统当前时间
$ date -s "20161018 17:17:00"
```

### cal命令

```
// 日历命令cal
$ cal
   December 2016      
Su Mo Tu We Th Fr Sa  
             1  2  3  
 4  5  6  7  8  9 10  
11 12 13 14 15 16 17  
18 19 20 21 22 23 24  
25 26 27 28 29 30 31
```

### df命令

```
// 查看磁盘使用情况 df
$ df
Filesystem     1K-blocks    Used Available Use% Mounted on
udev             1014104       4   1014100   1% /dev
tmpfs             204992     404    204588   1% /run
/dev/vda1       41151808 2912448  36125932   8% /
none                   4       0         4   0% /sys/fs/cgroup
none                5120       0      5120   0% /run/lock
none             1024952       0   1024952   0% /run/shm
none              102400       0    102400   0% /run/user
```

### free命令

```
// 查看空闲内存状况 free
$ free
             total       used       free     shared    buffers     cached
Mem:       2049904    1637628     412276        424     206704    1145068
-/+ buffers/cache:     285856    1764048
Swap:            0          0          0
```