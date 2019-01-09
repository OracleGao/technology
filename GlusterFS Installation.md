# GlusterFS安装

## Ubuntu 16.04
### Gluster Server安装
- server端安装自带client端
- 安装指令
``` shell
apt-get update
apt-get install -y glusterfs-server
```
- 验证指令
``` shell
gluster --version
```
``` txt
glusterfs 3.7.6 built on Dec 25 2015 20:50:46
Repository revision: git://git.gluster.com/glusterfs.git
Copyright (c) 2006-2011 Gluster Inc. <http://www.gluster.com>
GlusterFS comes with ABSOLUTELY NO WARRANTY.
You may redistribute copies of GlusterFS under the terms of the GNU General Public License.
```

### 创建nfs存储服务
- 查看server端外挂磁盘
``` shell
fdisk -l
```
``` txt
Disk /dev/vda: 40 GiB, 42949672960 bytes, 83886080 sectors
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: dos
Disk identifier: 0x77ba45a4

Device     Boot Start      End  Sectors Size Id Type
/dev/vda1  *     2048 83884031 83881984  40G 83 Linux


Disk /dev/vdb: 20 GiB, 21474836480 bytes, 41943040 sectors
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes


Disk /dev/vdc: 20 GiB, 21474836480 bytes, 41943040 sectors
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes


Disk /dev/vdd: 20 GiB, 21474836480 bytes, 41943040 sectors
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes

```

- 创建磁盘分区
``` shell
fdisk /dev/vdb
```
``` txt
Welcome to fdisk (util-linux 2.27.1).
Changes will remain in memory only, until you decide to write them.
Be careful before using the write command.

Device does not contain a recognized partition table.
Created a new DOS disklabel with disk identifier 0xdf49b747.

Command (m for help): n
Partition type
   p   primary (0 primary, 0 extended, 4 free)
   e   extended (container for logical partitions)
Select (default p): p
Partition number (1-4, default 1):
First sector (2048-41943039, default 2048):
Last sector, +sectors or +size{K,M,G,T,P} (2048-41943039, default 41943039):

Created a new partition 1 of type 'Linux' and of size 20 GiB.

Command (m for help): w
The partition table has been altered.
Calling ioctl() to re-read partition table.
Syncing disks.

```

- 格式化磁盘
``` shell
mkfs.ext4 /dev/vdb1
```
``` txt
mke2fs 1.42.13 (17-May-2015)
Creating filesystem with 5242624 4k blocks and 1310720 inodes
Filesystem UUID: 91ac074b-a340-4121-9985-33f1564a1a2b
Superblock backups stored on blocks:
        32768, 98304, 163840, 229376, 294912, 819200, 884736, 1605632, 2654208,
        4096000

Allocating group tables: done
Writing inode tables: done
Creating journal (32768 blocks): done
Writing superblocks and filesystem accounting information: done

```

- 挂在磁盘
``` shell
mkdir -p /glusterfs/data1
mount /dev/vdb1 /glusterfs/data1
```
- 验证挂在
``` shell
df -h
```
``` txt
Filesystem      Size  Used Avail Use% Mounted on
udev            3.9G     0  3.9G   0% /dev
tmpfs           799M  3.1M  796M   1% /run
/dev/vda1        40G  2.9G   35G   8% /
tmpfs           3.9G     0  3.9G   0% /dev/shm
tmpfs           5.0M     0  5.0M   0% /run/lock
tmpfs           3.9G     0  3.9G   0% /sys/fs/cgroup
tmpfs           799M     0  799M   0% /run/user/1000
/dev/vdb1        20G   44M   19G   1% /glusterfs/data1
```
``` shell
df -i
```
``` txt
Filesystem      Inodes  IUsed   IFree IUse% Mounted on
udev           1017240    411 1016829    1% /dev
tmpfs          1021851   1042 1020809    1% /run
/dev/vda1      2621440 143445 2477995    6% /
tmpfs          1021851      1 1021850    1% /dev/shm
tmpfs          1021851      3 1021848    1% /run/lock
tmpfs          1021851     16 1021835    1% /sys/fs/cgroup
tmpfs          1021851      4 1021847    1% /run/user/1000
/dev/vdb1      1310720     11 1310709    1% /glusterfs/data1
```

- 自动挂载
  - 编辑/etc/fstab文件实现开机自动挂载磁盘, 在已有的磁盘挂在下面增加新的磁盘挂载配置
  - 下面示例新增了/glusterfs/data1,/glusterfs/data2,/glusterfs/data3这三个开机自动磁盘挂载
```
# /etc/fstab: static file system information.
#
# Use 'blkid' to print the universally unique identifier for a
# device; this may be used with UUID= as a more robust way to name devices
# that works even if disks are added and removed. See fstab(5).
#
# <file system> <mount point>   <type>  <options>       <dump>  <pass>
# / was on /dev/vda1 during installation
UUID=3912d405-202c-45a4-96f9-0d5fc39ab7ce /               ext4    errors=remount-ro 0       1
/dev/fd0        /media/floppy0  auto    rw,user,noauto,exec,utf8 0       0

UUID=91ac074b-a340-4121-9985-33f1564a1a2b    /glusterfs/data1    ext4    defaults    0    0

UUID=9d9f7fac-a917-4ac4-b0b9-4d7f48e30815    /glusterfs/data2    ext4    defaults    0    0

UUID=5156f0a2-74af-4de4-bcbe-d4cda0093a00    /glusterfs/data3    ext4    defaults    0    0
```
  - 关于上面示例中的UUID,执行下面的指令获取
``` shell
ls -al /dev/disk/by-uuid
```
``` txt
total 0
drwxr-xr-x 2 root root 120 Jan  9  2019 .
drwxr-xr-x 5 root root 100 Jan  9  2019 ..
lrwxrwxrwx 1 root root  10 Jan  9 15:30 3912d405-202c-45a4-96f9-0d5fc39ab7ce -> ../../vda1
lrwxrwxrwx 1 root root  10 Jan  9 15:30 5156f0a2-74af-4de4-bcbe-d4cda0093a00 -> ../../vdd1
lrwxrwxrwx 1 root root  10 Jan  9 15:30 91ac074b-a340-4121-9985-33f1564a1a2b -> ../../vdb1
lrwxrwxrwx 1 root root  10 Jan  9 15:30 9d9f7fac-a917-4ac4-b0b9-4d7f48e30815 -> ../../vdc1
```

- 脚本汇总
``` shell
fidsk /dev/vdb
mkfs.ext4 /dev/vdb1
mkdir -p /glusterfs/data1
mount /dev/vdb1 /glusterfs/data1

fidsk /dev/vdc
mkfs.ext4 /dev/vdc1
mkdir -p /glusterfs/data2
mount /dev/vdc1 /glusterfs/data2

fidsk /dev/vdd
mkfs.ext4 /dev/vdd1
mkdir -p /glusterfs/data3
mount /dev/vdd1 /glusterfs/data3
```
