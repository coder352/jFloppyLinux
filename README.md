# 前期准备工作 会用到 busybox 编译 和 linux 的源码，以及一份教程，在百度云盘中放着，想要的可以联系我
# 但不用那些基础知识，直接写些脚本放进指定的目录，执行 config 就可以得到启动盘

./floppy.flp 是一张简单的启动光驱,没有自己的脚本,只是按照教程实现了一遍
./floppy.img 带有自己的照片,因为太大,超过了 1.44M ,所以flp装不下,换成了 img

首先制作一张软盘
dd if=/dev/zero of=floppy.flp bs=1024 count=1440
这是制作一张1.44M的软盘，然后放到virtualbox的FloppyController上
不管软盘做的有多大，挂到RedHat9的系统上，只能往里面写1.44M的数据

接着就可以在 FloppyLinux/floppyImage/ 目录里写脚本了
注意这里能实现的功能不多，最好先在我的 floppy.flp 挂载到virtualbox上打开，实验一下脚本能否运行，然后再制作Floppy
开机启动的脚本 要写在 /boot/grub/init.d/rdS 中
大小不要超过1.44M，否则要在iso的光盘上制作了
制作iso的步骤比较麻烦，可以
dd if=/dev/zero of=/floppy.img bs=1024 count=2880
这样也可以同样用 config 的生成脚本了,挂载点也是 /dev/fd0
FloppyLinux目录里已经提供了一个成品 floppy.flp 和 floppy.img 可以直接做启动盘

注意 config 脚本要在 root 权限下运行 
sudo ./config

then
cd /mnt/floppy
$ grub

grub> root (fd0)
Filesystem type is ext2fs, using whole disk

grub> setup (fd0)
Checking if "/boot/grub/stage1" exists... yes 
Checking if "/boot/grub/stage2" exists... yes
Checking if "/boot/grub/e2fs_stage1_5" exists... no
Running "install /boot/grub/stage1 (fd0) /boot/grub/stage2 p /boot/grub/grub.conf "... succeeded
Done.

grub> quit
这样就制作完成了

目录树的结构为:
[root@localhost mnt]# tree floppy/
floppy/
|-- boot
|   |-- bzImage
|   `-- grub
|       |-- grub.conf
|       |-- menu.1st -> grub.conf
|       |-- stage1
|       `-- stage2
|-- initrd.img.gz
`-- lost+found
 
3 directories, 6 files

# 实验指导中的 坑～
1. etc/init.c/rcS 要设置成可执行文件 777
2. boot/grub/grub.conf 中
timeout 设置为 非0 这样会显示 系统选择界面
default 设置 默认选择那个操作系统

#  中国石油大学 操作系统课程设计 实验指导(李永) 第三章 FloppyLinux 的实现
