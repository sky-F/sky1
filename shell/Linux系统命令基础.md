# Linux系统命令基础

## 一、Linux命令格式

```shell
[root@localhost ~]# 
```

- root：当前用户的名称
-  @：分隔符 
- localhost：当前主机的主机名 ~：用户当前所在的目录名称 “~”表示为用户家目录（发音tilde[ˈtɪldə]） 
- #： 用户身份提示符，#表示超级用户，也就是管理员；$表示普通用户 (发音pound[paʊnd])	



## 二、Linux目录

![image-20210730222335306](C:\Users\qq\AppData\Roaming\Typora\typora-user-images\image-20210730222335306.png)

```
根目录（/）
最高一级目录，所有目录都是根目录衍生出来，只有root用户具有写权限，一般根目录下只存放目录，不要存放件

/bin目录 – 用户二进制文件
包含二进制的可执行文件，你需要的常见的Linux命令都位于此目录下。

/sbin目录 – 系统二进制文件
这个目录下的命令通常由系统管理员使用， 对系统进行维护。

/etc– 配置文件
包含所有程序所需要的配置文件，也包含用于启动/停止单个程序的起动和关闭shell脚本。

/dev-设备文件
包含设备文件，包括终端设备、USB或连接到系统的任何设备，如网卡等。

/proc-进程信息文件
这是一个虚拟的文件系统，包含有关正在运行的进程信息。

/usr-用户程序
包含二进制文件、库文件、文档和二级程序的源代码。

/usr/bin中包含用户程序的二进制文件。如果你在/bin中找不到用户二进制文件，到/usr/bin目录看看。
/usr/sbin中包含系统管理员的二进制文件。如果你在/sbin中找不到系统二进制文件，到/usr/sbin目录看看。
/usr/lib中包含了/usr/bin和/usr/sbin用到的库。
/usr/local中包含了从源安装的用户程序。
/home -HOME目录
包含所有用户的个人档案，Linux是多用户的系统，所以用该目录保存各用户的信息。

/boot -引导加载程序
包含引导加载程序相关的文件。

/lib -系统库
包含支持位于/lib和/sbin下的二进制文件的库文件。

/opt -可选的附加应用程序
给主机额外安装软件所摆放的目录，以前的 Linux 系统中，习惯放置在 /usr/local 目录下

/mnt /media -挂载目录
光盘默认挂载点，通常光盘挂载于 /mnt/cdrom 下，也不一定，可以选择任意位置进行挂载。

/root 管理员家目录
```

以下几个目录注意不要误删除或者随意更改内部文件。

**/etc：** 上边也提到了，这个是系统中的配置文件，如果你更改了该目录下的某个文件可能会导致系统不能启动。

**/bin, /sbin, /usr/bin, /usr/sbin:** 这是系统预设的执行文件的放置目录，比如 ls 就是在 /bin/ls 目录下的。

值得提出的是，/bin, /usr/bin 是给系统用户使用的指令（除root外的通用户），而/sbin, /usr/sbin 则是给 root 使用的指令。

**/var：** 这是一个非常重要的目录，系统上跑了很多程序，那么每个程序都会有相应的日志产生，而这些日志就被记录到这个目录下，具体在 /var/log 目录下，另外 mail 的预设放置也是在这里。

## 三、Linux基本命令与常用符号

### **1、关机、重启命令**

**关机命令**

- init 0 #管理员可以使用
- halt
- shutdown -h
- poweroff

**重启命令**

- shutdown -r
- reboot

```shell
# shutdown常用命令
	-r	重启。默认延迟一分钟
# shutdown -r +3 "shutdown in 3 minutes"
	-h	关机。默认延迟一分钟
# shutdown -h 12：00/shutdown -h now
	-c	取消shutdown
# shutdown -c
```

### 2、查看系统信息

```shell
# uname --help
[root@localhost ~]# uname -s	#-s  输出内核名称
Linux
[root@localhost ~]# uname -n	#-n  输出网络节点上的主机名
localhost.localdomain
[root@localhost ~]# uname -r	#-r  输出内核发行号
3.10.0-327.el7.x86_64
[root@localhost ~]# uname -v	#-v  输出内核版本
#1 SMP Thu Nov 19 22:10:57 UTC 2015
[root@localhost ~]# uname -m	#-m  输出主机的硬件架构名称
x86_64
[root@localhost ~]# uname -p	#-p  输出处理器类型或"unknown"
x86_64
[root@localhost ~]# uname -i	#-i	输出硬件平台或"unknown"
x86_64
[root@localhost ~]# uname -o	#-o	输出操作系统名称
GNU/Linux
[root@localhost ~]# uname -a 	#-a  以如上次序输出所有信息。其中若-p和-i的结果不可知则省略
Linux localhost.localdomain 3.10.0-327.el7.x86_64 #1 SMP Thu Nov 19 22:10:57 UTC 2015 x86_64 x86_64 x86_64 GNU/Linux
```

### 3、date 查看和设置时间

```shell
时间方面：
%n : 下一行
%t : 跳格 tab
%H : 小时(00..23)
%I : 小时(01..12)
%k : 小时(0..23)
%l : 小时(1..12)
%M : 分钟(00..59)
%p : 显示本地 AM 或 PM
%r : 直接显示时间 (12 小时制，格式为 hh:mm:ss [AP]M)
%s : 从 1970 年 1 月 1 日 00:00:00 UTC 到目前为止的秒数
%S : 秒(00..60)
%T : 直接显示时间 (24 小时制)
%X : 相当于 %H:%M:%S
%Z : 显示时区


日期方面：
%a : 星期几 (Sun..Sat)
%A : 星期几 (Sunday..Saturday)
%b : 月份 (Jan..Dec)
%B : 月份 (January..December)
%c : 直接显示日期与时间
%d : 日 (01..31)
%D : 直接显示日期 (mm/dd/yy)
%h : 同 %b
%j : 一年中的第几天 (001..366)
%m : 月份 (01..12)
%U : 一年中的第几周 (00..53) (以 Sunday 为一周的第一天的情形)
%w : 一周中的第几天 (0..6)
%W : 一年中的第几周 (00..53) (以 Monday 为一周的第一天的情形)
%x : 直接显示日期 (yyyy-mm-dd)
%y : 年份的最后两位数字 (00.99)
%Y : 完整年份 (0000..9999)
```

格式化输出：

```
date +"%Y-%m-%d" 
2015-12-07
```

输出昨天日期：

```
date -d "1 day ago" +"%Y-%m-%d"
2015-11-19
```

2秒后输出：

```
date -d "2 second" +"%Y-%m-%d %H:%M.%S"
2015-11-20 14:21.31
```

传说中的 1234567890 秒：

```
date -d "1970-01-01 1234567890 seconds" +"%Y-%m-%d %H:%m:%S"
2009-02-13 23:02:30
```

普通转格式：

```
date -d "2009-12-12" +"%Y/%m/%d %H:%M.%S"
2009/12/12 00:00.00
```

apache格式转换：

```
date -d "Dec 5, 2009 12:00:37 AM" +"%Y-%m-%d %H:%M.%S"
2009-12-05 00:00.37
```

格式转换后时间：

```
date -d "Dec 5, 2009 12:00:37 AM 2 year ago" +"%Y-%m-%d %H:%M.%S"
2007-12-05 00:00.37
```

加减操作：

```
date +%Y%m%d               #显示前天年月日 
date -d "+1 day" +%Y%m%d   #显示前一天的日期 
date -d "-1 day" +%Y%m%d   #显示后一天的日期 
date -d "-1 month" +%Y%m%d #显示上一月的日期 
date -d "+1 month" +%Y%m%d #显示下一月的日期 
date -d "-1 year" +%Y%m%d  #显示前一年的日期 
date -d "+1 year" +%Y%m%d  #显示下一年的日期
```

设定时间：

```
date -s          #设置当前时间，只有root权限才能设置，其他只能查看 
date -s 20120523 #设置成20120523，这样会把具体时间设置成空00:00:00 
date -s 01:01:01 #设置具体时间，不会对日期做更改 
date -s "01:01:01 2012-05-23" #这样可以设置全部时间 
date -s "01:01:01 20120523"   #这样可以设置全部时间 
date -s "2012-05-23 01:01:01" #这样可以设置全部时间 
date -s "20120523 01:01:01"   #这样可以设置全部时间
```

检查一组命令花费的时间：

```
#!/bin/bash 
start=$(date +%s) 
nmap man.linuxde.net &> /dev/null 
end=$(date +%s) 
difference=$(( end - start )) 
echo $difference seconds.
```

### 4、文件和目录

- cd 目录 进入目录
- pwd： 查看当前的工作路径

- ls： 查看当前目录下有哪些文件
- mkdir 建立目录
- rmdir 删除空文件夹
- cat 查看文件内容
- rm 删除文件或目录
- cp 拷贝
- mv 移动/改名

### 5、常用符号

* 任意字符串	*	
* 任意字符        ？	
* 路径间隔符    /	
* 当前用户的家目录      ~	
* 管理员家目录为/root，其它用户的家目录在/home/用户名	

## 四、Linux常用快捷键

- **Ctrl + a 切换到命令行开始**

  这个操作跟Home实现的结果一样的，但Home在某些unix环境下无法使用，便可以使用这个组合；在Linux下的vim，这个也是有效的；另外，在windows的许多文件编辑器里，这个也是有效的。

- **Ctrl + e 切换到命令行末尾**

  这个操作跟END实现的结果一样的，但End键在某些unix环境下无法使用，便可以使用这个组合；在Linux下的vim，这个也是有效的；另外，在windows的许多文件编辑器里，这个也是有效的。

- **Ctrl + l（L） 清除屏幕内容，效果等同于clear**

- **Ctrl + u 清除剪切光标之前的内容**

  这个命令很有用，在nslookup里也是有效的

- Ctrl + k 剪切清除光标之后的内容

- Ctrl + y 粘贴刚才所删除的字符

  此命令比较强悍，删除的字符有可能是几个字符串，但极有可能是一行命令。

- Ctrl + r 在历史命令中查找 （这个非常好用，输入关键字就调出以前的命令了）

  这个命令强烈推荐，有时history比较多时，想找一个比较复杂的，直接在这里，shell会自动查找并调用，方便极了

- **Ctrl + c 终止命令**

- **Ctrl + d 退出shell，logout**

- Ctrl + z 转入后台运行

  不过，由Ctrl + z转入后台运行的进程在当前用户退出后就会终止，所以用这个不如用nohup命令&，因为nohup命令的作用就是用户退出之后进程仍然继续运行，而现在许多脚本和命令都要求在root退出时仍然有效。

## 五、centos中systemctl命令使用

**systemctl命令的基本操作格式是：

**systemctl  动作  服务名.service**

```shell
systemctl is-enabled iptables.service

systemctl is-enabled servicename.service #查询服务是否开机启动

systemctl enable *.service #开机运行服务

systemctl disable *.service #取消开机运行

systemctl start *.service #启动服务

systemctl stop *.service #停止服务

systemctl restart *.service #重启服务

systemctl reload *.service #重新加载服务配置文件

systemctl status *.service #查询服务运行状态

systemctl --failed #显示启动失败的服务


```



