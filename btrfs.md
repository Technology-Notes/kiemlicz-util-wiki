#Setup
Preliminary:
`apt-get install btrfs-tools`

In order to create filesystem on device (even not partitioned device):
`mkfs.btrfs /dev/sdb /dev/sdc /dev/sdd`

#References
1. https://www.howtoforge.com/a-beginners-guide-to-btrfs
2. https://wiki.debian.org/Btrfs
3. http://marc.merlins.org/perso/btrfs/post_2014-05-04_Fixing-Btrfs-Filesystem-Full-Problems.html
4. https://btrfs.wiki.kernel.org/index.php/Balance_Filters