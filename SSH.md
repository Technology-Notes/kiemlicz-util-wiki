#Basics
#Implementations
##OpenSSH
Client configurations sourcing order (first obtained value is used):

1. User options 
2. `~/.ssh/config`
3. `/etc/ssh/ssh_config`

Debian-based OSes send `LANG` and `LC_*` variables over SSH (this is implemetation feature). If the server is configured to receive them (`AcceptEnv` option in _OpenSSH_) then locale must exist on the server-side (must be generated).