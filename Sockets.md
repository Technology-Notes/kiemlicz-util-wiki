# Syscalls
| syscall | info |
|-|-|
|socket(domain, type, protocol)|returns socket file descriptor (fd)|
|bind(fd, *addr, addrlen)|bind to address, returns error code|
|listen(fd, backlog)|mark socket as passive (this is: as a socket accepting connections), backlog determines the maximum length to which the queue of pending connections for fd may grow|

# TCP socket states
![](https://upload.wikimedia.org/wikipedia/commons/thumb/a/a2/Tcp_state_diagram_fixed.svg/796px-Tcp_state_diagram_fixed.svg.png)

**Backlog** states how many connections can be queued before handing over (via _accept_ call) to the application. TCP three-way handshake has SYN RECEIVED (SYN received and SYN+ACK sent) intermediate state, which transits to ESTABLISHED after receiving the ACK from client. Thus backlog can be implemented in two ways:

1. The implementation uses a single queue, the size of which is determined by the backlog argument of the _listen_ syscall. When a SYN packet is received, it sends back a SYN/ACK packet and adds the connection to the queue. When the corresponding ACK is received, the connection changes its state to ESTABLISHED and becomes eligible for handover to the application. This means that the queue can contain connections in two different states: SYN RECEIVED and ESTABLISHED. Only connections in the latter state can be returned to the application by the _accept_ syscall. In this approach if the queue is full then any consecutive three-way handshake will be unsuccessful (client syn will be dropped). Not used in linux 
2. The implementation uses two queues, a SYN queue (or incomplete connection queue) and an accept queue (or complete connection queue). Connections in state SYN RECEIVED are added to the SYN queue and later moved to the accept queue when their state changes to ESTABLISHED, i.e. when the ACK packet in the 3-way handshake is received. As the name implies, the _accept_ call is then implemented simply to consume connections from the accept queue. In this case, the backlog argument of the listen syscall determines the size of the accept queue (second queue). Linux default.  
To set the max length of incomplete queue (first queue) use: `/proc/sys/net/ipv4/tcp_max_syn_backlog`. What happens when accept queue is full and client ACK arrives? Depends on `/proc/sys/net/ipv4/tcp_abort_on_overflow` it may clean the incomplete connection (drop)


# References
1. http://veithen.github.io/2014/01/01/how-tcp-backlog-works-in-linux.html
2. https://github.com/torvalds/linux/blob/master/net/ipv4/tcp_ipv4.c