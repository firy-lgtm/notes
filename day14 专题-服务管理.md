# day14 专题-服务管理

## 一、服务管理

### 1.1systemctl

```sh
systemctl管理的服务，默认要通过yum/apt，rpm/deb 方式安装，二进制和编译安装默认无法通过systemctl管理
需要书写systemctl配置文件才可以
```

管理服务：

- 开/关/重启start/restart当前情况
- 配置是否开机自启动（服务在开机的时候是否启动）

### 1.2 格式 

| systemctl | 核心指令与内容                         |
| --------- | -------------------------------------- |
| [Unit]    | 说明信息                               |
|           | Description=服务基本信息说明信息       |
|           | After=network.target 依赖 网络（常用） |
| [Service] | 核心，指定服务类型，开启，关闭指令     |
|           | Type=类型                              |
|           | ExecStart=xxx服务启动命令或脚本        |
|           | ExecStop=xxx服务关闭命令或脚本         |
|           | ExecRestart或ExecReload重启            |
| [Lnstall] | 指定运行级别                           |
|           | WantedBy=multi-user.target 固定写法    |

| 如果你的服务....                      | 那么应该选择...        |
| ------------------------------------- | ---------------------- |
| 在前台运行不退出(现代应用最佳实践)    | Type=simple            |
| 自己fork到后台(传统守护进程)          | Type=forking(务必设置) |
| 执行一个任务后就退出(初始化/配置脚本) | Type=oneshot           |
| 明确支持并发送"准备好了"信号          | Type=notify(最佳)      |
| 在D-Bus上注册名字                     | Type=dbus(罕见)        |

```sh
# 创建一个service
vim/usr/lib/systemd/system/文件名.service

# 通知系统systemctl修改
systemctl daemon-reload

#通过systemctl 管理：设置自启动
systemctl enable --now 服务名
```

## 二、系统负载均衡

### 2.1系统负载？

- 系统负载：系统繁忙程度一个指标
- 负载：系统可以运行状态和不可中断状态的进程数量

系统可运行状态R，S

不可中断进程D



### 2.2 检查

- 负载值接近系统核心总数
- 预警值：达到70%，80%

![image-20260115192922738](C:\Users\31345\AppData\Roaming\Typora\typora-user-images\image-20260115192922738.png)

### 2.3 复现流程

#### 2.3.1 cpu占用导致负载高

```sh
stress 命令

# 使用
stress --cpu 数量 --timeout 时间

# 检查
top
```

复现io占用导致的负载高

```sh
nohup stress --hdd 数量 --timeout 时间

top
iotop -o
pidstat -d 1
```

## 三、2个小面试题

### 3.1 Linux运行级别

运行级别：系统不同状态

| 运行级别 | 级别说明                        |                                                 |
| -------- | ------------------------------- | ----------------------------------------------- |
| 0        | poweroff关机                    |                                                 |
| 1        | secure救援模式/原来叫单用户模式 | 系统无法启动,root密码忘记,我们可以进入这个模式. |
| 2        | multi-user多用户                | 命令行模式,文本模式                             |
| 3        | multi-user多用户                | 命令行模式,文本模式                             |
| 4        | multi-user多用户                | 命令行模式,文本模式                             |
| 5        | graphical图形模式,桌面模式      | 需要安装对应的软件才行.                         |
| 6        | reboot重启                      |                                                 |

```sh
systemctl get-deaful
systemctl set-dafault mutli-user.target
```

### 3.2 Linux启动流程

![image-20260115193752010](C:\Users\31345\AppData\Roaming\Typora\typora-user-images\image-20260115193752010.png)



