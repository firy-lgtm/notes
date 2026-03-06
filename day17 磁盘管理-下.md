# day17 磁盘管理-下

## 一、专题-磁盘管理-案例

### 1.1磁盘案例

- Linux磁盘分区类似与windos   C:   D:    E:    F:    ....
- 一切从根开始

| 常见方案 | 说明                                                         | 大小                     |
| -------- | ------------------------------------------------------------ | ------------------------ |
| /boot/   | 引导分区,引导系统启动.存放linux内核文件.                     | 1g/2g                    |
| /跟分区  | 存放软件包,安装服务相当于是c盘                               | 20g起步,一般推荐20-XXXXG |
| swap     | 分区,特殊文件系统.没有挂载点.<br />特殊环境中数据库,存储,k8s环境,推荐关闭swap.特殊需求(比如java)配置swap. | 建议最多8G,任性除外.     |
| /data    | 独立数据分区,名字与路径未来自己定义.看需求,有重要数据.       | 剩余所有空间.            |

- 底层：raid
- 底层：raid + lvm（逻辑卷软件）默认，动态扩容

### 1.2 root密码忘记重新设置

- 这个故障不仅仅是root密码.
- 也适用于linux无法启动.误修改/etc/配置导致的无法启动.....
- 需要重启linux.
- Linux系统自带救援模式,系统不严重使用这个即可.比较严重需要使用光盘/u盘救援模式.

#### 1.2.1红帽类系统-救援模式/单用户模式

1. 重启linux系统在grub菜单中，输入e.进入编辑模式（麒麟:输入root，密码Kylin123123）
2. 找到Linux的行，ro-->rw这行结尾加上init=/bin/bash

![image-20260126190549802](C:\Users\31345\AppData\Roaming\Typora\typora-user-images\image-20260126190549802.png)

3.按ctrl + x 启动

4.进入到救援模式进行对应的操作即可

#### 1.2.2红帽类系统-光盘/优盘救援模式

2个系统

- 优盘/光盘上面可以进入系统,挂载与操作硬盘上面的系统与数据.
- 针对linux无法启动了,grub菜单都无法进入.
- 抢救数据.

重启linux,以光盘启动优盘.提示品牌logo页面esc(F12/F10具体按什么看提示.)

![image-20260126190933041](C:\Users\31345\AppData\Roaming\Typora\typora-user-images\image-20260126190933041.png)

选择优盘/光盘启动，进入到安装页面

选择troubleshooting 然后选择resuce

![image-20260126191034069](C:\Users\31345\AppData\Roaming\Typora\typora-user-images\image-20260126191034069.png)

进入系统然后挂载本地硬盘的根到当前环境中,然后进行操作

![image-20260126191142407](C:\Users\31345\AppData\Roaming\Typora\typora-user-images\image-20260126191142407.png)

mount/dev/磁盘分区/mnt

- 补充:制作启动linux的优盘.(安装系统,进入救援模式)
  . 
- https://www.ventoy.net/cn/
- 配置完成优盘后,复制对应系统的iso文件.
- 通优盘启动服务器.

#### 1.2.3紧急救援模式（一般自动进入）

![image-20260126191211942](C:\Users\31345\AppData\Roaming\Typora\typora-user-images\image-20260126191211942.png)
