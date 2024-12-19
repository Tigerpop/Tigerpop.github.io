---
layout: posts
title: "linux经典面试题"
date: 2022-10-29 15:42:07
description: "这是文章开头，显示在主页面，详情请点击此处。"
categories: 
- "Linux"
tags:
- "linux"
---

简介 <!--more-->

![image-20220120124414838](Linux%E7%BB%8F%E5%85%B8%E9%9D%A2%E8%AF%95%E9%A2%98.assets/image-20220120124414838-7031132.png)

```bash
 cat t.log | cut -d '/' -f 3 | sort | uniq -c | sort -nr -k 1    # cut 不能用空格来分割，awk 可以。uniq 可以把相邻的重复的行删除仅保留一行；-c 参数是用来统计相同行数量。sort -nr 从大到小排序，注意：sort 是对行 排序。 可以   man 命令 查看uniq 的相应用法 man uniq。
```



![image-20220121152234629](Linux%E7%BB%8F%E5%85%B8%E9%9D%A2%E8%AF%95%E9%A2%98.assets/image-20220121152234629.png)

```bash
netstat -anp | more # 查看端口与协议的对应关系。
```

![image-20220121152621661](Linux%E7%BB%8F%E5%85%B8%E9%9D%A2%E8%AF%95%E9%A2%98.assets/image-20220121152621661.png)

![image-20220121152501114](Linux%E7%BB%8F%E5%85%B8%E9%9D%A2%E8%AF%95%E9%A2%98.assets/image-20220121152501114.png)

```bash
 netstat -anp | grep "ESTABLISHED" | awk -F " " '{print $5}' | gawk -F ":" '{print $1}' | sort | uniq -c | sort -nr

# awk/gawk -F "分割标志" '{print $分割后的第n段}' 
# awk 和 cut 很类似，但是awk 用面广一点。
netstat -anp | grep "ESTABLISHED" | awk -F ' ' '{print $5}' | gawk -F ':' '{print $1}' | uniq -c | sort -nr -k 1 | awk -F " " '{print $2}'
```



![image-20220121184754596](Linux%E7%BB%8F%E5%85%B8%E9%9D%A2%E8%AF%95%E9%A2%98.assets/image-20220121184754596.png)

```bash
vim /etc/my.cnf   									# 更改mysql的进入密码设置。
# my.cof 内添加如下内容：跳过权限表(空密码登录)。
skip-grant-tables
# 注释掉  validate_password=off

systemctl restart mysqld.service 		# 重启mysql服务
mysql -u root -p  								  # 空密码登录

show databases;  										# 会看见一个mysql 的数据库
use mysql;
show tables; 											  # 会看见一个user表
desc user;												  # 找到字段authentication_string

update user set authentication_string=password("mysql654321") where user='root';  																		# 修改此字段更改初始密码为mysql654321

flush privileges;  									# 刷新mysql source 文件 linux 这样快速生效。
exit;

vim /etc/my.cof   									# 去这里注释掉（空密码登录）
# skip-grant-tables  

systemctl restart mysqld.service
mysql -u root -p 										# 用新的密码登录。
```



![image-20220121190959996](Linux%E7%BB%8F%E5%85%B8%E9%9D%A2%E8%AF%95%E9%A2%98.assets/image-20220121190959996.png)![image-20220121191322065](Linux%E7%BB%8F%E5%85%B8%E9%9D%A2%E8%AF%95%E9%A2%98.assets/image-20220121191322065.png)

```bash
cat access.log | awk -F " " '{print $1}' | sort | uniq -c | sort -nr | head -n 2
```



![image-20220121191714616](Linux%E7%BB%8F%E5%85%B8%E9%9D%A2%E8%AF%95%E9%A2%98.assets/image-20220121191714616.png)

```bash
# 假如要用来监听的，本机网络设备是 ens160
tcpdump -i ens160 host 192.168.200.1 and port 22 >> /home/tcpdump.log
```



![image-20220121194037639](Linux%E7%BB%8F%E5%85%B8%E9%9D%A2%E8%AF%95%E9%A2%98.assets/image-20220121194037639.png)

```bash
rewrite # 重写
ssl     # 安全加密

```



![image-20220121194312392](Linux%E7%BB%8F%E5%85%B8%E9%9D%A2%E8%AF%95%E9%A2%98.assets/image-20220121194312392.png)

![image-20220121194623810](Linux%E7%BB%8F%E5%85%B8%E9%9D%A2%E8%AF%95%E9%A2%98.assets/image-20220121194623810.png)

```bash
2) 注意事项
1、注意权限分离，不要把权限集中在一个部门；
2、权限最小化，尽可能少的给权限；
3、用sudo 代替 root用户；
4、用chatter 锁定一些关键文件夹的修改权限； 
# chattr +i /etc/passwd 给密码设置加锁，即便是root用户也呀解锁后才能改。chattr -i /etc/passwd 相应的解锁。
# which chattr / find / -name chattr 都能找到chattr 脚本的位置，所以还能为了保险起见给chattr 移动位置或者改名，记住位置和新名字即可，复原后就又能解锁了。
5、用rootkit hunter 工具检测 利用 rookit 搞的入侵。
6、Tripwire 检测文件系统的完整性。

```



![image-20220121201114476](Linux%E7%BB%8F%E5%85%B8%E9%9D%A2%E8%AF%95%E9%A2%98.assets/image-20220121201114476.png)

![image-20220121201055840](Linux%E7%BB%8F%E5%85%B8%E9%9D%A2%E8%AF%95%E9%A2%98.assets/image-20220121201055840.png)

```bash
1、能读；不能修改；能删除（因为对上级目录有w的权限）。
2、不能读；不能修改；不能删除。（因为上级目录都进不去，什么操作都做不了。）
3、能读；不能修改（因为没w权限）；不能删除（因为没有w权限）。
4、能读；不能修改（虽然有w权限，但是文件本身是只读的。）；能删除（虽然文件只读，但是上级目录有w权限，就能把文件直接删除。）。
```



![image-20220122115417417](Linux%E7%BB%8F%E5%85%B8%E9%9D%A2%E8%AF%95%E9%A2%98.assets/image-20220122115417417.png)

```bash
# 大家直接去看韩顺平老师的讲解吧，太多了，跳过。
```



![image-20220122115826281](Linux%E7%BB%8F%E5%85%B8%E9%9D%A2%E8%AF%95%E9%A2%98.assets/image-20220122115826281.png)

```bash
netstat     #网络状态监控
top					#系统运行状态
lsblk				#看出硬盘分区
find				#查找
ps -aux			#查看运行进程
chkconfig		#查看服务启动状态
systemctl		#管理系统服务器

```



![image-20220122120120510](Linux%E7%BB%8F%E5%85%B8%E9%9D%A2%E8%AF%95%E9%A2%98.assets/image-20220122120120510.png)

```bash
top
iotop # 可能需要先yum 或者apt install iotop
df -lh
netstat
ps -aux | grep XXX

```



![image-20220122120816620](Linux%E7%BB%8F%E5%85%B8%E9%9D%A2%E8%AF%95%E9%A2%98.assets/image-20220122120816620.png)

![image-20220122121518491](Linux%E7%BB%8F%E5%85%B8%E9%9D%A2%E8%AF%95%E9%A2%98.assets/image-20220122121518491.png)

```bash
cat t2.txt | gawk -F " " '{sum+=$2} END {print sum}'
150

# awk脚本，这里不是shell 脚本，没有 $变量 等等写法。而且 BEGIN、END 一定要大写才能生效。
关于 awk 脚本，我们需要注意两个关键词 BEGIN 和 END。

1、BEGIN{ 这里面放的是执行前的语句 }
2、END {这里面放的是处理完所有的行后要执行的语句 }
3、{这里面放的是处理每一行时要执行的语句}
```



![image-20220122122108812](Linux%E7%BB%8F%E5%85%B8%E9%9D%A2%E8%AF%95%E9%A2%98.assets/image-20220122122108812.png)

```bash
if [ -f 文件名 ]
then
	echo "存在"
else 
	echo "不存在"
fi
```



![image-20220122122456073](Linux%E7%BB%8F%E5%85%B8%E9%9D%A2%E8%AF%95%E9%A2%98.assets/image-20220122122456073.png)

```bash
9 8 7 6 5 4 3 2 10
sort -nr t3.txt | awk -F " " '{sum+=$1;print$1} END {print "和等于："sum}'
# 注意求和的过程中要 print $1 不然只有最后的求和。

如果这个是一行的话 
cat t3.txt | xargs -n1 | sort -n | awk -F " " 'BEGIN{sum=0}{sum+=$1;print$1}END{print "sumis" sum}' | awk '{a=a" "$1}END{print a}'
# xargs -n1 是把 一行变成一列。然后就能用 sort 的行排序了。
# {a=a" "$1}END{print a} 是又把列 转成行了，中间用空格隔开。
```



![image-20220122123512597](Linux%E7%BB%8F%E5%85%B8%E9%9D%A2%E8%AF%95%E9%A2%98.assets/image-20220122123512597.png)

```bash
grep -r "cat" /home | cut -d ":" -f 1
# grep -r 是连带着所有的内容递归过滤。
```



![image-20220122123913951](Linux%E7%BB%8F%E5%85%B8%E9%9D%A2%E8%AF%95%E9%A2%98.assets/image-20220122123913951.png)

```bash
find /home -name "*.*" | wc -l
find /home -name "*.*" | xargs wc -l
# find 会自动递归找。 *.* 会把隐藏的也显示。xargs 除了可以进行 行列转换以外，还能 把之前管道的整体内容，转为一行一行 套用到 后面的语句中执行。
```



![image-20220122124830528](Linux%E7%BB%8F%E5%85%B8%E9%9D%A2%E8%AF%95%E9%A2%98.assets/image-20220122124830528.png)

```bash
Nginx
Haproxy
keepalived
LVS
```



![image-20220122125027094](Linux%E7%BB%8F%E5%85%B8%E9%9D%A2%E8%AF%95%E9%A2%98.assets/image-20220122125027094.png)

```bash
vim /root/mail.sh  # 建立sh脚本
# mail.sh文件内写入
#!/bin/bash
cd /var/spool && /bin/tar -zcvf /home/`date +%Y-%m-%d_%H%M%S`.tar.gz /mail
# ； 是都执行、&& 是 前面成功了再执行后面的命令、 ｜｜ 是 前面不成功 再执行后面。

crontab -e      # 给sh脚本定时
# 写入
30 22 * * * /root/mail.sh 

# 可以检查一下定时文件是否ok
crontab -l
```



![image-20220122130803028](Linux%E7%BB%8F%E5%85%B8%E9%9D%A2%E8%AF%95%E9%A2%98.assets/image-20220122130803028.png)

![image-20220122170348153](Linux%E7%BB%8F%E5%85%B8%E9%9D%A2%E8%AF%95%E9%A2%98.assets/image-20220122170348153.png)

```bash
1、用sudo
2、定时自动更新，用阿里云等等。
3、apt / yum 等等用国内源替换，或者用代理。
4、有必要的端口才在防火墙打开，没用的端口关闭。
5、打开最大文件数。vim /etc/profile ulimit -SHn 65535
6、配置合理的备份策略
7、对nginx apache优化。
8、用chattr锁定一些关键文件夹文件 /etc/passwd /etc/shadow /etc/inittab。
等等。
```

 ![截屏2022-10-26 17.31.21](Linux%E7%BB%8F%E5%85%B8%E9%9D%A2%E8%AF%95%E9%A2%98.assets/%E6%88%AA%E5%B1%8F2022-10-26%2017.31.21.jpg)
