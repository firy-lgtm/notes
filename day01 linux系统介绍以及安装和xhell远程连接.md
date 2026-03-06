# day01 linux系统介绍以及安装和xhell远程连接 

## 一，linux介绍

### 1. 常见的Linux系统有哪些？

#### 1.1Debian系列

##### 1.1.1 Debian系统

稳定，更新慢

版本有10 11 12

##### 1.1.2 Ubuntu系统

桌面环境好用，开发人员使用多一些，ubuntu也有服务器版本ubuntu server

版本有22.04 18.04 20.04

##### 1.1.3 Kali系统

安全人员使用，安全扫描，渗透测试，有很多安全工具

#### 1.2 红帽系列

##### 1.2.1 CentOS

基于红帽企业版制作的,开源版本的linux系统.后来被红帽收购了centos 最后一个稳定的版本centos7.9(2009).后续的版本Centos Stream系列与红帽版本保持一致,目前最新版CentOS Stream 9和10
之前:先出RHEL企业版->CentOS
之后: CentOS Stream ->RHEL
使用建议:旧环境保持,新环境可以考虑使用其他linux.

版本有7.x 7.9经典

8.x 不推荐

从9开始CentOS Stream

##### 1.2.2 Rocky

RHEL->Rocky 目前内容使用不多,国外多

版本有9.x 10.x

#### 1.3 国产系列

##### 1.3.1 麒麟Linux

Kylin 国产基本上首选

版本有v10 sp3 23年发布

v11 版本 25年发布

##### 1.3.2 欧拉(华为)

OpenEuler

### 2. 工作环境

#### 2.1 物理服务器

特点：稳定,可以定制,配置可以很高,成本高,考虑到机房,带宽成本

选型：发展后期,访问量高

#### 2.2 虚拟机（虚拟化）

特点：物理机基础上+安装虚拟化软件,服务器利用率高

选型：测试环境,增加利用并

#### 2.3 云服务器

特点：不需要考虑硬件环境,只需要关注使用,使用简单

选型：初创公司,中小型

## 二，安装虚拟机

### 1. 安装麒麟系统

#### 1.1 插入镜像开启虚拟机

![image-20251225163237189](C:\Users\31345\AppData\Roaming\Typora\typora-user-images\image-20251225163237189.png)

#### 1.2 点击第一个

![image-20251225163505123](C:\Users\31345\AppData\Roaming\Typora\typora-user-images\image-20251225163505123.png)

#### 1.3 点击时间，直接点击完成

![image-20251225163557918](C:\Users\31345\AppData\Roaming\Typora\typora-user-images\image-20251225163557918.png)

#### 1.4 点击软件选择，选择如下

![image-20251225163726231](C:\Users\31345\AppData\Roaming\Typora\typora-user-images\image-20251225163726231.png)

![image-20251225163803263](C:\Users\31345\AppData\Roaming\Typora\typora-user-images\image-20251225163803263.png)

#### 1.5 点击安装目的地，直接点击完成

![image-20251225163831992](C:\Users\31345\AppData\Roaming\Typora\typora-user-images\image-20251225163831992.png)

#### 1.6 点击网络，配置如下

![image-20251225163907820](C:\Users\31345\AppData\Roaming\Typora\typora-user-images\image-20251225163907820.png)

![image-20251225163921406](C:\Users\31345\AppData\Roaming\Typora\typora-user-images\image-20251225163921406.png)

![image-20251225163933344](C:\Users\31345\AppData\Roaming\Typora\typora-user-images\image-20251225163933344.png)

![image-20251225163949938](C:\Users\31345\AppData\Roaming\Typora\typora-user-images\image-20251225163949938.png)

#### 1.7 点击用户设置，配置密码

用户名：root，设置密码：Duanlao007

![image-20251225164143814](C:\Users\31345\AppData\Roaming\Typora\typora-user-images\image-20251225164143814.png)

#### 1.8 最后重启系统，一次输入12cc

![image-20251225164238664](C:\Users\31345\AppData\Roaming\Typora\typora-user-images\image-20251225164238664.png)

### 2. 安装ubt系统

#### 2.1 选择英文

![image-20251225164503501](C:\Users\31345\AppData\Roaming\Typora\typora-user-images\image-20251225164503501.png)

#### 2.2 选择继续

![image-20251225164550344](C:\Users\31345\AppData\Roaming\Typora\typora-user-images\image-20251225164550344.png)

#### 2.3 继续

![image-20251225164640231](C:\Users\31345\AppData\Roaming\Typora\typora-user-images\image-20251225164640231.png)

#### 2.4 默认不用最小化

![image-20251225164707597](C:\Users\31345\AppData\Roaming\Typora\typora-user-images\image-20251225164707597.png)

#### 2.5 断网并配置ip

![image-20251225164738547](C:\Users\31345\AppData\Roaming\Typora\typora-user-images\image-20251225164738547.png)

![image-20251225164752646](C:\Users\31345\AppData\Roaming\Typora\typora-user-images\image-20251225164752646.png)

![image-20251225164801704](C:\Users\31345\AppData\Roaming\Typora\typora-user-images\image-20251225164801704.png)

![image-20251225164817158](C:\Users\31345\AppData\Roaming\Typora\typora-user-images\image-20251225164817158.png)

![image-20251225164830093](C:\Users\31345\AppData\Roaming\Typora\typora-user-images\image-20251225164830093.png)

![image-20251225164847688](C:\Users\31345\AppData\Roaming\Typora\typora-user-images\image-20251225164847688.png)

![image-20251225164915986](C:\Users\31345\AppData\Roaming\Typora\typora-user-images\image-20251225164915986.png)

![image-20251225164941885](C:\Users\31345\AppData\Roaming\Typora\typora-user-images\image-20251225164941885.png)

![image-20251225165002071](C:\Users\31345\AppData\Roaming\Typora\typora-user-images\image-20251225165002071.png)

![image-20251225165046313](C:\Users\31345\AppData\Roaming\Typora\typora-user-images\image-20251225165046313.png)

![image-20251225165105666](C:\Users\31345\AppData\Roaming\Typora\typora-user-images\image-20251225165105666.png)

#### 2.6 配置主机名和客户端

![image-20251225165225897](C:\Users\31345\AppData\Roaming\Typora\typora-user-images\image-20251225165225897.png)

![image-20251225165350860](C:\Users\31345\AppData\Roaming\Typora\typora-user-images\image-20251225165350860.png)

#### 2.7 选择安装openssh服务端

![image-20251225165549513](C:\Users\31345\AppData\Roaming\Typora\typora-user-images\image-20251225165549513.png)

#### 2.8 根据提示进行安装系统提示reboot now选择并进行重启ubt，最后根据提示回车，在安装过程中要及时连网

![image-20251225165731556](C:\Users\31345\AppData\Roaming\Typora\typora-user-images\image-20251225165731556.png)

![image-20251225165859512](C:\Users\31345\AppData\Roaming\Typora\typora-user-images\image-20251225165859512.png)

#### 2.8 进入root用户

```sh
输入 sudo su -
```

### 3.安装Rocky系统

由于安装Rocky系统与安装麒麟系统步骤相同这里省略

## 三，xhell远程连接（以麒麟虚拟机为例）

#### 3.1 点击新建

![image-20251028172431210](file://C:/Users/31345/AppData/Roaming/Typora/typora-user-images/image-20251028172431210.png?lastModify=1766653638)

#### 3.2 添加名称和需要连接的虚拟机IP

![image-20251028172520423](file://C:/Users/31345/AppData/Roaming/Typora/typora-user-images/image-20251028172520423.png?lastModify=1766653675)

#### 3.3 点击连接，点击接受并保存

![image-20251028172610445](file://C:/Users/31345/AppData/Roaming/Typora/typora-user-images/image-20251028172610445.png?lastModify=1766653704)

#### 3.4 输入虚拟机用户命名，并确认

![image-20251028172702139](file://C:/Users/31345/AppData/Roaming/Typora/typora-user-images/image-20251028172702139.png?lastModify=1766653718)

#### 3.5 输入虚拟机密码Duanlao007，并确认

![image-20251028172801540](file://C:/Users/31345/AppData/Roaming/Typora/typora-user-images/image-20251028172801540.png?lastModify=1766653747)

#### 3.6 结果如图表示成功

![image-20251225170938449](C:\Users\31345\AppData\Roaming\Typora\typora-user-images\image-20251225170938449.png)

#### 3.7 模板机优化

全部复制粘贴

```sh
#配置
echo "export PS1='[\[\e[34;1m\]\u@\[\e[0m\]\[\e[32;1m\]\H\[\e[0m\]\[\e[31;1m\] \w\[\e[0m\]]\\$ '" >>/etc/profile
#生效
source /etc/profile #把配置命令行的命令追加到/etc/profile中.
#重新连接Linux就生效.
#也可以通过查看/etc/profile文件内容进行检查
```

## 四，故障排错

报错问题：

### （一），xshell不能远程连接虚拟机

排错：

第一步：首先在xhell上ping虚拟机ip地址，查看是否可以ping通

第二步：随后检查检查虚拟网络编辑器是否有问题

第三步：检查虚拟机是否配置上ip地址

第四步：检查虚拟机设置是否正确

第五步：点击任务管理器----》点击服务----》打开服务，搜索vm虚拟机，检查vm的服务是否为自动，状态必须是正在运行

解决办法：

经过排错发现是在第五步出现错误,vm虚拟机没有设置为自动，状态并没有运行

### （二），xshell在连接rocky虚拟机时在账号密码ip地址正确的情况下提示ssh服务器拒绝密码

原因：连接报错是因为rocky默认是禁止root登录

解决办法：在设置虚拟机root密码时点击允许root用户使用密码进行ssh登录，如图所示

![a3d601ae7683e8e0aa4d66831a22476e](D:\qq\聊天缓存\Tencent Files\3134506919\nt_qq\nt_data\Pic\2025-12\Ori\a3d601ae7683e8e0aa4d66831a22476e.png)