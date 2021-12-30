## 1. add secondary ip address to you second network interface enp0s8. Each point must be presented with commands and showing that new address was applied to the interface. To repeat adding address for points 2 and 3 address must be deleted (please add deleting address to you homework log) Methods:
  ### 1. using ip utility (stateless)
```bash
[lode00@localhost ~]$ sudo ip addr add 192.168.1.30/24 dev enp0s8 && ip addr show enp0s8
3: enp0s8: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP group defa                                                                                                   ult qlen 1000
    link/ether 08:00:27:79:08:6e brd ff:ff:ff:ff:ff:ff
    inet 10.0.3.15/24 brd 10.0.3.255 scope global noprefixroute dynamic enp0s8
       valid_lft 77357sec preferred_lft 77357sec
    inet 192.168.1.30/24 scope global enp0s8
       valid_lft forever preferred_lft forever
    inet6 fe80::ccb1:8d61:e989:8d2e/64 scope link noprefixroute
    valid_lft forever preferred_lft forever
 ```
 ```bash
[lode00@localhost ~]$ sudo ip addr del 192.168.1.30/24 dev enp0s8
```
### 2. using centos network configuration file (statefull)
```bash
[root@localhost ~]# vim /etc/sysconfig/network-scripts/ifcfg-enp0s8:1
Type=Ethernet
BOOTPROTO=none
ONBOOT=yes
DEVICE=enp0s8:1
IPADDR=192.168.1.30
PREFIX=24
```
```bash
[root@localhost ~]$ reboot
[lode00@localhost ~]$ ip a show enp0s8
3: enp0s8: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP                                                                                                              group default qlen 1000
    link/ether 08:00:27:79:08:6e brd ff:ff:ff:ff:ff:ff
    inet 10.0.3.15/24 brd 10.0.3.255 scope global noprefixroute  dynamic enp0s8
       valid_lft 77357sec preferred_lft 77357sec
    inet 192.168.1.30/24 brd 192.168.1.255 scope global noprefixroute enp0s8:1
       valid_lft forever preferred_lft forever
    inet6 fe80::a00:27ff:fe79:86e/64 scope link
       valid_lft forever preferred_lft forever

[root@localhost ~]# ip addr del 192.168.1.30/24 dev enp0s8
```

###   3. using nmcli utility (statefull)
```bash
[lode00@localhost ~]$ sudo nmcli con mod enp0s8 +ipv4.addresses "192.168.1.30/24"
```
```bash
[root@localhost ~]$ reboot
[lode00@localhost ~]$ ip a show enp0s8
3: enp0s8: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP group default qlen 1000
    link/ether 08:00:27:79:08:6e brd ff:ff:ff:ff:ff:ff
    inet 192.168.1.30/24 brd 192.168.1.255 scope global noprefixroute enp0s8
       valid_lft forever preferred_lft forever
    inet 10.0.3.15/24 brd 10.0.3.255 scope global noprefixroute dynamic enp0s8
       valid_lft 86194sec preferred_lft 86194sec
    inet6 fe80::1c36:2ee4:8d57:750c/64 scope link noprefixroute
       valid_lft forever preferred_lft forever
```
## 2. You should have a possibility to use ssh client to connect to your node using new address from previous step. Run tcpdump in separate tmux session or separate connection before starting ssh client and capture packets that are related to this ssh connection. Find packets that are related to TCP session establish.
![image](https://user-images.githubusercontent.com/62034233/147776260-50c51dad-355c-42df-9004-ab508066735b.png)

```bash
[lode00@localhost ~]$ wc -l /tmp/monitor
788 /tmp/monitor
```
### 3. Close session. Find in tcpdump output packets that are related to TCP session closure.
![image](https://user-images.githubusercontent.com/62034233/147776292-f1103cc2-3b1f-4cec-aa7f-c36744bc07fa.png)

### 4. run tcpdump and request any http site in separate session. Find HTTP request and answer packets with ASCII data in it. Tcpdump command must be as strict as possible to capture only needed packages for this http request.

[lode00@localhost ~]$ curl http://curl.haxx.se
[lode00@localhost ~]$ tcpdump -A -s 0 'tcp port 80 and (((ip[2:2] - ((ip[0]&0xf)
![image](https://user-images.githubusercontent.com/62034233/147776361-1a226e45-f35a-4a96-bfc9-acde7fff8312.png)
