# Unattended system provisioning
Unattended installation comprises of two phases:
1. Installation of OS itself with automatic answers
2. Provisioning of installed OS (application installation, configuration etc.)

It is tightly coupled with [Infrastructure as a Code](https://en.wikipedia.org/wiki/Infrastructure_as_Code) concept.

## OS installation
Uses PXE boot, which can roughly be depicted as (BIOS example):  
![](https://icefyresan.files.wordpress.com/2014/12/pxe.jpg)

PXE booting relies on platform firmware, its configuration files are different for BIOS and its successor UEFI.

### [BIOS](https://en.wikipedia.org/wiki/BIOS)
Hardware initialization in not exactly standarized manner (as reverse engineered from IBM first implementation).  
After POST boots by reading and executing first sector on hard disk. Booting runs in 16-bit processor mode and has only 1MB of space to execute in. Has problems with parallel device initialization. Can boot only from hard disks of size less than 2.1TB  
Uses MBR partitioning scheme

### UEFI
Has no limitations of BIOS, standarized (by Intel).  
Specifies following servies available for OS and OS loader:  

|System table|
|----|
|Boot time services|
|Run time services|
|Console|
|Additional tables|

Boots by loading EFI program files.  
Uses GPT partitioning scheme

### Debian-based OS installation
Named as: _preseeding_  
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
 4. https://www.debian.org/releases/stable/amd64/ch03s06.html.en#UEFI
 5. http://fai-project.org/fai-guide/
 6. https://www.youtube.com/watch?v=bNL1pd-rwCU
 7. https://www.howtogeek.com/56958/htg-explains-how-uefi-will-replace-the-bios/
 8. https://superuser.com/questions/496026/what-is-the-difference-in-boot-with-bios-and-boot-with-uefi