# Unattended system installation
Unattended installation comprises two phases:
1. installation of OS itself with automatic answers
2. provisioning of installed OS (application installation etc.)

## OS installation
Uses PXE boot, which can roughly be depicted as:  
![](https://icefyresan.files.wordpress.com/2014/12/pxe.jpg)
As PXE booting relies on platform firmware, its configuration is different for BIOS and its successor UEFI
### Debian-based OS installation
Called: preseeding  
Example of preseed with custom partition setup
```
d-i partman-auto/choose_recipe select expert
d-i partman-auto/expert_recipe string \
boot-root :: \
                2048 2200 4096 ext4 \
                        $primary{ } $bootable{ } \
                        method{ format } format{ } \
                        use_filesystem{ } filesystem{ ext4 } \
                        mountpoint{ / } \
                . \
                1024 1100 1500 ext4 \
                        method{ format } format{ } \
                        use_filesystem{ } filesystem{ ext4 } \
                        mountpoint{ /home }  \
                 . \
                1024 1100 1500 ext4 \
                        method{ format } format{ } \
                        use_filesystem{ } filesystem{ ext4 } \
                        mountpoint{ /var }  \
              .  \
              1024 1030 1056 linux-swap \
                        method{ swap } format{ } \
                .
```
# References
 1. https://www.debian.org/releases/stretch/example-preseed.txt
 2. https://wikitech.wikimedia.org/wiki/PartMan
 3. https://wiki.debian.org/DebianInstaller/Preseed