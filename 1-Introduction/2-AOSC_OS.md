### AOSC OS 又是什么?

![AOSC OS 4 Logo](../img/OS4Logo.png)

AOSC OS 是一个由 AOSC 维护的 Linux 发行版。

最早期的 AOSC OS 使用 openSUSE Studio 构建（可以在 AOSC 的新浪博客找到相关信息），之后还尝试过基于 Debian。

在 OS 2 时期改为基于 [BLFS（Beyond Linux From Scratch）](http://www.linuxfromscratch.org/blfs/) 构建。

OS 3 则由 OS 2 作为工具链重新构建。  
现在最新版本为 OS 4，代号为 Duang-Duang（于 AOSCC 2016 投票决定）

AOSC OS 使用 [`dpkg`](https://wiki.debian.org/dpkg) + [`apt`](https://wiki.debian.org/Apt) 作为包管理系统。

设计上尽量从简，一般不进行拆包。

在易用性上有些许改善。  
~~大概是最适合 Wine 跑 Galgame 的 Linux 发行版~~

为 AMD64（x86_64），ARM(ARMv7,ARMv8,AArch64),PowerPC(PPC32BE,PPC64BE)（由于没有小端序的设备所以仅支持大端序），MIPS（32/64）（主要是 Loongson） 等架构提供支持（有计划为 RISC-V 提供支持）。

同时也有（还处于开发阶段的） WSAOSC，为 Windows 10 的 Windows Subsystem Linux 进行适配。