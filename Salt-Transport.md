Salt accommodates multiple configurable transports (communication between master - minions):
 - TCP
 - ZeroMQ (default)
 - RAET (Rapid Asynchronous Event Transport)

## Architecture (ZeroMQ transport)
There is **no bi-directional** communication using one channel only
  
Master communicates with minions via _Pub/Sub bus_ - this is broadcast type. 
Thus **every** minion receives the master requests (the filtering happens on the minion).
Master computes the number of expected replies (if using wildcards or computation is impossible, master will always assume all cached minions could reply)
Minions sends their requests and replies via _Direct bus_, this channel is private for master-minion pair.

For example, all of the following calls are insecure:  
`salt '*' grains.set some:password 'afm4o'`,  
`salt 'minion' grains.set some:password 'afm4o'`,  
`salt 'minion' state.apply db.setup pillar='{"some": {"password": "afm4o"}}'`  

On the other hand:
`salt 'minion' saltutil.refresh_pillar` is secure.  
Minion (and possibly all other minions) will receive the request to
refresh the pillar data. However only `minion` will establish private secure channel with master, which will use to fetch it's very own private pillar data.
 