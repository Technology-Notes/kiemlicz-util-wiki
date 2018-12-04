Salt accomodates multiple configurable transports (communication between master - minions):
 - TCP
 - ZeroMQ (default)
 - RAET

## Architecture (ZeroMQ transport)
There is no bi-directional communication using one channel only.  
Master communicates with minions via Pub/Sub channel - this is broadcast type. Thus every
minion receives the master requests (the filtering happens on the minion)
Master computes the number of expected replies (if using wildcards or computation is impossible, master will always assume all cached minions could reply)

Minions sends their requests and replies via direct channel, this channel is private for master-minion pair.

##