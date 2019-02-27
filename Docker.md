Originally LXC-based, widespread. Created for shipping and running applications. Advocates running one process per container.

## Basics
| term | meaning |
|-|-|
| image | Packaged application along with environment. Product of `docker build` command. Snapshot of a container. Analogous to the concept from _Object Oriented Programming_: `class` |
| container | Running instance of image. Analogous to the concept from _Object Oriented Programming_: `class` instance |
| tag | Extra information appended to image name. Helpful for enclosing image version information, if omitted: `latest` is assumed |
| registry | Repository that stores Docker Images, can be public or private |

Each image is identified by ID, can contain different names and tags.  
Image name contains of slash delimited name components, with optionally repository name prefixed. If the name doesn't contain 
registry name, then the public `registry-1.docker.io` is assumed.

## Usage
Build image from `Dockerfile` (invoke in directory containing `Dockerfile`):  
`docker build -t my-tag .`  
[Read more](#Building-images)

Create and start container (simplest form):  
`docker run --name <some_name> <image tag or name> [args]`

Stop container:  
`docker stop <container name or id>`

Remove container:  
`docker rm <container name or id>`

Remove image:  
`docker rmi <image name or id>`

Push image to registry:
1. Tag image with repository URL first. Mind that repo may support the `v1` and/or `v2` 'naming' format. Briefly speaking:
`v1` format doesn't support multiple path segments in image name, thus image: `rootName/subName` is invalid. For `v2` it is fine 
`docker tag <image> <repourl>/<name>:<tag>` 
2. Login to desired repository, `docker.io` is the default  
`docker login <repourl>` 
3. `docker push <repourl>/<name>:<tag>`

### Building images
There are two types of images:
 - _parent image_ - the image that is specified in `FROM` clause. Your image is based on _parent image_.
 - _base image_ - the image that has no `FROM` or `FROM scratch`

Most `Dockerfile`s use _parent images_ in their `FROM` clause.

`Dockerfile` contains all of the needed instructions to build the Image with your application.  
Each instruction corresponds to filesystem layer. Image is built within _build context_ (the current directory of `docker build` command - simply speaking).
All `COPY`-kind instructions are relative to this _build context_.

The main goal when building the image should be: 
 - minimize the number of layers, the new layer is created only with: `COPY`, `RUN` and `ADD` commands.
 - use cache as much as possible. When the `Dockerfile` processed, each instruction is examined if its outcome is present in the cache already. 
 The `ADD` and `COPY` will check if the underlying file has changed, the `RUN` commands will check only if the command string has not changed.
 Thus building the image with `RUN apt-get update` second time won't update the latest packages.
 - once the cache is invalidated (instruction that misses cache occurred), all subsequent instructions won't check the cache

Design goals of building images:
 - (try) to package one application in container
 - mind process reaping when application spawn processes 
 
#### Building base images
In order to build base image:
 - use debootstrap for Debian-based distributions (tool that installs Debian-based distributions into given filesystem) 
 - archive it and `docker import image.tar name` 

It is also possible to build base image from `Dockerfile`:  
```
FROM scratch
ADD some_binary /
CMD ["/some_binary"]
```
Such image will be able to execute the specified binary only.
 
### Debugging
Failed container can be pretty easily debugged
1. Grab exited container ID  
`docker ps -a`
2. Create image  
`docker commit <container id>`
3. Run image corresponding to failed container, overriding its potential `ENTRYPOINT`  
`docker run -it --entrypoint /bin/bash <image id>`

Live container can be debugged as well:
1. Grab the container name or ID: `docker ps`
2. Attach to the container with: `docker exec -it /bin/bash`

## Networking
Networking system in Docker is configurable. It is possible to set different networking driver for containers using:
`docker run --network=host ...`

### bridge
Default driver, creates the bridge that connects all containers withing Docker host. Docker host manages IP addresses and `iptables` rules
governing inbound/outbound traffic. From Docker host it should be possible to reach any port on the running container (host has routing configured for containers subnet: `ip r s`)
However by default containers will be unreachable from outside (outside of Docker host). In order to tell Docker host, 
that it should add `iptables` rules allows access to containers the port publish option must be used: `docker run... -p 8080:80 ...`.  
The `-p hostPort:containerPort` maps (TCP by default) port 8080 on the host to the container port 80.   

### host 
Uses the host networking interfaces. No network isolation. The `ip a` output from within the container will print same
entries as `ip a` on the Docker host

### overlay
Use when multiple containers need to communicate with each other and containers are scattered among different Docker hosts

### macvlan
TODO

### none
Disables networking

### custom
TODO

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

# References
 1. https://docs.docker.com/develop/develop-images/dockerfile_best-practices/#label