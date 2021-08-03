### 1、用户是否存在

输入一个用户，用脚本判断该用户是否存在

```shell
#!/bin/env bash
read -p '请输入用户名:' username
id $username &>/dev/null
[ $? -eq 0 ] && echo '用户存在' || echo '不存在'


#!/bin/env bash
read -p "请输入你要查询的用户名:" username
grep -w $username /etc/passwd &>/dev/null
if [ $? -eq 0 ]
then
    echo "该用户已存在"
else
    echo "该用户不存在"
fi
```

### 2、判断当前主机的内核版本

判断当前内核主版本是否为2，且次版本是否大于等于6；如果都满足则输出当前内核版本

思路：
1. 先查看内核的版本号	uname -r
2. 先将内核的版本号保存到一个变量里，然后再根据需求截取出该变量的一部分：主版本和次版本
3. 根据需求进步判

```shell
#!/bin/bash
kernel=`uname -r`
var1=`echo $kernel|cut -d. -f1`
var2=`echo $kernel|cut -d. -f2`
test $var1 -eq 2 -a $var2 -ge 6 && echo $kernel || echo "当前内核版本不符合要求"
或者
[ $var1 -eq 2 -a $var2 -ge 6 ] && echo $kernel || echo "当前内核版本不符合要求"
或者
[[ $var1 -eq 2 && $var2 -ge 6 ]] && echo $kernel || echo "当前内核版本不符合要求"

或者
#!/bin/bash
kernel=`uname -r`
test ${kernel:0:1} -eq 2 -a ${kernel:2:1} -ge 6 && echo $kernel || echo '不符合要求'

其他命令参考：
uname -r|grep ^2.[6-9] || echo '不符合要求'

```

### 3、计算1-100的奇数和

```shell
#!/bin/env bash
# 计算1-100的奇数和
# 定义变量来保存奇数和
sum=0

#for循环遍历1-100的奇数，并且相加，把结果重新赋值给sum

for i in {1..100..2}
do
	let sum=$sum+$i
done
#打印所有奇数的和
echo "1-100的奇数和是:$sum"
```

### 4、判断所输整数是否为质数

```shell
# 1. 让用户输入一个数，保存到一个变量里
# 2. 如果能被其他数整除就不是质数——>`$num%$i`是否等于0	`$i=2到$num-1`
# 3. 如果输入的数是1或者2取模根据上面判断又不符合，所以先排除1和2
# 4. 测试序列从2开始，输入的数是4——>得出结果`$num`不能和`$i`相等，并且`$num`不能小于`$i`
#!/bin/bash
read -p "请输入一个正整数字:" number

[ $number -eq 1 ] && echo "$number不是质数" && exit
[ $number -eq 2 ] && echo "$number是质数" && exit

for i in `seq 2 $[$number-1]`
	do
	 [ $[$number%$i] -eq 0 ] && echo "$number不是质数" && exit
	done
echo "$number是质数" && exit
```

### 5、局域网内脚本检查主机网络通讯写一个脚本

局域网内，把能ping通的IP和不能ping通的IP分类，并保存到两个文本文件里

以10.1.1.1~10.1.1.10为例

```shell
#!/bin/bash
#定义变量
ip=10.1.1
#循环去ping主机的IP
for ((i=1;i<=10;i++))
do
	ping -c1 $ip.$i &>/dev/null
	if [ $? -eq 0 ];then
		echo "$ip.$i is ok" >> /tmp/ip_up.txt
	else
		echo "$ip.$i is down" >> /tmp/ip_down.txt
	fi
	或者
	[ $? -eq 0 ] && echo "$ip.$i is ok" >> /tmp/ip_up.txt || echo "$ip.$i is down" >> /tmp/ip_down.txt
done

[root@server shell03]# time ./ping.sh         

real    0m24.129s
user    0m0.006s
sys     0m0.005s
```

**延伸扩展：shell脚本并发**

并行执行：
{程序}&表示将程序放到后台并行执行，如果需要等待程序执行完毕再进行下面内容，需要加wait

```shell

#!/bin/bash
#定义变量
ip=10.1.1
#循环去ping主机的IP
for ((i=1;i<=10;i++))
do
{

        ping -c1 $ip.$i &>/dev/null
        if [ $? -eq 0 ];then
                echo "$ip.$i is ok" >> /tmp/ip_up.txt
        else
                echo "$ip.$i is down" >> /tmp/ip_down.txt
        fi
}&
done
wait
echo "ip is ok...."

[root@server ~]# time ./ping.sh 
ip is ok...

real    0m3.091s
user    0m0.001s
sys     0m0.008s
```
