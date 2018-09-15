# Containerization
Both the host and container share **the same kernel**.  
Engine responsible for providing the execution environment for application is called _virtualization container_. 

# Solutions

## Docker
Originally LXC-based, widespread. Created for shipping and running applications. Advocates running one process per container.

### Vocabulary
| term | meaning |
|-|-|
| image | Product of `docker build` command. Snapshot of a container. Analogous to the concept from _Object Oriented Programming_: `class` |
| container | Running instance of image. Analogous to the concept from _Object Oriented Programming_: `class` instance |

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
Build image from `Dockerfile` (issue in directory containing `Dockerfile`):  
`docker build .`

Start container (simplest form):  
`docker run --name <some_name> <image tag or name> [args]`

Stop container:  
`docker stop <container name or id>`

Remove container:  
`docker rm <container name or id>`

Remove image:  
`docker rmi <image name or id>`

Push image to registry:
1. Tag image first with repository URL  
`docker tag <image> <repourl>/<tag>` 
2. Login to desired repository, `docker.io` is the default  
`docker login <repourl>` 
3. `docker push <repourl>/<tag>`

#### Debugging
Failed container can be pretty easily debugged
1. Grab exited container ID  
`docker ps -a`
2. Create image  
`docker commit <container id>`
3. Run image corresponding to failed container  
`docker run -it --entrypoint /bin/bash <image id>`

## LXC
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
 1. Modern Linux Administration - Sam R. Alapati
 2. https://wiki.debian.org/LXC
 3. https://www.stgraber.org/2013/12/20/lxc-1-0-blog-post-series/
 4. https://www.flockport.com/guides/
 5. https://wiki.debian.org/LXC/SimpleBridge
 6. http://man7.org/linux/man-pages/man5/lxc.container.conf.5.html
 7. https://wiki.debian.org/BridgeNetworkConnections