# Basics
## Configuration
### Host creation screen
Interface must be marked as managed in order to download os image.
# Provisioning
## Automated os installation
### Preseeding
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
# Integration
## Saltstack
Saltstack's grains map to foreman facts.  
Saltstack's pillar map to foreman parameters.  

# References
 1. https://www.debian.org/releases/stretch/example-preseed.txt
 2. https://wikitech.wikimedia.org/wiki/PartMan