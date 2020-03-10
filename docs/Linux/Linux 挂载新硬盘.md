# Linux 挂载新硬盘

## 前言

博客书

版本说明

```properties
linux=Amazon Linux 2 AMI
```

相关链接

## 步骤

1. 查看
2. 分区
3. 格式化
4. 挂载
5. 查看挂载
6. 配置
7. 重启系统再测试

### 查看

```shell
# 查看硬盘
fdisk -l
```

示例

```shell
[root@ip-172-31-19-85 ec2-user]# fdisk -l
Disk /dev/nvme1n1: 500 GiB, 536870912000 bytes, 1048576000 sectors
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes


Disk /dev/nvme0n1: 100 GiB, 107374182400 bytes, 209715200 sectors
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: gpt
Disk identifier: 65E66B8B-7479-4A5D-8D3C-3570E8F219E7

Device           Start       End   Sectors  Size Type
/dev/nvme0n1p1    4096 209715166 209711071  100G Linux filesystem
/dev/nvme0n1p128  2048      4095      2048    1M BIOS boot

Partition table entries are not in disk order.
```

### 分区

```shell
# 格式硬盘（/dev/sda 为硬盘目录）
fdisk /dev/sda
```

命令

```shell
Help:

  DOS (MBR)
   a   toggle a bootable flag
   b   edit nested BSD disklabel
   c   toggle the dos compatibility flag

  Generic
   d   delete a partition
   F   list free unpartitioned space
   l   list known partition types
   n   add a new partition
   p   print the partition table
   t   change a partition type
   v   verify the partition table
   i   print information about a partition

  Misc
   m   print this menu
   u   change display/entry units
   x   extra functionality (experts only)

  Script
   I   load disk layout from sfdisk script file
   O   dump disk layout to sfdisk script file

  Save & Exit
   w   write table to disk and exit
   q   quit without saving changes

  Create a new label
   g   create a new empty GPT partition table
   G   create a new empty SGI (IRIX) partition table
   o   create a new empty DOS partition table
   s   create a new empty Sun partition table
```

示例

```shell
[root@ip-172-31-19-85 ec2-user]# fdisk /dev/nvme1n1

Welcome to fdisk (util-linux 2.30.2).
Changes will remain in memory only, until you decide to write them.
Be careful before using the write command.

Device does not contain a recognized partition table.
Created a new DOS disklabel with disk identifier 0x88e062ee.

Command (m for help): n
Partition type
   p   primary (0 primary, 0 extended, 4 free)
   e   extended (container for logical partitions)
Select (default p): p
Partition number (1-4, default 1): 
First sector (2048-1048575999, default 2048): 
Last sector, +sectors or +size{K,M,G,T,P} (2048-1048575999, default 1048575999): 

Created a new partition 1 of type 'Linux' and of size 500 GiB.

# w 是保存退出，q 是不保存退出
Command (m for help): w
The partition table has been altered.
Calling ioctl() to re-read partition table.
Syncing disks.
```

### 格式化

```shell
# 格式化命令 mkfs，格式化类型：XFS 或者 EXT4
mkfs -t ext4 /dev/nvme1n1p1
```

示例

```shell
[root@ip-172-31-19-85 ec2-user]# mkfs -t ext4 /dev/nvme1n1p1
mke2fs 1.42.9 (28-Dec-2013)
Filesystem label=
OS type: Linux
Block size=4096 (log=2)
Fragment size=4096 (log=2)
Stride=0 blocks, Stripe width=0 blocks
32768000 inodes, 131071744 blocks
6553587 blocks (5.00%) reserved for the super user
First data block=0
Maximum filesystem blocks=2279604224
4000 block groups
32768 blocks per group, 32768 fragments per group
8192 inodes per group
Superblock backups stored on blocks: 
	32768, 98304, 163840, 229376, 294912, 819200, 884736, 1605632, 2654208, 
	4096000, 7962624, 11239424, 20480000, 23887872, 71663616, 78675968, 
	102400000

Allocating group tables: done                            
Writing inode tables: done                            
Creating journal (32768 blocks): done
Writing superblocks and filesystem accounting information: done   
```

### 挂载

```shell
# 挂载命令：mount，格式：mount [-t vfstype] [-o options] device dir
mount /dev/nvme1n1p1 /opt
```

### 查看挂载

```shell
# df -h 查看linux文件系统的磁盘使用情况
[root@ip-172-31-19-85 ec2-user]# df -h
Filesystem      Size  Used Avail Use% Mounted on
devtmpfs        7.6G     0  7.6G   0% /dev
tmpfs           7.7G     0  7.7G   0% /dev/shm
tmpfs           7.7G  480K  7.7G   1% /run
tmpfs           7.7G     0  7.7G   0% /sys/fs/cgroup
/dev/nvme0n1p1  100G  1.5G   99G   2% /
tmpfs           1.6G     0  1.6G   0% /run/user/1000
tmpfs           1.6G     0  1.6G   0% /run/user/0
/dev/nvme1n1p1  493G   73M  467G   1% /opt
```

### 配置

```shell
# 查看新挂载硬盘的UUID
blkid
# 安装配置文件（/etc/fstab）中同等配置方式配置
# UUID 挂载的目录 硬盘格式 [defaults,noatime  1   1](后面3个参数和第一个相同即可)
UUID=7a487823-831a-47e0-b9c5-97a7edc90077     /           xfs    defaults,noatime  1   1
UUID=47431fe6-f023-4f53-9d3f-a9472dda739b     /opt      ext4   defaults,noatime  1   1
```

### 重启系统再测试

```shell
reboot

[ec2-user@ip-172-31-19-85 ~]$ df -h
Filesystem      Size  Used Avail Use% Mounted on
devtmpfs        7.6G     0  7.6G   0% /dev
tmpfs           7.7G     0  7.7G   0% /dev/shm
tmpfs           7.7G  424K  7.7G   1% /run
tmpfs           7.7G     0  7.7G   0% /sys/fs/cgroup
/dev/nvme0n1p1  100G  1.5G   99G   2% /
/dev/nvme1n1p1  493G   73M  467G   1% /opt
tmpfs           1.6G     0  1.6G   0% /run/user/1000
```

