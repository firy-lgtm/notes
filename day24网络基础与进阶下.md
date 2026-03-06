# day24网络基础与进阶下

## 补充：DNS故障

- Linux无法上网
- 通过域名访问：ping域名，dig解析域名 报错，yum安装软件失败
- ping公网ip可以访问的

![image-20260128190951215](C:\Users\31345\AppData\Roaming\Typora\typora-user-images\image-20260128190951215.png)

此时需要检测与配置下DNS（网卡配置文件中）

```sh
#排查 是否DNS有问题
1.ping 域名
2.ping 公网ip
```

## 一、网络协议（补充）

### 1.ARP协议

- ARP：地址解析协议
- DNS协议：域名--->ip
- ARP协议：ip--->MAC地址
- ARP地址解析协议：ip地址转为网卡物理地址（MAC地址）

💀💀ARP协议是3层协议，工作在2层

解析流程简单：一问（广播）---> 一答（单播）

- 发出arp请求,发送广播找人谁知道10.0.0.2的mac地址,请告诉10.0.0.200
- 单播:所有局域网的设备都收到广播,只有10.0.0.2这个机器进行响应(回复):我是10.0.0.2我的
  mac地址是xx:xx:xxxxxxxx
- 缓存mac地址.

### 2.ICMP协议internet控制报文协议

ping使用的就是icmp协议

![image-20260128193137902](C:\Users\31345\AppData\Roaming\Typora\typora-user-images\image-20260128193137902.png)

禁止ping方案

- 内核参数<====配置文件/etc/sysctl.conf  /etc/sysctl.d/下面以.com结尾的文件
- 防火墙
- 安全组或物理防火墙

```sh
# 内核参数
临时禁止ping，重启后恢复
# 临时，重启系统失效
echo 1 > /proc/sys/net/ipv4/icmp_echo_ignore_all
或者
sysctl -w net.ipv4.icmp_echo_ignore_all=1

#准许ping
echo 0 > /proc/sys/net/ipv4/icmp_echo_ignore_allecho
或者
sysctl -w net.ipv4.icmp_echo_ignore_all=0

#永久生效
#/proc/sys/
#/proc/sys/目录下.
#目录中的/改为.就可以使用.
#net/ipv4/icmp_echo_ignore_all --> ##net.ipv4.icmp_echo_ignore_all =1

echo 'net.ipv4.icmp_echo_ignore_all =1 '>>/etc/sysctl.conf
sysctl -p #生效
```

### 3.网络协议小结

| 协议       | 名字                | 特点                   |
| ---------- | ------------------- | ---------------------- |
| DNS        | 域名解析系统/服务   | 域名->ip地址           |
| ARP        | 地址解析协议        | IP地址一>mac地址       |
| ICMP       | 因特网控制报文协议  | ping背后使用的协议     |
| DHCP       | 自动分配IP地址      | 可以扩展研究下.        |
| HTTP/HTTPS | 超文本传输协议/加密 | 第2个阶段网站服务讲解. |

## 二、网卡配置

目标：

- 修改网卡名字为eth0
- 修改网卡配置文件，ip，dns，网关，子网掩码（物理机，虚拟机（私有云））
- 公有云自动获取即可。服务器数量巨大自动获取

### 2.1网卡命名

结论：工作有需求的时候再去修改.做好记录,后面使用的时候参考即可.

```sh
网卡命名规则:
ens33 ensxxxxp
ethe < 统一修改为eth0....eth1  eth2...形式
```

修改网卡名字为etho(方案)

- 方法01:安装系统的时候修改 net.ifnames-0 biosdevname-0红帽类系统，ubt版本20.04版本及之前
- 方法02:安装完成系统修改系统启动的配置文件,网卡配置文件.

麒麟系统：

```sh
第1步:修改/boot/grub2/grub.cfg配置
麒麟系统是1inux后面即可
net.ifnames=0 biosdevname=0
sed -ri.bak '/\t+linux/s#$# net.ifnames=0 biosdevname=0#g'
/boot/grub2/grub.cfg

#修改之后检查内容
第2步:修改网卡配置文件.
1)修改网卡配置文件中NAME和DEVICE两个部分
vim修改

2)网卡配置文件名字改为eth0
mv修改

修改后结果:
grep -n eth0 /etc/sysconfig/network-scripts/ifcfg-eth0

重新启动Linux

centos是在Linux的16行结尾加入
net.ifnames=0 biosdevname=0

检查
grep -n linux16 /boot/grub2/grub.cfg
以下与麒麟一样
```

ubt系统：

![image-20260128200023774](C:\Users\31345\AppData\Roaming\Typora\typora-user-images\image-20260128200023774.png)

### 2.2网卡配置文件

物理机，虚拟化（虚拟机）需要改或配置

公有云保持默认即可（DHCP自动获取）

```sh
#配置文件
红帽类系统:/etc/sysconfig/network-scripts/ifcfg-etho 或ifcfg-ens33
debian/ubt: /etc/netplan/xxxx.yaml
debian/ubt:其他文件。。。

#生效
systemctl restart network #centos
ifdown ens33 && ifup ens33 #kylin

netplay apply #ubt/debian
```

centos，麒麟红帽

/etc/sysconfig/network-scripts/ifcfg-eth0

![image-20260128200816075](C:\Users\31345\AppData\Roaming\Typora\typora-user-images\image-20260128200816075.png)

常用的DNS大全

```sh
阿里云DNS
223.5.5.5
223.6.6.6

114
114.114.114.114
114.114.115.115

谷歌：
8.8.8.8
8.8.4.4
```

💀DNS地址的选择:授权默认的;如果没有默认的,选择当前网络环境的(找运营商);最后再使用公共DNS.

DNS配置文件

网卡配置文件DNS1DNS2优先.重启网卡后会覆盖/etc/resolv.conf文件

/etc/resolv.conf  实时生效

ubt网卡配置文件

w![image-20260128202825747](C:\Users\31345\AppData\Roaming\Typora\typora-user-images\image-20260128202825747.png)

### 2.3网络命令

| 网络命令                        | Linux                          | 用法 | windows |
| ------------------------------- | ------------------------------ | ---- | ------- |
| 查询ip地址                      | ip/ifconfig                    |      |         |
| 检查端口号                      | ss/netstat/lsof/nc/telnet/nmap |      |         |
| 检查连接情况                    | ss -ant/netsat -ant            |      |         |
| 检查速度                        | iftop/nethogs                  |      |         |
| dns解析                         | dig/host/nslookup              |      |         |
| 路径追踪                        | traceroute -nl,mrt             |      |         |
| 检查链路是否通畅                | ping,fping                     |      |         |
| 查询当前系统配置的网关/路由信息 | ip  ro/route -n                |      |         |
| 测试宽带                        |                                |      |         |

