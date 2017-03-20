# Containerization
Both the host and container share **the same kernel**.  
Engine responsible for providing the execution environment for application is called _virtualization container_. 

# Solutions
## LXC
Container's filesystem root location: `/var/lib/lxc/<container name>`  
Container creation scripts/templates for given distro: `/usr/share/lxc/templates`

To verify if LXC is supported on current OS/kernel run: `lxc-checkconfig`

Debian may have some issues with memory control via cgroups.  
Check kernel support: `cat /boot/config-$(uname -r) | grep CONFIG_MEMCG`  
If output contains both `CONFIG_MEMCG=y` and `CONFIG_MEMCG_DISABLED=y` means that memory cgroups must be explicitly enabled by kernel parameter (`cgroup_enable=memory`)

### Networking
Bridge network on host OS.

## Docker
LXC-based

# References
 1. Modern Linux Administration - Sam R. Alapati
 2. https://wiki.debian.org/LXC
 3. https://www.stgraber.org/2013/12/20/lxc-1-0-blog-post-series/
 4. https://www.flockport.com/guides/