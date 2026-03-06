# day 23 网络基础与进阶中

## 一、TCP 三次握手四次挥手

- 三次握手与网站的端口建立连接
- 等待传输数据
- 数据传输完成
- 断开四次挥手

![image-20260127191338991](C:\Users\31345\AppData\Roaming\Typora\typora-user-images\image-20260127191338991.png)

### 1.1三次握手-详情

![image-20260127191402989](C:\Users\31345\AppData\Roaming\Typora\typora-user-images\image-20260127191402989.png)

文字描述3次握手流程(TCP与端口建立)

- 客户端发出建立连接请求:SYN(标记)建立连接请求.发出seq的随机数(seq=100).
- 服务端收到客户端请求:ACK(确认标记),SYN(与客户端建立连接),发出ack=seq+1(101),seq=随机(666)
- 客户端收到服务端请求与确认:发出ACK(确认标记)确认建立连接,发出ack=seq+1(667)seq=ack(101)
- 到此,连接建立,开始传输数据.

#### 1.1.1抓包查看

```sh
wireshark过滤 80端口的数据包 tcp.port == 80
curl http://www.baidu.com
```

### 1.2 4次挥手-重点

4次挥手目的是断开连接

客户端主动断开，服务端断开

![image-20260127191858005](C:\Users\31345\AppData\Roaming\Typora\typora-user-images\image-20260127191858005.png)

#### 1.2.1详情

![image-20260127191942212](C:\Users\31345\AppData\Roaming\Typora\typora-user-images\image-20260127191942212.png)

文字描述4次挥手流程:

- 客户端:发出断开连接请求:(FIN)断开连接请求,seq=1000ack=668
- 服务端:收到客户端请求,确认:(ACK),确认断开,seq=668 ack=seq+1(1001)
- 服务端:向客户端发出断开连接请求,(FIN)断开连接请求,seq=668 ack=seq+1(1001)
- 客户端:确认收到请求,断开连接,(ACK)确认收到请求,seq=1001 ack=seq+1(669)
- 连接断开

## 二、抓包分析-3次握手与4次挥手

### 2.1准备

工具

- wireshark

| 常见TCP中的标记位 |                                     |
| ----------------- | ----------------------------------- |
| SYN               | sync 同步请求，与服务器端口建立连接 |
| ACK               | ack 确认（回复：收到）              |
| FIN               | finnish/final 结束，断开连接        |
| PSH               | 表示传输数据                        |

seq，ack

| seq与ack |                     |
| -------- | ------------------- |
| seq      | sequence 序列号随机 |
| ack      | ack 确认号          |

### 2.2三次握手与四次挥手小结

- 核心掌握他们流程
- 理解3次握手，4次挥手过程

```sh
# 1.抓包三次握手四次挥手过程
curl www.jd.com

# 2.抓包三次握手四次挥手过程
在windows下git bash运行
ssh -p 52113 root@10.0.0.200
退出连接，断开连接

# wireshark 抓包tcp.port == 52113

# 3.抓包三次握手Linux下面
telnet www.baidu.com 22端口 # wireshark 抓包wireshark 抓包 tcp.port == 22
```

## 三、TCP与UDP

### 3.1概述总结

| 4层协议 | 概述                                       |
| ------- | ------------------------------------------ |
| TCP     | 传输控制协议：基于3次握手4次挥手，可靠连接 |
| UDP     | 用户数据报告协议：不可靠连接               |

**面试题：TCP与UDP区别**

| 传输控制协议（TCP）                            | 数据宝协议（UDP）                                            |
| ---------------------------------------------- | ------------------------------------------------------------ |
| 面向连接（三次握手四次挥手，建立，断开连接）   | 无连接                                                       |
| 可靠传输                                       | 不可靠传输                                                   |
| 流控（检查数据是否丢失，丢失重新发送）         | 尽力而为，尽力传递                                           |
| 使用TCP应用：WEB浏览器；电子邮件；文件传输程序 | 使用UDP的应用：域名系统（DNS）；视频流；ip语言（VoLP）流媒体 |

### 3.2TCP数据包结构

![image-20260127194101008](C:\Users\31345\AppData\Roaming\Typora\typora-user-images\image-20260127194101008.png)

### 3.3UDP数据包结构

![image-20260127194133813](C:\Users\31345\AppData\Roaming\Typora\typora-user-images\image-20260127194133813.png)

## 四、OSI7层模型与网站访问流程

### 4.1OSI与数据包传输（包含网络设备）

![image-20260127194320313](C:\Users\31345\AppData\Roaming\Typora\typora-user-images\image-20260127194320313.png)

### 4.2用户访问www.baidu.com整个过程

1. DNS解析：域名-->ip
2. ip + 端口建立连接：三次握手
3. 传输数据:发送请求，等待处理，得到响应（http协议，https协议）
4. 断开连接：四次挥手

![image-20260127194614337](C:\Users\31345\AppData\Roaming\Typora\typora-user-images\image-20260127194614337.png)

### 4.3详细过程

大致步骤:

1. DNS解析,www.oldboylinux.cn解析得到ip
2. tcp3次握手:通过ip+端口,与服务器建立连接
3. 用户->服务器:发出请求(htp),请求豹纹;
4. 网站处理用户的请求;
5. 服务器->用户:发出响应(htp),响应豹纹;
6. tcp4次挥手:断开连接

细致步骤:

- DNS解析,www.oldboylinux.cn解析得到ip
- - 访问本地dns缓存,hosts文件
  - 访问本地DNS服务器
  - 访问根域名解析服务器,得到顶级域
  - 访问顶级域DNS,得到二级域
  - 访问二级域DNS,得到三级域名的ip
  - 返回本地DNS,缓存，再给用户
- tcp3次握手:通过ip+端口,与服务器建立连接
- - 客户端-->服务端:发出SYN建立连接请求,seq随机数
  - 服务端->客户端:发出ACK确认,SYN建立连接,ack=seq+1,seq随机
  - 客户端->服务端:发出ACK确认,ack=seq+1,seq
  - 连接建立
- 用户->服务器:发出请求(http),请求豹纹;
- 网站处理用户的请求;
- 服务器->用户:发出响应(htp),响应豹纹;
- tcp4次挥手:断开连接
- - 服务端->客户端:断开连接请求FIN,ack,seq
  - 客户端-->服务端:发出ACK确认,ack,seq
  - 客户端->服务端:发出FIN断开连接请求,ack,seq
  - 服务端-->客户端:发出ACK确认,ack,seq
  - 连接断开

### 4.4抓包详情

- dns解析

![image-20260127195701197](C:\Users\31345\AppData\Roaming\Typora\typora-user-images\image-20260127195701197.png)

- 三次握手

![image-20260127195719041](C:\Users\31345\AppData\Roaming\Typora\typora-user-images\image-20260127195719041.png)

- http请求与响应

![image-20260127195745238](C:\Users\31345\AppData\Roaming\Typora\typora-user-images\image-20260127195745238.png)

- 四次挥手

![image-20260127195810654](C:\Users\31345\AppData\Roaming\Typora\typora-user-images\image-20260127195810654.png)

## 五、DNS

- 域名:baidu.com jd.com taobao.com www.oldboyedu.com qq.com www.360buy.com
- DNS域名解析服务/协议:域名->ip地址
- 公共DNS服务器地址:223.5.5.5,114.114.114.114网卡配置文件里面写的.
- 域名哪里来,如何配置解析

目标:

- 域名结构
- 给已有的域名配置DNS解析.show.oldboylinux.cn-->ip
- DNS解析原理(域名-->ip)

###  5.1DNS解析详细流程

![image-20260127200204392](C:\Users\31345\AppData\Roaming\Typora\typora-user-images\image-20260127200204392.png)

![image-20260127200219487](C:\Users\31345\AppData\Roaming\Typora\typora-user-images\image-20260127200219487.png)

### 5.2DNS解析的命令

```sh
#缓存 windows
##系统级别
ipconfig /displaydns #显示
ipconfig /flushdns  #清空DNS缓存 需要管理员权限

##windows hosts文件
C:\Windows\System32\drivers\etc\hosts

##浏览器也有DNS缓存

#hosts文件

#详细解析过程1inux命令
digwww.baidu.com  #查询百度对应的ip dns解析的命令.
dig nbre.oldboylinux.cn

#详细DNS解析流程
dig +trace nbre.oldboylinux.cn
```