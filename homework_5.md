## Processes
* Run a sleep command three times at different intervals
```bash
[lode00@localhost ~]$ sleep 300 &
[1] 1553
[lode00@localhost ~]$ sleep 399 &
[2] 1554
[lode00@localhost ~]$ sleep 488 &
[3] 1555
```
* Send a SIGSTOP signal to all of them in three different ways.
```bash
[lode00@localhost ~]$ kill -s sigstop 1553
[lode00@localhost ~]$ kill -STOP 1554
[lode00@localhost ~]$ kill -n 19  1555
```
* Check their statuses with a job command
```bash
[lode00@localhost ~]$ jobs
[1]   Stopped                 sleep 300
[2]-  Stopped                 sleep 399
[3]+  Stopped                 sleep 488
```
* Terminate one of them. (Any)
```bash
[lode00@localhost ~]$ kill %2

```
* To other send a SIGCONT in two different ways.
```bash
[lode00@localhost ~]$ kill -s sigcont %2
[lode00@localhost ~]$ kill -n 18 %1
```

* Kill one by PID and the second one by job ID
```bash
[lode00@localhost ~]$ kill 1555
[lode00@localhost ~]$ kill %3
```

## systemd
* Write two daemons: one should be a simple daemon and do sleep 10 after a start and then do echo 1 > /tmp/homework, the second one should be oneshot and do echo 2 > /tmp/homework without any sleep
* Make the second depended on the first one (should start only after the first)
* Write a timer for the second one and configure it to run on 01.01.2019 at 00:00
* Start all daemons and timer.

Чего-то было лень ходить по папкам и менять конфиги, поэтому все сделал в одном bash файлике.
```bash
#!/bin/bash

var1=/etc/systemd/system/one.service
var2=/etc/systemd/system/second.service
var3=/etc/systemd/system/second.timer
#Создаем первого демона

if  [ ! -f "$var1" ]; then
   cat << 'EOF' > $var1
[Unit]
Description=First daemon

[Service]
ExecStart=/bin/bash -c 'sleep 10 && echo 1 > /tmp/homework'
RemainAfterExit=yes
EOF
fi

#Создаем второго демона

if  [ ! -f "$var2" ]; then
   cat << 'EOF' > $var2
[Unit]
Description=Second daemon
After=one.service
Wants=one.service

[Service]
Type=oneshot
ExecStart=/bin/bash -c 'echo 2 > /tmp/homework'
RemainAfterExit=yes
EOF
fi

#Cоздаем таймер

if  [ ! -f "$var3" ]; then
   cat << 'EOF' > $var3
[Unit]
Description=Timer Second daemon

[Timer]
OnCalendar=2019-01-01 00:00
Persistent=true
EOF
fi
```
* Check their statuses, timer list and /tmp/homework
Проверка статусов:
```bash
[root@localhost system]# systemctl status second.timer
● second.timer - Timer Second daemon
   Loaded: loaded (/etc/systemd/system/second.timer; static; vendor preset: disabled)
   Active: active (elapsed) since Tue 2021-12-14 23:02:17 MSK; 13s ago

Dec 14 23:02:17 localhost.localdomain systemd[1]: Started Timer Second daemon.
```
```bash
[root@localhost system]# systemctl status second.service
● second.service
   Loaded: loaded (/etc/systemd/system/second.service; static; vendor preset: disabled)
   Active: active (exited) since Tue 2021-12-14 22:54:15 MSK; 8min ago
 Main PID: 1934 (code=exited, status=0/SUCCESS)
````
```bash
[root@localhost system]# systemctl status  one.service
● one.service
   Loaded: loaded (/etc/systemd/system/one.service; static; vendor preset: disabled)
   Active: active (running) since Tue 2021-12-14 23:07:04 MSK; 9s ago
 Main PID: 2085 (bash)
   CGroup: /system.slice/one.service
           ├─2085 /bin/bash -c sleep 10 && echo 1 > /tmp/homework
           └─2087 sleep 10

Dec 14 23:07:04 localhost.localdomain systemd[1]: Stopped one.service.
Dec 14 23:07:04 localhost.localdomain systemd[1]: Started one.service.
```
```bash
[root@localhost system]# head /tmp/homework
1
```

* Stop all daemons and timer
```bash
[root@localhost system]# systemctl stop one.service second.service second.timer
```
## cron/anacron
* Create an anacron job which executes a script with echo Hello > /opt/hello and runs 
every 2 days

 Заходим в /etc/anacrontab и добавляем внизу строку
```bash
SHELL=/bin/sh
PATH=/sbin:/bin:/usr/sbin:/usr/bin
MAILTO=root
# the maximal random delay added to the base delay of the jobs
RANDOM_DELAY=45
# the jobs will be started during the following hours only
START_HOURS_RANGE=3-22

#period in days   delay in minutes   job-identifier   command
1       5       cron.daily              nice run-parts /etc/cron.daily
7       25      cron.weekly             nice run-parts /etc/cron.weekly
@monthly 45     cron.monthly            nice run-parts /etc/cron.monthly
2       0        homework               echo 'echo Hello > /opt/hello'> /home/script.sh && bash /home/script.sh
```
#### Второй вариант  решения:
1.Cоздаем отдельно файл  /home/lode00/anacrhw.sh:
```bash
#!/bin/bash
echo Hello > /opt/hello
```
2.Заходим в /etc/anacrontab и добавляем внизу строку
```bash
SHELL=/bin/sh
PATH=/sbin:/bin:/usr/sbin:/usr/bin
MAILTO=root
# the maximal random delay added to the base delay of the jobs
RANDOM_DELAY=45
# the jobs will be started during the following hours only
START_HOURS_RANGE=3-22

#period in days   delay in minutes   job-identifier   command
1       5       cron.daily              nice run-parts /etc/cron.daily
7       25      cron.weekly             nice run-parts /etc/cron.weekly
@monthly 45     cron.monthly            nice run-parts /etc/cron.monthly
2       0        hwnew                  /home/lode00/anacrhw.sh
```

```bash
[root@localhost opt]# anacron -f
```
* Create a cron job which executes the same command (will be better to create a script for 
this) and runs it in 1 minute after system boot.
```bash
@reboot sleep 60 && echo 'echo Hello > /opt/hello'> /home/cronscript.sh && bash /home/cronscript.sh
```
#### Второй вариант  решения:
1.Cоздаем отдельно файл  /home/lode00/cronhw.sh:
```bash
#!/bin/bash
echo Hello > /opt/hello
```
```bash
[lode00@localhost ~]$crontab -e
@reboot sleep 60 && /home/lode00.cronhw.sh
```
* Restart your virtual machine and check previous job proper execution
```bash
[lode00@localhost ~]$ cat /opt/hello
Hello
```
## lsof
* Run a sleep command, redirect stdout and stderr into two different files (both of them will 
be empty).
```bash
[lode00@localhost ~]$ sleep 3000 1> out.log 2>err.log &
[1] 1561
```
* Find with the lsof command which files this process uses, also find from which file it gain 
stdin.
```bash
[lode00@localhost ~]$ lsof -p 1561
COMMAND  PID   USER   FD   TYPE DEVICE  SIZE/OFF     NODE NAME
sleep   1561 lode00  cwd    DIR  253,0      4096  1068889 /home/lode00
sleep   1561 lode00  rtd    DIR  253,0       251       64 /
sleep   1561 lode00  txt    REG  253,0     33128 50443186 /usr/bin/sleep
sleep   1561 lode00  mem    REG  253,0 106176928 50338587 /usr/lib/locale/locale                                                                                                             -archive
sleep   1561 lode00  mem    REG  253,0   2156592    51838 /usr/lib64/libc-2.17.s                                                                                                             o
sleep   1561 lode00  mem    REG  253,0    163312    51831 /usr/lib64/ld-2.17.so
sleep   1561 lode00    0u   CHR  136,0       0t0        3 /dev/pts/0
sleep   1561 lode00    1w   REG  253,0         0  1068865 /home/lode00/out.log
sleep   1561 lode00    2w   REG  253,0         0  1068866 /home/lode00/err.log
```
#### И откуда же sleep принимает stdin?
u for read and write access, значит я так понял, что /dev/pts/0
Можно и так:
```bash
[lode00@localhost ~]$ lsof -c sleep
 ```


* List all ESTABLISHED TCP connections ONLY with lsof
```bash
[root@localhost ~]# lsof -i TCP -s TCP:ESTABLISHED
COMMAND  PID   USER   FD   TYPE DEVICE SIZE/OFF NODE NAME
sshd    1414   root    3u  IPv4  18414      0t0  TCP localhost.localdomain:ssh->gateway:7211 (ESTABLISHED)
sshd    1514 lode00    3u  IPv4  18414      0t0  TCP localhost.localdomain:ssh->gateway:7211 (ESTABLISHED)
```

