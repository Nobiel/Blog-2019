# Linux下以只读方式挂载Win10快速启动分区

Win10的快速启动是个好东西，可惜开启以后并不能做到真正的关机，硬盘会以类似于休眠的方式保存着关机前的状态，以便下次开机时快速启动。这样一来，如果电脑内同时装有Linux系统，想在Linux环境下挂载Windows的分区就变得不是那么容易了。

![无法挂载位置](https://ws4.sinaimg.cn/large/6273c3b7ly1ffry2lan17j20hu08iq3g.jpg)

*解决办法*

以只读方式挂载Windows分区，虽然不能进行写操作，但是可以读取分区内的文件。

首先准备一个目录用于挂载分区。注意必须是已存在的目录，可以在根分区下创建一个目录专门用于挂载。

```bash
#在根分区下创建挂载目录
sudo mkdir /mnt/Data 
```

查看需要挂载的分区并挂载。可以使用`parted -l`命令查看（注：使用`fdisk -l`命令不能正确显示GPT分区）。

![查看分区](https://wx4.sinaimg.cn/large/6273c3b7ly1ffry8rprb9j20k405k0ux.jpg)

```bash
#挂载Number 5分区即/dev/sda5
sudo mount -o ro /dev/sda5 /mnt/Data
```

挂载完毕后可用`df -T`或`lsblk -f`命令查看结果。

*关于几个挂载分区的命令*

> df -T  只可以查看**已经挂载**的分区和文件系统类型。
> fdisk -l 可以显示出**所有挂载和未挂载**的分区，但不显示文件系统类型。
> parted -l 可以查看**未挂载**的文件系统类型，以及哪些分区尚未格式化。
> lsblk -f 也可以查看**未挂载**的文件系统类型。
>
> 引自 [linux查看未挂载分区](http://ju.outofmemory.cn/entry/309926)

