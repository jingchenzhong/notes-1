---
tags: [ kernel ] 
title: An introduction to File system in Linux operating system
date: 2015-02-27T15:46:12+08:00 
category: cs
---

# Reference
[VFS notes](http://www.fieldses.org/~bfields/kernel/vfs.txt)
[Linux: Replacing atime With relatime][1]
[Why is Linux's filesystem designed as a single directory tree?][2]
[Design a file system](https://stackoverflow.com/questions/5376116/design-a-file-system)

[0]: http://yarchive.net/comp/linux/everything_is_file.html
[1]: http://web.archive.org/web/20110427023154/http:/kerneltrap.org/node/14148
[2]: https://unix.stackexchange.com/questions/93960/why-is-linuxs-filesystem-designed-as-a-single-directory-tree/
[3]: https://unix.stackexchange.com/questions/265620/on-unix-systems-why-do-we-have-to-explicitly-open-and-close-files-to-be/265621

# Everything is a file
The whole point with "everything is a file" is not that you have some
random filename (indeed, sockets and pipes show that "file" and "filename"
have nothing to do with each other), but the fact that you can use common
tools to operate on different things. - [Linus][0]

# File operations
[On Unix systems, why do we have to explicitly `open()` and `close()` files to be able to `read()` or `write()` them?][3]

# open()
最烦, 看什么系统调用参数了.
那一大堆很少用到的组合还有undefined, 太扯淡了.
也说不上碎碎的问题, 要理解着来, 看man手册, 贴子显然不是
最后的注意还是看实现吧.
build_open_flags()还是看不出
man里面有一句
 The (undefined) effect of O_RDONLY | O_TRUNC varies among implementations.  
On many systems the file is actually truncated.

#Hacks
I suddenly find that a good way to understand fs in kernel is to manupulate a small and complete fs, like ramfs or tmpfs.

#VFS
##Common concepts
* VFS:Common fs interface plus fs anstraction layer! 
## Unix fs-related abstractions: file, directory entries, inodes, and mount points.
* Filesytem: a hierarchial storage of data adhering to a specific structure.
In unix, fs are mounted at a specific mount point in global hierarchy known as a namespace.
* file: and ordered string of bytes.
* directory: analogous to a folder and usally contains related files.
* dentry: Each component of a path is called a directory entry.
* inode: Unix separate the concept of a file from any associated infomation(file metadata) inode!
* supreblock: a data structure containing information about the filesystem as a whole.

## VFS primary objects: superblock, inode, dentry, file.
* supreblock: a specific mounted filesystem.
* inode: unit for fs. all the information needed by the kernel to manipulate a file or directory. 
* dentry: accelerate access fs, dcache! a single component of a path, certainly include *regular* file.
VFS treats directory as non-exist *normal* file. directory and dentry is not the same at all, different congitive level!
In VFS, file, dentry, and inode all represents dir and file and others, in others words struct file can stand for a dir!
* file: an open normal file as associated with a process.

##Step of how to use ext2 fs
* Register a fs type in kernel.
struct file_system_type: basic fs driver codes!

* mkfs.ext2
* shell mount -> sys_mount -> file_system_type->mount => root dentry and dentry->d_sb!
* sys_open -> 
    path_init()
    1. get start point current->fs{root, pwd}
    2. link_path_walk() to the parent dentry of last entry vim in /sbin/vim. deal with diff mounted fs(ext2 and windows ntfs)
    do_last()
    1. dentry,look_dcache(), if not in dcache, d_alloc(); d_op = sb->s_dentry_op
    2. file, if not open, file->f_op = inode->i_fop
* sys_write ->
    file->f_op->write()...

##How make dentry
d_alloc() alloc new dentry
parent_entry->d_inode->i_op->lookup=ext2_lookup()-> ext2_iget() -> use raw_inode make inode->
set inode to dentry

#Procfs
##How many parts does procfs has?
* process-specific read only /proc/<pid>/
* process tuneable interface. /proc/<pid>/
* kernel sysctl, importanta kernel tuneable files. /proc/sys/kernel net vm fs...
* Read only kernel infomation. /proc
##I think, important directory need to know.
/proc/sys/kernel


# Sysfs
sysfs is strongly depend on driver module, just rmmod tg3 then /sys/class/net/enp9s0 went away!

# Ramfs
fs/ramfs

##reference
[Overview of RAMFS and TMPFS on Linux](http://www.thegeekstuff.com/2008/11/overview-of-ramfs-and-tmpfs-on-linux/)

#tmpfs
mm/shmem.c
[关于 tmpfs](http://wangcong.org/2012/02/17/-e5-85-b3-e4-ba-8e-tmpfs/)

#rootfs
## onset rootfs
mnt_init->init_rootfs
## mount rootfs
rootfs_mount
mount_fs
vfs_kern_mount
init_mount_tree
mnt_init
vfs_caches_init
start_kernel
x86_64_start_reservations
x86_64_start_kernel
## populate rootfs
do_basic_setup->rootfs_initcall(populate_rootfs);

#initramfs
init/initramfs.c

initrd->initramfs
{
	linus' ramfs -> /tmpfs
}

##Difference with initrd
initrd is image with specific fs type, like ext2, need driver built-in kernel.
initramfs is a cpio, like tar only simpler, populated to rootfs in kernel, with fs type rootfs

##request hard drive driver maybe fs driver
ata_host_register->ata_scsi_scan_host->__scsi_add_device->scsi_probe_and_add_lun ->scsi_add_lun

subsys_initcall(genhd_device_init);->kobj_map_init{bdev_map.probe.get = base_probe}

subsys_initcall(init_scsi);4->scsi_sysfs_register{autoprobe = 1;}
rootfs_initcall(populate_rootfs);->{unpack_to_rootfs; 解压initramfs到rootfs}
module_initinit_sd;6->scsi_register_driver ->driver_register->bus_add_driver ->driver_attach ->driver_probe_device-> drv->probe(dev)=sd_probe_async->add_disk -> register_disk -> get_gendisk -> kobj_lookup { bdev_map.probe.get()=base_probe(){request_module}}

##mount fs
kernel_init_freeable-> if /init in initramfs 可以访问 根文件系统挂在和chroot交给/init也就是systemd
如果/init不可以访问 prepare_namespace{挂在真正的文件系统}


# FAQ
## forbid ln
hard link to directory, recursive
hard link to file accross partitions, may led confilict with inode number.

## stick bit
chmod +t
1777
只有owner 和root才能删除这个文件, 用于/tmp

## time of file
### atime
perhaps the most stupid Unix design idea of all times," adding: "[T]hink about this a bit: 'For every file that is read from the disk, lets do a ... write to the disk! And, for every file that is already cached and which we read from the cache ... do a write to the disk!'" He further emphasized the performance impact thus:
#### find relatime options
cat /proc/mounts
man mount 

##mount root device
root= name_to_dev_t, mount_root in prepare_namespace
如果/init不能 sys_access, 则prepare_namespace,切换到真正的root=指定的设备上设备在sd_probe上初始化了.
systemd负责挂在文件系统, 切换.

subsys_initcall 4 genhd_device_init with base_probe{ request_module()}
module_init 6 ->init_sd->sync_schedule_domain(sd_probe_async