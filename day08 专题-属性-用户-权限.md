# day08 专题-属性-用户-权限

## 前提：进入内容

![img](https://dl4.weshineapp.com/gif/20240929/2f70106fde4f7e57e40ae9c3feac695f.gif?f=micro_5Yay)

- ls -lhi每一列的作用

- 打包压缩命令

- 用户管理：用户相关的知识点与命令



## 一.文件属性

### 1.1 整体概述

![image-20260106164508324](C:\Users\31345\AppData\Roaming\Typora\typora-user-images\image-20260106164508324.png)

### 1.2 inode-vs-block

未来讲解磁盘空间不足故障案例的时候使用.

现在理解inode,block作用

现在理解访问文件的时候inode,block作用.

#### 1.2.1 inode

inode索引节点。inode号码相当于是身份证号

inode空间存放：

- **各种属性信息(大小,权限,所有者,用户组,时间....)**
- **block的位置**
- **文件名不在inode中.**

#### 1.2.2 block

- block 块，数据块
- 存放文件的数据内容
- block大小默认是4kb，大文件会占用多个block

#### 1.2.3 访问文件（核心）

![980366d052401e87e84b6bf4004cc3f2](D:\qq\聊天缓存\Tencent Files\3134506919\nt_qq\nt_data\Pic\2026-01\Ori\980366d052401e87e84b6bf4004cc3f2.png)

#### 1.2.4 巡检查看inode数量，block数量👍👍👍

查看磁盘空间使用情况

```sh
# 查看磁盘空间使用情况（block）
df -h
# 查看inode使用情况
df -i

df 看整体 du看指定目录
```

df ，du搭配排查定位磁盘空间不足问题

```sh
# 1. df -h 查看磁盘空间的整体使用情况
df -h

# 2. du -sh 一层一层查看大小使用情况

# 3. 定位到大文件或目录

# 4. 确认是否可以删除
可以删除
不可以删除，扩容
```



#### 1.2.5 小结

理解inode,block作用

理解访问文件的时候inode，block作用

## 二. 文件类型

| 文件类型 | 说明                                                         | 符号        |
| -------- | ------------------------------------------------------------ | ----------- |
| 文件     | 命令，文本，压缩包                                           | -/file      |
| 目录     | 存放文件，目录                                               | d/directory |
| 软连接   | windos中的快捷方式                                           | l/Link      |
| 其他类型 |                                                              |             |
| 字符设备 | 一般有特别，专用功能<br />/dev/zero 白洞<br />/dev/null 黑洞<br />/dev/urandom 随机内容 | c           |
| 块设备   | 磁盘，硬盘分区                                               | b/block     |

```sh
dd if=/dev/zero  of=/var/log/lidao.bin  bs=1M count=10240
if input file 文件内容来源 /dev/zero
of output file 创建的文件
bs block size 每次复制多少
count 次数

/dev/zero 白洞,不断输出内容.
```

### 2.1 软链接(ln -s)

- 相当于windows快捷方式，存放源文件的位置
- 也叫符号连接

```sh
ln -s #创建软连接命令
格式：
ln -s 文件名 软连接名
```



- 给oldboy。txt创建软链接叫oldboy.txt.soft，并对软连接进行操作查看效果

- ```sh
  ln -s oldboy.txt oldboy.txt.soft
  ll oldboy.txt*
  
   cat oldboy.txt.soft 
  1
  2
  3 
  
   echo lidao >> oldboy.txt
   cat oldboy.txt.soft 
  1
  2
  3
  lidao
  ```

  

![image-20260106114838341](C:\Users\31345\AppData\Roaming\Typora\typora-user-images\image-20260106114838341.png)

麒麟系统给网卡配置文件创建软链接到/oldboy/ifcfg-ens33

```sh
# 1.创建软链接
ln -s /etc/sysconfig/network-scripts/ifcfg-ens33 /oldboy/ifcfg-ens33

# 2.查看是否创建成功
ll /oldboy/ifcfg-ens33
```

目录软链接

![image-20260106145627229](C:\Users\31345\AppData\Roaming\Typora\typora-user-images\image-20260106145627229.png)

### 2.2 硬链接 (ln 理解即可) 😒

- 多个文件拥有的相同的inode号码
- 限制：只能对文件创建的硬链接，不能对目录创建，不能跨分区
- 几乎不会使用，如果需要备份请使用cp或打包压缩包

```sh
ln # 创建硬链接
格式：
ln 源文件名 硬链接名
```

说简单一点就是，同一个商场的另一个出入口

### 2.3 打包压缩三剑客(●'◡'●)

场景：

- 备份
- 传输

| 压缩工具     | 应用场景                   |      |
| ------------ | -------------------------- | ---- |
| tar          | 最广泛                     | 最多 |
| unzip/zip    | zip格式默认各种系统都支持  | 其次 |
| gzip/gzip -d | 原地压缩，用来注释配置文件 | 最少 |

#### 2.3.1 tar

- 创建压缩包，压缩那个目录/文件，指定压缩包放在哪里？
- 创建/backup/etc.tar.zip，压缩/etc/目录

```sh
tar zcvf /backup/etc.tar.gz /etc/
# tar zcfv 路径/压缩包名 要压缩的目录/文件
z 压缩，通过gzip工具
c 创建包（打包）
v 显示过程
f 指定压缩包
x 解压 tar 包
t 查看包内文件列表
```



- 解压

```sh
tar xf 路径/压缩包名（在当前目录解压）
```

- 解压到指定目录

```sh
tar xf 路径/压缩包名 -C 目录路径
```

- 查看压缩包的内容

```sh
tar tf 路径/压缩包名
```

小结：

- 解压
- 解压到指定目录
- 查看压缩包的内容

#### 2.3.2 zip/unzip

掌握解压

```sh
# 压缩
zip -rq 路径/压缩包 要压缩的目录/文件

# 解压
unzip 路径/压缩包

# 解压到指定目录

unzip 路径/压缩包 -d 路径
```

![img](https://dl4.weshineapp.com/gif/20220321/037960af92190f5fc75d7d3cdcbbbe76.gif?f=micro_)
