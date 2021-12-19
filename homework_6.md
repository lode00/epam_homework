## Task 1
### 1.1. SSH to remotehost using username and password provided to you in Slack. Logout from remotehost.
```bash
[lode00@localhost ~]$ ssh Denis_Lobach@18.221.144.175
The authenticity of host '18.221.144.175 (18.221.144.175)' can't be established.
ECDSA key fingerprint is SHA256:Lqk214fPCrlvcsnoj1NGVS+Puxr7lGuEncIdALeLt78.
ECDSA key fingerprint is MD5:01:a1:c8:32:41:5c:9b:4b:0a:cc:f0:4b:7e:66:2b:38.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added '18.221.144.175' (ECDSA) to the list of known hosts.
Denis_Lobach@18.221.144.175's password:
Last login: Thu Dec 16 09:30:33 2021 from 188.242.217.36

       __|  __|_  )
       _|  (     /   Amazon Linux 2 AMI
      ___|\___|___|

https://aws.amazon.com/amazon-linux-2/
```
```bash
[Denis_Lobach@ip-172-31-33-155 ~]$ logout
Connection to 18.221.144.175 closed.
``` 

### 1.2. Generate new SSH key-pair on your localhost with name "hw-5" (keys should be created in ~/.ssh folder).
```bash
[lode00@localhost ~]$ ssh-keygen
Generating public/private rsa key pair.
Enter file in which to save the key (/home/lode00/.ssh/id_rsa): /home/lode00/.ssh/hw-5
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in /home/lode00/.ssh/hw-5.
Your public key has been saved in /home/lode00/.ssh/hw-5.pub.
The key fingerprint is:
SHA256:M83KG+4TY31I5hTuj+lizKEa/Aw8OjAee5dy/nI0IX0 lode00@localhost.localdomain
The key's randomart image is:
+---[RSA 2048]----+
|                 |
|          .      |
|      .  . .     |
|     . o E=      |
|      . SBo.     |
|o. o   +=+= .    |
|.oo * o==+ =     |
| o.+ @.o*oo .    |
|  o.*o*+++.      |
+----[SHA256]-----+
```

### 1.3. Set up key-based authentication, so that you can SSH to  remotehost  without password.
```bash
[lode00@localhost .ssh]$  ssh-copy-id -i hw-5.pub  Denis_Lobach@18.221.144.175
/usr/bin/ssh-copy-id: INFO: Source of key(s) to be installed: "hw-5.pub"
/usr/bin/ssh-copy-id: INFO: attempting to log in with the new key(s), to filter out any that are already installed
/usr/bin/ssh-copy-id: INFO: 1 key(s) remain to be installed -- if you are prompted now it is to install the new keys
Denis_Lobach@18.221.144.175's password:
Number of key(s) added: 1
Now try logging into the machine, with:   "ssh 'Denis_Lobach@18.221.144.175'"
and check to make sure that only the key(s) you wanted were added.
```
### 1.4 SSH to remotehost without password. Log out from remotehost.

Здесь входим с passphrase:
```bash
[lode00@localhost .ssh]$ ssh -i ~/.ssh/hw-5 Denis_Lobach@18.221.144.175
Enter passphrase for key '/home/lode00/.ssh/hw-5':
Last login: Thu Dec 16 09:55:42 2021 from 188.242.217.36

       __|  __|_  )
       _|  (     /   Amazon Linux 2 AMI
      ___|\___|___|

https://aws.amazon.com/amazon-linux-2/
No packages needed for security; 2 packages available
Run "sudo yum update" to apply all updates.
```
Чтобы убрать passphrase мы можем воспользоватьс ssh-keygen -p и заново прописать passphrase
```bash
[lode00@localhost .ssh]$ ssh-keygen -p
```
После этого все заработало:
```bash
[lode00@localhost .ssh]$ ssh -i hw-5 Denis_Lobach@18.221.144.175
Last login: Thu Dec 16 10:05:30 2021 from 188.242.217.36
```
### 1.5. Create SSH config file, so that you can SSH to remotehost simply running `ssh
remotehost` command. As a result, provide output of command `cat ~/.ssh/config`

Сделаем доступ для для user:
```bash
[lode00@localhost .ssh]$ chmod 600 ~/.ssh/config
[lode00@localhost .ssh]$ head config
Host remotehost
  Hostname 18.221.144.175
  User Denis_Lobach
  IdentityFile ~/.ssh/hw-5
[lode00@localhost .ssh]$ ssh remotehost
Last login: Thu Dec 16 10:07:15 2021 from 188.242.217.36
 ```
### 1.6. Using command line utility (curl or telnet) verify that there are some webserver running on port 80 of webserver.  Notice that webserver has a private network IP, so you can access it only from the same network (when you are on remotehost that runs in the same private network). Log out from remotehost.
```bash
[Denis_Lobach@ip-172-31-33-155 ~]$ curl  172.31.45.237:80 | head -n1
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100  1856  100  1856    0     0   386k      0 --:--:-- --:--:-- --:--:--  453k
<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.1//EN" "http://www.w3.org/TR/xhtml11/DTD/xhtml11.dtd"><html><head>
```
```bash
[Denis_Lobach@ip-172-31-33-155 ~]$ logout
Connection to 18.221.144.175 closed.
```
### 1.7. Using SSH setup port forwarding, so that you can reach  webserver from your localhost (choose any free local port you like).
```bash
[lode00@localhost .ssh]$ sudo  lsof -i -P -n | grep LISTEN
sshd      984   root    3u  IPv4  17319      0t0  TCP *:22 (LISTEN)
sshd      984   root    4u  IPv6  17335      0t0  TCP *:22 (LISTEN)
master   1317   root   13u  IPv4  18063      0t0  TCP 127.0.0.1:25 (LISTEN)
master   1317   root   14u  IPv6  18064      0t0  TCP [::1]:25 (LISTEN)
```
```bash
[lode00@localhost .ssh]$ ssh -f -N -L 2021:172.31.45.237:80 remotehost
```
### 1.8 Like in 1.6, but on localhost using command line utility verify that localhost and port you have specified act like webserver, returning same result as in 1.6.
```bash
[lode00@localhost .ssh]$ curl 127.0.0.1:2021 | head -n1
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100  1856  100  1856    0     0   7524      0 --:--:-- --:--:-- --:--:--  7544
<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.1//EN" "http://www.w3.org/TR/xhtml11/DTD/xhtml11.dtd"><html><head>
```

## Task 2:

### 2.1. Imagine your localhost has been relocated to Havana. Change the time zone on the localhost to Havana and verify the time zone has been changed properly (may be multiple commands).
```bash
[root@localhost ~]# date
Sun Dec 19 04:52:02 MSK 2021
[root@localhost ~]# timedatectl set-timezone America/Havana
[root@localhost ~]# date
Sat Dec 18 20:52:31 CST 2021
```
### 2.2. Find all systemd journal messages on localhost, that were recorded in the last 50 minutes and originate from a system service started with user id 81 (single command).
```bash
[root@localhost ~]# journalctl _UID=81 --since "50 minutes ago"
-- Logs begin at Sat 2021-12-18 15:04:31 CST, end at Sat 2021-12-18 20:52:27 CST
Dec 18 20:52:27 localhost.localdomain dbus[651]: [system] Activating via systemd
Dec 18 20:52:27 localhost.localdomain dbus[651]: [system] Successfully activated
```
Или можно сделать 
```bash
[root@localhost ~]# journalctl -S -50min --system _UID=81
-- Logs begin at Sat 2021-12-18 15:04:31 CST, end at Sat 2021-12-18 20:52:27 CST. --
Dec 18 20:52:27 localhost.localdomain dbus[651]: [system] Activating via systemd: service name='org.freedesktop.timedate1' unit='dbus-org.freedesktop.timedate1.service'
Dec 18 20:52:27 localhost.localdomain dbus[651]: [system] Successfully activated service 'org.freedesktop.timedate1'
```
### 2.3. Configure  rsyslogd  by adding  a  rule  to  the  newly created configuration file /etc/rsyslog.d/auth-errors.conf to log all security and authentication messages with thepriority alert and higher to the  /var/log/auth-errors file. Test the newly added log directive with the logger command (multiple commands).

Cоздаем файл:
```bash
[root@localhost ~]# cat << 'EOF' > /etc/rsyslog.d/auth-errors.conf
> authpriv.alert /var/log/auth-errors
> EOF
```
```bash
[root@localhost ~]# systemctl restart rsyslog
[root@localhost ~]# systemctl status rsyslog
● rsyslog.service - System Logging Service
   Loaded: loaded (/usr/lib/systemd/system/rsyslog.service; enabled; vendor preset: enabled)
   Active: active (running) since Sat 2021-12-18 20:59:46 CST; 4s ago
     Docs: man:rsyslogd(8)
           http://www.rsyslog.com/doc/
 Main PID: 2574 (rsyslogd)
   CGroup: /system.slice/rsyslog.service
           └─2574 /usr/sbin/rsyslogd -n

Dec 18 20:59:46 localhost.localdomain systemd[1]: Stopped System Logging Service.
Dec 18 20:59:46 localhost.localdomain systemd[1]: Starting System Logging Service...
Dec 18 20:59:46 localhost.localdomain rsyslogd[2574]:  [origin software="rsyslogd" swVersion="8.24.0-57.el7_9.1" x-pid="2574" x-info="http://www.rsyslog.com"] start
Dec 18 20:59:46 localhost.localdomain systemd[1]: Started System Logging Service.
```
```bash
[root@localhost ~]# logger -p authpriv.alert test
[root@localhost ~]# cat /var/log/auth-errors
Dec 18 21:00:18 localhost lode00: test
```
