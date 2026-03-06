

# day19 专题-四剑客

## 一、特殊符号

### 1.1引号系列

| 引号     | 说明                                                         |
| -------- | ------------------------------------------------------------ |
| 反引号   | 优先执行，加上命令  ``   $()   2种写法                       |
| 单引号   | 所见即所得，单引号里面的内容不会被处理，直接进行输出，命令解释器没有解析 |
| 双引号   | 类似于单引号，但有些特殊符号会被解析运行：`` , $ , $()       |
| 不加引号 | 不加引号类似于双引号，额外支持{} * 通配符                    |

```sh
echo "oldboy `whoami` $(pwd) lidao{01..5}"
echo 'oldboy `whoami` $(pwd) lidao{01..5}'
echo oldboy `whoami` $(pwd) lidao{01..5}
```

### 1.2 重定向符号

| 重定向符号                     | 说明                                      | 使用                        |
| ------------------------------ | ----------------------------------------- | --------------------------- |
| > 或 1>                        | 重定向符号,标准输出重定向,先清空然后写入. | 创建文件并写入内容.         |
| >> 或1>>                       | 追加重定向,标准输出追加重定向,追加到末尾  | 修改配置.                   |
| 2>                             | 标准错误输出重定向,先清空然后写入错误     | 较少单独用.                 |
| 2>>                            | 标准错误输出追加重定向,写入末尾           | 较少单独用.                 |
| >>oldboy.log 2>&1&>>oldboy.log | 错误信息和正确都写入到1个文件中           | 经常使用(运行脚本,定时任务) |
| <或0<                          | 与特定命令使用,读取文件内容tr,xargs       | 其他命令几乎不会用          |
| <<或0<<                        | cat命令向文件中批量写入多行内容.          | cat常用.                    |

- cat

```sh
cat >>文件名<<EOF
内容
EOF
```

- 了解:tr字母级别/字符级别的替换(大写->小写)

```sh
tr 'a-z' 'A-Z'< passwd #输出
```

- 了解:xargs参数转换,控制参数个数(列)

```sh
seq 10 >num.txt
xargs -n3 <num.txt
```

## 二、正则表达式

### 2.1简介

- linux一般是四剑客使用,开发语言也支持(python,golang,java......).
- Linux下正则一般与四剑客(find,grep,sed,awk)
- 高级语言(开发语言):python,golang,java,js
- **对字符进行过滤,精确一些.**

### 2.2注意事项

符号英文符号

配置grep,egrep-color颜色别名.

### 2.3正则符号

| 正则符号 | 符号                             | 命令              |
| -------- | -------------------------------- | ----------------- |
| 基础正则 | ^$   ^$   .   *   .*   [ ]   [^] | grep/sed/awk      |
| 扩展正则 | \| + { } ( ) ?                   | egrep/sed -r /awk |

### 2.4过滤开头，结尾，空行

```sh
过滤/etc/passwd以root开头的行
grep '^root'/etc/passwd

过滤/etc/passwd以bash结尾的行
grep 'bash$' /etc/passwd
过滤/etc/ssh/sshd_config以port开头的行不区分大小写
grep -i '^port' /etc/ssh/sshd_config

排除/etc/ssh/sshd_config空行或井号行
egrep -v '^$|#' /etc/ssh/sshd_config
```

### 2.5 []与组合

#### 2.5.1 [][abc] [] [abc] 1个整体包含3个情况,匹配a或b或c

基本用法与格式.

```sh
#过滤a或b或c
grep '[abc]'re.txt
grep -o '[abc]'re.txt

#过滤小写字母
grep '[a-z]'re.txt
#过滤大写字母
grep '[A-Z]'re.txt
#过滤数字

grep'[o-9]'re.txt

#过滤大小写字母
grep '[a-zA-Z]'re.txt #[a-Z]精简写法但是不通用.

#过滤大小写字母和数字
grep '[a-zA-Z0-9]'re.txt
grep '[o-Z]' re.txt#[0-Z] 精简写法但是不通用.
```

#### 2.5.2 +加号 表示前一个字符连续出现1次或1次以上

过滤出/etc/passwd中连续出现的字母(大小写)

```sh
egrep '[a-zA-Z]+'/etc/passwd
egrep -o '[a-zA-Z]+'/etc/passwd
```

过滤出/etc/passwd中连续出现的数字

```sh
egrep '[o-9]+'/etc/passwd
egrep -o '[o-9]+'/etc/passwd
```

去重与统计

```sh
egrep -o '[a-zA-Z]+'/etc/passwd|sort |uniq -c |sort -rnk1 |head
egrep -o '[o-9]+'/etc/passwd|sort |uniq -c |sort -rnk1 |head
```

### 2.6 awk中使用

```sh
#取出ip asens33的ip部分
ip a s ens33 |awk -F '[ /]+' 'NR==3{print $3}'
#取出hostnamectl 的kernel:行的 4.19.90 内核版本
hostnamectl |awk -F '[ -]+' '/Kernel:/{print $4}'
```

### 3.7 表示或者|

```sh
oldboy|lidao 

[abc] alblc
[a-z] alblcldlelf...|z
```

过滤配置文件内容

```sh
egrep -i '^port|'permitrootlogin|'passwordauth' /etc/ssh/sshd_config
```

检查软件包

```sh
rpm -qa |egrep '^tree|telnet|vim|lrzsz'
```

### 3.8 （)表示1个整体

```sh
egrep -i '^(port|permitrootlogin|passwordauth)' /etc/ssh/sshd_config
```

### 3.9 *表示所有

## 三、剩下的正则符号

### 3.1 [^]取反，排除

```sh
grep '^[^abc]'/etc/passwd
```

### 3.2{} a{n,m}前一个字符连续出至少n次,最多m次

```sh
a{n,m}前一个字符连续出至少n次,最多m次
a{n}前一个字符连续出n次
a{，m)}前一个字符连续出至少e次，最多m次
a{n，}前一个字符连续出至少n次
```

### 3.3?前一个字符出现0次或1次

```sh
egrep 'go?d're.txt
```

## 四、再战sed

### 4.1查看

#### 4.1.1取出文件的第5行

```sh
sed -n '5p' /etc/passwd
```

#### 4.1.2 取出文件的第5-10行

```sh
sed -n '5,10p' /etc/passwd
```

#### 4.1.3 过滤/etc/passwd包含root或oldboy的行

```sh
sed -nr '/^(root|oldboy)/p'/etc/passwd
egrep '^(root|oldboy)'/etc/passwd 
awk'/^(root|oldboy)/' /etc/passwd
```

#### 4,1,4 过滤时间范围(日志)

```sh
#1.取出secure文件中12号的11点44分到15:00的日志
sed -n '/12 11:44/ , /12 15:00/p'secure-20161219 |wc -1

#2.取出access.log文件中11:30 -11:50日志
sed -n'/2015:11:30/ ,/2015:11:50/p'access.log |wc -l
```

### 4.2 替换

sed 's###g'文件

##支持正则.

```sh
修改/etc/ssh/sshd_config

#有井号或没有井号PermitRootLogin 任意内容 
修改为
PermitRootLogin yes 
#修改
sed -i 's@#*PermitRootLogin.*@PermitRootLogin yes@g'sshd_config
#检查
grep '^Permit' sshd_config
#修改端口
sed -i 's@#*Port.*@Port 52114@g'sshd_config
#检查
grep '^Port' sshd_config
```

### 4.3删除

```sh
sed '1d' passwd
```

## 五、补充

### 5.1find

```sh
-type f或d
-name文件名
-iname文件不区分大小写
-size 大小 +10M
-mtime根据修改时间找 +77天之前的
```

找出/var/log/目录下以.log结尾的文件并且是7天之前.

```sh
find /var/log/ -type f -name "*.log" -mtime +7 |xargs echo rm -f
```

### 5.2awk

取行取列

```sh
NR==1
NR>=1
NR>1
NR<=1O
#指定行号 范围
awk 'NR>=5 && NR<=10' /etc/passwd

#使用正则过滤
awk '/root|oldboy/' /etc/passwd
```

