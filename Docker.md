Originally LXC-based, widespread. Created for shipping and running applications. Advocates running one process per container.

## Vocabulary
| term | meaning |
|-|-|
| image | Product of `docker build` command. Snapshot of a container. Analogous to the concept from _Object Oriented Programming_: `class` |
| container | Running instance of image. Analogous to the concept from _Object Oriented Programming_: `class` instance |

## Configuration
`dockerd` uses following configuration files:

`/etc/docker/daemon.json`

`/etc/default/docker` (for Debian based OSes)

### Change storage-driver:
```
> cat /etc/docker/daemon.json
{
  "storage-driver": "btrfs"
}
```
For full list of supported drivers refer to [storage drivers](https://docs.docker.com/engine/userguide/storagedriver/selectadriver/)

### Change dockerd sockets:
```
> cat /etc/docker/daemon.json
{
        "hosts": ["unix:///var/run/docker.sock", "tcp://0.0.0.0:2376"]
}
```
It is possible that run init/upstart/service script specifies `-H` flag for startup command.  
This must be removed from script itself, otherwise `dockerd` will fail to start

## Usage
Build image from `Dockerfile` (issue in directory containing `Dockerfile`):  
`docker build -t my-tag .`

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

### Debugging
Failed container can be pretty easily debugged
1. Grab exited container ID  
`docker ps -a`
2. Create image  
`docker commit <container id>`
3. Run image corresponding to failed container  
`docker run -it --entrypoint /bin/bash <image id>`

# References
 1. https://docs.docker.com/develop/develop-images/dockerfile_best-practices/#label