[🏠 返回本阶段首页](../../01-Linux-Cloud-Ops-Junior.md#️-本阶段主要学习内容) / [🗄️ 返回存储管理](../Indexes/07-存储管理.md)

# LVM逻辑卷管理

## LVM简介

- LVM(Logical Volume Manager) ：逻辑卷管理，是Linux环境下对磁盘分区进行管理的一种机制
- 在实际场景中，对于标准分区而言，可能出现根分区或者某一个分区的空间即将用完或者占用特别大，此时如果对其扩容，又同时不能停止上层业务，但是实际情况是挂载新硬盘然后进行数据迁移这个过程必须要停止业务
	- 为了解决这类问题，LVM就出现了，逻辑卷管理机制提供了一种很好的解决方案
	- LVM，使得分区可以更加弹性


## 相关术语及架构图

- PP(Physical Partition)：物理分区，用来存储数据的block块设备，可以是分区，磁盘，RAID或者SAN设备，不需要格式化
- PV(Physical Volume)：物理卷，LVM的基本存储逻辑块，但和基本的物理存储介质（如分区、磁盘等）比较，却包含有与LVM相关的管理参数。
- VG(Volume Group)：卷组，由一个或者多个物理卷组成，**一个PV物理卷只能加入到一个VG卷组，不能加入到多个VG中**
- LV(Logical Volume)：逻辑卷，类似于非标准的硬盘分区，在逻辑卷之上可以建立文件系统
- PE（Physical extent）：组成卷组的最小单位，具有唯一编号的PE是可以被LVM寻址的最小单元。PE的大小是可配置的，默认为4MB。
- LE（Logical extent）：逻辑卷也被划分为被称为LE的可被寻址的基本单位。在同一个卷组中，LE的大小和PE是相同的，并且一一对应。设置特定LV选项将会更改此映射，例如：镜像会导致每个LE映射到两个PE。

![[LVM.svg|700]](assets/LVM.svg)



## 逻辑卷相关操作

- 针对于LV逻辑卷的操作无非就是如下操作
	- 创建
	- 删除
	- 扩容
	- 删除

### 逻辑卷创建
---

1. 磁盘分区，此处步骤省略，自行分区，建议在分区时，可以对磁盘类型标签修改，方便自己查看具体是什么用途
```bash
[root@CentOS ~]# fdisk /dev/sda -l
Disk /dev/sda: 20 GiB, 21474836480 bytes, 41943040 sectors
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: dos
Disk identifier: 0x184c5511

Device     Boot    Start      End  Sectors Size Id Type
/dev/sda1           2048 20973567 20971520  10G 8e Linux LVM
/dev/sda2       20973568 41943039 20969472  10G 8e Linux LVM
```

>[!important]
>在实际环境中，物理磁盘建议先进行分区，如果直接对裸盘操作，后续想要把这部分空间分出来来做非LVM的用途，会比较麻烦！

2. 创建PV物理卷，并查看创建的PV物理卷

```bash
pvcreate /dev/sda1 /dev/sda2
```

```bash
  Physical volume "/dev/sda1" successfully created.
  Physical volume "/dev/sda2" successfully created.
```

- 查看PV
```bash
pvdisplay
```

- 这里看到的PV Size可能会比分区时要略小一点
```bash
 "/dev/sda1" is a new physical volume of "10.00 GiB"
  --- NEW Physical volume ---
  PV Name               /dev/sda1
  VG Name
  PV Size               10.00 GiB
  Allocatable           NO
  PE Size               0
  Total PE              0
  Free PE               0
  Allocated PE          0
  PV UUID               PlUlaY-GDHh-bQRk-xyQ3-AkJn-oBAJ-qU3Z2w

  "/dev/sda2" is a new physical volume of "<10.00 GiB"
  --- NEW Physical volume ---
  PV Name               /dev/sda2
  VG Name
  PV Size               <10.00 GiB
  Allocatable           NO
  PE Size               0
  Total PE              0
  Free PE               0
  Allocated PE          0
  PV UUID               8puta4-Xdds-awqW-A1bA-P7zh-Q1Qc-DMUbd6
```



3. 创建VG卷组，并查看创建的VG卷组
- 可以将多个PV加入VG卷组
```bash
vgcreate <VG Name> <device>
```

```bash
vgcreate vg0 /dev/sda1 /dev/sda2
```

```bash
  Volume group "vg0" successfully created
```

- 当`/dev/sda1`再次想加入别的卷组中时，无法加入，所以**一个PV只能加入一个VG**
```bash
[root@CentOS ~]# vgcreate vg1 /dev/sda1
  Physical volume '/dev/sda1' is already in volume group 'vg0'
  Unable to add physical volume '/dev/sda1' to volume group 'vg0'
  /dev/sda1: physical volume not initialized
```

- 查看创建的VG，加上指定的卷组名，可以只看改卷组信息
```bash
vgdisplay 
```

```bash
  --- Volume group ---
  VG Name               vg0
  System ID
  Format                lvm2
  Metadata Areas        2
  Metadata Sequence No  1
  VG Access             read/write
  VG Status             resizable
  MAX LV                0
  Cur LV                0
  Open LV               0
  Max PV                0
  Cur PV                2
  Act PV                2
  VG Size               19.99 GiB  # VG的大小，明显比刚才两个分区的大小总和略小
  PE Size               4.00 MiB   # 创建的PV只能是4的倍数，如果50M，最后是52M
  Total PE              5118
  Alloc PE / Size       0 / 0 
  Free  PE / Size       5118 / 19.99 GiB  # 如果要看VG是否还有剩余空间，这里可以看出来
  VG UUID               XpfnSf-q35Y-zx1I-5DfS-W4cD-jTWB-3bYGCF
```


4. 创建LV逻辑卷，并查看创建的LV逻辑卷

```bash
lvcreate -L <LV Size> -n <LV Name> <VG Name>
```

```bash
lvcreate -L 5G -n data vg0
```

```bash
  Logical volume "data" created.
```

>[!tip]
>逻辑卷的名字是全局唯一的，不会出现冲突的情况！

- 查看创建的LV，可以直接`lvdisplay`，查看所有的LV，也可以查看执指定的LV逻辑卷
```bash
lvdisplay /dev/vg0/data
```

```bash、
  --- Logical volume ---
  LV Path                /dev/vg0/data
  LV Name                data
  VG Name                vg0
  LV UUID                YZ7pAi-i6Fw-P47u-FtQ0-ALSY-NHSp-ZC2Lvf
  LV Write Access        read/write
  LV Creation host, time CentOS.example, 2026-11-24 19:38:11 +0800
  LV Status              available
  # open                 0
  LV Size                5.00 GiB
  Current LE             1280
  Segments               1
  Allocation             inherit
  Read ahead sectors     auto
  - currently set to     8192
  Block device           253:3
```

5. 格式化LV逻辑卷

```bash
mkfs.ext4 /dev/vg0/data
```

```bash
mke2fs 1.45.6 (20-Mar-2020)
Creating filesystem with 1310720 4k blocks and 327680 inodes
Filesystem UUID: 5f4dc0c3-f8be-4ec7-be18-f96a8a90c1ba
Superblock backups stored on blocks:
        32768, 98304, 163840, 229376, 294912, 819200, 884736

Allocating group tables: done
Writing inode tables: done
Creating journal (16384 blocks): done
Writing superblocks and filesystem accounting information: done
```

>[!warning]
>记住，在LVM中，使用的是上层的LV逻辑卷，格式化的也是LV逻辑卷，不要去对底层的磁盘分区进行格式化


6. 写入`/etc/fstab`，进行永久挂载，并查看挂载情况
```bash
vim /etc/fstab
```
 
- `blkid`查看LV本身的UUID
```bash
/dev/mapper/vg0-data: UUID="5f4dc0c3-f8be-4ec7-be18-f96a8a90c1ba" BLOCK_SIZE="4096" TYPE="ext4"
```

- 内容如下
```bash
UUID="5f4dc0c3-f8be-4ec7-be18-f96a8a90c1ba" /data ext4  defaults 0 0
```

```bash
mount -a
```

- 查看挂载情况
```bash

[root@CentOS ~]# df -Th
Filesystem           Type      Size  Used Avail Use% Mounted on
devtmpfs             devtmpfs  1.9G     0  1.9G   0% /dev
tmpfs                tmpfs     2.0G     0  2.0G   0% /dev/shm
tmpfs                tmpfs     2.0G   18M  1.9G   1% /run
tmpfs                tmpfs     2.0G     0  2.0G   0% /sys/fs/cgroup
/dev/mapper/cl-root  xfs        64G  6.0G   58G  10% /
/dev/mapper/cl-home  xfs        32G  255M   31G   1% /home
/dev/nvme0n1p1       xfs      1014M  225M  790M  23% /boot
tmpfs                tmpfs     391M  1.2M  390M   1% /run/user/42
tmpfs                tmpfs     391M     0  391M   0% /run/user/0
/dev/mapper/vg0-data ext4      4.9G   20M  4.6G   1% /data
```


---
- 两种写法，列出详细信息可以看到，这两个都是链接文件，指向同一个`../dm-3`的文件
	- `/dev/vg0/data`
	- `/dev/mapper/vg0-data`
```bash
[root@CentOS ~]# ll /dev/vg0/data
lrwxrwxrwx. 1 root root 7 Nov 24 19:41 /dev/vg0/data -> ../dm-3
[root@CentOS ~]# ll /dev/mapper/vg0-data
lrwxrwxrwx. 1 root root 7 Nov 24 19:41 /dev/mapper/vg0-data -> ../dm-3
```

- 逻辑卷不是实际存在的物理设备，也不是内核实际存在的，而是通过映射，而存在的设备，这种设备称为Device Mapper，简称DM


### 逻辑卷删除
---

>[!tip]
>按照架构图，从上往下依次删除·

1. 卸载文件系统
```bash
umount /data
```

2. 删除LV逻辑卷
```bash
lvremove [LV Path] -y
```

3. 删除VG卷组
```bash
vgremove [VG Name]
```

4. 删除PV物理卷
```bash
pvremove [PV Path]
```



### 逻辑卷扩容
---

>[!tip]
>扩容相较于缩容来说，几乎没有风险，而且不论是ext系列文件系统，还是xfs都支持扩容，同时可以在不影响业务的情况下对LV进行扩容

1. 扩容LV
```bash
lvextend -L <size> <PV Name>
```

- 关于这里大小的写法做一下说明
	- `+number`：扩大多少，此处是将`/dev/vg0/data`扩大5G
	- `number`：扩大到多少
```bash
lvextend -L +5G /dev/vg0/data
```

```bash
  Size of logical volume vg0/data changed from 5.00 GiB (1280 extents) to 10.00 GiB (2560 extents).
  Logical volume vg0/data successfully resized.
```

- 实际大小为如下显示
```bash
[root@CentOS ~]# lvdisplay /dev/vg0/data
  --- Logical volume ---
  LV Path                /dev/vg0/data
  LV Name                data
  VG Name                vg0
  LV UUID                YZ7pAi-i6Fw-P47u-FtQ0-ALSY-NHSp-ZC2Lvf
  LV Write Access        read/write
  LV Creation host, time CentOS.example, 2026-11-24 19:38:11 +0800
  LV Status              available
  # open                 1
  LV Size                10.00 GiB  # 此处已经扩大5G
  Current LE             2560
  Segments               2
  Allocation             inherit
  Read ahead sectors     auto
  - currently set to     8192
  Block device           253:3
```

- 但是查看挂载情况，依然是原始大小
```bash
[root@CentOS ~]# df -Th
Filesystem           Type      Size  Used Avail Use% Mounted on
devtmpfs             devtmpfs  1.9G     0  1.9G   0% /dev
tmpfs                tmpfs     2.0G     0  2.0G   0% /dev/shm
tmpfs                tmpfs     2.0G   18M  1.9G   1% /run
tmpfs                tmpfs     2.0G     0  2.0G   0% /sys/fs/cgroup
/dev/mapper/cl-root  xfs        64G  6.0G   58G  10% /
/dev/mapper/cl-home  xfs        32G  255M   31G   1% /home
/dev/nvme0n1p1       xfs      1014M  225M  790M  23% /boot
tmpfs                tmpfs     391M  1.2M  390M   1% /run/user/42
tmpfs                tmpfs     391M     0  391M   0% /run/user/0
/dev/mapper/vg0-data ext4      4.9G   20M  4.6G   1% /data
```


2. 在线拉伸文件系统
- 对于ext系列文件系统，使用下面的命令
```
resize2fs [LV Path]
```

```bash
resize2fs 1.45.6 (20-Mar-2020)
Filesystem at /dev/vg0/data is mounted on /data; on-line resizing required
old_desc_blocks = 1, new_desc_blocks = 2
The filesystem on /dev/vg0/data is now 2621440 (4k) blocks long.
```

再查看挂载情况，就是扩容后的大小了
```bash
[root@CentOS ~]# df -Th
Filesystem           Type      Size  Used Avail Use% Mounted on
devtmpfs             devtmpfs  1.9G     0  1.9G   0% /dev
tmpfs                tmpfs     2.0G     0  2.0G   0% /dev/shm
tmpfs                tmpfs     2.0G   18M  1.9G   1% /run
tmpfs                tmpfs     2.0G     0  2.0G   0% /sys/fs/cgroup
/dev/mapper/cl-root  xfs        64G  6.0G   58G  10% /
/dev/mapper/cl-home  xfs        32G  255M   31G   1% /home
/dev/nvme0n1p1       xfs      1014M  225M  790M  23% /boot
tmpfs                tmpfs     391M  1.2M  390M   1% /run/user/42
tmpfs                tmpfs     391M     0  391M   0% /run/user/0
/dev/mapper/vg0-data ext4      9.8G   23M  9.3G   1% /data
```


- 对于xfs文件系统，使用下面的命令
```bash
xfs_growfs [LV Path]
```


### 逻辑卷缩容
--- 

>[!caution]
>能不缩容就不要缩容，扩容没问题，但是缩容大概率会出问题！！仅ext系列支持缩容！！

- 对于较大的LV，如果要进行缩容，需要根据实际文件系统类型来区分
	- 如果是ext4的文件系统，可以扩容也可以缩容
	- 如果是xfs的文件系统，**只能扩容不能缩容**
- 缩容是一定要停止上层业务的

1. 卸载文件系统
```
umount /mnt/web
```
 
2. 扫描文件系统
```bash
e2fsck -f /dev/vg0/data
```

```bash
e2fsck 1.45.6 (20-Mar-2020)
Pass 1: Checking inodes, blocks, and sizes
Pass 2: Checking directory structure
Pass 3: Checking directory connectivity
Pass 4: Checking reference counts
Pass 5: Checking group summary information
/dev/vg0/data: 1962/1310720 files (0.2% non-contiguous), 117697/5242880 blocks
```

>[!tip]
>扫描文件系统的目的，是为了零散的数据分布，尝试放到一块，方便进行缩容

3. 缩小文件系统至15G
```bash
resize2fs /dev/vg0/data 15G
```

```bash 
resize2fs 1.45.6 (20-Mar-2020)
Resizing the filesystem on /dev/vg0/data to 3932160 (4k) blocks.
The filesystem on /dev/vg0/data is now 3932160 (4k) blocks long.
```

4. 缩容LV逻辑卷
```bash
lvreduce -L 15G /dev/vg0/data
```

- 此处出现警告，提示**这个操作可能会损坏数据，是否真的要进行缩容**
```
WARNING: Reducing active logical volume to 15.00 GiB.
THIS MAY DESTROY YOUR DATA (filesystem etc.)
Do you really want to reduce vg0/data? [y/n]: y
Size of logical volume vg0/data changed from 20.00 GiB (5120 extents) to 15.00 GiB (3840 extents).
Logical volume vg0/data successfully resized.
```

- 重新挂载
```bash
mount -a
```

## VG卷组操作

### VG扩容
---

>[!important]
>上面对于逻辑卷的扩容操作，是在VG卷组空间较为富裕的情况下，如果出现VG卷组空间不足，就需要先对VG卷组进行扩容

1. 将新的硬盘或磁盘分区做成PV物理卷
```bash
pvcreate /dev/sdb1
```

2. 将新的PV物理卷加入到需要进行扩容的VG卷组中
```bash
vgextend vg0 /dev/sd1
```

- 如果后续继续对LV逻辑卷扩容，那么操作同上


### VG缩容
---
>[!important]
>VG缩容的主要场景
>- 当底层磁盘出现坏道或者性能下降时，需要通过另外的磁盘将这个到瓶颈的磁盘进行更换，整个过程不能影响业务
>- 当空间够用时，删除多余的空间
>- 进行数据迁移时会用到

1. 移动原始数据到指定位置
```bash
pvmove /dev/sdc1 /dev/sde1
```

```bash
/dev/sdc1: Moved: 0.22%
/dev/sdc1: Moved: 37.50%
/dev/sdc1: Moved: 97.27%
```

- 查看数据是否移动到指定位置
```bash
[root@webserver01 ~]# pvdisplay
  --- Physical volume ---
  PV Name               /dev/sdc1
  VG Name               vg0
  PV Size               <100.00 GiB / not usable 3.00 MiB
  Allocatable           yes
  PE Size               4.00 MiB
  Total PE              25599
  Free PE               25599
  Allocated PE          0    # 这里已分配为0，说明数据已经移走
  PV UUID               SNq6N4-B1JA-xx1T-JRln-GfLr-taE2-t09Zgi
  
    --- Physical volume ---
  PV Name               /dev/sde1
  VG Name               vg0
  PV Size               <100.00 GiB / not usable 3.00 MiB
  Allocatable           yes
  PE Size               4.00 MiB
  Total PE              25599
  Free PE               15359
  Allocated PE          10240
  PV UUID               ZYJCrs-Nlb0-r6dd-QUWb-ypv8-6BUj-0y83hB
```


2. 将`/dev/sdc1`从vg0卷组中移除，一定要**确保`/dev/sdc1`中没有数据了**
```bash
vgreduce vg0 /dev/sdc1
```

3. 删除原始PV物理卷记录，对应存储设备可以拆下来或者进行下电处理
```bash
pvremove /dev/sdc1
```



>[!caution]
>进行VG缩容时，需要选择业务空闲时去做，缩容过程中，可能会影响性能，但是不会影响用户正常使用


## 修改逻辑卷属性

- LV有两种状态
	- available：可用
	- NOT available：不可用

>[!tip]
>修改逻辑卷属性，需要卸载文件系统，否则无法修改，如果未卸载，会有如下提示
>```bash
>Logical volume vg0/data contains a filesystem in use.
>```

- 卸载文件系统
```bash
umount /data
```

- 修改LV状态为不可用（NO-----`n`）
```bash
lvchange -a n /dev/vg0/data
```

- 查看逻辑卷状态
```bash
[root@CentOS ~]# lvdisplay /dev/vg0/data
  --- Logical volume ---
  LV Path                /dev/vg0/data
  LV Name                data
  VG Name                vg0
  LV UUID                YZ7pAi-i6Fw-P47u-FtQ0-ALSY-NHSp-ZC2Lvf
  LV Write Access        read/write
  LV Creation host, time CentOS.example, 2026-11-24 19:38:11 +0800
  LV Status              NOT available  # 状态为不可用
  LV Size                10.00 GiB
  Current LE             2560
  Segments               2
  Allocation             inherit
  Read ahead sectors     auto
```

- 此时再次进行挂载就无法挂载了，显示找不到这个设备
```bash
mount -a
mount: /data: can't find UUID="5f4dc0c3-f8be-4ec7-be18-f96a8a90c1ba".
```

- 通过下面的命令对逻辑卷扫描，可以看到对应的逻辑卷状态
```bash
lvscan
```

![[Pasted image 20261125101914.png|593]](assets/Pasted%20image%2020261125101914.png)

- 调整为可用状态（Yes----`y`）
```bash
lvchange -a y /dev/vg0/data
```

- 尝试挂载并擦查看LV状态，均为`ACTIVE`状态
```bash
[root@CentOS ~]# lvscan
  ACTIVE            '/dev/cl/swap' [<3.94 GiB] inherit
  ACTIVE            '/dev/cl/home' [31.18 GiB] inherit
  ACTIVE            '/dev/cl/root' [<63.88 GiB] inherit
  ACTIVE            '/dev/vg0/data' [10.00 GiB] inherit
```




## 逻辑卷恢复

>[!important]
>在LVM逻辑卷管理中，用户对逻辑卷的每一次操作，都会被记录下来，用于回滚操作，类似于Git的版本控制，也是记录所有Commit操作，方便项目进行版本回滚

- 在系统中有一个目录`/etc/lvm`，下面有一个目录`archive`，里面记录着对LVM的每一次操作
```bash
[root@CentOS ~]# cd /etc/lvm/
[root@CentOS lvm]# ls
archive  backup  cache  lvm.conf  lvmlocal.conf  profile
[root@CentOS lvm]# ls backup/
cl  vg0
[root@CentOS lvm]# ls archive/
cl_00000-616554388.vg  vg0_00000-1198618712.vg  vg0_00001-1603949437.vg  vg0_00002-513360665.vg
```

- 查看卷组的所有操作记录，可以看到所有状态记录
	- 保存命令执行前的状态
	- 保存命令执行后的状态
```bash
[root@CentOS lvm]# vgcfgrestore -l vg0

  File:         /etc/lvm/archive/vg0_00000-1198618712.vg
  VG name:      vg0
  Description:  Created *before* executing 'vgcreate vg0 /dev/sda1 /dev/sda2'
  Backup Time:  Tue Nov 24 19:33:21 2026


  File:         /etc/lvm/archive/vg0_00001-1603949437.vg
  VG name:      vg0
  Description:  Created *before* executing 'lvcreate -L 5G -n data vg0'
  Backup Time:  Tue Nov 24 19:38:11 2026


  File:         /etc/lvm/archive/vg0_00002-513360665.vg
  VG name:      vg0
  Description:  Created *before* executing 'lvextend -L +5G /dev/vg0/data'
  Backup Time:  Tue Nov 24 20:02:01 2026


  File:         /etc/lvm/backup/vg0
  VG name:      vg0
  Description:  Created *after* executing 'lvextend -L +5G /dev/vg0/data'
  Backup Time:  Tue Nov 24 20:02:02 2026

```

- 尝试模拟误删情况
```bash
umount /data
```

```bash
lvremove /dev/vg0/data
Do you really want to remove active logical volume vg0/data? [y/n]: y
  Logical volume "data" successfully removed
```

- 查看卷组状态，下面显示部分内容，现在需要进行回滚的状态是命令执行前的状态，即`/etc/lvm/archive/vg0_00003-997467406.vg`
```bash
[root@CentOS lvm]# vgcfgrestore -l vg0
  File:         /etc/lvm/archive/vg0_00003-997467406.vg
  VG name:      vg0
  Description:  Created *before* executing 'lvremove /dev/vg0/data'
  Backup Time:  Wed Nov 25 10:28:35 2026


  File:         /etc/lvm/backup/vg0
  VG name:      vg0
  Description:  Created *after* executing 'lvremove /dev/vg0/data'
  Backup Time:  Wed Nov 25 10:28:35 2026

```

- 进行状态回滚
```bash
vgcfgrestore -f /etc/lvm/archive/vg0_00003-997467406.vg vg0
```

```bash
Restored volume group vg0.
```

- 查看逻辑卷是否恢复，可以看到，当前恢复过来的逻辑卷默认是不可用的
```bash
[root@CentOS lvm]# lvdisplay /dev/vg0/data
  --- Logical volume ---
  LV Path                /dev/vg0/data
  LV Name                data
  VG Name                vg0
  LV UUID                YZ7pAi-i6Fw-P47u-FtQ0-ALSY-NHSp-ZC2Lvf
  LV Write Access        read/write
  LV Creation host, time CentOS.example, 2026-11-24 19:38:11 +0800
  LV Status              NOT available
  LV Size                10.00 GiB
  Current LE             2560
  Segments               2
  Allocation             inherit
  Read ahead sectors     auto
```

- 修改逻辑卷状态
```bash
lvchange -a y /dev/vg0/data
```

```bash
[root@CentOS lvm]# lvdisplay /dev/vg0/data
  --- Logical volume ---
  LV Path                /dev/vg0/data
  LV Name                data
  VG Name                vg0
  LV UUID                YZ7pAi-i6Fw-P47u-FtQ0-ALSY-NHSp-ZC2Lvf
  LV Write Access        read/write
  LV Creation host, time CentOS.example, 2026-11-24 19:38:11 +0800
  LV Status              available
  # open                 1
  LV Size                10.00 GiB
  Current LE             2560
  Segments               2
  Allocation             inherit
  Read ahead sectors     auto
  - currently set to     8192
  Block device           253:3
```


# 逻辑卷快照

- 快照的基本概念：能够允许数据的还原恢复。拍摄快照的时候，会将那一刻的数据进行定格；以后如果数据丢失或者损坏，可以通过这个时候拍摄的快照来进行数据的还原恢复
- 快照的特点
	- 速度快
	- 占用空间小
	- 可以保持数据的一致性
- 在创建快照的瞬间，几乎不产生任何数据复制或I/O开销。它只是建立了一个元数据指针，将未来的读写请求指向当前的数据块。从逻辑上看，快照和原卷都指向同一组数据。

>[!tip]
>场景：数据库运行在逻辑卷中，现在现需要对数据库进行备份操作，如果数据库正在运行，可能正在有数据写入或者数据被修改，备份的数据出现不一致的情况，

- 解决方案：先给逻辑卷创建快照，将快照挂载起来，进行数据备份

## COW快照

- COW(Copy-On-Write)：写时复制，逻辑卷快照就属于这种类型
- COW快照原理解释
	- 快照本身是记录索引，记录的是源卷的元数据，指向和源卷元数据同一个底层block数据块
	- 当源卷底层数据发生变化时，索引指向依旧是原始的block的数据块，但是底层的数据被新写入的数据覆盖了
	- 在覆盖前，旧的数据被写入到了COW快照区域，就使得快照区域越来越大
	- 当某些大的文件写入时，快照部分的数据也会越来越大
	- 当快照大小达到100%时，快照就会失效

![[COW.svg|700]](assets/COW.svg)

- 为逻辑卷拍摄快照
```bash
lvcreate -s -L 128M -n snap01 /dev/vg0/data
```

```bash
  Logical volume "snap01" created.
```

- 查看LV信息，当中出现的snap01的信息
```bash
[root@CentOS lvm]# lvdisplay /dev/vg0/data
  --- Logical volume ---
  LV Path                /dev/vg0/data
  LV Name                data
  VG Name                vg0
  LV UUID                YZ7pAi-i6Fw-P47u-FtQ0-ALSY-NHSp-ZC2Lvf
  LV Write Access        read/write
  LV Creation host, time CentOS.example, 2026-11-24 19:38:11 +0800
  LV snapshot status     source of
                         snap01 [active]
  LV Status              available
  # open                 1
  LV Size                10.00 GiB
  Current LE             2560
  Segments               2
  Allocation             inherit
  Read ahead sectors     auto
  - currently set to     8192
  Block device           253:3
```

- 创建挂载点并挂载
```bash
mkdir /opt/snap01
mount /dev/vg0/snap01 /opt/snap01/
```

挂载情况可以看到，类型，大小，使用情况与源卷一致，挂载点显示的内容也一致
```bash
[root@CentOS ~]# df -Th
Filesystem             Type      Size  Used Avail Use% Mounted on
devtmpfs               devtmpfs  1.9G     0  1.9G   0% /dev
tmpfs                  tmpfs     2.0G     0  2.0G   0% /dev/shm
tmpfs                  tmpfs     2.0G   18M  1.9G   1% /run
tmpfs                  tmpfs     2.0G     0  2.0G   0% /sys/fs/cgroup
/dev/mapper/cl-root    xfs        64G  6.0G   58G  10% /
/dev/mapper/cl-home    xfs        32G  255M   31G   1% /home
/dev/nvme0n1p1         xfs      1014M  225M  790M  23% /boot
tmpfs                  tmpfs     391M  1.2M  390M   1% /run/user/42
tmpfs                  tmpfs     391M     0  391M   0% /run/user/0
/dev/mapper/vg0-data   ext4      9.8G   23M  9.3G   1% /data
/dev/mapper/vg0-snap01 ext4      9.8G   23M  9.3G   1% /opt/snap01
```

- 查看快照信息
```bash
[root@CentOS ~]# lvdisplay /dev/vg0/snap01
  --- Logical volume ---
  LV Path                /dev/vg0/snap01
  LV Name                snap01
  VG Name                vg0
  LV UUID                zATW97-4Otz-wd3q-2jX6-iXPy-W4lj-YWyZbM
  LV Write Access        read/write
  LV Creation host, time CentOS.example, 2026-11-25 19:35:23 +0800
  LV snapshot status     active destination for data
  LV Status              available
  # open                 1
  LV Size                10.00 GiB
  Current LE             2560
  COW-table size         128.00 MiB
  COW-table LE           32
  Allocated to snapshot  0.01%   # 这里可以看到使用情况
  Snapshot chunk size    4.00 KiB
  Segments               1
  Allocation             inherit
  Read ahead sectors     auto
  - currently set to     8192
  Block device           253:6
```



>[!note]
>COW快照适合于读多写少的场景，例如文件服务器或者静态素材

## ROW快照

- ROW(Redirect-On-Write)：写时重定向，虚拟机快照就属于ROW类型
- 虚拟机为快照分配的默认空间为16M（VMware Workstation/ESXi），新写入的数据即增量数据是写入到快照里面
>[!tip]
>从某种角度上说，ROW快照的性能会比COW更好一点

- ROW快照原理解释
	- 删除某个快照时，会将快照上的增量数据与前面的快照进行合并
	- 场景1：删除snap01快照，snap01上的增量数据会与基础磁盘进行合并
	- 场景2：删除snap02快照，snap02的数据回合snap01进行合并
	- 场景3：回滚至快照snap01，删除回滚前的状态快照snap02，还原快照至snap01再对snap02进行删除，说明用户对snap02的状态并不需要了，所以就没有必要进行合并了
	- 场景4：按照顺序删除快照，最终snap01和snap02的数据快照都合并到基础磁盘中

![[ROW.svg|700]](assets/ROW.svg)


>[!note]
>ROW适合写多的场景，例如SSD存储，数据库，虚拟机等高写入场景





