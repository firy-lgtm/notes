# day09 用户管理



## 学习内容

- 用户管理命令（增删改查）

- 用户分类

- sudo提取

- 别名

  

## 一.用户管理命令

### 1.1 增加（核心）👍👍

- 添加用户oldboy，配置密码，切换用户

  ```sh
  # 1. 添加用户
  useradd 用户名
  
  # 2. 设置密码（Duanlao007）
  passwd 用户名
  
  # 3.切换用户
  su - 用户名
  
  # 4.退出
  ctrl + d
  ```

- 使用新用户远程登录（不会看day01）

- 修改远程登录用户名与密码即可

su 与 su -

- su - 切换用户的时候更新环境变量

| 命令 | 适用场景               | 环境变量         | 配置加载                  |
| ---- | ---------------------- | ---------------- | ------------------------- |
| su   | 临时操作、简单命令执行 | 保留原用户环境   | 不加载登录配置            |
| su - | 完整用户身份工作       | 加载目标用户环境 | 加载登录配置（如.bashrc） |

| useradd选项 | 说明                                                         |      |
| ----------- | ------------------------------------------------------------ | ---- |
| -s          | 指定命令解释器<br />/bin/bash<br />/sbin/nologin 不登录用户/虚拟用户 |      |
| -m          | 创建家目录/home/用户名 目录                                  |      |
| -M          | 不创建家目录                                                 |      |

- 一个小坑

```sh
ubt,dabian添加用户默认不创建家目录，命令解释器是/bin/sh
解决放法指定命令解释器与创建家目录
useradd -s /bin/bash -m 用户名
```

### 1.2 用户分类

| 用户分类 | 判断                                                         | 应用场景                                 |
| -------- | ------------------------------------------------------------ | ---------------------------------------- |
| root用户 | uid,gid 是0                                                  | 管理操作                                 |
| 虚拟用户 | 1-999（不是唯一）看命令解释器不是/bin/bsah，/bin/sh<br />常见命令解释器/sbin/nologin | 服务器运行需要                           |
| 普通用户 | uid,gid从1000开始（不是唯一）看命令解释器，看家目录          | 简单维护，禁止root登录后使用普通用户登录 |

添加虚拟用户

```sh
useradd -s /sbin/nologin -M  db_mysql（用户名）
```

添加虚拟用户指定uid，gid

```sh
# 一般指定uid即可
useradd -s /sbin/nologin -M  -u 1999 web_nfs(用户名)

#最保险uid，gid一致
# 创建用户组
groupadd -g 2000 web_new(用户组名)
# 创建用户
useradd -s /sbin/nologin -M -u 2000 -g web_new(用户组的名称) web_new(用户名)
```

### 1.3 查看（核心）👍👍👍

#### 1.3.1 用户是否存在

```sh
id 用户名
```

#### 1.3.2 查看当前用户

```sh
whoami
```

#### 1.3.3 用户登录情况

| 命令    | 说明                       | 使用场景                     |
| ------- | -------------------------- | ---------------------------- |
| w       | 负载实时登录信息运行命令   | 当前登录情况，负载信息       |
| who     | 实时登录信息（ip）         | 补充w内容，查看ip            |
| last    | 登录时间（当前，历史）所有 | 专用查看的登录信息，时间，ip |
| lastlog | 仅显示最近一次登录记录     | 最后登录记录                 |



### 1.4 删除

- 先锁定用户或注释用户,让用户暂时无法使用.
- 避免直接使用删除命令导致业务受损.

```sh
userdel 用户名 # 删除用户
userdel -r 用户名删除用户以及家目录

补充：
kill 进程号 或 kill -9 进程号
```

锁定用户

```sh
# 1.进入/etc/passwd
vim /etc/passwd
# 2.找到要锁定的用户，前面加上#号，把用户注释掉
```

锁定用户密码

```sh
# 锁定密码
passwd -l 用户名

# 解锁密码
passwd -u 用户名
```

### 1.5 修改

#### 1.5.1 修改密码（核心）👍

```sh
# 红帽系统
echo '新密码' | passwd --stdin 用户名

# 所有系统
vim 文件名
# 内容：
用户名:密码
#修改
cat 文件名 | chpasswd
```

修改命令解释器

```sh
# 1.直接修改
usermod -s 命令解释器（shell） 用户名  

# 2.进入/etc/passwd文件下修改
vim /etc/passwd
然后找到需要修改命令解释器的用户在后面直接修改
```

如何生成随机密码

```sh
# 麒麟，centos中
yum -y install expect # 然后使用mkpasswd
mkpasswd -l 20 -s 0

# 在ubt中
apt -y install libstring-mkpasswd-perl
mkpasswd.pl -l 20 -s 0

选项说明
	-l n	密码长度n
	-d n	最少n个数字
	-c n	最少n个小写字母
	-C n	最少n个大写字母
	-s n	最少n个特殊字符
```



#### 1.5.2 修改用户信息（几乎不用）

![img](https://dl4.weshineapp.com/gif/20240202/16249c4e9085f895950ab33b4672289e.gif?f=micro_)



## 二. 用户相关文件

- /etc/passwd
- /etc/shadow
- /etc/group
- /etc/gshadow

| 用户相关核心文件 | 说明                     |                                  |
| ---------------- | ------------------------ | -------------------------------- |
| /etc/passwd      | 用户信息                 | 掌握每列含义                     |
| /etc/shadow      | 密码信息                 | 收集到一起通过破解工具检查弱密码 |
| /etc/group       | 用户组信息               | 检查root组中是否有新增的用户     |
| /etc/gshadow     | 用户组密码信息，几乎不用 |                                  |

## 三.别名😎

#### 3.1 应用场景

- 给命令或脚本设置
- 防护作用
- 精简命令，比如 || == ls -la --color

#### 3.2 查看

```sh
alias
```

#### 3.3 配置

```sh
alias ll='ls -l --color=auto'
```

##### 3.3.1 配置grep，egrep

```sh
# 临时配置，重启结束
alias grep='grep --color'
alias egrep='egrep --color'

# 永久配置
# 进入 /etc/profile 文件
vim /etc/profile

# 到内容最后一行添加
alias grep='grep --color'
alias egrep='egrep --color'

#保存退出后 source 生效
source /etc/profile
```

**补充：在egrep中 | 表示或者时需要用引号扩起**👍

列如

```sh
egrep 'root|passwd' /etc/passwd
```

- 检查


```sh
alias grep egrep
```

##### 3.3.2 给rm命令配置别名

```sh
# 临时配置与使用
alias rm='echo do not use rm'

# 临时取消别名删除文件
\rm  -r 文件名及路径

# 永久取消别名
# 进入 ~/.bashrc 文件
vim ~/.bashrc

把要注释的别名用注释掉 #
```

## 五. sudo

### 5.1 应用场景

- 给普通用户使用
- 给普通用户临时--root权限运行😎
- sudo提权
- 普通用户对文件，命令没有权限

### 5.2 配置使用

#### 5.2.1 root

```sh
visudo == vim /etc/sudoers

# 精准授权命令
# 进入/etc/sudoers下
visudo
# 在文件下添加
用户名		All=(All)	/bin/授权的命令

# 授权所有命令并不需要密码
用户名		All=(All)	NOPASSWD:ALL
```

#### 5.2.2 普通用户使用

```sh
sudo + 命令测试即可
```

