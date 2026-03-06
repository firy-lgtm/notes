# day20 实战案例-编译安装

## 一、今日内容

- 编译nginx（未来主要是yum/apt安装，添加功能需要插件）
- 编译安装openssh
- 编译安装应用场景：
  - 定制化软件 开启/关闭，增加功能
  - 升级与解决漏洞

## 二、编译安装nginx

### 2.1环境准备

- 环境：麒麟
- 要求：80端口别被占用，防火墙关闭，selinux关闭

```sh
# 检查防火墙
systemctl is-active firewalld.service

# 检查端口
ss -lntup

# 检查selinux
getenforce
```

### 2.2 编译安装流程

- 下载软件包（源码）
- 解压到指定目录
- 安装依赖
- 进入目录进行编译（源代码---->可执行命令）
  - 配置（指定安装目录，开启/关闭功能）命令 ./configure
  - 编译（通过命令源代码--->可执行命令） 命令 make 背后调用cc或gcc命令进行编译
  - 安装（创建对应目录，复制命令,配置文件到安装目录下）命令:make install 背后调用: mkdir , cp

- 收尾：
  - 添加用户
  - 软连接
  - 测试
  - 配置systemctl
  - 部署站点
  - 浏览器访问测试

| 编译流程 | 命令              | 作用                                         | 内部做了什么                            |
| -------- | ----------------- | -------------------------------------------- | --------------------------------------- |
| 配置     | ./configure       | 指定安装目录,开启/关闭功能                   | 根据配置生成Makefile                    |
| 编译     | make              | 通过命令源代码一->可执行的命令               | 读取Makefile然后调用cc或gcc命令进行编译 |
| 安装     | make<br />install | 创建对应的目录,复制命令,配置文件到安装目录下 | mkdir,cp等操作                          |

### 2.3安装流程

- 下载安装包

```sh
打开nginx.org 找到 download 找到1.28，下好后拖入虚拟机
stable稳定版
```

- 解压

```sh
解压压缩包到当前目录（~目录）
tar xf nginx-1.28.1.tar.gz
```

- 安装依赖

```sh
安装openssl-devel pcre-devel
yum install -y openssl pcre
```

- 配置

```sh
# 进入nginx-1.28.1
cd nginx-1.28.1

配置安装目录/app/tools/nginx-1.28.1，编译安装不指定目录默认安装到/usr/local
配置用户（虚拟）
配置常用功能
--prefix=PATH
--user=USER
--group=GROUP
开启常用功能
--with-http_ssl_module #https功能
--with-http_v2_module  #http2.0功能
--with-http_stub_status_module  #状态检查模块
--with-stream  #4层负载均衡

./configure脚本


./configure --prefix=/app/tools/nginx-1.28.1/  \
			--user=nginx --group=nginx  \
			--with-http_ssl_module  \
			--with-http_v2_module  \
			--with-http_stub_status_module  \
			--with-stream
			
检查是否成功
echo $?
显示0为成功
```

- 编译

```sh
make   -j `nproc` #-j 核心数  nproc命令获取核心数.

检查结果
4
make[1]: Leaving directory '/root/nginx-1.28.1'  #提示这个ok了.

检查
echo $?
显示为0表示成功
```

编译安装

```sh
make install  
2
3
#检查结果 
4
make[1]: Leaving directory '/root/nginx-1.28.1' #提示这个离开 真的要离开了.

依旧检查
echo $?
```

- 编译安装后检查

```sh
检查是否存在目录
ll /app/tools/nginx-1.28.1/
ll /app/tools/nginx-1.28.1/sbin/

检查命令,编译安装参数是否ok
/app/tools/nginx-1.28.1/sbin/nginx -V
主要看 configure arguments: 这一行 就是我们./configure配置的内容.
```

### 2.4编译安装后收尾

- 添加用户nginx

```sh
创建用户
useradd nginx

检查
id nginx
```

- 配置软连接

```sh
ln -s /app/tools/nginx-1.28.1/ /app/tools/nginx
```

- nginx命令

```sh
#1.修改PATH添加一个目录进去 
export PATH=/app/tools/nginx/sbin/:$PATH
#2.永久生效写入到/etc/profile中.

#3.生效与检查
nginx -V
```

### 2.5测试

```sh
#1.启动nginx
nginx

#2.检查 端口 进程 
ss -lntup | nginx
#3.访问curl  
curl   http://10.0.0.200:80  #提示欢迎就ok了. 
<title>Welcome to nginx!</title>


#4.浏览器访问
http://10.0.0.200:80
```

### 2.6后续

- 配置systemctl

```sh
[Unit]
Description=nginx web server lidao996 
After=network.target

[Service]
#Type=forking
Type=notify/forking/simple
ExecStart=/app/tools/nginx/sbin/nginx 
ExecReload=/app/tools/nginx/sbin/nginx -s reload
KillMode=process

[Install]
WantedBy=multi-user.target


systemctl daemon-reload

注意：
配置前先把nginx关闭
```

- 部署个站点,小鸟飞飞.

```sh
vim /app/tools/nginx/conf/nginx.conf
#默认站点的配置文件 
#conf/nginx.conf #ngx主配置文件

worker_processes  1;
events {
    worker_connections  1024;
}
http {
    include       mime.types;
    default_type  application/octet-stream;
    sendfile        on;
    keepalive_timeout  65;
    server {
        #下面这个站点就是默认的
        #ip访问的就是这个默认的站点.
        #默认站点的代码目录在/app/tools/nginx/html/
        listen       80  default_server;
        server_name  localhost;
        location / {
            root   html;
            index  index.html index.htm;
        }
        error_page   500 502 503 504  /50x.html;
        location = /50x.html {
            root   html;
        }
    }
}
#检查配置文件
[root@kylin-v10-oldboy /app/tools/nginx]# nginx  -t 
nginx: the configuration file /app/tools/nginx-1.28.1//conf/nginx.conf syntax is ok
nginx: configuration file /app/tools/nginx-1.28.1//conf/nginx.conf test is successful

检查ok,success 

#重启nginx 使用systemctl 



修改默认的页面:
[root@kylin-v10-oldboy /app/tools/nginx]# echo  lidao996  >html/index.html 
[root@kylin-v10-oldboy /app/tools/nginx]# cat html/index.html
lidao996
[root@kylin-v10-oldboy /app/tools/nginx]# 
[root@kylin-v10-oldboy /app/tools/nginx]# curl 10.0.0.200 
lidao996

```

### 2.7部署新的站点

- 部署新的站点配置文件

```sh
nginx.conf中 增加内容
vim /app/tools/nginx/conf/nginx.conf

server {
        listen 80;
        server_name bird.oldboy.cn;
        root /app/code/bird/;
        location / {
          index index.html;
        }
      }
检查语法
重启服务
创建 /app/code/bird/目录 
mkdir -p /app/code/bird/

解压bird.tar.gz(群文件中) 到/app/code/bird/目录


检查/app/code/bird/是否有index.html 
```

- 命令行访问 /etc/hosts

```sh
# bird.oldboy.cn 解析到10.0.0.200 , 使用hosts解析 
vim /etc/hosts 

10.0.0.200  bird.oldboy.cn
#上面就是增加的内容 即可.

#测试 hosts解析 
ping bird.oldboy.cn 

#测试curl访问 
curl  bird.oldboy.cn 
```

- Windows 中配置hosts文件

```sh
1.sublime配置管理员权限打开

2.修改windows hosts文件 
C:\Windows\System32\drivers\etc\hosts   #不是hosts.txt

3.浏览器测试
http://bird.oldboy.cn 
```

![image-20260122170717423](C:\Users\31345\AppData\Roaming\Typora\typora-user-images\image-20260122170717423.png)

出现上面图片说明成功

## 三、编译安装升级openssh

### 3.1流程

- 安装,启动,测试telnet是否OK.
- 下载openssh 9.9源码包
- 安装依赖,./configure ,make ,make install 编译安装3步曲
- 后续步骤备份,
- 测试

### 3.2备用方案-启动telnet

```sh
安装telnet
yum install  -y telnet-server
```

### 3.3启动

```sh
systemctl start telnet.socket
ss -lntup
```

### 3.4连接使用

```sh
#添加用户
useradd lidap996007
#设置密码

密码：Oldboy996

#sudo权限
echo 'lidao996007 ALL=(ALL) NOPASSWD: ALL' >>/etc/sudoers
sudo su -

最后使用telnet连接
```



### 3.5安装openssh流程

```sh
#1.下载openssh-9.9软件包压缩包(源代码)
wget https://mirrors.aliyun.com/pub/OpenBSD/OpenSSH/portable/openssh-9.9p2.tar.gz

#2.解压 
 tar xf openssh-9.9p2.tar.gz 
#3.编译安装要在 解压后的目录进行.
cd openssh-9.9p2/

#3.编译安装-第1步骤 ./configure
###指定安装目录--prefix指定安装目录
./configure  --prefix=/app/tools/openssh-9.9p2/ 
echo $?  #检查上一个命令执行是否成功,输出0表示成,非0就是失败.

#4.编译安装-第2步骤-编译 make 

nproc #查看系统核心总数.
make -j `nproc`  #-j后面指的是核心数,指定核心数进行加速.
echo $? 

#5.编译安装-第3步骤-安装 make install 
make install
echo $? 

#6.后续收尾（创建软连接）
ln -s  /app/tools/openssh-9.9p2/  /app/tools/openssh


```

- 启动测试

```sh
修改sshd配置文件 注意这里配置不在/etc/在/app/tools/openssh/etc下.

#关闭sshd服务 

#修改新的opensssh配置文件 
修改端口,准许root登录,开启密码认证

[root@oldboy-kylin-101 /app/tools/openssh]# egrep -in 'port|permitrootlogin|passwordauth'  etc/sshd_config|head -3
13:Port 52113
32:PermitRootLogin yes
57:PasswordAuthentication yes

#关闭旧版本的ssh
systemctl stop sshd 
ps -ef |grep '/sbin/sshd'

#使用新版本的临时启动
/app/tools/openssh/sbin/sshd
#检查进程 /sbin/sshd 

#检查端口 52113 

```

### 3.6测试远程连接ssh

使用xshell远程连接即可

### 3.7后续步骤

```sh
创建目录备份已有的openssh8.2旧的命令.
mkdir -p /backup/openssh-8.2p1/{etc,bin,sbin}
cp -a /etc/ssh/ /backup/openssh-8.2p1/etc/
mv /bin/ssh-keygen   /bin/scp /bin/sftp  /bin/ssh*  /backup/openssh-8.2p1/bin/
mv /sbin/sshd  /sbin/ssh-change-mode   /backup/openssh-8.2p1/sbin/
```

### 3.8后续收尾-环境变量

```sh
#0.PATH存放Linux下命令的位置.


#1. 配置PATH环境变量
echo 'export PATH=/app/tools/openssh/bin/:/app/tools/openssh/sbin/:/app/tools/openssh/libexec/:$PATH' >>/etc/profile 

source  /etc/profile 


#2.检查命令位置
sshd --version 检查  是否有9.9提示即可.

```

### 3.9 书写systemctl文件

```sh
#1.重新创建sshd的systemctl文件.
cat>/usr/lib/systemd/system/sshd.service<<'EOF'
[Unit]
Description=OpenSSH 9.9 server daemon
Documentation=man:sshd(8) man:sshd_config(5)
After=network.target 

[Service]
Type=notify
ExecStart=/app/tools/openssh/sbin/sshd -D
ExecReload=/bin/kill -HUP $MAINPID
KillMode=process
Restart=on-failure
RestartSec=42s

[Install]
WantedBy=multi-user.target
EOF

#2.系统重新读取修改后的sshd.service文件.
systemctl daemon-reload  
systemctl enable  --now  sshd 
systemctl restart sshd 

```

### 3.10 修改配置文件为/etc/ssh

```sh
mkdir -p /backup/openssh8.2/
mv  /etc/ssh  /backup/openssh8.2/ssh-bak
ln -s  /app/tools/openssh/etc/  /etc/ssh
#后续修改/etc/ssh/sshd_config配置文件即可.
```

### 3.11测试连接并关闭telnet服务

```sh
systemctl  disable --now  telnet.socket
```

### 3.12复制ssh-copy-id命令

```sh
 cp ~/openssh-9.9p2/contrib/ssh-copy-id  /app/tools/openssh/bin/ 

 #升级Openssh后没有ssh-copy-id解决方案.

 chmod +x  /app/tools/openssh/bin/ssh-copy-id

```

### 3.13收尾

删除 删除下~/openssh-9.9p2目录
