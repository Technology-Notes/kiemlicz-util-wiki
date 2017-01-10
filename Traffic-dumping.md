#Basics
Normally packets received must have destination MAC address equal to NIC's MAC address.
Exceptions:
 1. Broadcast destination: `0xFFFFFFFFFFFF`
 2. Multicast destination: for IPv4 `0x01.....`, for IPv6 `0x3333....`

#Tcpdump
Tool based on _libpcap_ for packet capture
When does the traffic dumping actually take place:
* Incoming traffic: `wire -> NIC -> tcpdump -> netfilter/iptables -> application`
* Outgoing traffic: `application -> iptables -> tcpdump -> NIC -> wire`
##Dumping traffic with MAC of NIC

##Dumping any traffic aka. sniffing