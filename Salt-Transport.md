Salt accommodates multiple configurable transports (communication between master - minions):
 - TCP
 - ZeroMQ (default)
 - RAET (Rapid Asynchronous Event Transport)

## Architecture (ZeroMQ transport)
There is no bi-directional communication using one channel only
  
Master communicates with minions via _Pub/Sub bus_ - this is broadcast type. 
Thus every minion receives the master requests (the filtering happens on the minion)
Master computes the number of expected replies (if using wildcards or computation is impossible, master will always assume all cached minions could reply)

Minions sends their requests and replies via _Direct bus_, this channel is private for master-minion pair.

##