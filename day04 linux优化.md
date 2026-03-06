# day04 linux优化

## 一. 安装常用工具

### 1.1 Centos

### 1.2 麒麟Linux

#### 1.2.1 检查网络情况

```sh
ping baidu.com #结果有个time即可.
```

#### 12.2 配置yum源并安装常用工具

yum 红帽类系统安装软件的命令.

给麒麟增加一个软件下载地址(epel  centos7地址)

```sh
#1.下载额外的软件源
yum install -y wget
wget -O /etc/yum.repos.d/epel.repo https://mirrors.aliyun.com/repo/epel-7.repo
#      指定文件                    指定下载地址
#wget用于下载指定的文件/软件包
-O指定下载到哪里,指定文件名.

#2.检查
ll /etc/yum.repos.d/

#安装Linux常用工具
yum install -y    tree telnet  vim wget  bash-completion    lrzsz  net-tools sysstat  iotop iftop htop unzip nc nmap telnet bc  psmisc httpd-tools  bind-utils nethogs expect


#检查 运行命令不提示命令找不到即可.
运行tree命令
运行telnet 10.0.0.200 22 

这里会有些提示
未找到匹配的参数: iftop #先略过即可.

#麒麟中有趣命令 检查epel是否配置成功.
yum install -y  sl  cowsay  aalib

sl命令
cowsay 66 6命令
animalsay 666 命令
aafire命令
```

| 软件包          | 说明                   |
| --------------- | ---------------------- |
| tree            | 以树形显示目录         |
| telnet          | 测试端口               |
| vim             | 进阶编辑器             |
| bash-completion | 默认自动补全工具包     |
| unzip           | 解压zip格式压缩包      |
| lrzsz           | rz命令和sz命令         |
| net-tools       | net-tools              |
| expect          | mkpasswd命令           |
| psmisc          | pstree,killall命令     |
| htop            | htop命令,top命令升级版 |

### 1.3  Ubuntu配置 (22.04)

Ubt和debian系统中安装软件用的是apt命令.

#### 1.3.1 配置apt下载地址

清华大学ubt的apt源地址： https://mirrors.tuna.tsinghua.edu.cn/help/ubuntu/

```sh
#0.切换root用户
sudo su -

#1.备份/etc/apt/sources.list文件 到/tmp/
cp /etc/apt/sources.list  /tmp/

#2.vim编辑/etc/apt/sources.list 文件
dG #vi/vim快捷键 删除当前行到最后一行的内容.


#3.下面清华下载地址与阿里下载地址 2选1 .

```

```sh
清华源-ubt-22.04-jammy

# 默认注释了源码镜像以提高 apt update 速度，如有需要可自行取消注释
deb http://mirrors.tuna.tsinghua.edu.cn/ubuntu/ jammy main restricted universe multiverse
# deb-src http://mirrors.tuna.tsinghua.edu.cn/ubuntu/ jammy main restricted universe multiverse
deb http://mirrors.tuna.tsinghua.edu.cn/ubuntu/ jammy-updates main restricted universe multiverse
# deb-src http://mirrors.tuna.tsinghua.edu.cn/ubuntu/ jammy-updates main restricted universe multiverse
deb http://mirrors.tuna.tsinghua.edu.cn/ubuntu/ jammy-backports main restricted universe multiverse
# deb-src http://mirrors.tuna.tsinghua.edu.cn/ubuntu/ jammy-backports main restricted universe multiverse

deb http://security.ubuntu.com/ubuntu/ jammy-security main restricted universe multiverse
# deb-src http://security.ubuntu.com/ubuntu/ jammy-security main restricted universe multiverse

# 预发布软件源，不建议启用
# deb http://mirrors.tuna.tsinghua.edu.cn/ubuntu/ jammy-proposed main restricted universe multiverse
# # deb-src http://mirrors.tuna.tsinghua.edu.cn/ubuntu/ jammy-proposed main restricted universe multiverse
```

```sh
阿里云源-ubt-22.04-jammy

deb https://mirrors.aliyun.com/ubuntu/ jammy main restricted universe multiverse
deb-src https://mirrors.aliyun.com/ubuntu/ jammy main restricted universe multiverse

deb https://mirrors.aliyun.com/ubuntu/ jammy-security main restricted universe multiverse
deb-src https://mirrors.aliyun.com/ubuntu/ jammy-security main restricted universe multiverse

deb https://mirrors.aliyun.com/ubuntu/ jammy-updates main restricted universe multiverse
deb-src https://mirrors.aliyun.com/ubuntu/ jammy-updates main restricted universe multiverse

# deb https://mirrors.aliyun.com/ubuntu/ jammy-proposed main restricted universe multiverse
# deb-src https://mirrors.aliyun.com/ubuntu/ jammy-proposed main restricted universe multiverse

deb https://mirrors.aliyun.com/ubuntu/ jammy-backports main restricted universe multiverse
deb-src https://mirrors.aliyun.com/ubuntu/ jammy-backports main restricted universe multiverse

```

```sh
配置完成后续操作

#4.执行apt update 生成本地软件包缓存,如果不执行则下载软件失败.   修改了sources.list文件.
apt update 

#4.安装软件
apt install -y tree  telnet  vim   lrzsz   wget    unzip 

#5.趣味软件
apt install -y cmatrix lolcat  nyancat screenfetch  libaa-bin

nyancat 彩虹猫
lolcat
cmatrix
screenfetch 信息展示
aafire 
```

## 二. 配置命令行颜色

### 2.1 修改命令提示符

### 2.2 麒麟，红帽

修改命令行提示,修改一次即可.

```sh
#配置
echo "export PS1='[\[\e[34;1m\]\u@\[\e[0m\]\[\e[32;1m\]\H\[\e[0m\]\[\e[31;1m\] \w\[\e[0m\]]\\$ '" >>/etc/profile
#生效
source /etc/profile

#把配置命令行的命令追加到/etc/profile中.
#重新连接Linux就生效.
#也可以通过查看/etc/profile文件内容进行检查

cat /etc/profile看最后一行
```

### 2.3 ubt系统

```sh
#1.切换到root 
sudo su -


#2.执行指令(针对用户的)
echo "export PS1='[\[\e[34;1m\]\u@\[\e[0m\]\[\e[32;1m\]\H\[\e[0m\]\[\e[31;1m\] \w\[\e[0m\]]\\$ '" >>~/.bashrc

#3.重新登录后检查颜色

```

## 三.  关闭与检查防火墙

#### 3.1 红帽类系统

```sh
#红帽类系统-关闭防火墙
systemctl disable   --now   firewalld

systemctl status firewalld
```

![image-20251230112214119](C:\Users\31345\AppData\Roaming\Typora\typora-user-images\image-20251230112214119.png)

#### 3.2 ubt系统

```sh
 #ubt
systemctl disable   --now   ufw

systemctl status ufw 
无绿色即可.
```

![image-20251230112306457](C:\Users\31345\AppData\Roaming\Typora\typora-user-images\image-20251230112306457.png)

## 四. openssh服务配置

openssh就是远程服务. 服务名字叫sshd
  1. 修改远程连接端口
  2. ubt系统root,禁止root远程登录

温馨提示:
 1. 麒麟系统修改端口号后,需要关闭防火墙

检查已有端口 使用的端口

```sh
# 修改ssh端口号前检查端口是否被使用
ss -lntup
```

配置文件:/etc/ssh/sshd_config

| sshd_config的内容                  |                                                      |
| ---------------------------------- | ---------------------------------------------------- |
| Port 22                            | 修改ssh端口号,端口范围1-65535  推荐10000以上 52113   |
| #PermitRootLogin prohibit-password | 右边是非yes,禁止root远程登录,ubt默认禁止root远程登录 |

麒麟

```sh
#1.修改sshd配置文件
vim  /etc/ssh/sshd_config
#2.修改Port 22的行
Port 52113  #去掉注释 
#3.保存退出后重启服务
systemctl restart sshd 

#4.检查端口号
ss -lntup 
```

ubt

```sh
#1.修改sshd配置文件
vim  /etc/ssh/sshd_config

修改Port 22的行
Port 52113  #去掉注释 

找到#PermitRootLogin 去掉注释,
修改为
PermitRootLogin yes

#2.保存退出后重启服务
systemctl restart sshd 

#3.给root设置密码
passwd   

#4重新用户root远程登录即可
```

## 五. 经典故障

### 5.1 ubt

#### 5.1.1 ubt会自动更新

原因:系统在进行检查更新或更新与当前apt命令冲突.

方案：等待

![image-20251230104845326](C:\Users\31345\AppData\Roaming\Typora\typora-user-images\image-20251230104845326.png)

#### 5.1.2 403 forbidden拒绝

背景: 执行apt update 报错

提示 403 forbidden 拒绝.

解决: 更换源下载地址.

![image-20251230105247842](C:\Users\31345\AppData\Roaming\Typora\typora-user-images\image-20251230105247842.png)

#### 5.1.3 permission denied 权限拒绝

背景: 所有操作使用root用户,普通用户运行报错了.

解决:切换到root

![image-20251230105343476](C:\Users\31345\AppData\Roaming\Typora\typora-user-images\image-20251230105343476.png)

#### 5.1.4 apt install 的时候提示 unable  to locate package 无法定位软件包(查找软件包)

故障提示: unable  to locate package

原因:

1. 修改apt源配置但是没有执行apt update
2. apt的源的问题.

解决：重新apt update或重新配置源.

没有执行apt update

![image-20251230111223283](C:\Users\31345\AppData\Roaming\Typora\typora-user-images\image-20251230111223283.png)

apt配置文件有问题

![image-20251230111247370](C:\Users\31345\AppData\Roaming\Typora\typora-user-images\image-20251230111247370.png)

#### 5.1.5 检查系统版本

cat /etc/os-release

![image-20251230111350899](C:\Users\31345\AppData\Roaming\Typora\typora-user-images\image-20251230111350899.png)

#### 5.1.6 ubt网卡配置文件与修改生效

```sh
#1.编辑
root@oldboy-99-ubt:~# cat /etc/netplan/00-installer-config.yaml
# This is the network config written by 'subiquity'
network:
  ethernets:
    ens33:
      addresses:
      - 10.0.0.202/24
      nameservers:
        addresses:
        - 223.5.5.5
        - 223.6.6.6
        search: []
      routes:
      - to: default
        via: 10.0.0.2
  version: 2
#2.生效
netplan  apply 
networkctl reload 

```

## 补充：{ }生成序列

![img](https://dl4.weshineapp.com/gif/20200807/86259d4919e59ed9f7f085b47a5d997f.gif?f=micro_)

叫法：

（）小括号

  [ ]  中括号

  { }  大括号/花括号

1.基础格式

生成数字序列

```sh
echo {1..100}
```

生成字母序列

```sh
echo {a..z}
echo {A..Z}
```

把输出写入文件

```sh1
echo {1.100} > oldboy.txt
```

批量创建文件

```sh
# 查看格式，不会创建
echo touch oldboy{1..100}.txt
# 正在运行
touch oldboy{1..100}.txt
```

批量创建目录

```sh
# 查看格式，不会创建
echo mkdir oldboydir{01..10}
# 正在运行
mkdir oldboydir{01..10}
```

用法补充：

```sh
echo {1..10..2} #表示从1开始到10结束 每次增加2，用法极少
```

seq命令，生成数字序列，每个数字是1行

```sh
seq 10 生成10个数字 核心
seq 5 20 生成5到20范围的数字
seq 5 2 20 生成5到20范围数字 每次增加2
```

![img](https://dl4.weshineapp.com/gif/20170814/b8184b3cc27a999410872f543388cdf6.gif?f=micro_)
