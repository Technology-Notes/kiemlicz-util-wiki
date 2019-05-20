# Containerization
Often seen as na advancement from `chroot` technique. `chroot` creates the filesystem 'isolation' only.

Containers provide more: process isolation via kernel namespaces and resource usage control via cgroups.  
Both the host and container share **the same kernel**.  
Engine responsible for providing the execution environment for application is called _virtualization container_. 

## Benefits
 - faster than full VM (doesn't require running hypervisor) 
 - easier to provision (doesn't require full VM setup)
 - cheaper
 - better resource utilization
 - easier to scale 

## Solutions
- [Docker](Docker)
- [LXC](LXC)

# References
 1. Modern Linux Administration - Sam R. Alapati
