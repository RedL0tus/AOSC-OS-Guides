### 配置引导器

我们现在需要一个程序在上电之后告诉电脑需要启动什么东西，这个程序就是引导器。

现代 PC 主要分为两种引导方式，UEFI 和 MBR，在这里只讨论前者（现代 PC 也基本上都支持它）。

而 UEFI 要求把引导器放到一个特殊的分区，叫做 EFI 系统分区（EFI System Partition, ESP）。我们需要先把它挂载到 /efi 下，如果你喜欢也可以选择别的位置：

```
# mkdir /efi
# mount /dev/<ESP> /efi
```

AOSC OS 默认使用 GRUB 作为引导器，在这里先安装 GRUB 并生成它的配置文件：

```
# grub-install --target=x86_64-efi --bootloader-id=AOSC-GRUB --efi-directory=/efi
# grub-mkconfig -o /boot/grub/grub.cfg
```

如果以前顺利的话，下一步就是**添加用户**了。

