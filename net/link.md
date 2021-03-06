---
tags: [ net ] 
title: Data link layer
date: 2015-02-27T15:46:13+08:00 
category: net
---

[1]: https://greenhost.nl/2013/04/10/multi-queue-network-interfaces-with-smp-on-linux/
# Multi-queue
[Multi-queue network interfaces with SMP on Linux][1]
##Common concepts
* The link layer 
is the group of methods and communications protocols that only operate on the link that a host is physically connected to. 

* The link 
is the physical and logical network component used to interconnect hosts or nodes in the network 

* a link protocol 
is a suite of methods and standards that operate only between adjacent network nodes of a local area network segment 
or a wide area network connection.

* MTU
This limits the number of bytes of data to 1500(Ethernet II) and 1492(IEEE 802), respectively. 
This characteristic of the link layer is called the MTU, its maximum transmission unit.

* PMTU
/proc/sys/net/ipv4/ip_no_pmtu_disc
0 enable, 1 disable

cat /proc/sys/net/core/warnings

/proc/sys/net/ipv4/tcp_mtu_probing
!0 enable tcp_mtu_probing()
If you are using Jumbo Frames, we recommend setting tcp_mtu_probing = 1 to 
help avoid the problem of MTU black holes. Setting it to 2 sometimes causes performance problems.

net/ipv4/icmp.c
icmp_unreach(
type 3, code 4
icmph->type == ICMP_DEST_UNREACH //3
case ICMP_FRAG_NEEDED //4
icmp_err,

## Frame 
[Ethernet Frame](http://www.infocellar.com/networks/ethernet/frame.htm)
+ 一种不太确定的非严格的真实划分
TCP/IP -> Ethenet II frame
IPX/APPLETALK -> 802.3/LLC(802.2), SNAP, mac 发来的包走这条路.
* jumbo frame?

#net_device
link -> net_device -> if
driver -> manipulate dev->state through netif_*_on/off -> dev->flags
+rfc2863
+ [operational state](https://www.kernel.org/doc/Documentation/networking/operstates.txt)
+ [Monitoring Interface Administrative State and Physical State on Cumulus Linux](https://support.cumulusnetworks.com/hc/en-us/articles/202693826-Monitoring-Interface-Administrative-State-and-Physical-State-on-Cumulus-Linux)
* dev->operstate
admin state is if flag
operate state is link_state
Administrative state is the result of "ip link set dev
<dev> up or down" and reflects whether the administrator wants to use
the device for traffic.
enp9s0: <NO-CARRIER,BROADCAST,MULTICAST,UP> mtu 1500 qdisc mq state DOWN mode DEFAULT group default qlen 1000
operate is DOWN, amdin is UP>
 Operational state
shows the ability of an interface to transmit this user data.
    "UNKNOWN", "NOTPRESENT", "DOWN", "LOWERLAYERDOWN",
    "TESTING", "DORMANT",    "UP" 
IF_OPER_UNKNOWN,
see rfc2863

* dev->link_mode 
IF_LINK_MODE_DORMANT wifi
IF_LINK_MODE_DEFAULT wire
对应dev->operstate in 转化方法rfc2863_policy()

* dev->state
__LINK_STATE_START,     这是内核自身的标记位__dev_open init_dummy_netdev __dev_close_many      
__LINK_STATE_PRESENT,         也是内核自己的, 用的比 START早
__LINK_STATE_NOCARRIER,        
__LINK_STATE_LINKWATCH_PENDING, 也是辅助状态不明, nocarrier和dormant都可接收的
__LINK_STATE_DORMANT

* dev->flags dev_get_flags()
+ if flags form man netdevice or or kernel src codes
/sys/class/net/<dev>/flags
              IFF_UP            Interface is running.
              IFF_BROADCAST     Valid broadcast address set.
              IFF_DEBUG         Internal debugging flag.
              IFF_LOOPBACK      Interface is a loopback interface.
              IFF_POINTOPOINT   Interface is a point-to-point link.
              IFF_RUNNING       Resources allocated.
              IFF_NOARP         No arp protocol, L2 destination address not set.
              IFF_PROMISC       Interface is in promiscuous mode.
              IFF_NOTRAILERS    Avoid use of trailers.
              IFF_ALLMULTI      Receive all multicast packets.
              IFF_MASTER        Master of a load balancing bundle.
              IFF_SLAVE         Slave of a load balancing bundle.

              IFF_MULTICAST     Supports multicast
              IFF_PORTSEL       Is able to select media type via ifmap.
              IFF_AUTOMEDIA     Auto media selection active.
              IFF_DYNAMIC       The addresses are lost when the interface goes
                                down.
              IFF_LOWER_UP      Driver signals L1 up (since Linux 2.6.17)
              IFF_DORMANT       Driver signals dormant (since Linux 2.6.17)
              IFF_ECHO          Echo sent packets (since Linux 2.6.25)

* netdev_queue->state 
[Understand __QUEUE_STATE_FROZEN](http://thread.gmane.org/gmane.linux.kernel/709444/focus=714632)
    __QUEUE_STATE_DRV_XOFF,     netif_tx_stop_queue
    __QUEUE_STATE_STACK_XOFF,    
    __QUEUE_STATE_FROZEN,
可以确定这个frozen这个标志位就是为了dev_watchdog服务, 从所有内核态代码调用的位置得出的.

* dev_watchdog,

#Neighbor 
* ip_output_finish2 -> __neigh_create -> tbl->constructor -> arp_constructor{
if !dev->header_ops   //slip is the case, see sl_setup
	neigh->ops = &arp_direct_ops
	neigh->output = neigh_direct_output
else if ARPHRD_ROSE/AX25/NETROM
	arp_broken_ops
	neigh->ops->output
else if dev->header_ops->cache
	neigh->ops = &arp_hh_ops
else
	arp_generic_ops
                                                          
if (neigh->nud_state & NUD_VALID)                  
	neigh->output = neigh->ops->connected_output;   
else                                  
	neigh->output = neigh->ops->output;
}

* ip_output_finish2 -> dst_neigh_output -> neigh_resolve_output

*  ipv4 Neighbor output instance of ethernet
see alloc_etherdev_mqs-> ether_setup{
dev->header_ops = &eth_header_ops;
dev->type       = ARPHRD_ETHER;
eth_header_ops.cache = eth_header_cache
}
so neigh->ops = &arp_hh_ops; neigh->output = neigh_resolve_output in arp_hh_ops

	//tg3_init_one
	dev->netdev_ops = &tg3_netdev_ops;
	dev->ethtool_ops = &tg3_ethtool_ops;
	dev->watchdog_timeo = TG3_TX_TIMEOUT;

	//In ppp
static void ppp_setup(struct nethernetet_device *dev) 
{                                           
    dev->netdev_ops = &ppp_netdev_ops;       
    dev->hard_header_len = PPP_HDRLEN;        
    dev->mtu = PPP_MRU;
    dev->addr_len = 0;  
    dev->tx_queue_len = 3
    dev->type = ARPHRD_PPP
}

#PPP SLIP

#Data Framing
dst_neigh_output->dev_hard_header ->  eth_header

#TC Qdisc
##Bibliography
http://tldp.org/HOWTO/Traffic-Control-HOWTO/intro.html
lartc.rog
http://ace-host.stuart.id.au/russell/files/tc/

##Common concepts
Shaping: Shapers delay packets to meet a desired rate.
Scheduling: Schedulers arrange and/or rearrange packets for output.
Classifying: Classifiers sort or separate traffic into queues.
Policing: Policers measure and limit traffic in a particular queue.
Dropping: Dropping discards an entire packet, flow or classification.
Marking: Marking is a mechanism by which the packet is altered.

##Add new qdisc
RTM_NEWQDISC -> tc_modify_qdisc

##The execution of u32 tc rule
### user space tc qidsc add 
 u32_parse_opt 
{
	-> parse_selector ->...-> parse_ip
	struct nlmsghdr *n
	rta = NLMSG_TAIL(n)
	rta->type = TCA_U32_SEL

}
### kernel space
NETLINK_ROUTE -> RTM_NEWTFILTER: see tc_filter_init -> tc_ctl_tfilter->(tp->ops->change = u32_change in net/sched/cls_u32.c) 
tcf_exts_validate: init police and action of this shel tc command, 
put sel in tc_u_knode;
tc_u_knode insert in tc_u_hnode
root is tcf_proto 入殓 by prior.
tcf_proto -> tc_u_hnode -> tc_u_knode -> sel
也就是用户太的selector没变存到内核中了.
enqueue -> filter_list ->u32->classify() this classify is implement by u32!
tcf_proto_ops->.kind = "u32", .classify   =   u32_classify,
police and action invoke in tcf_action_exec , act register by tcf_register_action.

* TCA_U32_CLASSID in u32_set_parms
filter classid and flowid is the same meaning in russell tc doc
* TCA_KIND in filter is u32...register_tcf_proto_ops

##FAQ
* conflict tc qidsc del with softnet_data->softnet_data
 [PATCH] pkt_sched: Destroy gen estimators under rtnl_lock().
http://thread.gmane.org/gmane.linux.network/102444/focus=102592
After synchronize_rcu() in dev_deactivate() we are sure any qdisc_run(),
from dev_queue_xmit() or net_tx_action() can only see and lock noop_qdisc.
This was happened in dev_deactivate_many()
* difference between synchronize_net and  synchronize_rcu?
http://article.gmane.org/gmane.linux.network/196309/match=net_device+dismantle
In this patch, we replace synchronize_rcu with synchronize_net().

#LLC (TCP/IP rarely use this sub layer)
* ptype MAC layer 之上, 可能是data link(llc) or network layer(ip)
定义了所有从驱动上来的packet接收函数, 这里有ip_rcv 还有pppoe_rcv,llc_rcv, NO snap_rcv
dev_add_pack
llc_rcv{snap_rcv}
netif_receive_skb ->ip/llc_rcv

# Netpoll
可以说是linker层的netfilter 更raw
netconsole就是基于他, 屌炸天.
不走协议栈, 中断完蛋了, 也能用, 纯poll.

# NAPI
[网络数据包收发流程(一)：从驱动到协议栈](http://blog.chinaunix.net/uid-24148050-id-464587.html)
New API (NAPI) is an interface to use interrupt mitigation techniques for networking devices in the Linux kernel. 
Such an approach is intended to reduce the overhead of packet receiving. 
类似机制, Add [blk-iopoll](https://lwn.net/Articles/346187/), a NAPI like approach for block devices
1. dirver: device->DMA->ring
2. IRQ: disable irq, napi schedule 
do_IRQ->handler = gfar_receive{
	disable irq
	__netif_rx_schedule
}

3. softirq:driver function, clean ring buffer, netif_receive_skb
->net_rx_action {
n->poll = gfar_poll
{
	gfar_clean_rx_ring->gfar_process_frame
	{
		skb->protocol = eth_type_trans(skb, dev);
		netif_receive_skb
	}
	enable irq

# RPS

# Driver 
* skb->protocol
+ assignment in ip_output by = htons(ETH_P_IP)
+ assignment in driver by = eth_type_trans() 

# MAC
* Addressing,LAN switching

# relations of concept
Qdisc -- NET_XMIT_SUCCESS
dev -- NETDEV_TX_OK


