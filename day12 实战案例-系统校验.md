# day12 实战案例-系统校验

## 一、方案01-md5效验

- hash哈希计算.生成指纹信息

- 检查文件内容是否发生变化，无法效验属性信息。

```sh
echo oldboy >oldboy.txt 
md5sum oldboy.txt 
3fe396c01f03425cb5e2da8186eb090d  oldboy.txt 
 # 创建指纹 md5sum oldboy.txt >zhiwen.md5 
 cat zhiwen.md5  
 3fe396c01f03425cb5e2da8186eb090d  oldboy.txt 
 # 效验指纹 md5sum --check zhiwen.md5 
 oldboy.txt: 成功
```

## 二、方案02-aide

```sh
#1.安装aide  
##1.1安装
安装aide软件包 
yum install -y aide  libgcrypt
yum -y install libgcrypt

##1.2检查aide 是否安装成功 

##1.3查看aide 软包内容 看下配置文件名字 路径.


#2.配置
修改/etc/aide.conf 
内容：
#diy lidao996 监控目录
/bin/ FIPSR
/sbin/ FIPSR
/etc/ FIPSR

#3.创建资源库，读取配置文件 进程初始化
aide --init   -c /etc/aide.conf  
aide --init 或 -i
#4.修改指纹库文件名
/var/lib/adie/aide.db.new.gz
把aide.db.new.gz 重新命名为 aide.db.gz

#5.对比校验
aide --check  或 -C

#重新生成/更新指纹库
```

- aide.conf配置文件说明

```sh
配置文件
FIPSR = p+i+n+u+g+s+m+c+acl+selinux+xattrs+sha256

p permission权限
i inode 号码
n 硬链接数
u user所有者
g group用户组
c ctime属性变化时间
acl+selinux 特殊权限
xattrs      特殊属性
sha256  哈希命令
```

## 三、堡垒机

### 3.1堡垒机？

未来通过堡垒机连接控制Linux系统更加安全

堡垒机自带记录公共，限制功能

网站安全必要一环

| 堡垒机选型    | 说明       |
| ------------- | ---------- |
| 开源堡垒机    | Jumpserver |
| 自己开发/自研 |            |
| 商业软件/硬件 | Jumpserver |

### 3.2xshell备用方案

- 远程连接
- 文件上传

```sh
#1.远程连接
ssh -p 52113 root@10.0.0.200

#2.scp命令使用 远程传输
scp -P 52113 jumpserver-ce-v4.10.6-x86_64.tar.gz root@10.0.0.200:/root/
```

### 3.3使用

```sh
#0.关闭其他服务tomcat,mysql,firewalld 

#1.解压到/app/tools/目录下 

#2.运行安装脚本 
进入目录 
然后运行./jmsctl.sh install

#3.运行启动服务命令
./jmsctl.sh start 

#4.检查 
./jmsctl.sh  status  

#5.浏览器登录 
http://ip:80 登录 
默认admin
密码ChangeMe
新密码duanlao007
```

### 3.4堡垒机的使用

#### 3.4.1前往用户列表添加用户

![image-20260114164415101](C:\Users\31345\AppData\Roaming\Typora\typora-user-images\image-20260114164415101.png)

#### 3.4.2随后点击资产列表创建资产

![image-20260114164431255](C:\Users\31345\AppData\Roaming\Typora\typora-user-images\image-20260114164431255.png)

#### 3.4.3追后查看资产授权

![image-20260114164444348](C:\Users\31345\AppData\Roaming\Typora\typora-user-images\image-20260114164444348.png)

### 3.5查看回放

#### 3.5.1切换到审计台

![image.png](https://cdn.nlark.com/yuque/0/2026/png/55339034/1768203527818-cb62efe9-9d2d-4c08-b32a-68c0d6dd5974.png?x-oss-process=image%2Fformat%2Cwebp)

#### 3.5.2点击会话记录进行查看回放

![image-20260114164456912](C:\Users\31345\AppData\Roaming\Typora\typora-user-images\image-20260114164456912.png)