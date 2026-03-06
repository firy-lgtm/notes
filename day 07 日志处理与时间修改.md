# day07 日志处理与时间修改😊

## 一. 日志处理后续

### 1.1 sort排序

| sort选项 | 说明                                         |
| -------- | -------------------------------------------- |
| -n       | 对数字排序                                   |
| -r       | 逆序排序（常用）                             |
| -k       | 指定列                                       |
| -h       | 前面命令涉及到人类可读大小，排序-h，不要用-n |

对文件内容排序

```sh
sort -n sort.txt 正常排序
sort -rn sort.txt 逆序排序
```

排序后取出数字最大的5个

```sh
sort -rn sort.txt | head -5
```

sort对多列内容进行排序

对ll /etc/ 命令结果中的第5列排序并列出前5

```sh
ll /etc/ |sort -rnk5 | head -5
```

对ps -ef的第二列排序，取最大的前5个

```sh
ps -ef | sort -nrk2 | head -5
```

du -sh命令 查看指定目录的大小 du -sh /*  查看根下所有目录的大小,对命令结果排序取出最大5个.

```sh
du -sh /* | sort -hrk1 | head -5
```

### 1.2 uniq 命令😎

一般不单独使用

```sh
# 编辑一个文件uniq.txt
# 查看文件内容同意样的内容有多少
sort -nr uniq.txt | uniq -c

注：去重用uniq
```

### 1.3 日志分析

#### 2.3.1 上传文件到Linux

Irzsz 软件包(通过远程连接工具上传文件.)

rz 上传运行命令选择上传的文件即可(windows->linux)

sz 下载  sz/etc/hostname   （linux->windows）

```sh
###红帽系统###
# 1.安装lrzsz
yum -y install lrzsz

# 2.检查
rpm -qa | grep lrzsz

###debian/ubt###
# 1.安装
apt update
apt install -y lrzsz

# 2.检查
dpkg -l | grep lrzsz
```

unzip用于解压 .zip 结尾的压缩包

```sh
unzip 文件名
```

#### 2.3.2 日志分析



secure文件关键单词Failed password的行,倒数第4列ip,统计每个ip次数,取出前5.

目标:分析这个文件破解你密码的ip,ip次数

关键词Failed password

```sh
#0.在文件中过滤出想要的内容:grep  过滤failed password的行
#1.awk取出倒数第4列
#2.相同ip挨着 排序
#3.去重统计次数 
#4.加工: 对次数排序
#5.加工: 取出次数最多的5个
grep -i 'failed password' secure-20161219 | awk '{print$(NF-3)}' | sort | uniq -c | sort -nrk1 | head -5
```

#### 小结（主要流程）

过滤出我们要的内容，grep，awk

相同

对解暑进行加工，sor

取出前几个

![img](https://dl4.weshineapp.com/gif/20220315/0a9a0cdc1536e982aaad8b974faf044f.gif?f=micro_)

## 二.时间查看与修改

### 2.1 修改时间与修改时区

```sh
# 手动修改（不建议）

# 自动修改
# 1.安装ntpdate
yum -y install ntpdate (麒麟)
apt -y install ntpdate (ubt)

# 2.查看时区timedatectl
timedatectl

# 3.修改时区
timedatectl set-timezone Asia/Shanghai

# 4.自动修改时间(这里使用阿里云的时间服务器)
ntpdate ntp.aliyun.com

# 5.查看时间
date
```

### 2.2 以指定格式显示时间/日期

```sh
date +%F  # F 表示年月日
date +%T  # T 表示时分秒
```

### 2.3 反引号与时间

上面写的显示指定的日期(时间).

未来使用在创建文件,创建目录,创建压缩包时候.

创建带有时间/日期的文件目录,压缩包.

![img](https://dl4.weshineapp.com/gif/20220718/034128cf68e94328a8efa59cb74c29ea.gif?f=micro_)

反引号:命令,命令要有输出.

**优先执行**，运行命令的时候选运行反引号里面的命令，命令会把输出留下来。

```sh
''或$() 说明:优先执行,运行命令的时候先运行反引号里面的命令.命令会把输出留下来.
创建带有时间，日期的文件.
未来广泛使用在脚本中.

# 创建带有日期的文件
touch lidao-`date +%F`.txt

#创建带有日期 年-月-日_周几的目录
mkdir -p /lidao/$(date +%F_%w)
```

## 三.再战脚本😒

![img](https://dl4.weshineapp.com/gif/20170222/3265a1ec7a1d7eaab9e0fb05e993661e.gif?f=micro_5Yqq5Yqb)

### 3.1 变量😂？

| 变量对比  | 变量名字   | 书的名字                         |
| --------- | ---------- | -------------------------------- |
| 名字      | kui        | 葵花宝典                         |
| 查看      | echo $kui  | 看书                             |
| 创建/修改 | kui=“内容” | 欲练此功必先自宫若不自宫也能成功 |

![image-20260105150648544](C:\Users\31345\AppData\Roaming\Typora\typora-user-images\image-20260105150648544.png)

### 3.2 变量名字的要求

- 不能以数字开头

- 变量有最好有一定含义

- user_name username 用户名字

- sys_release_name 系统发行版本

- 使用_即可


### 4.3 变量与脚本

```sh
# 用法列：
yhm=`hostnamectl | grep 'Operating System' | awk -F ': ' '{print $2}'`
# 输出
echo $yhm
# 结果
Kylin Linux Advanced Server V10 (Lance)
```

## 今日总结

- grep,awk,sort,uniq,

- 目标:分析acess.log和secure日志

- 时间相关命令:date,ntpdate,timedatectl

- 反引号:优先执行加上命令,输出.

- 重新书写脚本:变量

- 目标:书写巡检脚本,跟着课程不断完善巡检脚本


![img](https://dl4.weshineapp.com/gif/20191223/c4e4e939c46d3286c322f210f3e5e225.gif?f=micro_5Yqg5rK5)