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
1. Tag image first with repository URL  
`docker tag <image> <repourl>/<tag>` 
2. Login to desired repository, `docker.io` is the default  
`docker login <repourl>` 
3. `docker push <repourl>/<tag>`

### Building images
`Dockerfile` contains all of the needed instructions to build the Image with your application.  
Each instruction corresponds to filesystem layer. Image is built within _build context_ (the current directory of `docker build` command - simply speaking).
All `COPY`-kind instructions are relative to this _build context_.

The main goal when building the image should be: 
 - minimize the number of layers, the new layer is created only with: `COPY`, `RUN` and `ADD` commands.
 - use cache as much as possible. When the `Dockerfile` processed, each instruction is examined if its outcome is present in the cache already. 
 The `ADD` and `COPY` will check if the underlying file has changed, the `RUN` commands will check only if the command string has not changed.
 Thus building the image with `RUN apt-get update` second time won't update the latest packages.
 - once the cache is invalidated (instruction that misses cache occurred), all subsequent instructions won't check the cache
 
### Debugging
Failed container can be pretty easily debugged
1. Grab exited container ID  
`docker ps -a`
2. Create image  
`docker commit <container id>`
3. Run image corresponding to failed container  
`docker run -it --entrypoint /bin/bash <image id>`

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