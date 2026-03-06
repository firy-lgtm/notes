# day02 排障与Linux基础命令

## 一，排除故障

### 1.准备内容

#### 1.1 ip地址

介绍：

ip地址相当于是人家庭住址

ip地址简单分类：局域网ip（私网），公网

| ip分类                 | 特点                                                         |
| ---------------------- | ------------------------------------------------------------ |
| 局域网ip（私网，内网） | 仅在当前环境使用，一般192.168.xx.xx,172.16.xx.xx,10.0.0.xxx  |
| 公网ip                 | 比较厉害了，不容易获取，固定，拥有公网ip的设备，可以在任意地点被访问 |

如何区分：网站 [www.ip123.com](www.ip123.com)

如何查看linux系统ip：

```sh
linux下面：
ip a
ifconfig
windows下面：
键盘 win+r 输入 cmd 打开终端
ipconfig
```

#### 1.2 服务与端口

介绍：

pory端口

端口在计算机中用与区分不同的服务

端口用于给用户提供访问

以下是常见的服务与端口

| 常见服务              | 功能                                 | 默认端口 |
| --------------------- | ------------------------------------ | -------- |
| 远程连接服务（ssh）   | 让运维人员可以远程连接控制 linux系统 | 22       |
| 网站服务（http协议）  | 提供给用户，访问网站/app             | 80       |
| 网站服务（https协议） | 提供给用户，访问网站/app.加密        | 443      |

```sh
查看当前 linux系统存在哪些端口：
ss -lntup 推荐
netstat -lntup
```

#### 1.3 协议

双方共同遵守的内容，了解即可

### 2. 远程连接排除流程

| 命令与作用 | 说明                           |
| ---------- | ------------------------------ |
| ping       | 检查网络链路（路线）是否通畅   |
| telnet     | ip + 端口 是否可以访问（通畅） |

![image-20251226164037197](C:\Users\31345\AppData\Roaming\Typora\typora-user-images\image-20251226164037197.png)

telnet的安装

```sh
# 1.首先检查是否可以上网
ping baidu.com
# 2.安装软件包（红帽类系统）
yum install -y telnet
```

### 3. Linux规章制度

#### 3.1 linux组成

![image-20251226164524132](C:\Users\31345\AppData\Roaming\Typora\typora-user-images\image-20251226164524132.png)

```sh
#查看系统部分信息
uname -a
#查看系统内核版本
uname -r
#查看系统的全部信息
cat /etc/os-release
```

#### 3.2 linux命令行默认格式

![image-20251226164823099](C:\Users\31345\AppData\Roaming\Typora\typora-user-images\image-20251226164823099.png)

#### 3.3 linux命令格式

![image-20251226164903978](C:\Users\31345\AppData\Roaming\Typora\typora-user-images\image-20251226164903978.png)

#### 3.4 目录

![image-20251226164942318](C:\Users\31345\AppData\Roaming\Typora\typora-user-images\image-20251226164942318.png)

核心目录：

/etc/   /home/   /root/   /tmp/   /bin/   /sbin/   ...

![image-20251226165006126](C:\Users\31345\AppData\Roaming\Typora\typora-user-images\image-20251226165006126.png)

#### 3.5 linux绝对路径vs相对路径

| 绝对路径 | 从根开始的路径，不容易丢           |
| -------- | ---------------------------------- |
| 相对路径 | 不是从根开始的路径，相对与当前目录 |

## 二，Linux基础命令

1.创建目录

```sh
mkdir 创建目录
mkdir -p 一次性创建多个目录
mkdir -pv 一次性创建多个目录，并查看创建过程
.文件名 表示创建隐藏目录
```

2.创建文件

```sh
touch 文件名.txt/.文件名.txt 创建文件或隐藏文件
```

3.查看目录或文件

```sh
ls -l 查看目录 ll 是 ls -l 的缩写 
# -r 逆序排序 
# -t 时间排序
tree 以树状图形式显示目录内容 
如果没有就输入 yum install -y tree 安装
# 在 ll 或 teee 后面加 -a 表示显示隐藏目录或文件
cat 查看文件内容
```

4.进入目录与查看位置

```sh
cd /目录 进入目录 cd 回到家目录 cd .. 回到上一层
pwd 查看当前所在目录的绝对路径
```

5.删除目录或文件

```sh
rm 删除目录或文件
# 使用-f参数 强制删除不提示
# 使用-r参数 递归删除 删除目录 没有特殊需求不用使用
# 使用-rf参数 也可以删除文件
```

6.移动文件或目录

```sh
cp (copy，复制)
cp + 源文件 + 你想要复制到哪里去
mv (move，移动)
mv + 源文件 + 你想要移动到哪里去
```

## 三，常用快捷键

```sh
tab 快速补全
ctrl + c 取消
ctrl + l 清屏
```

## 四，故障排错

报错问题：

在使用 rocky系统安装 telnet 的软件包时出错，软件包一直安装不上

排错：

第一步：检查使用ping命令去 ping baidu.com 检查虚拟机是否可以连接百度

第二步：检查vm虚拟机的网络编辑器是否有问题

第三步：检查虚拟机是否配置上ip地址

第四步：检查虚拟机设置是否正确

解决办法：

经过排查发现xshell可以连接虚拟机，但是虚拟机不能上网，网络适配器与虚拟机ip也没有任何问题，最后使用 nmtui命令进入网络管理器发现是以太网的dns服务出现问题，然后重启网络

注：在使用 nmtui 的时候要在虚拟机本机上操作，不能在xshell上操作，因为在重启网络时，xshell会断开连接

