### 初始配置

可以看到现在新的根分区下面已经有了 AOSC OS 的所有文件。

我们来做一些基本配置。

但是在这之前我们需要做一些准备工作：

#### 挂载虚拟文件系统：

输入以下命令：

```
for i in dev proc sys; do sudo mount --rbind /$i /mnt/$i; done
```

这是段 BASH Shell 脚本，作用是挂在多个目录。  
这段脚本相等于：

```
sudo mount --rbind /dev /mnt/dev
sudo mount --rbind /proc /mnt/proc
sudo mount --rbind /sys /mnt/sys
```

解释：

* `sudo`： 提升权限
* `mount`：挂载
  * `--rbind`：递归绑定整个目录树上所有的文件系统
  * `/dev`, `/proc`, `/sys`：要挂载的目录
  * `/mnt/dev`, `/mnt/proc`, `/mnt/sys`：挂载点  

#### chroot!

输入以下命令：

```
sudo chroot /mnt /bin/bash
```

解释：

* `sudo`：提升权限
* `chroot`： 更改根目录（相当于是进入那个系统）（`chroot` 即 `change-root`）
  * `/mnt`： 要切换到的根目录
  * `/bin/bash`： 切换后要启动的程序（`/bin/bash`是之前提到的 Shell）

现在你应该能看到一个新的提示符：

```
root@ipsum [ / ] #
```

恭喜你，你现在已经进入了这个新的 AOSC OS 的环境。

如果 `chroot` 失败了，可能是你的路径错了，也可能是你下载错架构的 tarball 了。

#### 生成 `fstab`：

`fstab` 是个配置文件，储存于 `/etc/fstab`，用于在启动的时候告诉 `mount` 哪些分区是需要自动挂载的。  
在后面会详细介绍如何写 `fstab`，现在可以直接使用 `genfstab` 命令生成 `fstab`。

```
genfstab -U -p / >> /etc/fstab
```

#### 生成 initramfs

`initramfs` 是一个镜像文件，它包含了引导你的系统所需要的驱动等等。  
如果你使用了 LVM 以及一些别的特殊情况，这个步骤是必须的，在这里先不讨论。  
AOSC OS 提供一个触发 dracut 的脚本来自动生成 initramfs：

```
sh /var/ab/triggered/dracut
```

接下来就是**配置引导器**了。

