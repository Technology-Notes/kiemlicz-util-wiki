#Containerization
Both the host and container share **the same kernel**. Engine responsible for providing the execution environment for application is called _virtualization container_. 

#Solutions
##LXC
Container's filesystem root location: `/var/lib/lxc/<container name>`
##Docker
LXC-based

#References
 1. Modern Linux Administration - Sam R. Alapati
 2. https://wiki.debian.org/LXC
 3. https://www.stgraber.org/2013/12/20/lxc-1-0-blog-post-series/
 4. https://www.flockport.com/guides/