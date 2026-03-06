# day27 shell编程

## 一、判断

### 1.[  ]

- 很少单独使用,基本上与if一起使用.
- 隐藏用法与&&,l搭配使用实现初级简易的判断.

```sh
&&and符号 并且 前一个命令执行成功了，然后执行后面的命令.简单的判断.

|| 或 前一个命令执行失败了，然后执行后面的命令.
```

#### 1.1文件类型判断

| 文件类型判断 | 说明                              | 应用场景                                  |
| ------------ | --------------------------------- | ----------------------------------------- |
| -f           | file 检查文件是否存在             |                                           |
| -d           | directory检查目录是否存在         | 操作目录之前检查目录是否存在，如/backup/. |
| -x           | x权限 检查是否存在并且是否有x权限 | 检查命令是否存在，未来使用which即可       |
| -h/L         | 是否存在，是否为软连接            |                                           |
| -s           | 文件是否存在，文件内容是否大于0   |                                           |

```sh
[ -f /etc/hosts ] && echo 存在  || echo 失败
[ -d /etc/ ] && echo 存在  || echo 失败
[ -x /bin/ls ] && echo 存在  || echo 失败
[ -L /etc/rc.local ] && echo 存在  || echo 失败
```

#### 1.2比大小

| 比较大小 | 说明                 | 对应符号 |
| -------- | -------------------- | -------- |
| -eq      | equal 等于           | ==       |
| -ne      | not equal 不等于     | !=       |
| -gt      | great than 大于      | >        |
| -ge      | great equal 大于等于 | >=       |
| -lt      | less than 小于       | <        |
| -le      | less equal 小于等于  | <=       |

```sh
equal 等于
great 大于
less 小于
than 比较
```

#### 1.3字符串

| 字符串比较       | 说明                             | 应用场景                       |
| ---------------- | -------------------------------- | ------------------------------ |
| ""串1"  =  "串2" | 字符串等于                       | 不方便使用数字，可以使用字符串 |
| "串1"  !=  "串2" | 字符串不等于                     | 不方便使用数字，可以使用字符串 |
| -z  "变量"       | zero 变量/字符为空则为成功       | 检查变量是否为空               |
| -n  "变量"       | not zero 变量/字符不为空则为成功 | 检查变量是否为空               |

#### 1.4 案例-检查服务脚本

```sh
书写检查服务是否运行，是否开机自启动的脚本


bash chack_service.sh 服务名

systemctl is-active 服务名  #结果是active运行

systemctl is-enabled 服务名 #结果为enbled开机自启动

#双分支判断 2种情况
if [];then
执行运行的命令
else
执行失败后运行的命令
```

![image-20260202103338662](C:\Users\31345\AppData\Roaming\Typora\typora-user-images\image-20260202103338662.png)

#### 1.5逻辑运算

- 与或非

| 逻辑运算（与或非） | 说明                        |      |
| ------------------ | --------------------------- | ---- |
| 与（并且）and      | -a 多个条件同时成立         |      |
| 或（或者）or       | -o 多个条件有一个成立的就行 |      |
| 非（取反）         | ! 取反                      |      |



#### 1.6正则

- 做判断。比如检查
- $# 参数个数
- -z 变量是否为空
- 使用正则匹配



- [  ]  vs  [[  ]]
- - 区别：[ ]不支持正则，[[  ]]支持正则

```sh
num=123
[[ "num" =~ [0-9] ]] && echo 成功 || echo 失败
```



#### 1.7案例-书写一个检查端口脚本，检查指定端口是否存在

```sh
bash check_port_exist.sh 80

0	检查变量是否为数字（用正则）
1.
```

![image-20260202120345564](C:\Users\31345\AppData\Roaming\Typora\typora-user-images\image-20260202120345564.png)

### 2.if

| if判断   | 说明                               | 应用场景                 |
| -------- | ---------------------------------- | ------------------------ |
| if单分支 | 脚本开头做检查                     | 检查变量，参数个数，数字 |
| if双分支 |                                    |                          |
| if多分支 | 多级别检查，磁盘70%，80%，90%，95% |                          |

### 案例-书写磁盘空间脚本

![image-20260202152218566](C:\Users\31345\AppData\Roaming\Typora\typora-user-images\image-20260202152218566.png)

### 2.2case语句

类似于if多分支判断

case用于实现菜单选择功能，而不是真的做范围对比

给出选择，用户进行选择即可

应用场景：

- 多功能的安装脚本
- 判断系统（红帽类系统，ubt/debian系统）
- 服务管理脚本  bash  manage_java.sh  start | stop | restart | status



- case格式

```sh
case "变量" in
	1) echo 安装nginx ;;
	2) echo 安装mysql ;;
	3) echo 安装所有 ;;
	*) echo 输入错误重新输入
esac

#注意 *表示所有，用于兜底

```



- read 交互式创建 变量.选择-p输出，增加脚本交互功能

![image-20260202155221092](C:\Users\31345\AppData\Roaming\Typora\typora-user-images\image-20260202155221092.png)

#### 2.3.1 案例-判断系统发行版本

```sh
os_name=`hostnamectl | grep -i 'Operating System' | awk '{print $3}'`

case "$os-name" in
	Kylin | Rocky | CentOS) echo 使用yum安装 ;;
	Debuan | Ubuntu) echo 使用apt安装 ;;
	*) echo "未识别的系统，请手动检查"
esac
```

- source/bash/sh 运行脚本
- - bash/sh 运行脚本即可
  - source /etc/profile生效 .通过source加载其他我看内容（遍变量文件）比如加载/etc/os-release文件内容。

![image-20260202162934311](C:\Users\31345\AppData\Roaming\Typora\typora-user-images\image-20260202162934311.png)

### 3.循环

- for循环

- while循环

#### 3.1for循环

```sh
for 变量名 in 变量值清单（列表）
do
	执行命令
done


#示列
for num in (1..10) `seq 15 20`
do
	echo $num
done

```

#### 案例-改造服务脚本

![image-20260202165919551](C:\Users\31345\AppData\Roaming\Typora\typora-user-images\image-20260202165919551.png)

#### 案例-批量检查磁盘分区的使用率，使用情况等等

```sh
1.获取所有磁盘分区 挂载/分区名  /etc/fstab 排除swap 空行 注释行
2.丢入到循环中
1） df -h 挂载点 创建变量
2) 分区名
3）大小
4）使用率
5）输出
```

#### 3.2while

- 应用场景：
- - 死循环
  - 读取文件内容

```sh
满足条件才会进行循环
while 条件
do
需要执行的命令
done
```

![image-20260203101631310](C:\Users\31345\AppData\Roaming\Typora\typora-user-images\image-20260203101631310.png)

##### 3.2.1死循环

- 需要书写巡检脚本，精确到秒进行检查

###### 案例

![image-20260203102734538](C:\Users\31345\AppData\Roaming\Typora\typora-user-images\image-20260203102734538.png)

##### 3.2.2读取文件

格式：

```sh
while read line
do
内容命令
done <文件名
```



```sh
通过awk，sort，uniq操作统计生成ip.txt
ip txt第一次 第2列IP地址
要求读取ip，txt文件屏蔽次数大于
```

#### 3.3循环控制语句

| 循环控制语句 | 说明                             |      |
| ------------ | -------------------------------- | ---- |
| break        | 打断，结束循环                   |      |
| eontinue     | 跳过，结束当前循环，进行下次循环 |      |
| exit         | 结束脚本                         |      |
| return       |                                  |      |

```sh
for n in {1..10}
do
        [ $n -eq 4 ] && break
        echo $n
done
```

##### 3.3.1案例-书写一个猜数字的脚本

```sh

```

### 4.计算操作

| 计算方案 | 说明 | 应用                                           |
| -------- | ---- | ---------------------------------------------- |
| let      | 整数 | 常用let i=i+1  ==let i++                       |
| $()      | 整数 | RANDMON%100 取余                               |
| $[]      | 整数 |                                                |
| expr     | 整数 | 用于检查输入是否为数字，现在我们用正则检查即可 |
| awk      | 小数 |                                                |
| bc       | 小数 |                                                |

#### 4.4案例-书写一个域名过期检查脚本

```sh

```

