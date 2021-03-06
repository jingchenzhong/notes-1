---
tags: [ kernel ] 
date: 2015-04-25
title: Yet another guide on the way to linux kernel hacking
category: cs
---
[1]: https://www.gnu.org/software/global/links.html
[2]: http://os.phil-opp.com/
[3]: https://blacks3pt3mb3r.wordpress.com/linux-stuffz/264-2/


# Build minimal bootable rootfs
./etc/yum.repos.d/
./etc/yum.repos.d/fedora-updates-testing.repo
./etc/yum.repos.d/fedora.repo
./etc/yum.repos.d/fedora-updates.repo
./etc/yum.repos.d/fedora-cisco-openh264.repo
can be gotten by supermin --prepare bash -o /tmp/supermin.d
or just copy from you host to some place like /home/firo/kernel/k/testfs/
sudo dnf --releasever=27 --installroot=/home/firo/kernel/k/testfs/ --setopt=reposdir=/home/firo/kernel/k/testfs/etc/yum.repos.d install dnf udev passwd

# Boot the kernel using this directroy as rootfs through qemu

# Resources
[Writing an OS in Rust][1]

# Subscribe linux kenrel maillist


# linux next source code
[Working with linux-next](https://www.kernel.org/doc/man-pages/linux-next.html)
要add git://git.kernel.org/pub/scm/linux/kernel/git/next/linux-next.git不是https.
我之前改了好多bug基于linus的分支, 提交后被告知别人已改. 后来Julia告诉我要基于linux-next开发.
我的linux next remote 就叫next, 基于其创建开发分支.

# Source code navigation
['Source code reading' related sites][1]


# Applying patches
[100 Linux Tutorials: How to Apply a Patch to the Linux Kernel Stable Tree][0]

# Setup kvm test machine
[Building a KVM host machine.][3]


# How to build a minimal kernel for testing?
[tiny config @ kernel.org][5]
[3 attempts to reduce the configurations][6]
[Fedora equivalent of debootstrap](7)
Then enable following config option
CONFIG_CHR_DEV_SG
Some ftrace stuff
CONFIG_SLUB
CONFIG_KASAN

# For nfs
dnf install nfs-utils
cat /etc/exports # For more details, man exports
/home/firo/kernel/k/testfs 127.0.0.1(rw,sync,fsid=0,no_root_squash)
systemctl start nfs-server.service
systemctl status nfs-server.service 
● nfs-server.service - NFS server and services
   Loaded: loaded (/usr/lib/systemd/system/nfs-server.service; disabled; vendor preset: disabled)
   Active: active (exited) since Sat 2018-03-17 17:52:29 CST; 4s ago

## Test the nfs
sudo mount -t nfs localhost://home/firo/kernel/k/testfs /mnt
ls /mnt/
bin  boot  dev  etc  home  lib  lib64  media  mnt  opt  proc  root  run  sbin  srv  sys  tmp  usr  var
if touch prermission denied
add no_all_squash to/etc/exports

## NFS vers
rpcinfo -t localhost nfs 
program 100003 version 3 ready and waiting
program 100003 version 4 ready and waiting

rpcinfo -p | grep nfs
    100003    3   tcp   2049  nfs
    100003    4   tcp   2049  nfs
    100227    3   tcp   2049  nfs_acl
## NFS errors
[   54.600121] NFS: sending MNT request for 10.0.2.2:/buildarea1/firo/ima/export/dist
[   54.600121] NFS: failed to create MNT RPC client, status=-101
[   54.600121] NFS: unable to mount server 10.0.2.2, error -101
CONFIG_E100 CONFIG_E1000 ...and IP_PNP and DHCP BOOTP RARP

# Qemu boot systemd
qemu-system-x86_64 -nographic -enable-kvm  -kernel ./bzImag  -append ' console=ttyS0 ip=dhcp root=/dev/nfs nfsroot=192.168.0.104:/home/firo/kernel/k/testfs,nfsvers=3,tcp rw nfsrootdebug debug  raid=noautodetect selinux=0 enforcing=0 '

nfsvers=3,tcp and 192.168.0.104 or 10.0.2.2 are mandatory!

## dev-ttyS0.device
[**    ] A start job is running for dev-ttyS0.device (7s / 1min 30s)
[ TIME ] Timed out waiting for device dev-ttyS0.device.
[DEPEND] Dependency failed for Serial Getty on ttyS0.

CONFIG_FHANDLE=y
https://github.com/systemd/systemd/issues/3446
And install systemd-udev

# input comand overwrite itself?
http://shallowsky.com/blog/tags/embedded/
resize
xterm-resize-330-3.fc27.x86_64
/etc/profile
PS1='[\[\033[0;32m\]\u@\h:\[\033[36m\]\W\[\033[0m\]]\$ '

# qemu
access denied by acl file
qemu-system-x86_64: -netdev bridge,id=inet: bridge helper failed
https://blog.christophersmart.com/2016/08/31/configuring-qemu-bridge-helper-after-access-denied-by-acl-file-error/

sudo ip link set enp0s31f6 master br0
# systemd
autologin
Change /usr/lib/systemd/system/serial-getty@.service to
# ExecStart=-/sbin/agetty -o '-p -- \\u' --keep-baud 115200,38400,9600 %I $TERM
ExecStart=-/sbin/agetty -a root --keep-baud 115200,38400,9600 %I $TERM


## Unuseful unfsd obselete
exports0 
 cat var/exports0 
/buildarea1/firo/x564 (rw,no_root_squash,no_all_squash,insecure)

/buildarea1/firo/x564/host-cross/usr/bin/pseudo /buildarea1/firo/x564/host-cross/usr/sbin/unfsd -p -N -i /buildarea1/firo/x564/var/nfs0.pid -e /buildarea1/firo/x564/var/exports0 -x 11111 -n 3049 -y 21111 -m 3048 
 /buildarea1/firo/x564/host-cross/usr/bin/qemu-system-x86_64 -nographic -k en-us -kernel /buildarea1/firo/x564/bitbake_build/tmp/deploy/images/bzImage-qemux86-64.bin -redir udp:20485::17185 -redir tcp:7747::1534 -redir udp:7747::1534 -redir tcp:7748::80 -redir udp:7745::6443 -redir tcp:7741::23 -redir tcp:7740::22 -redir tcp:8998::5698 -redir tcp:8978::5678 -redir tcp:6633::3333 -net user,hostname=qemu33 -net nic,macaddr=52:54:0:12:34:77,model=i82557b -show-cursor -usb -usbdevice wacom-tablet -vga vmware -gdb tcp::4534 -append console=ttyS0,115200 ip=dhcp root=/dev/nfs nfsroot=10.0.2.2:/buildarea1/firo/x564/export/dist,nfsvers=3,port=6349,mountprog=24411,nfsprog=14411,udp,mountport=6348 rw clocksource=pit oprofile.timer=1 UMA=1 -pidfile /buildarea1/firo/x564/host-cross/var/qemu33.pid

## For sda
./kernel/configs/kvm_guest.config
VFS: Cannot open root device "sda" or unknown-block(0,0): error -6
Please append a correct "root=" boot option; here are the available partitions:
Kernel panic - not syncing: VFS: Unable to mount root fs on unknown-block(0,0)
Kernel Offset: disabled
CONFIG_BLK_DEV_SD
CONFIG_SATA_AHCI XX
CONFIG_ATA XX
CONFIG_ATA_PIIX ok
process 1 (init) attempted a POSIX timer syscall while CONFIG_POSIX_TIMERS is not set

CONFIG_POSIX_TIMERS
FILE_LOCK if udev
PACKET if udev
UNIX if udev
DEVTMPFS if udev
CONFIG_INOTIFY_USER if udev
CONFIG_TMPFS if udev
CONFIG_DEBUG_INFO_REDUCED if CONFIG_DEBUG_INFO


CONFIG_NETFILTER
smp
ext4
CONFIG_SERIAL_NONSTANDARD=y

udev requires hotplug support, not started ... failed!
not cpu hotplug
CONFIG_HOTPLUG=y
CONFIG_UEVENT_HELPER_PATH=‚Äù"
CONFIG_NET=y
CONFIG_UNIX=y
CONFIG_SYSFS=y
CONFIG_SYSFS_DEPRECATED*=n
CONFIG_PROC_FS=y
CONFIG_TMPFS=y
CONFIG_TMPFS_POSIX_ACL=y¬†
CONFIG_INOTIFY=y
CONFIG_SIGNALFD=y

# Hacker
[Peter Zijlstra: From DOS to kernel hacking][4]


# What to fix?
[KernelProjects][3]
[linux-wanking@vger.kernel.org][1]
[2.7 GETTING STARTED WITH KERNEL DEVELOPMENT][2]

[0]: https://www.youtube.com/watch?v=6zUVS4kJtrA
[1]: https://lwn.net/Articles/283982/
[2]: https://www.linux.com/publications/how-participate-linux-community
[3]: https://kernelnewbies.org/KernelProjects
[4]: https://lwn.net/Articles/286244/
[5]: https://tiny.wiki.kernel.org/
[6]: http://mgalgs.github.io/2015/05/16/how-to-build-a-custom-linux-kernel-for-qemu-2015-edition.html
[7]: https://rwmj.wordpress.com/2009/03/05/fedora-equivalent-of-debootstrap/



# Reference
[Operating Systems: Three Easy Pieces](http://pages.cs.wisc.edu/~remzi/OSTEP/)
[BrokenThorn Entertainment Operating System Development Series](http://www.brokenthorn.com/Resources/OSDevIndex.html)
[Speeding up kernel development with QEMU](https://lwn.net/Articles/660404/)
[Linux Kernel Reading Guide](http://www.linux.org/threads/linux-kernel-reading-guide.5384/)
[Phrack](http://phrack.org/index.html)
[Pre-defined Compiler Macros](https://sourceforge.net/p/predef/wiki/Architectures/)

# kernel hacking
The cxgb3_*_send() functions return NET_XMIT_ values, which are
positive integers values. So don't treat positive return values
as an error.
67f1aee6f45059fd6b0f5b0ecb2c97ad0451f6b3
# old questions
What is platform driver?
find source code of config
How to print backtrace by kernel oops, and why it's not exact?
What is abi 
why get_unaligned_be16?
 _THIS_IP_ vs __FUNCTIONS__

# kernel data type
[__u32](http://yarchive.net/comp/linux/kernel_headers.html) and [this](http://www.linuxjournal.com/article/5783) and dd3e chapter 10.

# The principle of kernel & driver backport
将高本版kernel的feature移植到低版本的kernel的过程就是backport.
1. 尽量保持与mainline的代码一致
2. 做好取舍不要引入太多patch. 
## 开发流程
理清所引入feature的代码. 主要数据结构以及功能流程.


[走近Linux内核-- 王聪](http://wangcong.org/2007/03/09/-e8-b5-b0-e8-bf-91linux-e5-86-85-e6-a0-b8/)
# kernel hacker之路
我实在太想聊这个话题 -- 内核hacker的成长之路!
不是教你写第一个kernel module, 就灭火了. 而是持续的一步步成长为
内核的中坚力量, 甚至是maintainer.
作为一个内核爱好者, 从接触到现在已经3年半了. 然而, 我对自己是非常失望的.
因为我看了3年多的书, 从今年(15)5月才开始, 给社区提补丁.
起始我在12年的11月份曾经给社区, 提过几次补丁. 后来有那么两三次, 想帮着修复
kernel panic 和oops的问题, 基本上个人感觉难度非常太大就无极而终了.
5月份这次, 我是因为实在不想在看书籍了, 市面上稍微有点名的书籍, 我都看过.
实在太厌烦再看书了, 我把自己和另外一个内核hacker 王聪做了对比, 得出结论:

	赶快滚去给社区提patch! 
我挨个看了王聪给社区提的前50个patch. 都是很简单的修改.
我几次挫败都是, 因为没能找到合适内核事情去做, 最终没有下文了. 
今天, 我在给社区提了20个左右的patch 10几个被接收了. 我现在找到了一条成长路.
## 内核我现在认为可以给新人做得事
https://lwn.net/Articles/284099/  这个帖子, 提供了新手可以做得事情, 我觉得非常好.
smatch coccinelle的分析结果.
源码中标注的FIXME和TODO, 简单的提过去很难被接受, 难的这些开发者自己都没搞定, 新人更难.
这也不是一条好路.
内核bugzilla kerneloops上问题, 这个很难.
找一个和内核相关的工作驱动啊, 网络开发, 虚拟化, 存储都非常赞.
自己搞个feature, 感觉更难, 这个需要需求驱动.
没有一条轻松的路让你走, 但你却不应该光着脚走在上面!!!
内核之外有很多值得珍惜的. 尽力做好, 你能做的, 开始点滴积累.
终有一天会聚成沧海, 前提是你等得到那天, 不要亏待自己.
内核之路始终时不那么清晰, 但是反思过后, 你便知道, 该去做什么了.
这也许是最难受的情况了.
Linus 之前说过, 搞得内核你得用起来. uml 是个不错的开端.
用起来才是王道!

#git
[gittutorial - A tutorial introduction to Git](http://git-scm.com/docs/gittutorial)
# 你提patch, 社区的困惑是什么?
每个kernel newbie 都应该完整看完这个mail list
http://thread.gmane.org/gmane.linux.kernel/683798/focus=684297
你能看到鹰派的Al还有温和派Andrew Morton.
为什么http://kernelnewbies.org/这么重要网站, 不做的好一点, 至少界面友好点.
应该找个机会, 整一下.
# Fix kernel mistakes
内核至今如此优秀就是因为, 成千上万前赴后继的开发者笔耕不缀的结果.
别当豆包不当干粮, 虽然, 你不能设计出很牛的算法, or 什么子系统,
但是内核还有很多问题有待解决, 正所谓老虎也要打, 苍蝇也要拍; 千里之堤毁于蚁穴.
我提过好些这种patch Dan Carpenter都告诉我Linus已经改了.
## kernel newbie上有个list
[KernelJanitors/Todo](http://kernelnewbies.org/KernelJanitors/Todo)
* Style fix
如:
remove_wait_queue(entry->wait_address,&entry->wait);
remove_wait_queue(entry->wait_address, &entry->wait);
## Smatch
smatch这个工具是Dan写的主要就是为了找到内核的小问题, 基本用法:

        make CHECK="~/path/to/smatch/smatch -p=kernel" C=1 \
                bzImage modules | tee warns.txt
smatch 会产生好多可疑的问题, 细心寻找吧.
你找到了, 那么就是修改了.
记住在smatch之前一点要git pull next master:now一下, 保证checkout到了now的分支.
## Coccinelle
Coccinelle是 Julia Lawall 写的静态检测工具.very nice.
自己研究吧
Documentation/coccinelle.txt
http://pagesperso-systeme.lip6.fr/Julia.Lawall/tutorial.pdf
## 生成patch
先修改.
之后git add
在commit 之前你要 git log --online path/to/modification/file
看下提交的titile:

	git log  --oneline drivers/base/firmware_class.c
	5455c8c firmware: Fix memory leak in error path
	e0fd9b1 firmware: use const for remaining firmware names
	f9692b2 firmware: fix possible use after free on name on a
主义冒号:后一定要有一个空格!
你会还是看下Document下的submitting的文档.
之后commit:
先是类似上面的一行oneline 简要说明
空一行.
之后具体描述下.
commit后, 生成patch
如果你只提交了一次:

	git format-patch HEAD^..HEAD
这个命令就ok了.
如果commit多次, 自己斟酌两次diff的commit id了, HEAD^ 和HEAD都是commit id.
这样就生成了patch, 一般叫做0001-xxx-ooo.patch之类的
如我这个, 已被接收:

	0001-firmware-Fix-memory-leak-in-error-path.patch
commit id 是5455c8c3284a63e2673d1be7f040fb245cbf9be9
## 测试patch
复杂的patch要编译内核, 安装的机器上跑一下.
## 发送patch
天朝用户自己打梯子吧proxychains shadowsocks.
基本步骤是
先编译一下:
make path/to/modification/file.o
之后
./scripts/checkpatch.pl
之后 get maintainer
./scripts/get_maintainer.pl

发送的时候, 发给维护者 --cc其他人, 还有cc 一个mailist, 如果没有合适的list的话,
就cc到linux-kernel@vger.kernel.org, 这是个开放的list, 有合适的list了, 就不要cc它了.

	proxychains git send-email --to ming.lei@canonical.com --cc gregkh@linuxfoundation.org --cc kernel-janitors@vger.kernel.org 0001-firmware-Fix-memory-leak-in-error-path.patch
免不了你要返工重新修改.这时候, 新生成的patch这样:

	git format-patch --subject-prefix="PATCH v2" HEAD^..HEAD
改几次就v几.
和社区交流的时候, 要注意礼貌, 而且要感谢别人对你的patch做出的建议,
没有人的时间是被猪拱来的.

这些都是比较简单(代码量上)的patch, 要想提交深度的还需要对某方面的深度.
基本上这就完了, 你的真正的patch就给社区了.
# 进阶decent
这个是昨天晚上改drivers代码时候, 看到TODO的注释猛然想到的.

	grep -nr 'FIXME' --include="*.c"  ./ | tee fixmek.log
	grep -nr 'TODO' --include="*.c" ./ | tee todok.log
	wc -l fixmek.log 
有6000多个.

# Deeply involved
http://vger.kernel.org/~davem/net_todo.html
这个列表上的基本都过期了....哎
往深了走不是难事, 主要是意识到内核, 不是什么神秘的东西, 选好一个方向
简单看看概念见我的<如何学习> 就可以实践.
必须要意识到, 动手比看再多概念管用, 我就是之前看了太多, 还发展出一个哲学
体系出来:-) 确实随着现代信息科学给社会带来的巨大变化, 哲学也必须要更新.
才能更好的服务于人.
这几天就在, 找内核哪里还不完善, 自己能补上, 今天6号了.
# patch formate advices
* Julia Lawall Sorry to be picky, 
but normally people put a space after the colon.  Also,
the subject line could be shorter: Remove unneeded cast.  
The description part of the subject doesnt have to be unique, 
just the whole thing, asfter the [PATCH] part.
* Dan Carpenter
Otherwise your patch was fine, btw.  Other improvements.
Don't put "Drivers:" in the subject.
On Wed, Apr 22, 2015 at 09:10:50PM +0800, Firo Yang wrote:
> From: Firo Yang <firogm@gmail.com>
Don't include this line.  We can get it from you email address.

Include everyone from the ./scripts/get_maintainer.pl output except
don't include linux-kernel@vger.kernel.org if there is another mailing
list there already.
* To find  patch prefix

	git log --oneline  path/to/file.c
* 更新patch 要加v几

	git format-patch --subject-prefix="PATCH v2" xxx..ooo
* 提交多个patch 要手动生成一个[PATCH 0/N], 这个0就是要写简要描述的.

	proxychains git send-email --subject "[PATCH v2 0/15] Remove unneeded casts of memory-alloc function return values" --thread --compose --confirm=compose --to firogm@gmail.com *.patch
* Dave Miller偏好
@@ -325,13 +325,15 @@ static inline void empty_child_dec(struct key_vector *n)
  static struct key_vector *leaf_new(t_key key, struct fib_alias *fa)
  {
-       struct tnode *kv = kmem_cache_alloc(trie_leaf_kmem, GFP_KERNEL);
-       struct key_vector *l = kv->kv;
+       struct tnode *kv;
+       struct key_vector *l;
Dave Miller usually prefers it if variables are ordered from longest to shortest.  
So you should probably have l defined first, and then kv.

# FAQ
kernel module name: scripts/Makefile.lib $(subst -,_,$1)
