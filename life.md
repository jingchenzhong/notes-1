---
title: The life of Firo
date: 2015-02-27 15:46:11
category: firo
---

#TODO Tal
Go to a place to live comfortably, grow up. 
American kernel job & Visa
Passport 

# Cognition in philosophy

# Dressing
# Career
Working style, modular	

# philosophy
现象学分析方法?
什么是观念, 概念?
# CS
libev 变量的生存周期
# kernel
What is platform driver?

find source code of config
How to print backtrace by kernel oops, and why it's not exact?
What is .byte in oops
 3f:   31                      .byte 0x31 
 Nevermind that .byte 0x31 - it's just a truncated instruction (we have one
 starting with 31 just several lines above). 
What is abi
why get_unaligned_be16?
 _THIS_IP_ vs __FUNCTIONS__
## net
*first_device 用途？
subsys 在前, device在后.
* What is bridge?
linux bridge 
netdev_rx_handler_register(dev, br_handle_frame, p);
__netif_receive_skb -> rx_handler=br_handle_frame
and generic concept: hub, switch?
hub: layer 1, bradcast, exclusive share, 报文可被侦听.
switch: layer 2,  mac port route, CAM table in linux bridge module!
switch with vlan: layer 3, 因为vlan之间的报文转发需要路由, 所以是layer层技术.
*What is the type in ip link?
net_poll
napi
* What is Head-of-line blocking

# shopping
Hangers
Face masker
克林莱天然橡胶手套
白博士泡沫式厨房清洁剂
擦擦克林
威猛先生
长柄刷子 
淋浴喷头
抹布
盘子 盆子
 
# Linguistic
西方哲人眼中的文字观
positive psychology

# ETC
鲍勃迪伦

# Chronobiology
身体是硬件, 思想是软件.
Principle: health, effective
* Physical execise 21:30, 45 minutes
有氧和无氧隔天锻炼.
Aerobic exercise: 跑步
Anaerobic exercise: 囚徒健身
一个20天计划, 一天6艺一式, 隔天自增第二式, 以此类推.
同时联系英语听力.
* Take a bath 22:15
* Sleep at 22:30
Sleep 8 hours
躺在床上不玩手机
* Getting up at 6:30
* 伸展下筋骨去洗漱.
* Develop good eating habits.
Avoid eating junk food as much as possible.
* Drink a lot of water everyday.
* Rest 10 minutes/hour
* Maintain Mental fitness and Be happy.
Talking with family
* Writing, before 22:40, 10 minutes
Lyubishchev
Keep diary
## Skin care
* 不熬夜, 早睡, 早起!
* Fitness
* Cleansing
Daily ?
Lab series multi-action face wash
* Toning

Water, more water.
* Moisturizing
Kiehl‘s ultra facial cream!
* Exfoliants
For face, once a week.

# Physical exercise
* Belly, Abdomen			
Crunsh(Not Situps), Leg raise
1、配合有氧运动减脂，这是关键，否则仰卧起坐也不用做了，最好的减脂运动就是有氧运动，这个说来话长，不深入了；
2、每天都要对腹肌进行刺激，这样才能保证不退化；
3、放弃大强度疲劳训练；
4、腹肌训练放在每次训练的最后；
5、每次训练只要选择一个动作，做3组，每组20-30次即可，当然要达到有效地刺激；
* Chest,Pectorails		
Dumbbell fly; Bench press; Pushups 30+1/day	
* Back					
Pull-up
* Shoulder,Deltoid		
Front raise;Shoulder fly; Handstand push-up
* Arms, Biceps			
Biceps up, Chin up
* Leg											 
* Eyes
* Ears				
40 ~ 60 decibel, wear headset not beyond 3 ~ 4 hour, relax/half hour 
* Masturbation			
1 time two week 

#Diary 
plan record analysis feedback

# Daily stuff
Fix philosophy.
English
Experience in kernel hacking and cs
Sleep and physical exercises
Extend knowledge.

# 1014

# 1013
USB ULPI
USB stick -> USB OTG PHY/Transceiver->ULPI->SOC usb controller
Transceivers are called Medium Attachment Units (MAUs) in IEEE 802.3 documents 
[What is a USB transceiver?](http://electronics.stackexchange.com/questions/39381/what-is-a-usb-transceiver)
transceiver最主要的作用是将数字信号转换为模拟信号。一般的transceiver只需要正确上电就可以了，不用配置。
http://bbs.csdn.net/topics/360017200

# Components
OTG device init in MACHINE_START->init_machine   = ams_delta_init ->omap1_usb_init -> otg_device_init
OTG driver init module_platform_driver(fsl_otg_driver);->fsl_otg_probe-> conf & start->request_irq(fsl_otg_isr){ suspend & resume}
USB dr device init dr_controller_setup drivers/usb/gadget/udc/fsl_udc_core.c
USB dr driver init module_platform_driver_probe(udc_driver, fsl_udc_probe);
USB host device init
USB host driver init

USB的协议实现在usb.c,主机控制器在drivers/usb/host/文件夹实现
整个usb部分由抽象层（usb.c)，规格定义(ehci-hcd.c)和具体实现(ehci-fsl.c)来分工实现

## NB
fsl_otg_conf 类似于otg_ulpi_create



