# Setting up a NAS from scratch

## hardware
* Intel NUC / Core i7 10th Gen 
* OWC Thunderbay 4 mini
* 4 x 1TB SSDs [Samsung EVO Basic] 

## Software
* Fedora 33 WS

## Steps
Before we create the RAID we first need to create LVM Volumes.
The logical volume creation steps are: 
1. Create phyiscal volumes
1. Create volume groups
1. Create logical volumes

Once this is done we will create the RAID. 
The steps to create the RAID are:
1. Using Cockpit
2. Command line

### Create physycal volumes
first check if they show up after adding them to the OWC enclosure. The connection is using thunderbolt 3, which fedora 33 supported out of the box.
```bash
$ sudo lsblk -f
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
Define LVM physical volumes ou of the physical disks added in the OWC bay, /dev/sd[b-e]
```bash
$ sudo pvcreate /dev/sd[b-e]
```
Now you should see them as physical volume. note that they are formatted as lvm type partitions.

```bash
$ sudo pvs
  PV         VG Fmt  Attr PSize   PFree  
  /dev/sdb      lvm2 ---  931.51g 931.51g
  /dev/sdc      lvm2 ---  931.51g 931.51g
  /dev/sdd      lvm2 ---  931.51g 931.51g
  /dev/sde      lvm2 ---  931.51g 931.51g
```
Or more details using the `pvdisplay` command
```bash
$ sudo pvdisplay
  "/dev/sdb" is a new physical volume of "931.51 GiB"
  --- NEW Physical volume ---
  PV Name               /dev/sdb
  VG Name               
  PV Size               931.51 GiB
  Allocatable           NO
  PE Size               0   
  Total PE              0
  Free PE               0
  Allocated PE          0
  PV UUID               DELucN-iozl-AhyW-Ij1j-yAZ0-daiQ-6QAiT0
   
  "/dev/sdc" is a new physical volume of "931.51 GiB"
  --- NEW Physical volume ---
  PV Name               /dev/sdc
  VG Name               
  PV Size               931.51 GiB
  Allocatable           NO
  PE Size               0   
  Total PE              0
  Free PE               0
  Allocated PE          0
  PV UUID               JUuu4M-fmeZ-j7kA-irUY-3Xom-lnxY-bczFIh
   
  "/dev/sdd" is a new physical volume of "931.51 GiB"
  --- NEW Physical volume ---
  PV Name               /dev/sdd
  VG Name               
  PV Size               931.51 GiB
  Allocatable           NO
  PE Size               0   
  Total PE              0
  Free PE               0
  Allocated PE          0
  PV UUID               FNG91w-x9p2-bJgR-5pcX-b21g-Ghl6-7Qm8pq
   
  "/dev/sde" is a new physical volume of "931.51 GiB"
  --- NEW Physical volume ---
  PV Name               /dev/sde
  VG Name               
  PV Size               931.51 GiB
  Allocatable           NO
  PE Size               0   
  Total PE              0
  Free PE               0
  Allocated PE          0
  PV UUID               4hs98p-yf3a-eny4-TLJs-PUnx-8r19-ElxZ73
```
Note that they are not added to any Volume Groups yet. 

#### create volume group

```bash
$ sudo vgcreate $VG /dev/sd[b-e]
Volume group "OWC_S1_VG" successfully created
$ sudo pvscan -v 
  PV /dev/sdb   VG OWC_S1_VG       lvm2 [931.51 GiB / 931.51 GiB free]
  PV /dev/sdc   VG OWC_S1_VG       lvm2 [931.51 GiB / 931.51 GiB free]
  PV /dev/sdd   VG OWC_S1_VG       lvm2 [931.51 GiB / 931.51 GiB free]
  PV /dev/sde   VG OWC_S1_VG       lvm2 [931.51 GiB / 931.51 GiB free]
  Total: 4 [<3.64 TiB] / in use: 4 [<3.64 TiB] / in no VG: 0 [0   ]

```
Voilà
