# day16 磁盘管理-下

## 一、mbr vs gpt

- MBR（也叫dos分区表）
- mbr   vs   gpt 比上面的核心

| 磁盘分区表   | 共同点                         | 区别                                            | 使用的命令   |
| ------------ | ------------------------------ | ----------------------------------------------- | ------------ |
| mbr（msdos） | 引导系统启动，存放磁盘分区信息 | 主分区，扩展分区，逻辑分区支持硬盘小于2tb       | fdisk/parted |
| gpt          | 引导系统启动，存放磁盘分区信息 | 主分区无限（100+）2tb以上也支持mbr形式（msdos） | gdisk/parted |

```sh
parted 硬盘名字
创建分区表
mkpart primary 0 100g #primary主分区 0 开始  100g 结束

退出 q
删除 rm id号
查看 p
```

## 二、故障案例-磁盘空间不足系列

| 常见原因                                         | 本质      | 排查，解决                               |
| ------------------------------------------------ | --------- | ---------------------------------------- |
| 大文件，大目录导致磁盘空间不足，最常见           | block不足 | df，du组合，最后找到文件，目录确认后删除 |
| 大量小文件，临时文件                             | inode不足 | 找大目录，排查目录下面文件数量，删除     |
| 文件未彻底删除，文件还在使用中，导致空间没有释放 | block不足 | lsof或过滤/proc/目录内容...              |

### 2.1 indoe不足导致的磁盘空间不足

创建1个小分区，使用文件创建分区，50MB，格式化与挂载

```sh
#1. 创建50mb文件
mkdir -p /disk/
dd if=/dev/zero  of=/disk/50mb  bs=1M count=50

#2. 格式化文件
mkfs.xfs  /disk/50m

#3.创建挂载点与挂载
mkdir -p /test/indoe/
mount /disk/50m  /test/indoe/

#4.检查新分区 inode block使用情况
df -h /test/inode/
df -h /test/inode/

#5.进入目录创建大量文件 查看提示
export LANG=en_US.UTF-8 #临时修改语言为英文方便记录错误

cd /test/inode/
ls | wc -l
touch {01..25600}
ls | wc -l
df -i /test/inode/
touch lidao{01..100}.txt

touch lidap{01..100}.txt
```

预备姿势：

- |xargs

``` 
|  传递字符串，适用于使用过滤，排序，去重指令。
|xargs 转换为参数，文件名/目录名

find /etc/ -type f -name “*.conf” |xargs ls -lh
```

- 处理大量小文件

```sh
#创建大量小文件
mkdir -p /test/many-indoes/
cd /test/many-indoes/

echo {1..50000} |xargs touch

#删除文件
ls |xargs rm -f
```

删除大量小文件小结

- ls |xargs rm -f
- ls a* |xargs rm -f
- ls 0* |xargs rm -f
- 删除目录（记录权限，所有者）危险💀

完整流程

- df -h 磁盘正常，df -i磁盘满了。
- 定位：找出系统的大目录（block）find 目录大小大于1MB find/-type  d  -size  +1M
- 删除大量小文件

### 2.2block未彻底删除

文件彻底删除条件：

- rm删除入口
- 没有进程占用

现象：磁盘空间不足，df -h ,du -sh对不上

排查：找出未彻底删除的文件，lsof | grep delete 找大的

- 解决：结束进程，重启服务

```sh
#复现故障：模拟进程占用
dd  if=/dev/zero  of=/var/log/big  bs=1M  count=20480
nohup tail -f /var/log/big &

\rm -f /var/log/big

#df,du排查

#lsof | grep delete

备用排查命令
/proc/*/fd | grep detele
find  /proc/  -type 1 |xargs  ls -l | grep detele
通过ps查看进程
```

## 三、swap

- 开启或增加swap
- 关闭swap：追求性能，k8s的时候，ai的时候

### 3.1增加 swap

```sh
#1. 创建目录与指定大小文件
mkdir -p /disk/
dd  if=/dev/zero  of=/disk/swap  bs=1M  count=1024
file /disk/swap

#2.格式化成为swap
mkswap /disk/swap
chmod 600 /disk/swap
ll /disk/swap

#3.挂载激活swap
swapon /disk/swap

#4.激活swap
free -h

#5.永久生效（暂时可以不做）
grep disk  /etc/fstab

# Accessible filesystems, by reference, are maintained under '/dev/disk/'.
#/disk/swap    swap    swap  defaults    0  0
```

### 3.2关闭

```sh
#1.命令
swapoff /disk/swap
swapoff  -a  #关闭所有

#2.修改配置文件
/etc/fstab 注释掉 swap的行

#3.ubt/debian
systemctl mask swap.target  #关闭
```

## 四、raid

raid磁盘冗余阵列

应用场景：

- 物理服务器使用
- 管理硬盘

特点：

- 可以获取更高的容量
- 可以获取更高的性能
- 可以获取更高的冗余（安全）

如何通过raid对磁盘进行管理：raid级别

![image-20260119182600629](C:\Users\31345\AppData\Roaming\Typora\typora-user-images\image-20260119182600629.png)

![image-20260119182640273](C:\Users\31345\AppData\Roaming\Typora\typora-user-images\image-20260119182640273.png)
