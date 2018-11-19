Container's filesystem root location: `/var/lib/lxc/<container name>`  
Container creation scripts/templates for given distro: `/usr/share/lxc/templates`

To verify if LXC is supported on current OS/kernel run: `lxc-checkconfig`

Debian may have some issues with memory control via cgroups.  
Check kernel support: `cat /boot/config-$(uname -r) | grep CONFIG_MEMCG`  
If output contains both `CONFIG_MEMCG=y` and `CONFIG_MEMCG_DISABLED=y` means that memory cgroups must be explicitly enabled by kernel parameter (`cgroup_enable=memory`)

### Networking
Described in configuration file `lxc-create -f <config_file>`  
Create bridge interface on host OS and link to container:
```
lxc.network.type = veth
lxc.network.flags = up
lxc.network.link = br0

lxc.network.type = veth
lxc.network.flags = up
lxc.network.link = br1

lxc.network.type = empty
```
For "isolated" bridge interface for containers, configure `lxc-net`.  
Refer to [5](#References)

# References