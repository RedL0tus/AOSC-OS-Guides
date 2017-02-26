### 分区

AOSC OS 至少需要一个主分区，也可以选择把 `/home` 之类的目录作为独立分区挂载。

首先我们打开终端模拟器，可以看到 Shell 的提示符，比如这样：
```
lorem@ipsum ~ %
```
以这作为例子，我们来看看这代表什么：

- `@` 之前的是**当前用户的用户名**。在这个例子里用户名为 `lorem`
- `@` 之后的是**现在的主机名**。在这个例子里为 `ipsum`
- `~` 的位置的内容代表的是当前所在的目录。`~` 表示现在所在的目录是当前用户的个人目录（一般为`/home/<用户名>`）
- `%` 为提示符，没什么特殊意义。（一般情况下普通用户应该是 `$`，`root` 帐号会是 `#`，例子里修改过了。）

在看到这个提示符后我们就能开始输入命令，进行安装啦~

#### 分区

所谓分区，其实就是平时说的 `C 盘`啊，`D 盘`啊等等的，实际上可以理解成对硬盘的空间进行划分。

分区的信息记录在磁盘的储存空间开头的几个扇区，那部分内容叫做 `分区表`。

在 Windows 里是自动挂载并分配盘符了，所以能在资源管理器里看到。

而 Linux 下一般不会（除非安装了 `gvfs` 之类的软件）。

在 Linux 下，你的硬盘会以块设备（Block Device）的形式存在于 `/dev` 目录下。

`/dev` 是一个虚拟文件夹（也就是并不在硬盘上实际存在），它会把电脑上的设备映射成一个个文件。

我们可以通过 `lsblk` 命令来查看所有块设备，`lsblk` 的输出一般如下：
```
lorem@ipsum ~ % lsblk
NAME        MAJ:MIN RM   SIZE RO TYPE MOUNTPOINT
sda           8:0    0 119.2G  0 disk 
├─sda1        8:1    0    16M  0 part 
└─sda2        8:2    0 119.2G  0 part 
sdb           8:16   0 931.5G  0 disk 
├─sdb1        8:17   0     1G  0 part
├─sdb2        8:18   0   300G  0 part
└─sdb3        8:19   0 630.5G  0 part /mnt/android-build
sdc           8:32   1  14.5G  0 disk 
└─sdc1        8:33   1  14.5G  0 part 
nvme0n1     259:0    0 238.5G  0 disk 
├─nvme0n1p1 259:1    0     1G  0 part 
├─nvme0n1p2 259:2    0   150G  0 part 
├─nvme0n1p3 259:3    0    10G  0 part [SWAP]
└─nvme0n1p4 259:4    0  77.5G  0 part /

```
其中：  
- `NAME` 一栏底下的表示块设备的名称。  
  - 其中像 `sda`, `sdb`, `sdc`, `nvme0n1` 等等为设备本身
  - 下面的 `sda1`, `sdb1`, `adc1`, `nvme0n1p1` 等等的为设备上的分区 
- `MAJ:MIN` 一栏显示的是块设备的主设备号（MAJ）和次要设备号（MIN）
- `RM` 一栏表示是否为可移动设备，`0` 表示不是。（移动硬盘可能会被识别为不可移动，这里的 `sdb` 为移动硬盘）
- `SIZE` 一栏表示的自然是设备的大小了。
- `RO` 一栏表示是否为只读（Read-Only），也就是说是不是只能读取不能写入。其中 0 表示不是只读
- `TYPE` 一栏显示设备的类型。
- `MOUNTPOINT` 一栏表示设备的挂载点。

#### 那什么是挂载点呢？  
挂载点表示这个设备被挂载在哪里。

比如例子里的 `/dev/nvme0n1p4`，被挂载在 `/`，这就表示 `/` 里的文件实际上是写入在 `/dev/nvme0n1p4` 的。

同理可得 `/mnt/android-build` 实际上是 `/dev/sdb3` 的内容。

那有人可能就会问了，既然设备在 `/dev` 下面，为什么不直接读取/写入呢？

我们可以来试试看，如果用 `cat` 这个程序来直接读取并输出 `/dev/sdb3` 的内容。（下面是它的输出的节选）
```
XFSB^@^@^P^@^@^@^@^@    ��^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^AqR���IP�(�c�_Ȟ^@^@^@^@^H^@^@^E^@^@^@^@^@^@^@`^@$
@^@^@^@^F^@^@^@^B^@^@^@&^A�����������������������������������������������������������������������������������$
��^@^SFc^@(�+^@+��^@^U�o^@^U8�^@^@^Y^B^@
��^@-HS^@-It^@-P�^@-H[^@^U�o^@+��^@(�+^@^SFc^@^@^Y^B^@-P�^@^U8�^@^A^ZA^@-D^?^@(�+^@(��^@^U9=^@^D?^L^@(��^@^U8$
�W^@(�P^@(�q^@(�U^@^E��^@&2�^@^@�P^@^B�d^@^B��^@^F.w^@^F.y^@^F.{^@
��^@^S �^@^F4T^@^UF^L^@^K�^D^@^K�N^@^UF!^@^U�;^@^@^Nm^@^K O^@^L^W9^@^L^Y^F^@
��^@
��^@
�C^@^E� ^@^A^ZA^@^N��^@^N�^Y^@^U9H^@^UF ^@^R^\?^@^Sh�^@^U^]�^@^U^]�^@^U^]�^@^U^]�^@^@l�^@^Az�^@^D^�^@   ��^@^$

```
可以看到里面输出的是一些夹杂着文本的乱码。

这就要说到文件系统了。

分区里的文件并不是直接写入的，而是需要经过文件系统。文件系统相当于是一个组织文件的东西。

有了文件系统，我们才能正确的，有效率的读取文件，才能知道哪一部分是哪个文件。

#### 说了这么多废话，到了分区的时候了

(就不讲 MBR 的了)（早日转 GPT 早日获得新生）


（以下内容~~照搬~~参考自 https://blog.yoitsu.moe/arch-linux/installing_arch_linux_for_complete_newbies.html ，感谢萌狼能让我偷懒）
这里用 `cgdisk` 来给硬盘分区。

比如我这里 `/dev/nvme0n1` 是目标硬盘，于是运行 `cgdisk` 时加上 `/dev/nvme0n1` 这个参数：

```
                                                 cgdisk 1.0.1

                                           Disk Drive: /dev/nvme0n1
                                          Size: 500118192, 238.5 GiB

Part. #     Size        Partition Type            Partition Name
----------------------------------------------------------------
            1007.0 KiB  free space
   1        1024.0 MiB  EFI System
   2        150.0 GiB   Linux filesystem          Gentoo
   3        10.0 GiB    Linux swap
   4        77.5 GiB    Linux filesystem          Ipsum
            327.5 KiB   free space













    [ Align  ]  [ Backup ]  [  Help  ]  [  Load  ]  [  New   ]  [  Quit  ]  [ Verify ]  [ Write  ]


                                     Create new partition from free space

```
`cgdisk` 的界面大概像这样啦，用上下方向键把光标移动到汝之前的空闲空间上去（例如咱这里是最后一个）

新硬盘的话应该只有一个 free space 😂

用左右方向键把下面一排按钮上的光标移动到 New 上，然后按 Enter。

（这里看不出光标😂，黑色背景下光标应该是白的吧😂😂）

接下来会问几个问题（# 开头的是咱加上的注释😂）：
```
# 数字可能和汝看到的不一样😂
# 起始扇区的位置，直接 Enter 就行
First sector (749424640-972906511, default = 749424640):
# 大小，可以是扇区数，也可以是实际的大小（例如 100M，20G一类的），要用掉整个剩余空闲空间的话，直接 Enter 就行。
Size in sectors or {KMGTP} (default = 223481872):
# 分区类型，默认的就好
# 但是如果要建立新的 EFI 系统分区的话 ，分区类型是 :code:`ef00`
# 但是如果要建立新的 交换空间（就是虚拟内存啦）的话 ，分区类型是 :code:`8200`
Current type is 8300 (Linux filesystem)
Hex code or GUID (L to show codes, Enter = 8300):
# 设置卷标，不设置也行。
Current partition name is ''
Enter new partition name, or <Enter> to use the current name:
```
然后汝应该会发现下面的空闲空间变成 `Linux filesystem` 了呗~

要保存分区表的话，用左右方向键把下面一排按钮上的光标移动到 `Write` 上，然后按 `Enter`。

```
Are you sure you want to write the partition table to disk? (yes or no):

        Warning!! This may destroy data on your disk!
```
在这里输入 `yes` （就是 `yes`，不是 `y` `Y` `YES` 啥的😂），然后按 `Enter`。

然后下面会闪过一行 `The operation has completed successfully` , 这时就可以退出了。

用左右方向键把下面一排按钮上的光标移动到 `Quit` 上，然后按 `Enter`。

然而汝以为这样就结束了？还没格式化呢 (╯°Д°）╯︵/(.□ .)

再次用 `lsblk` 确认下分区名称，为了以防万一记得加上 `-f` 参数：
```
root@archiso ~ # lsblk -f
NAME   FSTYPE   LABEL       UUID                                 MOUNTPOINT
sda
├─sda1 vfat               3C44-B4ED
├─sda2
├─sda3 ntfs               42E243C5E243BBC3
├─sda4 ntfs               58741F29741F0A00
└─sda5
sdb
└─sdb1 vfat   ARCH_201610 EAC8-F012                            /run/archiso/bootmnt
sr0
loop0  squashfs                                                  /run/archiso/sfs/airootfs

```
第一排分别表示设备名称，文件系统类型，卷标，UUID 和挂载点。

咱这里的话 sda1 那个 vfat 分区就是 EFI 系统分区啦，sda5 就是刚刚新建的分区啦~（因为还没格式化所以没有文件系统😂）

用 mkfs.ext4 把那个分区格式化成 ext4 文件系统咯~

记得自己看清楚是哪个分区别格式化错了 😂
```
root@archiso ~ # mkfs.ext4 /dev/sda5
mke2fs 1.43.3 (04-Sep-2016)
Creating filesystem with 27935234 4k blocks and 6987776 inodes
Filesystem UUID: a3943e57-6217-4a5f-8e57-ade5771315c0
Superblock backups stored on blocks:
    32768, 98304, 163840, 229376, 294912, 819200, 884736, 1605632, 2654208,
    4096000, 7962624, 11239424, 20480000, 23887872

Allocating group tables: done
Writing inode tables: done
Creating journal (131072 blocks): done
Writing superblocks and filesystem accounting information: done

root@archiso ~ #
```
等一排文字闪过就格式化完了……

如果要格式化新的 EFI 系统分区的话，用 mkfs.vfat

如果要格式化新的 交换空间的话，用 mkswap

**接下来就该用 mount 挂载分区啦**