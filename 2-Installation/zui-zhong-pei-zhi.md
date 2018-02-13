### 最终配置

#### 设置时区：

AOSC OS 的所有时区信息都存放在 `/usr/share/zoneinfo/<区域>/<城市>` 比如中国大陆就是 `/usr/share/zoneinfo/Asia/Shanghai`。至于为什么是上海不是北京，去问 ISO 吧（

而系统以 /etc/localtime 作为实际时区，这时我们需要建立一个符号链接：

```
# ln -sf /usr/share/zoneinfo/Asia/Shanghai /etc/localtime
```

解释：

* `ln`：创建链接的程序。
* `-s`：软链接。
* `-f`：强制。
* `/usr/share/zoneinfo/Asia/Shanghai`：这是要链接的目录。
* `/etc/localtime`：这是链接到的目录。

#### 设置 locale：

locale 是一些区域性设置等等，下载下来的 tarball 并没有自己生成好，所以你需要自己生成你需要的：

```
# locale-gen
```

其实默认情况下 AOSC OS 会生成极大量的 locale，当中大部分是可以省去的，你可以通过修改 `/etc/locale.gen` 来做到。

然后是设置你的语言，在 `/etc/locale.conf`里写入你想用的语言，在例子里是中文：

```
# echo “LANG=zh_CN.UTF-8” >> /etc/locale.conf
```

解释：

* `echo “LANG=zh_CN.UTF-8”`：输出 “LANG=zh\_CN.UTF-8”。
* `>> /etc/locale.conf`：重定向到 `/etc/locale.conf`。

#### 设置主机名：

还记得之前那个拉风的 `ipsum` 么？

现在轮到你来修改它啦！

```
# echo ”YourGreatHostname“ > /etc/hostname
```

然后你就能愉快得重启啦！

```
# exit
# reboot
```



