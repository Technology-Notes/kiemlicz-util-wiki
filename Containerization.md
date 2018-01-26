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
Originally LXC-based, widespread.
### Vocabulary
| term | meaning |
|-|-|
| image |  |
| container |  |

### Configuration
`dockerd` uses following configuration files:

`/etc/docker/daemon.json`

`/etc/default/docker` (for Debian based OSes)

#### Change storage-driver:
```
> cat /etc/docker/daemon.json
{
  "storage-driver": "btrfs"
}
```
For full list of supported drivers refer to [storage drivers](https://docs.docker.com/engine/userguide/storagedriver/selectadriver/)

#### Change dockerd sockets:
```
> cat /etc/docker/daemon.json
{
        "hosts": ["unix:///var/run/docker.sock", "tcp://0.0.0.0:2376"]
}
```
It is possible that run init/upstart/service script specifies `-H` flag for startup command.  
This must be removed from script itself, otherwise `dockerd` will fail to start

### Usage
Push image:
1. Tag image first with repository URL  
`docker tag <image> <repourl>/<tag>` 
2. Login to desired repository, `docker.io` is the default  
`docker login <repourl>` 
3. `docker push <repourl>/<tag>`

# References
 1. Modern Linux Administration - Sam R. Alapati
 2. https://wiki.debian.org/LXC
 3. https://www.stgraber.org/2013/12/20/lxc-1-0-blog-post-series/
 4. https://www.flockport.com/guides/