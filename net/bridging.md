Bridging

== Sturcture ==
Bridging is a router using MAC address at L2 in essence.
A bridge transparently relays traffic between multiple network interfaces. 
In plain English this means that a bridge connects two or more physical Ethernets together to form one bigger (logical) Ethernet.


Spanning tree protocol(Rapid STP,Multiple STP)
the algorithm is not executed on a single host that later distributes the result to all the others; 
instead, this is a distributed protocol.

BPUD
Bridge protocol data units
Configuration BPDU
Used to define the loop-free topology. 
Topology Change Notification (TCN) BPDU
Used by a bridge to notify the root bridge about a detected topology change. 

BPDU Aging
On a stable network, the time depends mainly on how loaded the bridges are and how fast they can process BPDUs.


Root Bridge
The root bridge is the only bridge that generates BPDUs
The root bridge makes sure each bridge in the network comes to know about a topology change when one occurs

Designated Bridges
While each tree has only one root bridge, there is one designated bridge for each LAN, 
which becomes the bridge all hosts and bridges on the LAN use to reach the root.
The designated bridge is chosen by determining which bridge on the LAN has the lowest path cost to the root bridge.

Bridge Port
While root ports lead toward the root of the tree (i.e., the root bridge), designated ports lead toward the leaves.






Function
========
Rassive learning
Flooding
Aging
Bridging Loops



Switch:




Time-line
========
newe_bridge_dev() create net_device and net_bridge.
