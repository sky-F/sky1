# shell脚本学习笔记



shell脚本本质上就是要将完成一件事情的所有命令按照执行的先后顺序写入一个文件，并给改文件执行权限

## 一、数据检索命令

### 1、行检索：grep  egrep



```shell
# cp /etc/passwd /tmp/passwd
# cd /tmp
$ grep -i root passwd	#忽略大小写匹配包含root的行
$ grep -w ftp passwd 	#精确匹配ftp单词
$ grep -wo ftp passwd 	#打印匹配到的关键字ftp
$ grep -n root passwd 	#打印匹配到root关键字的行好
$ grep -ni root passwd 	#忽略大小写匹配统计包含关键字root的行
$ grep -nic root passwd #忽略大小写匹配统计包含关键字root的行数
$ grep -i ^root passwd #忽略大小写匹配以root开头的行
$ grep bash$ passwd 	#匹配以bash结尾的行
$ grep -n ^$ passwd 	#匹配空行并打印行号
$ grep ^$ /etc/vsftpd/vsftpd.conf #匹配以$号开头的行
$ grep -v ^$ /etc/vsftpd/vsftpd.conf #匹配不以$号开头的行
$ grep -A 5 mail passwd  	#匹配包含mail关键字及其后5行
$ grep -B 5 mail passwd   	#匹配包含mail关键字及其前5行
$ grep -C 5 mail passwd 	#匹配包含mail关键字及其前后5行
```



### 2、字符串检索:cut  tr

#### **cut用于列截取**

- -c: 以字符为单位进行分割。
- -d: 自定义分隔符，默认为制表符。\t
- -f: 与-d一起使用，指定显示哪个区域。

```shell
# cut -d: -f1 1.txt 			以:冒号分割，截取第1列内容
# cut -d: -f1,6,7 1.txt 		以:冒号分割，截取第1,6,7列内容
# cut -c4 1.txt 				截取文件中每行第4个字符
# cut -c1-4 1.txt				截取文件中每行的1-4个字符
# cut -c4-10 1.txt 
# cut -c5- 1.txt 				从第5个字符开始截取后面所有字符
```

#### tr 字符转换：替换，删除

tr用来从标准输入中通过替换或删除操作进行字符转换；主要用于删除文件中控制字符或进行字符转换。

使用tr时要转换两个字符串：字符串1用于查询，字符串2用于处理各种转换。

语法：

```powershell
commands|tr  'string1'  'string2'

tr  'string1'  'string2' < filename

tr options 'string1' < filename

-d 删除字符串1中所有输入字符。
-s 删除所有重复出现字符序列，只保留第一个；即将重复出现字符串压缩为一个字符串。

a-z 任意小写
A-Z 任意大写
0-9 任意数字
```

举例：

```shell
# tr -d '[:/]' < 3.txt 						删除文件中的:和/
# cat 3.txt |tr -d '[:/]' 					删除文件中的:和/
# tr '[0-9]' '@' < 3.txt 					将文件中的数字替换为@符号
# tr '[a-z]' '[A-Z]' < 3.txt 				将文件中的小写字母替换成大写字母
# tr -s '[a-z]' < 3.txt 					匹配小写字母并将重复的压缩为一个
# tr -s '[a-z0-9]' < 3.txt 					匹配小写字母和数字并将重复的压缩为一个
```


## 二、数据处理命令       

### 1、数据排序：sort

```powershell
sort：将文件的每一行作为一个单位，从首字符向后，依次按ASCII码值进行比较，最后将他们按升序输出。

语法：
sort [options] [filename]

-u ：去除重复行
-r ：降序排列，默认是升序
-o : 将排序结果输出到文件中  类似 重定向符号 >
-n ：以数字排序，默认是按字符排序
-t ：分隔符
-k ：第N列

-b ：忽略前导空格。
-R ：随机排序，每次运行的结果均不同。
 
```

 示例：
```shell
# sort -n -t: -k3 1.txt 按照用户的uid进行升序排列
# sort -nr -t: -k3 1.txt 按照用户的uid进行降序排列
# sort -n 2.txt 按照数字排序
# sort -nu 2.txt 按照数字排序并且去重
# sort -nr 2.txt 
# sort -nru 2.txt 
# sort -nru 2.txt 
# sort -n 2.txt -o 3.txt 按照数字排序并将结果重定向到文件
# sort -R 2.txt 
# sort -u 2.txt 
```



### 2、数据去重: uniq

```powershell
uniq：去除连续重复行

语法：
uniq [options] [filename]

-i: 忽略大小写
-c: 统计重复行次数
-d:只显示重复行
```

示例：

```shell
# uniq 2.txt 
# uniq -d 2.txt 
# uniq -dc 2.txt 
```



### 3、文本数据合并: paste

```powershell
paste工具用于合并文件行输出到屏幕，不会改动源文件

-d：自定义间隔符，默认是tab,只接受一个字符
-s：将每个文件中的所有内容按照一行输出，文件中的行与行以TAB间隔。
```

示例：

```shell
# cat a.txt 
hello
# cat b.txt 
hello world
888
999
# paste a.txt b.txt 
hello   hello world
        888
        999
# paste b.txt a.txt   
hello world     hello
888
999

# paste -d'@' b.txt a.txt 
hello world@hello
888@
999@

# paste -s b.txt a.txt 
hello world     888     999
hello
```



### 4、数据输出: tee

```shell
tee工具从标准输入读取并写入标准输出和文件，即：双向覆盖重定向<屏幕输出|文本输入>
somecommand |tee filename

-a 双向追加重定向

# echo hello world
# echo hello world|tee file1
# cat file1 
# echo 999|tee -a file1
# cat file1 
```

### 5、数据处理: xargs    

管道(|):上一个命令的输出作为下一个命令的输入，做的是**数据源**。

```shell
xargs 可以将管道或标准输入（stdin）数据转换成命令行参数，也能够从文件的输出中读取数据。

xargs 一般是和管道一起使用。

命令格式：
''[somecommand]|[filename]'' |xargs -item  command

OPTIONS:
-a file 从文件中读入作为sdtin
-E flag flag必须是一个以空格分隔的标志，当xargs分析到含有flag这个标志的时候就停止。
-p 当每次执行一个argument的时候询问一次用户。
-n num 后面加次数，表示命令在执行的时候一次用的argument的个数，默认是用所有的。
-t 表示先打印命令，然后再执行。
# -i 或者是-I，这得看linux支持了，将xargs的每项名称，一般是一行一行赋值给 {}，可以用 {} 代替。
-r no-run-if-empty 当xargs的输入为空的时候则停止xargs，不用再去执行了。
-d delim 分隔符，默认的xargs分隔符是回车，argument的分隔符是空格，这里修改的是xargs的分隔符。


注意：linux命令格式一般为
命令    命令选项     参数
上一个命令的输出就是下一个命令的参数  这句话结合命令语法  应该知道输出的内容在下一个命令的位置
```

示例：

> ```bash
> $ echo "hello world" | xargs echo
> hello world
> ```

上面的代码将管道左侧的标准输入，转为命令行参数`hello world`，传给第二个`echo`命令。

`xargs`命令的格式如下。

> ```bash
> $ xargs [-options] [command]
> ```

真正执行的命令，紧跟在`xargs`后面，接受`xargs`传来的参数。

`xargs`的作用在于，大多数命令（比如`rm`、`mkdir`、`ls`）与管道一起使用时，都需要`xargs`将标准输入转为命令行参数。

> ```bash
> $ echo "one two three" | xargs mkdir
> ```

上面的代码等同于`mkdir one two three`。如果不加`xargs`就会报错，提示`mkdir`缺少操作参数。

多行输入单行输出：

```bash
# cat test.txt | xargs
a b c d e f g h i j k l m n o p q r s t u v w x y z
```

-n 选项多行输出：

```bash
# cat test.txt | xargs -n3

a b c
d e f
g h i
j k l
m n o
p q r
s t u
v w x
y z
```

-d 选项可以自定义一个定界符：

```bash
# echo "nameXnameXnameXname" | xargs -dX

name name name name
```

结合 -n 选项使用：

```bash
# echo "nameXnameXnameXname" | xargs -dX -n2

name name
name name
```

查找所有的 jpg 文件，并且压缩它们：

```bash
find . -type f -name "*.jpg" -print | xargs tar -czvf images.tar.gz
```



## 三、常用字符

- !:               							 执行历史命令   !! 执行上一条命令
- $:                					      变量中取内容符
- "+"    "-"    "*"    /     %:        对应数学运算  加 减 乘 除 取余数  
- &:                后台执行
- ;：               分号可以在shell中一行执行多个命令，命令之间用分号分割    
- \:                转义字符
- ``:               反引号 命令中执行命令    echo "today is `date +%F`"
- ' ':              单引号，脚本中字符串要用单引号引起来，但是不同于双引号的是，单引号不解释变量
- " ":              双引号，脚本中出现的字符串可以用双引号引起来

```shell
通配符    
    ~:                家目录    # cd ~ 代表进入用户家目录
    *:                星号是shell中的通配符  匹配所有
    ?:                问号是shell中的通配符  匹配除回车以外的一个字符
    [list]: 匹配[list]中的任意单个字符
[!list]: 匹配除list中的任意单个字符
{string1,string2,...}： 匹配string1,string2或更多字符串


重定向
>      覆盖输入 
>> 追加输入
< 输出
<< 追加输出

管道命令
|：               管道符 上一个命令的输出作为下一个命令的输入   cat filename | grep "abc"
```

## 四、shell定义

### 1、shell脚本：

就是将需要执行的命令保存到文本中，按照顺序执行。它是解释型的，意味着不需要编译。

**若干命令 + 脚本的基本格式 + 脚本特定语法 + 思想= shell脚本**

### 2、shell脚本的基本写法

（1）**脚本第一行**，魔法字符==**#!**==指定解释器【必写】

`#!/bin/bash` 表示以下内容使用bash解释器解析

**注意：** 如果直接将解释器路径写死在脚本里，可能在某些系统就会存在找不到解释器的兼容性问题，所以可以使用:**`#!/bin/env 解释器`** **`#!/bin/env bash`**

（2）**脚本第二部分**，注释(#号)说明，对脚本的基本信息进行描述【可选】

```shell
#!/bin/env bash

# 以下内容是对脚本的基本信息的描述
# Name: 名字
# Desc:描述describe
# Path:存放路径
# Usage:用法
# Update:更新时间

#下面就是脚本的具体内容
commands
...
```

（3）**脚本第三部分**，脚本要实现的具体代码内容

### 3、shell脚本的执行方法

- 标准脚本执行方法（建议）

```shell
1) 编写shell脚本

2) 脚本增加可执行权限
# chmod +x test.sh

3) 标准方式执行脚本

# /home/root/test.sh
或者
# ./test.sh

注意：标准执行方式脚本必须要有可执行权限。
```

- 非标准的执行方法（不建议）

1. 直接在命令行指定解释器执行

```shell
# bash test.sh
或者
# bash -x first_shell.sh
+ echo 'hello world'
hello world
----------------
-x:一般用于排错，查看脚本的执行过程
-n:用来查看脚本的语法是否有问题
------------
```

2. 使用`source`命令读取脚本文件,执行文件里的代码

```shell
# source test.sh
hello world
```

## 五、变量

变量是用来临时保存数据的，该数据是可以变化的数据

### 1、定义一个变量

**变量格式： 变量名=值**

在shell编程中的变量名和等号之间不能有空格。

变量名命名规则：

- ​    命名只能使用英文字母，数字和下划线，首个字符不能以数字开头。
- ​    中间不能有空格，可以使用下划线（_）。
- ​    不能使用标点符号。
- ​    不能使用bash里的关键字（可用help命令查看保留关键字）。

```shell
# 1A=hello						首个字符不能以数字开头
-bash: 1A=hello: command not found

# A =123						中间不能有空格
-bash: A: command not found
# A= 123
-bash: 123: command not found
# A = 123
-bash: A: command not found
# A=123
# echo $A
123

# *A=hello						   不能使用标点符号	
-bash: *A=hello: command not found

特别说明：对于有空格的字符串给变量赋值时，要用引号引起来
# A=hello world
-bash: world: command not found
# A="hello world"
# A='hello world'
```

### 2、变量分类

系统中的变量根据作用域及生命周期可以分为四类：本地变量、环境变量、全局变量、内置变量

本地变量：当前用户自定义的变量。当前进程中有效，其他进程及当前进程的子进程无效。

环境变量：当前进程有效，并且能够被子进程调用。

全局变量：全局所有的用户和程序都能调用，且继承，新建的用户也默认能调用.

内置变量：shell本身已经固定好了它的名字和作用.

| 变量类型 | 作用域                       | 生命周期           |
| -------- | ---------------------------- | ------------------ |
| 本地变量 | 当前shell环境(子shell不能用) | 脚本结束或终端结束 |
| 环境变量 | 当前shell或者子shell         | 当前进程结束       |
| 全局变量 | 所有用户及shell环境          | 关机               |
| 内置变量 | 所有用户及shell环境          | 关机               |

##### 本地变量

用户自定义的变量，定义在脚本或者当前终端中，脚本执行完毕或终端结束变量失效。

##### 环境变量

定义在用户家目录下的.bashrc或.bash_profile文件中，用户私有变量，只能本用户使用。

**查看当前用户的环境变量 env**

查询当前用户的所有变量(临时变量与环境变量) set

##### 将当前变量变成环境变量 export

```shell
定义一个临时变量
# export A=hello //临时将一个本地变量（临时变量）变成环境变量
 # env|grep ^A
A=hello

定义一个永久生效变量：
vim .bash_profile 或者 ~/.bashrc
A=hello

```



**关于export说明**
用户登录时:
1) 用户登录到Linux系统后，系统将启动一个用户shell。在这个shell中，可以使用shell命令或声明变量，也可以创建并运行 shell脚本程序。

运行脚本时:
2) 运行shell脚本程序时，系统将创建一个子shell。此时，系统中将有两个shell，一个是登录时系统启动的shell，另一个是系统为运行脚本程序创建的shell。当一个脚本程序运行完毕，它的脚本shell将终止，可以返回到执行该脚本之前的shell。

从这种意义上来说，用户可以有许多 shell，每个shell都是由某个shell（称为父shell）派生的。在子shell中定义的变量只在该子shell内有效。如果在一个shell脚本程序中定义了一个变量，当该脚本程序运行时，
这个定义的变量只是该脚本程序内的一个局部变量，其他的shell不能引用它，要使某个变量的值可以在其他shell中被改变，可以使用export命令对已定义的变量进行输出。 

export命令将使系统在创建每一个新的shell时定义这个变量的一个拷贝。这个过程称之为变量输出。

**父shell与子shell**

![image-20210801205332147](C:\Users\qq\AppData\Roaming\Typora\typora-user-images\image-20210801205332147.png)

##### 全局变量

使用export命令将本地变量输出为当前shell中的环境变量
所有用户及shell都可以使用，可以在/etc/profile /etc/bashrc下永久定义

- 相关配置文件

| 文件名              | 说明                               | 备注                                            |
| ------------------- | ---------------------------------- | ----------------------------------------------- |
| $HOME/.bashrc       | 当前用户的bash信息,用户登录时读取  | 定义别名、umask、函数等                         |
| $HOME/.bash_profile | 当前用户的环境变量，用户登录时读取 |                                                 |
| $HOME/.bash_logout  | 当前用户退出当前shell时最后读取    | 定义用户退出时执行的程序等                      |
| /etc/bashrc         | 全局的bash信息，所有用户都生效     |                                                 |
| /etc/profile        | 全局环境变量信息                   | 系统和所有用户都生效                            |
| $HOME/.bash_history | 用户的历史命令                     | history -w 保存历史记录 history -c 清空历史记录 |

**说明：**以上文件修改后，都需要*重新source*让其生效或者退出重新登录。

- 用户登录系统后, 读取相关文件的顺序

1. `/etc/profile`
2. `$HOME/.bash_profile`
3. `$HOME/.bashrc`
4. `/etc/bashrc`
5. `$HOME/.bash_logout`

##### 内置变量

系统变量(内置bash中变量) ： shell本身已经固定好了它的名字和作用

```shell
$?：上一条命令执行后返回的状态，当返回状态值为0时表示执行正常，非0值表示执行异常或出错
 若退出状态值为0，表示命令运行成功
 若退出状态值为127,表示command not found
 若退出状态值为126,表示找到了该命令但无法执行（权限不够）
 若退出状态值为1&2,表示没有那个文件或目录
 
$$：当前所在进程的进程号     echo $$   eg：kill -9 `echo $$`  = exit   退出当前会话

$!：后台运行的最后一个进程号  （当前终端）  # gedit &
!$ 调用最后一条命令历史中的参数
!! 调用最后一条命令历史


$#：脚本后面接的参数的个数
$*：脚本后面所有参数，参数当成一个整体输出，每一个变量参数之间以空格隔开
$@: 脚本后面所有参数，参数是独立的，也是全部输出

$0：当前执行的进程/程序名  echo $0     
$1~$9 位置参数变量
${10}~${n} 扩展位置参数变量  第10个位置变量必须用{}大括号括起来

# cat 2.sh 
#!/bin/bash
#xxxx
echo "\$0 = $0"
echo "\$# = $#"
echo "\$* = $*"
echo "\$@ = $@"
echo "\$1 = $1" 
echo "\$2 = $2" 
echo "\$3 = $3" 
echo "\$11 = ${11}" 
echo "\$12 = ${12}" 

了解$*和$@的区别：
$* :表示将变量看成一个整体
$@ :表示变量是独立的

#!/bin/bash
for i in "$@"
do
echo $i
done

echo "======我是分割线======="

for i in "$*"
do
echo $i
done

# bash 3.sh a b c
a
b
c
======我是分割线=======
a b c
```



| 内置变量   | 含义                                                         |
| ---------- | ------------------------------------------------------------ |
| $?         | 上一条命令执行后返回的状态；状态值为0表示执行正常，非0表示执行异常或错误 |
| $0         | 当前执行的程序或脚本名   (./02.sh)                           |
| $#         | 脚本后面接的参数的个数         3个                           |
| $*         | 脚本后面所有参数，参数当成一个整体输出，每一个变量参数之间以空格隔开 (参数数组a b c) |
| $@         | 脚本后面所有参数，参数是独立的，也是全部输出 (参数数组a b c) |
| $1~$9      | 脚本后面的位置参数，$1表示第1个位置参数，依次类推            |
| ${10}~${n} | 扩展位置参数,第10个位置变量必须用{}大括号括起来(2位数字以上扩起来) |
| $$         | 当前所在进程的进程号，如`echo $$`                            |
| $！        | 后台运行的最后一个进程号     测试: sleep 400 &(后台运行)/sleep 400(ctrl+z 暂停运行), 再运行jobs, 查看当前进程的后台子进程. |
| !$         | 调用最后一条命令历史中的参数                                 |



### 3、其他

```shell
# url=www.baidu.com
# echo ${#url}      获取变量的长度
```

### 4、交互式输入

##### read命令

功能：默认接受键盘的输入，回车符代表输入结束
应用场景：人机交互
命令选项

```shell
-p		打印信息
-t		限定时间
-s		不回显
-n		输入字符个数
```

```shell
#1、交互输入登陆名
echo -n "$HOSTNAME login: "
read account

#2、交互输入密码
read -s -t30 -p "Password: " pw
echo
```

## 六、基本运算

算术运算：默认情况下，shell就只能支持简单的整数运算

运算内容：加(+)、减(-)、乘(*)、除(/)、求余数（%）

### 1、 四则运算符号

| 表达式 | 举例                          | 备注                                     |
| ------ | ----------------------------- | ---------------------------------------- |
| $(( )) | echo $((1+1))                 | 运算元素必须是变量，无法直接对整数做运算 |
| $[ ]   | echo $[10-5]                  |                                          |
| expr   | expr 10 / 5                   | 格式比较古板，注意空格                   |
| let    | n=1;let n+=1 等价于 let n=n+1 |                                          |

- 整形运算：expr		let		$(())		bc
- 浮点运算		– bc		采用的命令组合的方式来实现的 echo “scale=N;表达式”|bc

### 2、示例

#### （1)内存使用率统计，要求打印内存使用率

```shell
[root@localhost ~]# cat mem_use.sh 
#!/bin/bash
memory_total=` free | grep Mem | awk '{print $2}'`
memory_use=` free | grep Mem | awk '{print $3}'`
echo "内存使用率: $(($memory_use*100/$memory_total))%"
echo "内存使用率：`echo "scale=2; $memory_use*100/$memory_total"|bc`%"
```

