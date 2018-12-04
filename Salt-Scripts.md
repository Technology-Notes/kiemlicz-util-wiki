Means of interaction with Salt

#### salt

`salt '*' test.ping`  
salt runs on master, accepts the user command, applies them via salt-master process

`(salt -> salt-master)    --transport-->    salt-minions`

#### salt-call

salt-call runs the execution modules directly, doesn't require running salt-minion at all, unless `--local` option is passed it still requires salt-master connection

#### salt-run

salt-run runs on the master, talks to the salt-master process, allows to run `runner` modules

#### salt-ssh

Doesn't use salt-minion process at all, requires only SSH daemon running and python binaries on the remote minion. It is much slower than using salt-minion

#### salt-cloud

#### salt-api

Runs on the salt-master, exposes multiple connectors and ACL

#### salt-proxy

Pretends to be salt-minion, allows provisioning of the devices that cannot run salt-minion or cannot be connected via SSH

#### salt-syndic

Runs on multiple salt-masters and proxies traffic from multiple masters that it runs on to one desired (uber) salt-master 

#### salt-key

Runs on salt-master, manages minion keys

#### salt-cp

Copy files from salt-master to salt-minion (other way around as well)

#### spm

Salt Package Manager, standarization over multiple salt formulas.

#### salt-extend

Boilerplate generator

#### salt-unity

