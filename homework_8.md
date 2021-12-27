## 1. Imagine you was asked to add new partition to your host for backup purposes. To simulate appearance of new physical disk in your server, please create new disk in Virtual Box (5 GB) and attach it to your virtual machine. Also imagine your system started experiencing RAM leak in one of the applications, thus while developers try to debug and fix it, you need to mitigate OutOfMemory errors; you will do it by adding some swap space. /dev/sdc - 5GB disk, that you just attached to the VM (in your case it may appear as /dev/sdb, /dev/sdc or other, it doesn't matter)
Выдал все равно sdb :/
```bash
[root@localhost ~]# fdisk -l | grep sdb
Disk /dev/sdb: 5368 MB, 5368709120 bytes, 10485760 sectors
```

### 1.1. Create a 2GB   !!! GPT !!!   partition on /dev/sdc of type "Linux filesystem" (means all the following partitions created in the following steps on /dev/sdc will be GPT as well)
Сделал через gdisk, предварительно установил :)
```bash
[root@localhost ~]# gdisk /dev/sdb
GPT fdisk (gdisk) version 0.8.10

Partition table scan:
  MBR: not present
  BSD: not present
  APM: not present
  GPT: not present

Creating new GPT entries.

Command (? for help): n
Partition number (1-128, default 1): 1
First sector (34-10485726, default = 2048) or {+-}size{KMGTP}:
Last sector (2048-10485726, default = 10485726) or {+-}size{KMGTP}: +2G
Current type is 'Linux filesystem'
Hex code or GUID (L to show codes, Enter = 8300):
Changed type of partition to 'Linux filesystem'

Command (? for help): p
Disk /dev/sdb: 10485760 sectors, 5.0 GiB
Logical sector size: 512 bytes
Disk identifier (GUID): 23390482-FDD0-4B75-B278-B7FBA29AF637
Partition table holds up to 128 entries
First usable sector is 34, last usable sector is 10485726
Partitions will be aligned on 2048-sector boundaries
Total free space is 6291389 sectors (3.0 GiB)

Number  Start (sector)    End (sector)  Size       Code  Name
   1            2048         4196351   2.0 GiB     8300  Linux filesystem

```
### 1.2. Create a 512MB partition on /dev/sdc of type "Linux swap"
```bash
Partition number (2-128, default 2): 2
First sector (34-10485726, default = 4196352) or {+-}size{KMGTP}:
Last sector (4196352-10485726, default = 10485726) or {+-}size{KMGTP}: +512M
Current type is 'Linux filesystem'
Hex code or GUID (L to show codes, Enter = 8300): L
0700 Microsoft basic data  0c01 Microsoft reserved    2700 Windows RE
3000 ONIE boot             3001 ONIE config           4100 PowerPC PReP boot
4200 Windows LDM data      4201 Windows LDM metadata  7501 IBM GPFS
7f00 ChromeOS kernel       7f01 ChromeOS root         7f02 ChromeOS reserved
8200 Linux swap            8300 Linux filesystem      8301 Linux reserved
8302 Linux /home           8400 Intel Rapid Start     8e00 Linux LVM
a500 FreeBSD disklabel     a501 FreeBSD boot          a502 FreeBSD swap
a503 FreeBSD UFS           a504 FreeBSD ZFS           a505 FreeBSD Vinum/RAID
a580 Midnight BSD data     a581 Midnight BSD boot     a582 Midnight BSD swap
a583 Midnight BSD UFS      a584 Midnight BSD ZFS      a585 Midnight BSD Vinum
a800 Apple UFS             a901 NetBSD swap           a902 NetBSD FFS
a903 NetBSD LFS            a904 NetBSD concatenated   a905 NetBSD encrypted
a906 NetBSD RAID           ab00 Apple boot            af00 Apple HFS/HFS+
af01 Apple RAID            af02 Apple RAID offline    af03 Apple label
af04 AppleTV recovery      af05 Apple Core Storage    be00 Solaris boot
bf00 Solaris root          bf01 Solaris /usr & Mac Z  bf02 Solaris swap
bf03 Solaris backup        bf04 Solaris /var          bf05 Solaris /home
bf06 Solaris alternate se  bf07 Solaris Reserved 1    bf08 Solaris Reserved 2
bf09 Solaris Reserved 3    bf0a Solaris Reserved 4    bf0b Solaris Reserved 5
c001 HP-UX data            c002 HP-UX service         ea00 Freedesktop $BOOT
eb00 Haiku BFS             ed00 Sony system partitio  ed01 Lenovo system partit
Press the <Enter> key to see more codes: 8200
ef00 EFI System            ef01 MBR partition scheme  ef02 BIOS boot partition
fb00 VMWare VMFS           fb01 VMWare reserved       fc00 VMWare kcore crash p
fd00 Linux RAID
Hex code or GUID (L to show codes, Enter = 8300): 8200
Changed type of partition to 'Linux swap'

Command (? for help): P
Disk /dev/sdb: 10485760 sectors, 5.0 GiB
Logical sector size: 512 bytes
Disk identifier (GUID): F746011C-4B1F-4258-8E78-4CEAF9F79FF3
Partition table holds up to 128 entries
First usable sector is 34, last usable sector is 10485726
Partitions will be aligned on 2048-sector boundaries
Total free space is 5242813 sectors (2.5 GiB)

Number  Start (sector)    End (sector)  Size       Code  Name
   1            2048         4196351   2.0 GiB     8300  Linux filesystem
   2         4196352         5244927   512.0 MiB   8200  Linux swap

Command (? for help): w

Final checks complete. About to write GPT data. THIS WILL OVERWRITE EXISTING
PARTITIONS!!

Do you want to proceed? (Y/N): y
OK; writing new GUID partition table (GPT) to /dev/sdb.
The operation has completed successfully.
```

### 1.3. Format the 2GB partition with an XFS file system
```bash
[root@localhost ~]# mkfs -t xfs /dev/sdb1
meta-data=/dev/sdb1              isize=512    agcount=4, agsize=131072 blks
         =                       sectsz=512   attr=2, projid32bit=1
         =                       crc=1        finobt=0, sparse=0
data     =                       bsize=4096   blocks=524288, imaxpct=25
         =                       sunit=0      swidth=0 blks
naming   =version 2              bsize=4096   ascii-ci=0 ftype=1
log      =internal log           bsize=4096   blocks=2560, version=2
         =                       sectsz=512   sunit=0 blks, lazy-count=1
realtime =none                   extsz=4096   blocks=0, rtextents=0
[root@localhost ~]# sudo parted /dev/sdb
GNU Parted 3.1
Using /dev/sdb
Welcome to GNU Parted! Type 'help' to view a list of commands.
(parted) print
Model: ATA VBOX HARDDISK (scsi)
Disk /dev/sdb: 5369MB
Sector size (logical/physical): 512B/512B
Partition Table: gpt
Disk Flags:

Number  Start   End     Size    File system  Name              Flags
 1      1049kB  2149MB  2147MB  xfs          Linux filesystem
 2      2149MB  2685MB  537MB                Linux swap

(parted) quit
```

### 1.4. Initialize 512MB partition as swap space
```bash
[root@localhost ~]# mkswap /dev/sdb2
Setting up swapspace version 1, size = 524284 KiB
no label, UUID=b22e2a91-dfba-40db-8514-4387ef722881
[root@localhost ~]# lsblk -f
NAME            FSTYPE      LABEL UUID                                   MOUNTPOINT
sda
├─sda1          xfs               a0c33895-c355-4f5b-834a-42c01128c258   /boot
└─sda2          LVM2_member       EN1BIU-Kfep-cQzU-7l8b-GC0a-xOjp-Klp6Vm
  ├─centos-root xfs               221e7118-bcf3-4486-8b7e-53f562169774   /
  └─centos-swap swap              56290922-b322-41ab-a119-3855d96d9d8f   [SWAP]
sdb
├─sdb1          xfs               f253c2d2-4b60-43f4-9a79-3d018c116fd8
└─sdb2          swap              b22e2a91-dfba-40db-8514-4387ef722881
sr0
```
### 1.5. Configure the newly created XFS file system to persistently mount at /backup
```bash
[root@localhost ~]# mkdir /backup
[root@localhost ~]# mount /dev/sdb1 /backup
[root@localhost ~]# df -h
Filesystem               Size  Used Avail Use% Mounted on
devtmpfs                 908M     0  908M   0% /dev
tmpfs                    919M     0  919M   0% /dev/shm
tmpfs                    919M  8.6M  911M   1% /run
tmpfs                    919M     0  919M   0% /sys/fs/cgroup
/dev/mapper/centos-root   17G  2.6G   15G  15% /
/dev/sda1               1014M  194M  821M  20% /boot
tmpfs                    184M     0  184M   0% /run/user/1000
/dev/sdb1                2.0G   33M  2.0G   2% /backup
```
### 1.6. Configure the newly created swap space to be enabled at boot
```bash
[root@localhost ~]# blkid /dev/sdb1 && blkid /dev/sdb2
/dev/sdb1: UUID="f253c2d2-4b60-43f4-9a79-3d018c116fd8" TYPE="xfs" PARTLABEL="Linux filesystem" PARTUUID="088e2e80-14c5-4014-b347-4d5ace9950af"
/dev/sdb2: UUID="b22e2a91-dfba-40db-8514-4387ef722881" TYPE="swap" PARTLABEL="Linux swap" PARTUUID="afbe2b51-65a6-4dfc-93da-a99662bb260d"
[root@localhost ~]#vi /etc/fstab
UUID="f253c2d2-4b60-43f4-9a79-3d018c116fd8" /backup        xfs      defaults        0 0
UUID="b22e2a91-dfba-40db-8514-4387ef722881"  swap           swap     defaults        0 0
```
```bash
[root@localhost ~]# swapon -a
```
```bash
[root@localhost ~]# swapon -s
Filename                                Type            Size    Used    Priority
/dev/dm-1                               partition       2064380 0       -2
/dev/sdb2                               partition       524284  0       -3
```
### 1.7. Reboot your host and verify that /dev/sdc1 is mounted at /backup and that your swap partition  (/dev/sdc2) is enabled
```bash
[root@localhost ~]# reboot
```
```bash
[root@localhost ~]# lsblk
NAME            MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
sda               8:0    0 19.7G  0 disk
├─sda1            8:1    0    1G  0 part /boot
└─sda2            8:2    0 18.7G  0 part
  ├─centos-root 253:0    0 16.7G  0 lvm  /
  └─centos-swap 253:1    0    2G  0 lvm  [SWAP]
sdb               8:16   0    5G  0 disk
├─sdb1            8:17   0    2G  0 part /backup
└─sdb2            8:18   0  512M  0 part [SWAP]
sr0              11:0    1 1024M  0 rom
```
```bash
[root@localhost ~]# swapon -s
Filename                                Type            Size    Used    Priority
/dev/dm-1                               partition       2064380 0       -3
/dev/sdb2                               partition       524284  0       -2
```
## 2. LVM. Imagine you're running out of space on your root device. As we found out during the lesson default CentOS installation should already have LVM, means you can easily extend size of your root device. So what are you waiting for? Just do it!
### 2.1. Create 2GB partition on /dev/sdc of type "Linux LVM"
```bash
[root@localhost ~]# gdisk /dev/sdb
GPT fdisk (gdisk) version 0.8.10

Partition table scan:
  MBR: protective
  BSD: not present
  APM: not present
  GPT: present

Found valid GPT with protective MBR; using GPT.

Command (? for help): n
Partition number (3-128, default 3):
First sector (34-10485726, default = 5244928) or {+-}size{KMGTP}:
Last sector (5244928-10485726, default = 10485726) or {+-}size{KMGTP}: +2G
Current type is 'Linux filesystem'
Hex code or GUID (L to show codes, Enter = 8300): L
0700 Microsoft basic data  0c01 Microsoft reserved    2700 Windows RE
3000 ONIE boot             3001 ONIE config           4100 PowerPC PReP boot
4200 Windows LDM data      4201 Windows LDM metadata  7501 IBM GPFS
7f00 ChromeOS kernel       7f01 ChromeOS root         7f02 ChromeOS reserved
8200 Linux swap            8300 Linux filesystem      8301 Linux reserved
8302 Linux /home           8400 Intel Rapid Start     8e00 Linux LVM
a500 FreeBSD disklabel     a501 FreeBSD boot          a502 FreeBSD swap
a503 FreeBSD UFS           a504 FreeBSD ZFS           a505 FreeBSD Vinum/RAID
a580 Midnight BSD data     a581 Midnight BSD boot     a582 Midnight BSD swap
a583 Midnight BSD UFS      a584 Midnight BSD ZFS      a585 Midnight BSD Vinum
a800 Apple UFS             a901 NetBSD swap           a902 NetBSD FFS
a903 NetBSD LFS            a904 NetBSD concatenated   a905 NetBSD encrypted
a906 NetBSD RAID           ab00 Apple boot            af00 Apple HFS/HFS+
af01 Apple RAID            af02 Apple RAID offline    af03 Apple label
af04 AppleTV recovery      af05 Apple Core Storage    be00 Solaris boot
bf00 Solaris root          bf01 Solaris /usr & Mac Z  bf02 Solaris swap
bf03 Solaris backup        bf04 Solaris /var          bf05 Solaris /home
bf06 Solaris alternate se  bf07 Solaris Reserved 1    bf08 Solaris Reserved 2
bf09 Solaris Reserved 3    bf0a Solaris Reserved 4    bf0b Solaris Reserved 5
c001 HP-UX data            c002 HP-UX service         ea00 Freedesktop $BOOT
eb00 Haiku BFS             ed00 Sony system partitio  ed01 Lenovo system partit
Press the <Enter> key to see more codes:
ef00 EFI System            ef01 MBR partition scheme  ef02 BIOS boot partition
fb00 VMWare VMFS           fb01 VMWare reserved       fc00 VMWare kcore crash p
fd00 Linux RAID
Hex code or GUID (L to show codes, Enter = 8300): 8e00
Changed type of partition to 'Linux LVM'
Command (? for help): w
Final checks complete. About to write GPT data. THIS WILL OVERWRITE EXISTING
PARTITIONS!!

Do you want to proceed? (Y/N): Y
OK; writing new GUID partition table (GPT) to /dev/sdb.
Warning: The kernel is still using the old partition table.
The new table will be used at the next reboot.
The operation has completed successfully.
You have new mail in /var/spool/mail/root
```
```bash
[root@localhost ~]# partprobe
```
```bash
[root@localhost ~]# lsblk
NAME            MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
sda               8:0    0 19.7G  0 disk
├─sda1            8:1    0    1G  0 part /boot
└─sda2            8:2    0 18.7G  0 part
  ├─centos-root 253:0    0 16.7G  0 lvm  /
  └─centos-swap 253:1    0    2G  0 lvm  [SWAP]
sdb               8:16   0    5G  0 disk
├─sdb1            8:17   0    2G  0 part /backup
├─sdb2            8:18   0  512M  0 part [SWAP]
└─sdb3            8:19   0    2G  0 part
sr0              11:0    1 1024M  0 rom
```
### 2.2. Initialize the partition as a physical volume (PV)
```bash
[root@localhost ~]#  pvcreate /dev/sdb3
  Physical volume "/dev/sdb3" successfully created.
```
### 2.3. Extend the volume group (VG) of your root device using your newly created PV
```bash
[root@localhost ~]# vgextend centos /dev/sdb3
  Volume group "centos" successfully extended
  ```
### 2.4. Extend your root logical volume (LV) by 1GB, leaving other 1GB unassigned
```bash
[root@localhost ~]# lvextend -L +1G /dev/centos/root
  Size of logical volume centos/root changed from 16.71 GiB (4278 extents) to 17.71 GiB (4534 extents).
  Logical volume centos/root successfully resized.
 ```
### 2.5. Check current disk space usage of your root device
```bash
[root@localhost ~]# df -h
Filesystem               Size  Used Avail Use% Mounted on
devtmpfs                 908M     0  908M   0% /dev
tmpfs                    919M     0  919M   0% /dev/shm
tmpfs                    919M  8.6M  911M   1% /run
tmpfs                    919M     0  919M   0% /sys/fs/cgroup
/dev/mapper/centos-root   17G  2.6G   15G  15% /    
/dev/sdb1                2.0G   33M  2.0G   2% /backup
/dev/sda1               1014M  194M  821M  20% /boot
tmpfs                    184M     0  184M   0% /run/user/1000
```
### 2.6. Extend your root device filesystem to be able to use additional free space of root LV
https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/6/html/storage_administration_guide/xfsgrow
```bash
[root@localhost ~]# xfs_growfs  /dev/mapper/centos-root
meta-data=/dev/mapper/centos-root isize=512    agcount=5, agsize=1095168 blks
         =                       sectsz=512   attr=2, projid32bit=1
         =                       crc=1        finobt=0 spinodes=0
data     =                       bsize=4096   blocks=4642816, imaxpct=25
         =                       sunit=0      swidth=0 blks
naming   =version 2              bsize=4096   ascii-ci=0 ftype=1
log      =internal               bsize=4096   blocks=2560, version=2
         =                       sectsz=512   sunit=0 blks, lazy-count=1
realtime =none                   extsz=4096   blocks=0, rtextents=0
```
### 2.7. Verify that after reboot your root device is still 1GB bigger than at 2.5.
До этого было:
```bash
/dev/mapper/centos-root   17G  2.6G   15G  15% /
```
Сейчас:
```bash
[root@localhost ~]# df -h | grep /dev/mapper
/dev/mapper/centos-root   18G  2.5G   16G  15% /
```
