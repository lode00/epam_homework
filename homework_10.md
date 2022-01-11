## Boot process
*  enable recovery options for grub, update main configuration file and find new item in grub2 config in /boot.
```bash
[root@localhost ~]# nano /etc/default/grub
GRUB_TIMEOUT=5
GRUB_DISTRIBUTOR="$(sed 's, release .*$,,g' /etc/system-release)"
GRUB_DEFAULT=saved
GRUB_DISABLE_SUBMENU=true
GRUB_TERMINAL_OUTPUT="console"
GRUB_CMDLINE_LINUX="crashkernel=auto rd.lvm.lv=centos/root rd.lvm.lv=centos/swap rhgb quiet"
GRUB_DISABLE_RECOVERY="false"
```
```bash
[root@localhost ~]# grub2-mkconfig -o /boot/grub2/grub.cfg
```
*  modify option vm.dirty_ratio:
   1. using echo utility
 ```bash
[root@localhost ~]#  echo 60 > /proc/sys/vm/dirty_ratio &&  sysctl -a | grep ^vm.dirty_ratio
sysctl: reading key "net.ipv6.conf.all.stable_secret"
sysctl: reading key "net.ipv6.conf.default.stable_secret"
sysctl: reading key "net.ipv6.conf.enp0s3.stable_secret"
sysctl: reading key "net.ipv6.conf.enp0s8.stable_secret"
sysctl: reading key "net.ipv6.conf.lo.stable_secret"
vm.dirty_ratio = 60
```
   2. using sysctl utility
```bash
root@localhost ~]# sysctl -w vm.dirty_ratio=30
vm.dirty_ratio = 30
```
   3. using sysctl configuration files
```bash
[root@localhost ~]# vi /etc/sysctl.conf
```
```bash
# sysctl settings are defined through files in
# /usr/lib/sysctl.d/, /run/sysctl.d/, and /etc/sysctl.d/.
#
# Vendors settings live in /usr/lib/sysctl.d/.
# To override a whole file, create a new file with the same in
# /etc/sysctl.d/ and put new settings there. To override
# only specific settings, add a file with a lexically later
# name in /etc/sysctl.d/ and put new settings there.
#
# For more information, see sysctl.conf(5) and sysctl.d(5)
vm.dirty_ratio=50
```
```bash
[root@localhost ~]#  sysctl --load
vm.dirty_ratio = 50
```

## extra
* Inspect initrd file contents. Find all files that are related to XFS filesystem and give a short description for every file.
```bash
[root@localhost ~]# uname -r
3.10.0-1160.45.1.el7.x86_64                                                                                                          
[root@localhost ~]#   mkinitrd --force /boot/initrd_test.img 3.10.0-1160.45.1.el7.x86_64
```
```bash
[root@localhost modules]# cpio -civt < /boot/initrd_test.img
drwxr-xr-x   3 root     root            0 Jan 11 03:43 .
drwxr-xr-x   3 root     root            0 Jan 11 03:43 kernel
drwxr-xr-x   3 root     root            0 Jan 11 03:43 kernel/x86
drwxr-xr-x   2 root     root            0 Jan 11 03:43 kernel/x86/microcode
-rw-r--r--   1 root     root        19456 Jan 11 03:43 kernel/x86/microcode/GenuineIntel.bin
-rw-r--r--   1 root     root            2 Jan 11 03:43 early_cpio
40 blocks
```
* Study dracut utility that is used for rebuilding initrd image. Give an example for adding driver/kernel module for your initrd and recreating it.
```bash
[root@localhost modules]# # dracut 1__dracut.img 3.10.0-1160.45.1.el7.x86_64
[root@localhost modules]# lsinitrd my__dracut.img | head -n 12
Image: my__dracut.img: 21M
========================================================================
Early CPIO image
========================================================================
drwxr-xr-x   3 root     root            0 Jan 11 03:50 .
-rw-r--r--   1 root     root            2 Jan 11 03:50 early_cpio
drwxr-xr-x   3 root     root            0 Jan 11 03:50 kernel
drwxr-xr-x   3 root     root            0 Jan 11 03:50 kernel/x86
drwxr-xr-x   2 root     root            0 Jan 11 03:50 kernel/x86/microcode
-rw-r--r--   1 root     root        19456 Jan 11 03:50 kernel/x86/microcode/GenuineIntel.bin
========================================================================
Version: dracut-033-572.el7
```
## Selinux
* Disable selinux using kernel cmdline

 Временно отключить selinux:
```bash
[root@localhost init.d]# setenforce 0
[root@localhost init.d]# getenforce
Permissive
```
Отключить навсегда:
```bash
[root@localhost ~]# vi /etc/sysconfig/selinux

# This file controls the state of SELinux on the system.
# SELINUX= can take one of these three values:
#     enforcing - SELinux security policy is enforced.
#     permissive - SELinux prints warnings instead of enforcing.
#     disabled - No SELinux policy is loaded.
SELINUX=disabled
# SELINUXTYPE= can take one of three values:
#     targeted - Targeted processes are protected,
#     minimum - Modification of targeted policy. Only selected processes are pr$
#     mls - Multi Level Security protection.
SELINUXTYPE=targeted
[root@localhost ~]# getenforce
Disabled
```
## Firewalls

* Add rule using firewall-cmd that will allow SSH access to your server *only* from network 192.168.56.0/24 and interface enp0s8 (if your network and/on interface name differs - change it accordingly).
```bash
[root@localhost ~]#  firewall-cmd --zone=trusted --add-source=192.168.56.0/24 &&                                                                                                             firewall-cmd --zone=trusted --add-service=ssh && firewall-cmd --permanent --zone                                                                                                             =trusted --add-interface=enp0s8
success
success
The interface is under control of NetworkManager, setting zone to 'trusted'.
success
```
```bash
[root@localhost ~]# firewall-cmd --zone=trusted --list-all
trusted (active)
  target: ACCEPT
  icmp-block-inversion: no
  interfaces: enp0s8
  sources: 192.168.56.0/24
  services: ssh
  ports:
  protocols:
  masquerade: no
  forward-ports:
  source-ports:
  icmp-blocks:
  rich rules:
```
* Shutdown firewalld and add the same rules via iptables.
```bash
[root@localhost ~]# systemctl stop firewalld &&  firewall-cmd --state
not running
[root@localhost ~]# iptables -A INPUT -p tcp --dport ssh -s 192.168.56.0/24 -i enp0s8 -j ACCEPT
[root@localhost ~]# iptables -A INPUT -p tcp --dport ssh -j DROP
```
