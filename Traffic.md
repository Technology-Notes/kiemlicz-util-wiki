# Basics
Normally packets received must have destination MAC address equal to NIC's MAC address.
Exceptions:
 1. Broadcast destination: `0xFFFFFFFFFFFF`
 2. Multicast destination: for IPv4 `0x01.....`, for IPv6 `0x3333....`

# Tcpdump
Tool based on _libpcap_ for packet capture.
Traffic dumping takes place at specific points in time:
* Incoming traffic: `wire -> NIC -> tcpdump -> netfilter/iptables -> application`
* Outgoing traffic: `application -> iptables -> tcpdump -> NIC -> wire`

## Dumping traffic with MAC of NIC
Dump local traffic using tcpdump:  
`tcpdump -i eth0 -w /tmp/outfile.pcap host 1.1.1.1`

Dump traffic on remote (eth0) host and visualize it locally with wireshark:  
```
mkfifo /tmp/dump
ssh user@remote "sudo tcpdump -s0 -U -n -w - -i eth0 'not port 22'" > /tmp/dump
wireshark -k -i <(cat /tmp/dump)
```

## Dumping any traffic aka. sniffing

# References
1. https://www.wains.be/pub/networking/tcpdump_advanced_filters.txt