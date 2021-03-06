---
tags: [ cs ] 
title: System booting
date: 2015-11-12
category: cs
---

# Reference
Documentation/x86/boot.txt
[Introducing initramfs, a new model for initial RAM disks](http://linuxdevices.linuxgizmos.com/introducing-initramfs-a-new-model-for-initial-ram-disks-a/)
[0]: http://duartes.org/gustavo/blog/post/kernel-boot-process/

[The Kernel Boot Process][0]

# kernel boot process
arch/x86/boot/header.S::start_of_setup
arch/x86/boot/main.c::main()
	arch/x86/boot/memory.c::detect_memory()
	arch/x86/boot/memory.c::detect_memory_e820() = boot_params.e820_entries
	...
	arch/x86/boot/pm.c::go_to_protected_mode()
arch/x86/boot/pmjump.S::protected_mode_jump
arch/x86/kernel/compressed/head_64.S::startup_32
arch/x86/kernel/compressed/head_64.S::startup_64
arch/x86/kernel/head_64.S::startup_64
kernel/main.c::start_kernel()
	...
	arch/x86/kernel/setup_64.c::setup_arch()
		...
		arch/x86/kernel/setup_64.c::memory_setup()
		arch/x86/kernel/e820_64.c::machine_specific_memory_setup()
		arch/x86/kernel/e820_64.c::sanitize_e820_map(boot_params.e820_map, &boot_params.e820_entries)
		...
		arch/x86/kernel/e820_64.c::finish_e820_parsing()
		...
		arch/x86/kernel/e820_64.c::e820_register_active_regions()
		...
		arch/x86/kernel/acpi/boot.c::acpi_boot_table_init()
		arch/x86/kernel/pci-dma.c::dma32_reserve_bootmem()
		arch/x86/kernel/acpi/sleep.c::acpi_reserve_bootmem()
		arch/x86/kernel/efi_64.c::efi_reserve_bootmem()
		...
		arch/x86/kernel/acpi/boot.c::acpi_boot_init()
		...
		arch/x86/kernel/e820_64.c::e820_reserve_resources()
		arch/x86/kernel/e820_64.c::e820_mark_nosave_regions()
		arch/x86/kernel/e820_64.c::e820_setup_gap()
		...
	}
	drivers/acpi/bus.c::acpi_early_init()
	kernel/main.c::rest_init()
		arch/x86/kernel/process_32.c::kernel_thread(kernel_init, NULL, CLONE_FS | CLONE_SIGHAND);
} 
kernel/main.c::kernel_init()
 ...
 do_basic_setup()
  ...
  drivers/base/init.c::driver_init()
  ...
  init/main.c::do_initcalls()
 }
 ...
 init/main.c::init_post()
}
# Reloctaion for decompress
974f221c84b05b1dc2f5ea50dc16d2a9d1e95eda
x86/boot: Move compressed kernel to the end of the decompression buffer

# Realmode
The first 640KB RAM 
cat /proc/iomem 
00000000-00000fff : reserved
00001000-000913ff : System RAM
00091400-0009ffff : reserved
000a0000-000bffff : PCI Bus 0000:00
000c0000-000cfdff : Video ROM
000d0000-000d99ff : Adapter ROM
000da000-000dafff : Adapter ROM
000e0000-000fffff : reserved
  000f0000-000fffff : System ROM
00100000-bcb01fff : System RAM   # 1MB; Bootloader uses real_to_prot and prot_to_real to load the kernel image to this place.
  01000000-0183d36c : Kernel code # That's why the kernel was called big kernel.
  0183d36d-01f42d3f : Kernel data
  020c1000-02209fff : Kernel bss
# Horrable commit description on phys_base
commit 1ab60e0f72f71ec54831e525a3e1154f1c092408
Author: Vivek Goyal <vgoyal@in.ibm.com>
Date:   Wed May 2 19:27:07 2007 +0200

    [PATCH] x86-64: Relocatable Kernel Support


# Normalize %cs
程序的起始地址在链接脚本中被设置为 0，如果 setup 被加载到其他地方(起始地
址不为 0 的地方)，那么指令里面访问的全局符号都会有重定位的问题。由于 Boot Loader
跳转到上面这段代码的时候，把 DS 设置为 setup 加载的基地址，而第 17 行访问_end 默认
是用数据段寄存器 DS 的，所以不会有重定位的问题。但是在 38 行转移指令用的 CS 寄存器，
而符号 main 的地址是在链接期决定的，现在加载的基地址改变了，那肯定会出问题了。所
以事先在第 24 行把 CS 设置为和 DS 一样，这样就解决了重定位的问题了。代码的最后跳转
到了 arch/x86/boot/main.c 中的 main 函数中继续执行。

# print with int $0x10
https://stackoverflow.com/questions/38041478/int-10h-not-working-in-qemu

# A20
[Why enable A20 line in Protected Mode?][https://stackoverflow.com/questions/33827474/why-enable-a20-line-in-protected-mode]
This gate, (the A20 gate), is controlled by a GPIO pin on the keyboard controller IC. Thus, you need to enable it before going into protected mode. If you didn't, (and say you flat-mapped all 4GB of physical memory), then as Micheal Petch indicated, "every odd numbered megabyte region will be inaccessible. So 1mb-2mb will actually reference 0-1mb, 3mb-4mb will reference 2mb-3mb etc." See also:

# Contents
What basic initializations should be perfomed when power on?
How do we load the kernel to memory?
What does kernel do when kernel boot.
MBR vs BIOS?
PC vs embedded
Kernel init 
before start_kernel
start_kernel
reset_init & kernel_init

# Boot and init
Power button -> cpu reset -> BIOS -> hard drive -> Grub boot.S/MBR aa5a -> Grub diskboot.S -> ... -> The kernel real-mode setup code. _start of arch/x86/boot/header.S
Aligh register, Stack and BSS for C function to run. -> main->startup_32->startup_64-> __START_KERNEL_map->... start_kernel
[Kernel legacy boot sector](https://github.com/torvalds/linux/blob/master/Documentation/x86/boot.txt#L130) start form 4d5a of arch/x86/boot/header.S 
is only used by something link 'qemu-system-x86_64 vmlinuz-3.18-generic'. It's obsoleted that is what legacy means.


# p4080 Board
PBL 
1. initialize I2C, SPI, eLBC, eSDHC.
2. Load RCW and boot init commands from above.
3. write date to conf register and memory
# config
CCSRBAR -> CCSR 
default is 0x0_FE00_0000  
CCSRBAR is in CCSR memory. odd. stored in 0(CCSRBAR).
CCSRBAR always points to itself.
holdoff
# u-boot
ft_fixup_cpu -> determine_mp_bootpg(NULL) & fdt_add_mem_rsv

main_loop-> ...->./common/bootm_os.c:410:... do_bootm_states-> 
{
bootm_find_other->bootm_find_ramdisk_fdt->bootm_find_fdt->boot_get_fdt&set_working_fdt_addr(0x0200000)->IMAGE_FORMAT_FIT->
&
boot_fn = bootm_os_get_boot_func(images->os.os)=[IH_OS_LINUX] = 
&
boot_selected_os->boot_fn=do_bootm_linux->boot_body_linux->image_setup_linux->image_setup_libfdt->
board/freescale/corenet_ds/corenet_ds.c ft_board_setup-> ft_cpu_setup->
}

# PPC multicore booting
* There are two possibilites to make secondary cores booting failed.
1. There is a bug in the code executed by Secondary cores
2. The spin table address, passed to croe0, is not correct, so we did not really kick the secondary cores.

CCSRBAR
MPC86xx_MCM_OFFSET
srio_boot_master_release_slave

uboot->
set the cpu-release-addr at funciton ft_fixup_cpu(), I am not sure.

core0 run, 2nd cores holdoff
core0 set BPTR and kick(BRR) 2nd cores, 2nd cores spintable loop in uboot function setup_mp
core0 -> arch/powerpc/cpu/mpc85xx/start.S->_start_e500
board_init_r->cpu_init_r-> setup_mp-> {
__bootpg_addr = (u32)virt_to_phys(&__second_half_boot_page); //Assembly code for put core to spin tablea. Used by __secondary_start_page
__spin_table_addr = (u32)get_spin_phys_addr();// used by __second_half_boot_page 
bootpg = BPTR = __secondary_start_page
plat_mp_up->kick BRR & Boot Space Translation
}
2nd cores holdoff -> __secondary_start_page
kernel->
core0 boot kernel, core0 kickoff 2nd cores spintable, triger 2nd core's spin table by writing  the spin table field with the desired address
2nd cores ePAPR->spintable->addr_l=__early_start->__secondary_start->smp_ops->setup_cpu->cpu_idle
arch/powerpc/kernel/head_fsl_booke.S
U-boot logs:
Reserving MP boot page to 7ffff000^M^M - --   -   MP spin table
address.
SPAG : fdt_fixup_fman_firmware, 567^M^M
^M^M
..r.^M^M
VDBG : ft_cpu_setup, 737, comment fdt_fixup_memory ^M^M
VDBG: Secondary cores are not held in reset.^M^M
Kernel Logs:
smp_85xx_kick_cpu: timeout waiting for core 1 to ack^M^M
smp: failed starting cpu 1 (rc -2)^M^M
smp_85xx_kick_cpu: timeout waiting for core 2 to ack^M^M
smp: failed starting cpu 2 (rc -2)^M^M
smp_85xx_kick_cpu: timeout waiting for core 3 to ack^M^M
smp: failed starting cpu 3 (rc -2)^M^M
Brought up 1 CPUs^M^M
devtmpfs: initialized^M^M

Note that u-boot can't detect  memory above 2G and we need to check
if kernel is accessing at high mem of 2G or 4G.


