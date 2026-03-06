# day25 shell编程-语法篇

## 一、目标

- 掌握语法:变量,环境变量,判断,循环,函数.

- 案例:

- - 定时巡检脚本

  - 安装部署脚本.比如:编译安装openssh,二进制安装tomcat,二进制安装mysql.

  - - 手动安装一次,成功了.记录流程.
    - 书写脚本,自动化完成.

  - 服务管理脚本

  - - 未来推荐直接使用systemctl即可.
    - 无法直接使用systemctl的需要书写脚本.java相关. java.shstartlstoplrestartIstatus

  - 系统检查脚本/系统优化脚本/系统安全加固脚本

  - 其他功能脚本

    

## 二、脚本的环境变量

### 2.1 #！/bin/bash是什么？

```sh
#!/bin/bash
#! 开头
指定脚本的命令解释器

pythin 脚本
#!/bin/python3

推荐书写脚本就加上
尤其是脚本后面给服务器使用，系统调用
```

### 2.2如何创建脚本的时候自动添加说明信息

```sh
进入/etc/vimrc
末尾添加
autocmd BufNewFile *.py,*.cc,*.sh,*.java,*.bash exec ":call SetTitle()"

func SetTitle()  
    if expand("%:e") =~ 'sh\|bash'  
        call setline(1,"#!/bin/bash")
        call setline(2, "##############################################################")  
        call setline(3, "# File Name: ".expand("%"))
        call setline(4, "# Version: V1.0")
        call setline(5, "# Author: lidao996")
        call setline(6, "# Organization: www.oldboyedu.com")
        call setline(7, "# Description:")
        call setline(8, "##############################################################")
        call setline(9, "")
    endif  
endfunc
```



## 三、变量

| 变量分类             | 说明                                                         | 坑                             |
| -------------------- | ------------------------------------------------------------ | ------------------------------ |
| 普通变量（局部变量） | 最常用，命名不能以数字开头<br />命名最好体现出变量功能ip  = sys_ip  net_ip | echo $(name)<br />避免金庸新作 |
| 特殊变量             | $?   $n   $0   $#   $*   $@                                  | 判断，循环搭配使用             |
| 环境变量（全局变量） | PATH，PS1，LANG，UID                                         | UID主要用于检查是否为root      |

### 3.1特殊变量👍👍👍👍👍

| 常用必会的特殊变量 | 说明                                                         |                                                            |
| ------------------ | ------------------------------------------------------------ | ---------------------------------------------------------- |
| $0                 | 脚本名字                                                     | 错误输出的时候，提示用户如何使用这个脚本<br />结合判断使用 |
| $n                 | 位置参数,命令行参数内容通过$1,$2,...形式传递到脚本中,实现简易交互/传参 | 脚本检查，我们都会使用.命令行与脚本的桥梁                  |
| $#                 | 脚本参数个数                                                 | 与判断结合使用检查参数个数                                 |
| $*                 | 取出脚本所有参数相当于写$1...$n                              | 与循环搭配                                                 |
| $@                 | 取出脚本所有参数相当于写$1...$n                              | 与循环搭配                                                 |



#### 3.1.1 $n 与 $0

```sh
#!/bin/bash
##############################################################
# File Name: show.sh
# Version: V1.0
# Author: firy
# Organization: www.firy.cn
# Description:
##############################################################

echo $1 $2
echo $0

```

#### 3.1.2 面试题：$n ,n>9之后会怎么样

```sh
解决
echo $1 $2 $5 ${10} ${13}
echo $0
把数字放在花括号中
```

#### 3.1.3案例1-检查指定服务器状态

systemctl is-active

systemctl is-enabled

```sh
#1.vars
name="$*"
echo "请睁开眼睛看 服务是否启动与开机自启动，后续改为if判断"
systemctl is-active   $name
systemctl is-enabled  $name

#2.check var
if [ $# -eq 0 ];then
        echo "Usage: bash $0 server name"
        exit 1
fi

#3.user
user="$*"
echo "该用户的情况"
id $user
```

#### 案例2-检查指定的用户是否存在

id用户名

```sh
#1.vars
name=$1

#2.id
id $name
```

#### 3.1.4 $#

```sh
#脚本参数个数等于0，则输出帮助并退出脚本
if [ $# -eq 0 ];then
	echo "usage: $0 server name"
	exit 1
#-eq	equal	等于
#-ne	not equal
```

#### 3.1.5 案例3-改造01和02增加脚本参数判断功能

```sh
#1.vars
name="$*"
echo "请睁开眼睛看 服务是否启动与开机自启动，后续改为if判断"
systemctl is-active   $name
systemctl is-enabled  $name

#2.check var
if [ $# -eq 0 ];then
        echo "Usage: bash $0 server name"
        exit 1
fi

#3.user
user="$*"
echo "该用户的情况"
id $user
```

#### 3.1.6 案例4-回收站脚本代替rm命令

```sh
df -h /tmp/
tmp 目录属于tmps(内存)系统重新启动内容消失
umount -lf /tmp
systemctl mask tmp.mount

1.创建临时目录 /tmp/随机名字/ mktemp -d
2.mv 待删除内容 临时目录
3.输出一句xxx文件，目录放入回收站中 回收站目录xxxx

最后配置别名rm==运行脚本

#1.vars
files="$*"
recycle_dir=`mktemp -d`

#2.check
if [ $# -eq 0 ];then
        echo "Usage: bash $0 files dirs"
        exit 1
fi

#3.mv
mv $files $recycle_dir

#$4.结果输出
echo "$files已经放到回收站中，目录$recycle_dir"


alias rm='bash /server/scripts/shell/02.recycle.sh'
```

### 3.2环境变量

| 环境变量 | 作用                                      |
| -------- | ----------------------------------------- |
| PATH     | 存放命令的位置/目录                       |
| PS1      | 命令行格式                                |
| LANG     | language语言，字符集                      |
| UID      | 获取当前用户的UID<br />搭配判断是否为root |

```sh
字符集:语言在计算中表达形式.
#临时修改(重新登录失效)书写在脚本开头即可
export LANG=en_US.UTF-8

#永久生效
localectl set-locale zh_CN.UTF-8
```

#### 3.2.1案例4-安装指定的软件包

```sh
#1.vars
names="$@"

#2.root?
if [ $UID -ne 0 ];then
        echo "你不行，换root来0v0"
        exit 1
fi

#3.$#
if [ $# -eq 0 ];then
        echo "来，你告诉我，你的变量那去了？？？"
        exit 2
fi

#4.yum
yum install -y $names

#5.待补充 检查               
```

