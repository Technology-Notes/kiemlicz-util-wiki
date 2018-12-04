Agent daemon that runs on the remote (managed) server  
Receives commands from the Salt Master and responds with the results  
Runs on top of the OS (Windows, Linux, Mac)  

## Connection
Minion generates keypair
Sends public key
Waits for Master to accept
Master generates symmetric key, establishes secure connection
Minion sends grains

## Targeting
