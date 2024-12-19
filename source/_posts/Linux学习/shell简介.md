---
layout: posts
title: "shell简介"
date: 2022-10-29 15:42:07
description: "这是文章开头，显示在主页面，详情请点击此处。"
categories: 
- "Linux"
tags:
- "shell"
---

简介 <!--more-->

		类似于解释器，解释给Linux内核。

![image-20220116152241867](shell%E7%AE%80%E4%BB%8B.assets/image-20220116152241867-7030603.png)

​		国内一般都是bash，

​		执行方式：可以chmod 加权限直接运行shell文件，也可以不加权限直接 sh XXX.sh 运行，两种方法都是可以的。

# shell的变量

## 自定义变量		![image-20220116153216923](shell%E7%AE%80%E4%BB%8B.assets/image-20220116153216923.png)

​		![image-20220116154744742](shell%E7%AE%80%E4%BB%8B.assets/image-20220116154744742.png)

​		![image-20220116154933412](shell%E7%AE%80%E4%BB%8B.assets/image-20220116154933412.png)

（反引号是键盘1左侧的～按键。）

```bash
# sh脚本内输入：
#!/bin/bash
# 定义变量A
A=100
# 输出变量A
echo $A
echo A=$A
echo "A=$A"
# 撤销变量A
unset A
echo A=$A
# 声明静态变量B=2,不能unset
readonly B=2
echo B=$B
unset B
# 将指令返回的结果赋给变量
C=`date`
D=$(date)
echo "C=$C"
echo "D=$D"

# 运行脚本后输出：
100
A=100
A=100
A=
B=2
var.sh: 第 14 行:unset: B: 无法取消设定: 只读 variable
C=2022年 01月 15日 星期六 13:39:41 EST
D=2022年 01月 15日 星期六 13:39:41 EST

```



## 环境变量

​		![image-20220116160140472](shell%E7%AE%80%E4%BB%8B.assets/image-20220116160140472.png)

​		在/etc/profile 里export 一个全局变量，然后输出。

```bash
vim /etc/profile
		export MYSQL_HOME=/usr/bin/mysql
source /etc/profile

# shell 脚本内输入： 
:<<!
多行注释
多行注释
多行注释
!
# 输出环境变量/全局变量的值
echo MYSQL_HOME=$MYSQL_HOME

# 输出：
MYSQL_HOME=/usr/bin/mysql

```



## 位置参数变量

​		![image-20220116162903588](shell%E7%AE%80%E4%BB%8B.assets/image-20220116162903588.png)

​		写一个脚本position.sh,在此脚本中获取到命令行的各个参数信息。

```bash
# 脚本内写入：
#!/bin/bash
echo "0=$0 1=$1 2=$2"
echo $*
echo "$@"
echo $#

# 命令
sh position.sh 100 200 300 400

# 输出
0=./position.sh 1=100 2=200
100 200 300 400
100 200 300 400
4

```

​		

## 预定义变量（用的不多）

​		![image-20220116163916063](shell%E7%AE%80%E4%BB%8B.assets/image-20220116163916063.png)

​		

```bash
# 新建一个脚本preVar.sh
#!/bin/bash
echo "当前执行的进程id=$$"
# 以后台方式运行shell脚本,结尾+ &
/root/shcode/position.sh &
echo "后台最后一个运行的进程id=$!"
echo "执行的结果是$?"

# 输出
当前执行的进程id=26947
后台最后一个运行的进程id=26948
执行的结果是0
[root@localhost shcode]# 0=/root/shcode/position.sh 1= 2=


0
# 卡住，因为脚本里面又运行了另一个脚本。ctrl+c退出。

```



# 运算符

​		![image-20220116170028631](shell%E7%AE%80%E4%BB%8B.assets/image-20220116170028631.png)

```bash
# 输入 
#!/bin/bash
RES1=$(((2+3)*4))
echo "res1=$RES1"

RES2=$[(2+3)*4]
echo "res2=$RES2"

TEMP=`expr 2 + 3`
RES3=`expr $TEMP \* 4`
echo "temp=$TEMP"
echo "res3=$RES3"

SUM=$[$1+$2]
echo "参数和是 $SUM"
~                                                                                           
~                                                                                           
# 输出
# sh oper.sh 20 50
res1=20
res2=20
temp=5
res3=20
参数和是 70

```



# 条件判断

​		

![image-20220116172538434](shell%E7%AE%80%E4%BB%8B.assets/image-20220116172538434.png)

![image-20220116172605505](shell%E7%AE%80%E4%BB%8B.assets/image-20220116172605505.png)

​		

```bash
# 输入
#!/bin/bash
if [ "ok" = "ok" ]
then
        echo "equal"
fi

if [ 23 -ge 22 ]
then        
        echo "大于等于"
fi          
           
if [ -f /root/shcode/aaa.txt ]
then          
        echo "存在" 
fi

if [ aaa.txt ] 
then 
        echo "hello aaa.txt" 
fi  

# 输出
equal
大于等于
存在
hello aaa.txt

```



# 流程控制	

## if

```bash
if [ 条件判断式 ]
then
		代码
fi

if [ 条件判断式 ]
then
		代码
elif [ 条件判断式 ]
then
		代码
...
fi
```

## case

```bash
# 脚本内写入
#!/bin/bash
case $1 in
"1")
echo "周一"
;;
"2")
echo "周二" 
;;          
*)          
echo "other"
esac 

# 输出 .sh 参数
周一/周二/other
```

## for

```bash
# 输入
# for的第一种写法
#!/bin/bash
# $* 参数当作一个整体
for i in "$*"
do
        echo "num is $i"
done
# $@ 是分别对待
echo "========="
for j in "$@"
do
        echo "num is $j"
done
# for的第二种写法
SUM=0
for (( i=1; i<=5; i++ ))  # 注意空格
do
        SUM=$[$SUM+$i]
done
echo "sum = $SUM"

# 输出
# ./testFor1.sh 100 200 300 
num is 100 200 300
=========
num is 100
num is 200
num is 300
sum = 15
```

## while	

```bash
# 第一种写法
while 条件/命令
do
    循环体
done
# while的条件可以是各种终端的命令。包括外部命令或bash内建（built-in）命令都可以。因为命令都是有返回值的（可以用echo $?查看），命令执行的成功与否就是while条件的真或假。

#输入
#!/bin/bash
SUM=0
i=0
while [ $i -le $1 ]
do
        SUM=$[$SUM+$i]
        i=$[$i+1]
done
echo "SUM is $SUM"

#输出
#  ./testWhile.sh 10
SUM is 55

# 第二种写法
while 命令
do
    循环体
done < 文件名 or > 文件名
#!/bin/bash
#从/etc/passwd文件中读取用户名并输出
oldIFS=$IFS #IFS是文件内部分隔符
IFS=":"     #设置分隔符为:
while  read username var #var变量不可少
do
    echo "用户名:$username"
done < /etc/passwd 
IFS=$oldIFS
'''熟悉/etc/passwd文件结构的朋友都知道这个文件的每一行包含了一个用户的大量信息（用户名只是第一项）。 这里我们实际只输出了用户名。但是注意while的read后面除变量username外还有个var，尽管我们并不输出这个变量的值。 但它却必不可少，如果我们写成while read username那么username的值等于passwd文件这一整行的内容（IFS=”:”也就不起作用了）
'''
#!/bin/bash
#每隔10分钟，ping一下局域网内主机192.168.1.101，
#并把结果记录到ping.txt文件中
while date
do
    ping -c5 192.168.1.101 >/dev/null 2>&1 
    if [ $? = 0 ];then
        echo OK
    else
        echo FAIL
    fi
    sleep 600 #600秒是10分钟
done > ping.txt
# linux 中0:表示键盘输入(stdin)，1:表示标准输出(stdout),系统默认是1， 2:表示错误输出(stderr)
# /dev/null代表linux的空设备文件，所有往这个文件里面写入的内容都会丢失，俗称“黑洞”。那么执行了>/dev/null之后，标准输出就会不再存在，没有任何地方能够找到输出的内容。
# 2>&1 这条命令用到了重定向绑定，采用&可以将两个输出绑定在一起。这条命令的作用是错误输出将和标准输出同用一个文件描述符，说人话就是错误输出将会和标准输出输出到同一个地方。
# command >/dev/null 2>&1 执行了这条命令之后，该条shell命令将不会输出任何信息到控制台，也不会有任何信息输出到文件中。
# cat ping.txt 可见
2015年 01月 31日 星期六 16:03:13 CST
OK
```

## read

​		读取控制台输入。

![image-20220116221011014](shell%E7%AE%80%E4%BB%8B.assets/image-20220116221011014.png)

```bash
# 输入
#!/bin/bash
read -p "请输入一个数NUM1:" NUM1
echo "你输入的数是$NUM1"

read -t 10 -p "请在10s内输入一个数：" NUM2
echo "你输入的数是$NUM2"

# 输出
请输入一个数NUM1:1
你输入的数是1
请在10s内输入一个数：你输入的数是  # 因为第二次我没及时输入。

```



# shell的函数

## 系统函数

​		韩介绍了两个，和别的编程语言大同小异,其实大家 查查文档就好。

```bash
# basename 用于获取文件名，去掉/等杂的东西，也可去掉后缀。
basename /home/my/test.txt
# 输出 test.txt
basename /home/my/test.txt .txt
# 输出 test
```

```bash
# dirname 返回完整路径。
dirname /home/my/hello.txt
# 输出/home/my
```

## 自定义函数

​		![image-20220116224551707](shell%E7%AE%80%E4%BB%8B.assets/image-20220116224551707.png)	

```bash
# shell 文件内写如下内容：
#!/bin/bash
# 定义函数
function GetSum() {
        SUM=$[$n1+$n2]
        echo "两者和是$SUM"
}
read -p "输入一个数n1" n1
read -p "输入第二个数n2" n2

# 调用函数
GetSum $n1 $n2

# 运行 ./testFun.sh 
输入一个数n1100
输入第二个数n2200
两者和是300

```

​		

# shell综合案例

​		![image-20220117142638415](shell%E7%AE%80%E4%BB%8B.assets/image-20220117142638415.png)

​		![image-20220117145640323](shell%E7%AE%80%E4%BB%8B.assets/image-20220117145640323.png)

## 补充知识点：

```bash
find 位置 -ctime +10 -name "a.txt"
# 在此位置找10天前建立的 名字为a.txt 的文件。find 的搜索模板 可以用正则的通配符；
-mtime   -n +n                #按文件更改时间来查找文件，-n指n天以内，+n指n天以前
-atime    -n +n               #按文件访问时间来查GIN: 0px">
-ctime    -n +n              #按文件创建时间来查找文件，-n指n天以内，+n指n天以前

XXX -exec rm {} \;
XXX -exec rm {} \+
# 将前面XXX得到的 结果 拼接到{}中，然后执行exec后面的内容，这里是进行删除操作。
# 第一行的写法是将找到的文件名拼接到 {} 中，然后执行命令。第二行类似，不同的是把所有文件名拼接成一条命令。如下：
rm 1
rm 2
rm 3
rm 1 2 3

-e filename #如果 filename存在，则为真
-d filename #如果 filename为目录，则为真 
-f filename #如果 filename为常规文件，则为真
-L filename #如果 filename为符号链接，则为真
-r filename #如果 filename可读，则为真 

# Shell 里面的中括号（包括单中括号与双中括号）可用于一些条件的测试：
# 算术比较, 比如一个变量是否为0, [ $var -eq 0 ]。
# 文件属性测试，比如一个文件是否存在，[ -e $var ], 是否是目录，[ -d $var ]。
# 字符串比较, 比如两个字符串是否相同， [[ $var1 = $var2 ]]。


# | 在Linux中 | 是作为管道符来使用的，将 ‘|’前面命令的输入当做后面命令的输入；
# || ，用 || 分割的命令具有短路效应，及如果前面的命令为真，在后面的命令不会执行，如果前面的命令为假，则继续执行后面的命令；
# & ，用 & 连接的多个符号将同时执行，不管命令是否执行成功， & 放在命令最后 表示后台运行；
# && ，用&&连接的符号也具有短路效应，即当前面的命令执行的结果为假时，后面的命令将不会得到执行，只有当前面的命令执行结果为真时后面的命令才会得到执行；



```

## 创建备份脚本

```bash
cd /usr/sbin  # 习惯root用户在这个文件夹里面写脚本。
vim mysql_db_backup.sh # 创建备份脚本。
```

```bash
#!/bin/bash
# 备份目录位置
BACKUP=/data/backup/db
# 当前时间
DATETIME=$(date +%Y-%m-%d_%H%M%S)
echo "$DATETIME"  # 检查
# 数据库地址
HOST=localhost
# 数据库用户名
DB_USER=root
# 数据库密码
DB_PW=my123456
# 备份的数据库名
DATABASE=FirstTestDatabase

# 如果备份目录位置不存在，就创建一个。
[ ! -d $BACKUP/$DATETIME ] && mkdir -p "$BACKUP/$DATETIME"  # -p 是创建多级用户。

# 备份数据库
mysqldump -u$DB_USER -p$DB_PW --host=$HOST -q -R --databases $DATABASE > $BACKUP/$DATETIME/$DATETIME.txt

# 将日期文件夹连带备份文件打包，并删除原文件夹和文件。
cd $BACKUP
tar -zcvf $DATETIME.tar.gz $DATETIME
rm -rf $DATETIME

# 逐个删除10天前的备份文件
find $BACKUP -atime +10 -name "*.tar.gz" -exec rm -rf {} \; 
echo "数据库$DATABASES 成功备份！"

```

## 定时调用

```bash
crontab -e
*/1 * * * *  /usr/sbin/mysql_db_backup.sh
# 为了快速看见效果，我定隔一分钟搞一下这脚本。
30 2 * * * sh /usr/sbin/mysql_db_backup.sh
```

