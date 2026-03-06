# day18 专题-定时任务

## 一、故障案例

### 1.1定时任务运行命令或脚本

故障现象：

- 脚本或命令在命令行可以直接运行
- 但是把他们写入定时任务中则无法执行或输出

原因：

- 定时任务在运行命令或脚本的时候PATH只有/bin和/usr/bin没有/sbin/或其他路径
- 导致定时任务运行命令或脚本，/bin/以外的命令，无法识别，提示命令找不到

解决：

- 针对特定的命令使用绝对路径，不推荐，命令较少可以这样做

根本方法：

- 在脚本开头重新定义export PATH=/usr/bin/:/usr/sbin/:/usr/local/bin/:/usr/local/sbin
- 在脚本开头source /etc/profile

### 1.2定向到空问题

- 定向到黑洞/dev/null，不需要记录命令,脚本执行过程,结果.
- 追加到文件>>/var/log/test.log 2>&1
- 命令或脚本定向到空>/dev/null2>&1或追加到文件>>/var/log/test.log 2>&1
- 邮件服务开启postfix,以邮件形式发送给root.
- 没有开启，输出到临时文件(小文件)->inode不足。

## 二、定时表白

准备阶段：

- 1.准备俩个邮箱：163邮箱和qq邮箱
- 163邮箱为发件人，qq邮箱为收件人

### 2.1发送测试

麒麟/centos

```sh
yum install -y sendmail

# 使用
echo 123 | mail -s 每日表白 xxxqq.com 
123 #为内容
-s #每日表白为标题
```

### 2.2 配置发件人

```sh
yum install -y mailx

# 进入mail.rc 
vim /etc/mail.rc

# 在最后添加
set from=  配置发件人邮箱
set smtp=smtp.163.com                  配置使用163发送邮件 qq邮件服务器  smtp.qq.com
set smtp-auth-user=    用户名:邮箱名字
set smtp-auth-password=授权码        T  授权码,不是邮件的密码,需要登录邮箱后开启或申请.
set smtp-auth=login                    认证形式login
```

### 2.3 书写表白脚本

```var
vim /server/scripts/文件名.sh

#内容
#!/bin/bash
#author:lidoa996
#desc:每日土味情话

#1.vars
url="使用自己的api接口"
loves=”发件人邮箱 收件人邮箱“
echo $mail
curl -s $ur1 | mailx -s "每日情话" $loves

#发送脚本
crontab -e

#内容
* * * * * sh 文件绝对路径 >/dev/null 2>&1

注意：这里的*表示分，时，日，月，周
```

## 三、定时巡检与发送检查结果

```sh
#!/bin/bash
#author:dani996
#desc:系统巡检脚本
export PATH=/app/tools/mysql/bin/:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin
export LANG=en_US.UTF-8

#1.系统基本信息
##vars
sys_time=`date +%F`
sys_release=`grep -i 'pretty_name' /etc/os-release |awk -F '=' '{print $2}'`
sys_kernel=`hostnamectl |grep -i 'kernel' |awk -F ':' '{print $2}'`
sys_load=`w |grep -i 'load' |awk -F ':' '{print $NF}'`
sys_name=`hostname`
echo "############################################"
echo "##1.基本信息################################"
echo "日期：$sys_time"
echo "发行版本信息:$sys_release"
echo "内核版本：$sys_kernel"
echo "负载信息：$sys_load"
echo "主机名：$sys_name"
echo "############################################"


#2.cpu信息
##vars
cpu_architecture=`lscpu |grep -i 'Architecture'|awk '{print $2}'`
cpu_core=`lscpu |grep -i 'Core(s)'|awk '{print $NF}'`
cpu_counts=`lscpu |grep -i ^'Cpu(s)' |awk '{print $2}'`
echo "##2.cup信息##################################"
echo "cpu架构:$cpu_architecture"
echo "cpu核心数:$cpu_core"
echo "cpu个数:$cpu_counts"
echo "#############################################"


#3.内存信息
##vars
mem_total=`free -h |grep -i 'mem' |awk '{print $2}'`
mem_ava=`free -h |grep -i 'mem' |awk '{print $NF}'`
echo "##3.内存信息#################################"
echo "内存总大小:$mem_total"
echo "内存可用大小:$mem_ava"
echo "#############################################"

#4进程信息
##var
proc_total=`ps -ef|wc -l`
proc_sshd_count=`ps -ef |grep 'sshd' |grep -v 'grep'|wc -l`
echo "##4.进程信息#################################"
echo "进程总数:$proc_total"
echo "sshd服务进程数量:$proc_sshd_count"
echo "#############################################"

#5.网络信息
##vars
net_ip=`ip a s ens33 |grep 'inet ' |awk '{print $2}'`
net_ports=`ss -lntup |tail -n+2 |awk '{print $5}' |awk -F ':' '{print $NF}' |uniq |xargs`
echo "ip地址:$net_ip"
echo "系统存在的端口:$net_ports"


#6.all in one 
file="/var/log/xx.log"   #这里的文件要自己创建
cat >$file<<EOF
 "############################################"
 "##1.基本信息################################"
 "日期：$sys_time"
 "发行版本信息:$sys_release"
 "内核版本：$sys_kernel"
 "负载信息：$sys_load"
 "主机名：$sys_name"
 "############################################"
 "##2.cup信息##################################"
 "cpu架构:$cpu_architecture"
 "cpu核心数:$cpu_core"
 "cpu个数:$cpu_counts"
 "#############################################"
 "##3.内存信息#################################"
 "内存总大小:$mem_total"
 "内存可用大小:$mem_ava"
 "#############################################"
 "##4.进程信息#################################"
 "进程总数:$proc_total"
 "sshd服务进程数量:$proc_sshd_count"
 "#############################################"
 "##5.网络信息#################################"
 "ip地址:$net_ip"
 "系统存在的端口:$net_ports"
 "#############################################"
EOF

#7.发送邮件
cat $file | mail -s "每日巡检-$sys_time"  3134506919@qq.com

```

```sh
#书写定时任务
crontab -e
* * * * * bash /server/scripts/02.sys_info.sh >>/var/log/syscheck.log 2>&1
```

## 四、四剑客-sed

### 替换

```sh 
sed 's#被替换#替换内容#g' 文件名
s 表示替换
g 表示全局替换
### 分割作用
可以换为 @@@ 或 ///
```

