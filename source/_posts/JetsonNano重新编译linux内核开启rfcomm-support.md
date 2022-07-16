---
title: JetsonNano重新编译linux内核开启rfcomm_support
tags:
  - [JetsonNano]
categories:
  - gallery
date: 2022-07-16 16:57:21
---

## 问题

处理Jetson Nano开发版Ubuntu系统读取外部设备蓝牙传输的数据时，串口调试无法连接。`lsusb`命令可以查看到蓝牙适配器是存在的，`hcitool scan`也是可以搜索到外部的蓝牙设备的，`ls /dev/rf*`没有返回任何的rfcomm*，只有一个/dev/rfkill，绑定蓝牙物理设备地址的时候，`sudo rfcomm bind 0 xx:xx:xx:xx:xx:xx，返回错误，RFCOMM TTY support not availabel。

## 原因

Jetson nano默认的内核不支持RFCOMM协议，可以用一下命令查看

```zsh
zcat /proc/config.gz |grep RFCOMM
```

显示的信息如下所示

```zsh
CONFIG_BT_RFCOMM =  y
CONFIG_BT_RFCOMM_TTY is not set
```

所以我们需要重新编译kernel的源代码，编译的过程下面介绍，我们需要修改的就是menuconfig上面Bluetooth subsystem support中找到RFCOMM TTY support选项，将它开启即可。

## 重新编译内核过程

教程参考自一个[国外大佬的博客](https://blog.hypriot.com/post/nvidia-jetson-nano-build-kernel-docker-optimized/)

1. ### 查看你的当前linux内核模块的开启情况

首先你可以使用一个脚本查看你的当前linux内核模块的开启情况

```bash
wget https://github.com/moby/moby/raw/master/contrib/check-config.s
chmod +x check-config.sh
```

然后执行该脚本

```bash
./check-config.sh
```

然后你就会得到下图所示的结果

![](https://cdn.jsdelivr.net/gh/JJymsWorld/Jymscloudiimmgg@main/img/20220427171803.png)

事实上，这一步的检查如果已经通过上面的命令确定了RFCOMM未开启，不做此检查也是可以的。

2. ### 准备内核编译环境

在Jetson nano内核编译之前，我们首先需要确认所有的需要的构建工具都已经安装好了

```bash
sudo apt-get update
sudo apt-get install -y libncurses5-dev
```

3. 下载适用于Jetson Nano 的Linux内核资源文件

我们可以在nvidia开发者的官网找到下载链接https://developer.nvidia.com/embedded/，下载BSP Sources。

![](https://cdn.jsdelivr.net/gh/JJymsWorld/Jymscloudiimmgg@main/img/20220427172631.png)

```bash
$ cd
$ mkdir -p nano-bsp-sources
$ cd nano-bsp-sources
$ wget https://developer.nvidia.com/embedded/l4t/r32_release_v6.1/sources/t210/public_sources.tbz2
$ ls -alh Jetson-Nano-public_sources.tbz2
-rw-rw-r-- 1 pirate pirate 133M Mar 16 06:46 Jetson-Nano-public_sources.tbz2

```

然后解压下载得到的文件

```bash
tar -xvf public_sources.tbz2  Linux_for_Tegra/source/public/kernel_src.tbz2 --strip-components=3
cd Linux_for_Tegra/source/public
tar -xvf kernel_src.tbz2
```

4. ### 编译未修改内核

首先编译一下未修改的内核，以此确认我们已经安装了所有的依赖，并且熟悉一下编译内核的过程，保证后续的不会出错。

在编译之前，首先需要导入kernel configuration文件，在我们下载的资源里面是没有这个文件的，我们可以直接从当前系统导出。

```bash
cd ~/kernel/kernel-4.9
zcat /proc/config.gz > .config
```

然后确认一下config文件内容

```bash
head -10 .config
```

然后开始内核编译工作

```bash
make prepare
make modules_prepare

time make -j5 Image
time make -j5 modules

```

![](https://cdn.jsdelivr.net/gh/JJymsWorld/Jymscloudiimmgg@main/img/jetson-nano-board-compile-kernel.jpg)

上述命令执行整个过程大概需要一个小时左右，可以直接在Jetson nano上进行上述工作。

5. ### 用编译的内核替换当前linux系统的内核

首先可以使用`uname -a`命令查看当前内核信息，方便后面对比

在安装内核前，首先备份当前的内核，方便出现问题是我们能够替换回原来的内核。

```bash
#backup the old kernel Image file
sudo cp /boot/Image /boot/Image.original

#Install modules and kernel image
cd /kernel/kernel4.9
sudo make modules_install
sudo cp /arch/arm64/boot/Image /boot/Image

#确认内核已经更换
ls -alh /boot/Image*
```

然后重新启动系统即可，重启后可使用`uname -a`命令查看当前内核是否已经更改。

ps：每次更改内核设置都需要编译一个新的内核，然后安装kernel Image和kernel Modules

6. ### 更改内核设置并重新执行上述内核编译操作

我们使用内核设置的可视化面板`menuconfig`来更改内核设置。这个工具在我们之前安装的`libncurses5-dev`中已经包含。相关命令如下：

```bash
#备份内核设置
cd /kernel/kernel-4.9
cp .config kernel.config.original

make menuconfig
```

![](https://cdn.jsdelivr.net/gh/JJymsWorld/Jymscloudiimmgg@main/img/v2-5d665fbef3174a3a981aa6d3b7dfe763_1440w.jpg)

找到如图所示的位置，按Y键启用该模块。然后重新执行内核编译命令，最后替换到`boot/Image`

```bash
cd /kernel/kernel-4.9

#编译内核
make prepare
make modules_prepare

time make -j5 Image
time make -j5 modules

#替换内核文件
sudo make modules_install
sudo cp arch/arch64/boot/Image /boot/Image
```

重启操作系统，即完成模块修改和内核替换。

## 调试蓝牙模块

在上面重新编译内核之后，并没有完全解决问题，蓝牙串口模块仍然无法连接。

这里我使用的是pybluez来进行蓝牙通讯，代码使用的官方实例代码，例子如下：

```python
from bluetooth import *

server_sock = BluetoothSocket(RFCOMM)
server_sock.bind(("", PORT_ANY))
server_sock.listen(1)

port = server_sock.getsockname()[1]

uuid = "94f39d29-7d6d-437d-973b-fba39e49d4ee"

advertise_service(server_sock, "SampleServer",
                  service_id=uuid,
                  service_classes=[uuid, SERIAL_PORT_CLASS],
                  profiles=[SERIAL_PORT_PROFILE],
                  #                   protocols = [ OBEX_UUID ]
                  )

print("Waiting for connection on RFCOMM channel %d" % port)

client_sock, client_info = server_sock.accept()
print("Accepted connection from ", client_info)

try:
    while True:
        data = client_sock.recv(1024)
        if str(data.decode()) == '0':
            client_sock.send("verify 0")
        if len(data) == 0: break
        print("received [%s]" % data.decode())
except IOError:
    pass

print("disconnected")

client_sock.close()
server_sock.close()
```

执行此代码，提示错误如下：

```python
Traceback (most recent call last):
  File "rfcomm-server.py", line 20, in <module>
    profiles = [ SERIAL_PORT_PROFILE ],
  File "/usr/lib/python2.7/site-packages/bluetooth/bluez.py", line 176, in advertise_service
    raise BluetoothError (str (e))
bluetooth.btcommon.BluetoothError: (2, 'No such file or directory')
```

这里有两个方法可以尝试，可以选择对自己有效的方法：

方法1：

修改`/etc/systemd/system/dbus-org.bluez.service`文件中`ExecStart=/usr/lib/bluetooth/bluetoothd`为`ExecStart=/usr/lib/bluetooth/bluetoothd -C`，然后执行`sudo sdptool add SP`，这样便能够生成`/var/run/sdp`文件，我的情况是已经存在这个文件，所以我没有执行最后一条命令。

方法2：

创建一个覆盖文件在`/etc/systemd/system/bluetooth.service.d/override.conf`

内容如下

```
[Service]
ExecStart=
ExecStart=/usr/lib/bluetooth/bluetoothd -C
```

这里的文件夹和文件可能不存在，不存在的话直接创建即可。

然后重启bluetooth service或者重启操作系统。

再次执行上面的pybluez代码，通过手机蓝牙串口调试软件即可正常连接。

## 其他问题记录

1. no advertisable device
   原因：由于蓝牙不可见导致
   相关帖子：[https://stackoverflow.com/questions/63788077/how-can-i-make-raspberry-pis-bluetooth-discoverable-to-iphone](https://link.zhihu.com/?target=https%3A//stackoverflow.com/questions/63788077/how-can-i-make-raspberry-pis-bluetooth-discoverable-to-iphone)
   解决方案：增加代码在检测到这类错误时，开启蓝牙可发现
2. no such file or directory
   原因：Bluez 5的bug导致，需要使用兼容模式蓝牙
   相关帖子：[https://stackoverflow.com/questions/36675931/bluetooth-btcommon-bluetootherror-2-no-such-file-or-directory](https://link.zhihu.com/?target=https%3A//stackoverflow.com/questions/36675931/bluetooth-btcommon-bluetootherror-2-no-such-file-or-directory)
   解决方案：增加代码在发生该问题时，修改相应文件内容，并重启服务
3. Permission denied
   原因：sdp执行没有权限
   相关帖子：[https://bbs.archlinux.org/viewtopic.php?id=201672](https://link.zhihu.com/?target=https%3A//bbs.archlinux.org/viewtopic.php%3Fid%3D201672)
   解决方案：增加代码修改/var/run/sdp的权限
