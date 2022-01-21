## Repositories and Packages

* Download sysstat package.
```bash
[lode00@localhost ~]$ sudo  wget  http://mirror.centos.org/centos/7/os/x86_64/Packages/sysstat-10.1.5-19.el7.x86_64.rpm
--2021-12-24 03:30:13--  http://mirror.centos.org/centos/7/os/x86_64/Packages/sysstat-10.1.5-19.el7.x86_64.rpm
Resolving mirror.centos.org (mirror.centos.org)... 18.169.244.118, 2a00:1dc0:3061::10
Connecting to mirror.centos.org (mirror.centos.org)|18.169.244.118|:80... connected.
HTTP request sent, awaiting response... 200 OK
Length: 323020 (315K) [application/x-rpm]
Saving to: ‘sysstat-10.1.5-19.el7.x86_64.rpm’

100%[===================================================================================================================================================>] 323,020     1.72MB/s   in 0.2s

2021-12-24 03:30:14 (1.72 MB/s) - ‘sysstat-10.1.5-19.el7.x86_64.rpm’ saved [323020/323020]
```
* Get information from downloaded sysstat package file.
```bash
[lode00@localhost ~]$ epmqi  sysstat-10.1.5-19.el7.x86_64.rpm
 $ rpm -qip sysstat-10.1.5-19.el7.x86_64.rpm
Name        : sysstat
Version     : 10.1.5
Release     : 19.el7
Architecture: x86_64
Install Date: (not installed)
Group       : Applications/System
Size        : 1172488
License     : GPLv2+
Signature   : RSA/SHA256, Fri 03 Apr 2020 05:08:48 PM CDT, Key ID 24c6a8a7f4a80eb5
Source RPM  : sysstat-10.1.5-19.el7.src.rpm
Build Date  : Wed 01 Apr 2020 12:36:37 AM CDT
Build Host  : x86-01.bsys.centos.org
Relocations : (not relocatable)
Packager    : CentOS BuildSystem <http://bugs.centos.org>
Vendor      : CentOS
URL         : http://sebastien.godard.pagesperso-orange.fr/
Summary     : Collection of performance monitoring tools for Linux
Description :
The sysstat package contains sar, sadf, mpstat, iostat, pidstat, nfsiostat-sysstat,
tapestat, cifsiostat and sa tools for Linux.
The sar command collects and reports system activity information. This
information can be saved in a file in a binary format for future inspection. The
statistics reported by sar concern I/O transfer rates, paging activity,
process-related activities, interrupts, network activity, memory and swap space
utilization, CPU utilization, kernel activities and TTY statistics, among
others. Both UP and SMP machines are fully supported.
The sadf command may be used to display data collected by sar in various formats
(CSV, XML, etc.).
The iostat command reports CPU utilization and I/O statistics for disks.
The tapestat command reports statistics for tapes connected to the system.
The mpstat command reports global and per-processor statistics.
The pidstat command reports statistics for Linux tasks (processes).
The nfsiostat-sysstat command reports I/O statistics for network file systems.
The cifsiostat command reports I/O statistics for CIFS file systems.
```

* Install sysstat package and get information about files installed by this package.
```bash
[lode00@localhost ~]$ rpm -i  sysstat-10.1.5-19.el7.x86_64.rpm
```
```bash
[lode00@localhost ~]$ sudo rpm -q  sysstat
sysstat-10.1.5-19.el7.x86_64
```
 
* Check if NGINX repository enabled or not.
![image](https://user-images.githubusercontent.com/62034233/147356373-349267e1-2e45-4d54-b06d-2b2bf804ed87.png)

```bash
[lode00@localhost ~]$ sudo yum install yum-utils
```
```bash
[lode00@localhost ~]$ sudo vi /etc/yum.repos.d/nginx.repo
```
```bash
[lode00@localhost ~]$ sudo yum-config-manager --enable nginx-mainline
```
```bash
[lode00@localhost ~]$ yum repoinfo *nginx*
Loaded plugins: fastestmirror
Loading mirror speeds from cached hostfile
 * base: mirrors.datahouse.ru
 * extras: mirror.axelname.ru
 * updates: mirror.axelname.ru
Repo-id      : nginx-mainline/7/x86_64
Repo-name    : nginx mainline repo
Repo-status  : enabled
Repo-revision: 1635866853
Repo-updated : Tue Nov  2 11:27:36 2021
Repo-pkgs    : 875
Repo-size    : 391 M
Repo-baseurl : http://nginx.org/packages/mainline/centos/7/x86_64/
Repo-expire  : 21,600 second(s) (last: Fri Dec 24 04:19:30 2021)
  Filter     : read-only:present
Repo-filename: /etc/yum.repos.d/nginx.repo
Repo-id      : nginx-stable/7/x86_64
Repo-name    : nginx stable repo
Repo-status  : enabled
Repo-revision: 1637076068
Repo-updated : Tue Nov 16 10:21:09 2021
Repo-pkgs    : 256
Repo-size    : 131 M
Repo-baseurl : http://nginx.org/packages/centos/7/x86_64/
Repo-expire  : 21,600 second(s) (last: Fri Dec 24 04:19:30 2021)
  Filter     : read-only:present
Repo-filename: /etc/yum.repos.d/nginx.repo

repolist: 1,131
```
* Install NGINX.
```bash
[lode00@localhost ~]$ sudo yum install nginx
```
* Check yum history and undo NGINX installation.
 ```bash
[lode00@localhost ~]$ sudo yum history info  nginx
Loaded plugins: fastestmirror
Transaction ID : 11
Begin time     : Fri Dec 24 04:28:38 2021
Begin rpmdb    : 345:7f63e051460c36db7b34a33ef49727b420c431c1
End time       :            04:28:39 2021 (1 seconds)
End rpmdb      : 346:5365f89a8b61a0c0ba2ba8bb9125ce2ba3261eb8
User           : lode00 <lode00>
Return-Code    : Success
Command Line   : install nginx
Transaction performed with:
    Installed     rpm-4.11.3-46.el7_9.x86_64                      @updates
    Installed     yum-3.4.3-168.el7.centos.noarch                 @anaconda
    Installed     yum-plugin-fastestmirror-1.1.31-54.el7_8.noarch @anaconda
Packages Altered:
    Install nginx-1:1.21.4-1.el7.ngx.x86_64 @nginx-mainline
```
```bash
[lode00@localhost ~]$ sudo yum history undo 11
Loaded plugins: fastestmirror
Undoing transaction 11, from Fri Dec 24 04:28:38 2021
    Install nginx-1:1.21.4-1.el7.ngx.x86_64 @nginx-mainline
No package matched to remove: nginx-1:1.21.4-1.el7.ngx
history undo
```
* Disable NGINX repository.
```bash 
[lode00@localhost ~]$ sudo yum-config-manager --disable repository *nginx*
```
* Remove sysstat package installed in the first task.
```bash
[lode00@localhost ~]$ sudo yum remove sysstat
```
* Install EPEL repository and get information about it.
```bash
[lode00@localhost ~]$  sudo yum -y install epel-release &&  yum info epel-release
Installed Packages
Name        : epel-release
Arch        : noarch
Version     : 7
Release     : 11
Size        : 24 k
Repo        : installed
From repo   : extras
Summary     : Extra Packages for Enterprise Linux repository configuration
URL         : http://download.fedoraproject.org/pub/epel
License     : GPLv2
Description : This package contains the Extra Packages for Enterprise Linux (EPEL) repository
            : GPG key as well as configuration for yum.

Available Packages
Name        : epel-release
Arch        : noarch
Version     : 7
Release     : 14
Size        : 15 k
Repo        : epel/x86_64
Summary     : Extra Packages for Enterprise Linux repository configuration
URL         : http://download.fedoraproject.org/pub/epel
License     : GPLv2
Description : This package contains the Extra Packages for Enterprise Linux (EPEL) repository
            : GPG key as well as configuration for yum.
```
* Find how much packages provided exactly by EPEL repository.
Доступных пакетов:
```bash
[lode00@localhost ~]$ repoquery -qa --repoid=epel | wc -l
13701
```
Также нашел еще тут 
https://www.redhat.com/en/blog/whats-epel-and-how-do-i-use-it
```bash
[lode00@localhost ~]$ sudo yum --disablerepo="*" --enablerepo="epel" list available | wc -l
13920
```
* Install ncdu package from EPEL repo.
```bash
​[lode00@localhost ~]$ sudo yum --enablerepo="epel" install ncdu
 ```

## Work with files
​
* Find all regular files below 100 bytes inside your home directory.
```bash
[lode00@localhost BUILD]$ find /home/lode00 -size -100b  -type f -print
/home/lode00/.bash_logout
/home/lode00/.bash_profile
/home/lode00/.bashrc
/home/lode00/.ssh/known_hosts
/home/lode00/.ssh/hw-5
/home/lode00/.ssh/hw-5.pub
/home/lode00/.ssh/config
/home/lode00/.config/mc/ini
/home/lode00/.config/mc/panels.ini
/home/lode00/.cache/mc/Tree
/home/lode00/.local/share/mc/history
/home/lode00/.bash_history
/home/lode00/new/test.txt
/home/lode00/den.sh
/home/lode00/ex.sh
/home/lode00/new_text.txt
/home/lode00/awk2.txt
/home/lode00/out.log
/home/lode00/err.log
/home/lode00/^C
/home/lode00/calc.sh
/home/lode00/.rpmmacros
/home/lode00/rpmbuild/SOURCES/home-0.0.1.tar.gz
/home/lode00/rpmbuild/SPECS/home.spec
/home/lode00/rpmbuild/SRPMS/home-0.0.1-1.el7.src.rpm
/home/lode00/home-0.0.1/home7.sh
/home/lode00/home-0.0.1/home7.txt
/home/lode00/hello.spec
```
* Find an inode number and a hard links count for the root directory. The hard link count should be about 17. Why?
```bash
[root@localhost /]# stat / | grep Links
Device: fd00h/64768d    Inode: 64          Links: 19
```
Всего у нас обычно 17 жестких ссылок, так как  файловая система Linux состоит из 17 каталогов.
(https://pingvinus.ru/note/linux-directories-structure)
```bash
[root@localhost /]# ls -la
total 20
dr-xr-xr-x.  19 root root  251 Dec  7 05:36 .
dr-xr-xr-x.  19 root root  251 Dec  7 05:36 ..
lrwxrwxrwx.   1 root root    7 Nov 25 11:23 bin -> usr/bin
dr-xr-xr-x.   5 root root 4096 Dec  2 10:27 boot
drwxr-xr-x.  20 root root 3080 Dec 24 03:48 dev
drwxr-xr-x.  80 root root 8192 Dec 24 05:28 etc
drwxr-xr-x.  16 root root 4096 Dec 14 16:12 home
lrwxrwxrwx.   1 root root    7 Nov 25 11:23 lib -> usr/lib
lrwxrwxrwx.   1 root root    9 Nov 25 11:23 lib64 -> usr/lib64
drwxr-xr-x.   2 root root    6 Apr 11  2018 media
drwxr-xr-x.   2 root root    6 Apr 11  2018 mnt
drwxr-xr-x.   2 root root   19 Dec 14 16:12 opt
dr-xr-xr-x. 107 root root    0 Dec 24 03:48 proc
dr-xr-x---.   5 root root  205 Dec 24 03:20 root
drwxr-xr-x.  23 root root  700 Dec 24 07:51 run
lrwxrwxrwx.   1 root root    8 Nov 25 11:23 sbin -> usr/sbin
drwxr-xr-x.   3 root root   19 Dec  7 05:36 share
drwxr-xr-x.   3 root root   19 Dec  7 03:28 shares
drwxr-xr-x.   2 root root    6 Apr 11  2018 srv
dr-xr-xr-x.  13 root root    0 Dec 24 03:48 sys
drwxrwxrwt.   9 root root  224 Dec 24 07:47 tmp
drwxr-xr-x.  13 root root  155 Nov 25 11:23 usr
drwxr-xr-x.  19 root root  267 Nov 25 11:40 var
```
* Check what inode numbers have "/" and "/boot" directory. Why?
```bash
[root@localhost /]# (stat / | grep Inode ) &&  (stat /boot | grep Inode )
Device: fd00h/64768d    Inode: 64          Links: 19
Device: 801h/2049d      Inode: 64          Links: 5
```
 Оба они смонтированы в корневой файловой системе 
* Check the root directory space usage by du command. Compare it with an information from df. If you find differences, try to find out why it happens.
```bash
[lode00@localhost ~]$ du -hs / 
du: cannot read directory ‘/home/eve’: Permission denied
du: cannot read directory ‘/home/Test’: Permission denied
du: cannot read directory ‘/home/students’: Permission denied
du: cannot read directory ‘/home/glen’: Permission denied
du: cannot read directory ‘/home/antony’: Permission denied
du: cannot read directory ‘/home/lesly’: Permission denied
du: cannot read directory ‘/home/holmes’: Permission denied
du: cannot read directory ‘/home/watson’: Permission denied
du: cannot read directory ‘/home/lestrade’: Permission denied
du: cannot read directory ‘/home/gregson’: Permission denied
du: cannot read directory ‘/home/jones’: Permission denied
du: cannot read directory ‘/shares/cases’: Permission denied
2.6G    /
```
```bash
[lode00@localhost ~]$ df -h /
Filesystem               Size  Used Avail Use% Mounted on
/dev/mapper/centos-root   17G  2.6G   15G  16% /
```
Разница заключается в том, что du – выполняет запрос непосредственно к каждому найденному файлу в разделе, а df – к файловой системе.
* Check disk space usage of /var/log directory using ncdu
```bash
[lode00@localhost log]$  ncdu -x /var/log
 Total disk usage:   3.7 MiB  Apparent size:   3.9 MiB  Items: 64
 ```
