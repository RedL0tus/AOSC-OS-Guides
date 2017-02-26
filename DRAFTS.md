<<<<<<<<<<<<< 2-Installion/AMD64/4.PrepareDisk：
首先我们输入 `fdisk`，并回车，我们会看到：
```
lorem@ipsum ~ % fdisk

Usage:
 fdisk [options] <disk>      change partition table
 fdisk [options] -l [<disk>] list partition table(s)

Display or manipulate a disk partition table.

Options:
 -b, --sector-size <size>      physical and logical sector size
 -B, --protect-boot            don't erase bootbits when creating a new label
 -c, --compatibility[=<mode>]  mode is 'dos' or 'nondos' (default)
 -L, --color[=<when>]          colorize output (auto, always or never)
                                 colors are enabled by default
 -l, --list                    display partitions and exit
 -o, --output <list>           output columns
 -t, --type <type>             recognize specified partition table type only
 -u, --units[=<unit>]          display units: 'cylinders' or 'sectors' (default)
 -s, --getsz                   display device size in 512-byte sectors [DEPRECATED]
     --bytes                   print SIZE in bytes rather than in human readable format
 -w, --wipe <mode>             wipe signatures (auto, always or never)
 -W, --wipe-partitions <mode>  wipe signatures from new partitions (auto, always or never)

 -C, --cylinders <number>      specify the number of cylinders
 -H, --heads <number>          specify the number of heads
 -S, --sectors <number>        specify the number of sectors per track

 -h, --help     display this help and exit
 -V, --version  output version information and exit

Available columns (for -o):
 gpt: Device Start End Sectors Size Type Type-UUID Attrs Name UUID
 dos: Device Start End Sectors Cylinders Size Type Id Attrs Boot End-C/H/S Start-C/H/S
 bsd: Slice Start End Sectors Cylinders Size Type Bsize Cpg Fsize
 sgi: Device Start End Sectors Cylinders Size Type Id Attrs
 sun: Device Start End Sectors Cylinders Size Type Id Flags

For more details see fdisk(8).

```
上面的内容是 fdisk 的帮助信息，至于为什么会出现这个，是因我们没指定设备。

那就先来看看我们有哪些设备吧，输入 `fdisk -l`:
```
Disk /dev/nvme0n1: 238.5 GiB, 256060514304 bytes, 500118192 sectors
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: gpt
Disk identifier: XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX

Device             Start       End   Sectors  Size Type
/dev/nvme0n1p1      2048   2099199   2097152    1G EFI System
/dev/nvme0n1p2   2099200 316671999 314572800  150G Linux filesystem
/dev/nvme0n1p3 316672000 337643519  20971520   10G Linux swap
/dev/nvme0n1p4 337643520 500117503 162473984 77.5G Linux filesystem

Disk /dev/sda: 119.2 GiB, 128035676160 bytes, 250069680 sectors
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: gpt
Disk identifier: XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX

Device     Start       End   Sectors   Size Type
/dev/sda1   2048     34815     32768    16M Microsoft reserved
/dev/sda2  34816 250068991 250034176 119.2G Microsoft basic data

```
可以看到有 `Disk /dev/blablabla` 这样的信息，这当中的 `/dev/blablabla` 代表的就是设备的名字，比如 `/dev/nvme0n1` 和 `/dev/sda`。

在这里比如我要对 `/dev/nvme0n1` 做编辑，输入 `fdisk /dev/nvme0n1`
你可能会发现，`fdisk` 可能并不会正常启动，只会有以下输出：
```
lorem@ipsum ~ % fdisk /dev/nvme0n1                                                                     :(

Welcome to fdisk (util-linux 2.29.1).
Changes will remain in memory only, until you decide to write them.
Be careful before using the write command.

fdisk: cannot open /dev/nvme0n1: Permission denied
```
会有个 `Permission denied` 的输出，这表示你当前用户的权限不够。

在这是有两种解决方法：
- 输入 `su root` 或 `sudo su`，切换到 `root`
- 在命令前面加上 `sudo`，暂时提升到 `root`（推荐）。  
（关于权限的内容，会在后面详细介绍）

在这里用 `sudo fdisk /dev/nvme0n1`
```
lorem@ipsum ~ % sudo fdisk /dev/nvme0n1
[sudo] password for lorem: 

Welcome to fdisk (util-linux 2.29.1).
Changes will remain in memory only, until you decide to write them.
Be careful before using the write command.


Command (m for help): 
```
==========================================