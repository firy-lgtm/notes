# day06 日志分析及查看

## 一.脚本

脚本：命令大礼包。一些命令写到同一个文件中运行

脚本文件要求：

1.以.sh或.bash结尾

2.文件开头要写明信息

3.准备专用目录：/server/scriipts

![img](https://dl4.weshineapp.com/gif/20220415/842d7ae57aca946c82fe780fe9e37e0d.gif?f=micro_)

```sh
# 1.创建目录
mkdir /server/scripts

# 2.创建文件并编辑
vim /server/scripts/01.sys_info.sh

#!/bin/bash
# author：lidao996
# desc：系统巡检脚本
echo "======= 系统基本信息 ======="
echo "主机名    : $(hostname)"
echo "操作系统  : $(grep PRETTY_NAME /etc/os-release 2>/dev/null | cut -d= -f2 | tr -d '"' || uname -s)"
echo "内核版本  : $(uname -r)"
echo "系统架构  : $(uname -m)"
echo "CPU信息   : $(grep "model name" /proc/cpuinfo | head -1 | cut -d: -f2 | xargs)"
echo "CPU核心数 : $(grep -c "^processor" /proc/cpuinfo)"
echo "内存总量  : $(free -h | grep Mem | awk '{print $2}')"
echo "内存可用  : $(free -h | grep Mem | awk '{print $7}')"
echo "磁盘使用  : $(df -h / | tail -1 | awk '{print $5}') 已用"
echo "IP地址    : $(hostname -I 2>/dev/null | awk '{print $1}' || ip addr show 2>/dev/null | grep "inet " | grep -v  127.0.0.1 | head -1 | awk '{print $2}' | cut -d/ -f1)"
echo "运行时间  : $(uptime -p)"
echo "系统负载  : $(uptime | awk -F'load average:' '{print $2}')"
echo "==========================="

#运行
bash 01.sys_info.sh
```

注意：

先测试好命令然后写入脚本

top-bn1 非交互模式的top方便后续过滤加工

## 二. 故障案例：某开发人员在测试环境中使用vim编辑10G日志导致系统内存不足？避免

背景：

开发人员想通过查看日志方式,看代码/程序是否有问题.

通过vim编辑日志

突然发系统崩溃了.

步骤：

结束对应进程.

重启linux.

原因:vim编辑文件,文件加载到内存中,文件过大.

避免:

查看日志不要使用cat和vim.

查看日志的命令组合,tail/head,less/more,grep.

## 三. 查看日志文件命令组合

### 3.1 tail与head

tail 尾部 查看文件末尾的内容，默认查看10行

head 头部 查看文件开头的内容，默认查看10行

```sh
# 查看文件默认的5行 eg：/etc/passwd
tail -5 /etc/passwd
head -5 /etc/passwd

# 查看末尾的实时更新
tail -f /var/log/messages
注意：只有tail有 -f 选项，head没有
```

### 3.2 grep在文件中查找过滤

四剑客：

1. find

2. grep

3. sed

4. awk

   过滤出我们想要的或不要的内容

   grep仅仅过滤，查看，无法修改文件内容

   | grep命令 | 说明                               |
   | -------- | ---------------------------------- |
   | -i       | ignore case忽略大小写,不区分大小写 |
   | -n       | 给过滤出的内容加上行号.            |
   | -v       | 排除/取反.包含指定内容的行不要了.  |
   | -color   | 给过滤出出来的内容,加上颜色.       |


### 3.3 less/more组合

按页查看文件内容,类似于vim.但是less/more无法修改文件内容.

less快捷键查看/etc/services

```sh
q 退出
G 最后一行
g 第一行
100g 某一行
空格或ctrl+f 按页查看 下一页
ctrl+b 上一页
```

more按页查看,到文件最后一行自动退出.

```sh
空格或ctrl+f 按页查看 下一页
ctrl+b 上一页
```

## 四.日志分析预备

wc 统计文件行数

| 管道

### 4.1 wc

统计/etc/passwd 行数

```sh
wc -l /etc/passwd #小结字母L wc -l
```

注意：

wc‘核心与其他命令一起搭配使用

统计数量

直接执行 wc 文件名：输出文件的行数、单词数、字节数

结合选项可单独统计某一项（如 -l 统计行数、-w 统计单词数、-c 统计字节数

### 4.2 管道 |

左边右边加上命令

管道：类似于接力，前面一个命令执行，把命令输出的通过管道传递给后面的命令

![image-20260104165459597](C:\Users\31345\AppData\Roaming\Typora\typora-user-images\image-20260104165459597.png)