20190215 试验添加xfce

>petalinux-config -c rootfs
>
>进入Petalinux Package Groups中xfce

xfce是否可以支持usb键鼠？



进阶参考

http://www.elecfans.com/emb/fpga/20170208481528_a.html



在SD卡建立系统分区rootfs

ug1144



安装gnome的一般流程

http://www.cnblogs.com/sbaicl/archive/2013/01/09/2853174.html

安装gdm用于登入



调整分辨率

<http://www.blog.chinaunix.net/uid-25909722-id-3019407.html>

<http://bbs.chinaunix.net/thread-1365366-1-1.html>



20190215 16:56

xfce可以启动，但是通过usb接口接入的鼠标无法工作



petalinux安装软件的可以使用rpm，在petalinux-config -c rootfs中filesystem packages -> mics -> rpm



20190215 17:28

petalinux添加USB驱动

https://blog.csdn.net/lulugay/article/details/86752721

https://www.cnblogs.com/vacajk/p/6148857.html

查看

Device Drivers -> USB Supports

试验添加USB ULPI PHY interface support，因为ug1182提至USB0使用了ULPI



20200119 15:13

petalinux 安装 ubuntu

https://blog.csdn.net/xingpenglu/article/details/82658361

