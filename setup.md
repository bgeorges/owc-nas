#Setting up a NAS from scratch

##hardware
Intel NUC / Core i7 10th Gen 
OWC Thunderbay 4 mini
4 x 1TB SSDs [Samsung EVO Basic] 

## Software
Fedora 33 WS

## Steps
### Add physycal disks
first check if they are here
```bash
$ do lsblk -f
```
Should return something like this:
```bash
$ sudo lsblk -f
NAME        FSTYPE      FSVER    LABEL                 UUID                                   FSAVAIL FSUSE% MOUNTPOINT
sda                                                                                                          
├─sda1      xfs                  BACKUP                6077123e-aeeb-4f1b-865e-6a94820f6143    508.2G     1% /backup
└─sda2      ext4        1.0      Cloud                 c1d287b3-ef48-4c1c-8af9-3b09cb513770    110.5G     0% /cloud
sdb                       
sdc                                                                                                          
sdd                                                                                                          
sde                                                                                                          
zram0                                                                                                        [SWAP]
nvme0n1                                                                                                      
├─nvme0n1p1 vfat        FAT32                          2BD5-062A                               578.5M     3% /boot/efi
├─nvme0n1p2 ext4        1.0                            e2628b16-57a5-46d9-9633-e1e56bdff69d    699.9M    21% /boot
└─nvme0n1p3 btrfs               fedora_localhost-live 0dc9ae69-4b4d-4b98-ae1f-3cf4a910c978    916.3G     1% /home
```
add the physical disks
```bash
$ sudo pvcreate /dev/sdb-e
```
