Means of interaction with Salt

#### salt

`salt '*' test.ping`  
salt runs on master, accepts the user command, applies them via salt-master process

`(salt -> salt-master)    --transport-->    salt-minions`

#### salt-call

salt-call runs the execution modules directly, doesn't require running salt-minion at all, unless `--local` option is passed it still requires salt-master connection

#### salt-run
