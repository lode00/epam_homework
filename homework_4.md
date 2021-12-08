## Task 1: Users and groups
* Используйте команды: groupadd, useradd, passwd, chage и другие.Создайте группу sales с GID 4000 и пользователей bob, alice, eve c основной группой sales. 
```bash
[root@localhost ~]# groupadd -g 4000 sales
[root@localhost ~]# useradd -g sales bob
[root@localhost ~]# useradd -g sales alice
[root@localhost ~]# useradd -g sales eve
```
Проверка:
```bash
[root@localhost ~]# cat /etc/group | tail -1
sales:x:4000:
[root@localhost ~]# cat /etc/passwd | tail -3
bob:x:1001:4000::/home/bob:/bin/bash
alice:x:1002:4000::/home/alice:/bin/bash
eve:x:1003:4000::/home/eve:/bin/bash
``` 
* Изменить пользователям пароли
```bash
[root@localhost ~]# passwd bob
[root@localhost ~]# passwd alice
[root@localhost ~]# passwd eve
```
* Все новые аккаунты должны обязательно менять свои пароли каждый 30 дней.

Для этого мы заходим в /etc/login.defs  и ставим  PASS_MAX_DAYS 30 
Также можно изменить командой chage -M 30
```bash
[root@localhost ~]#chage -M 30 alice
[root@localhost ~]#chage -M 30 bob
[root@localhost ~]#chage -M 30 eve
```
Проверка через chage -l
```bash
[root@localhost ~]# chage -l eve
Last password change                                    : Dec 07, 2021
Password expires                                        : Jan 06, 2022
Password inactive                                       : never
Account expires                                         : never
Minimum number of days between password change          : 0
Maximum number of days between password change          : 30
Number of days of warning before password expires       : 7
```
* Новые аккаунты группы sales должны истечь по окончанию 90 дней срока
```bash
[root@localhost ~]# chage -E $(date -d +90days +%Y-%m-%d) alice
[root@localhost ~]# chage -E $(date -d +90days +%Y-%m-%d) bob
[root@localhost ~]# chage -E $(date -d +90days +%Y-%m-%d) eve
Если мы говорим про нового пользователя - то тогда можно к примеру сразу в useradd прописать
[root@localhost ~]# useradd -g sales -e  2022-01-01 Test 
```
* bob должен изменять его пароль каждые 15 дней
```bash
[root@localhost ~]# chage -M 15 bob
```
* Заставьте пользователей сменить пароль после первого логина
```bash
[root@localhost ~]# chage -d 0 bob
[root@localhost ~]# chage -d 0 eve
[root@localhost ~]# chage -d 0 alice
```
## Task 2: Controlling access to files with Linux file system permissions

* Создайте трёх пользователей glen, antony, lesly
```bash
[root@localhost ~]# mkdir /home/students
[root@localhost ~]# groupadd students
[root@localhost ~]# useradd -g students glen
[root@localhost ~]# useradd -g students antony
[root@localhost ~]# useradd -g students lesly
```
* Должен быть возможен только пользовательский и групповой доступ, создание и удаление файлов в /home/students. Файлы, созданные в этой директории, должны автоматически присваиваться группе студентов students.

Тут немного схитрил и использовал вот этот сайт, очень удобно как по мне.
https://chmodcommand.com/chmod-2770
```bash
[root@localhost ~]#chown -R :students /home/students
[root@localhost ~]#chmod 2770 /home/students
```
## Task3: ACL
* От суперпользователя создайте папку /share/cases и создайте внутри 2 файла murders.txt и moriarty.txt.
```bash
[root@localhost ~]# mkdir -p /share/cases && cd "$_" && touch {murders,moriarty}.txt
```
* Создайте группу bakerstreet с пользователями holmes, watson.Создайте группу scotlandyard с пользователями lestrade, gregson, jones.Задайте всем пользователям безопасные пароли.
```bash
[root@localhost cases]# groupadd bakerstreet
[root@localhost cases]# groupadd scotlandyard
[root@localhost cases]# useradd -g bakerstreet holmes &&   useradd -g bakerstreet watson
[root@localhost cases]# useradd -g scotlandyard lestrade && useradd -g scotlandyard gregson && useradd -g scotlandyard jones
[root@localhost cases]# passwd watson 
[root@localhost cases]# passwd holmes 
[root@localhost cases]# passwd jones
[root@localhost cases]# passwd lestrade
[root@localhost cases]# passwd gregson
```
* Директория и всё её содержимое должно принадлежать группе bakerstreet, при этом файлы должны обновляться для чтения и записи для владельца и группы (bakerstreet). У других пользователей не должно быть никаких разрешений

Чтобы на чтение и запись имели только 2 группы, а также пользователь jones имел доступ только на чтение напишем следующее:
```bash
[root@localhost share]# setfacl -m g::-,g:bakerstreet:rw,g:scotlandyard:rw /share/cases &&  setfacl -m u:jones:r /share/cases
[root@localhost share]# setfacl -m g::-,g:bakerstreet:rw,g:scotlandyard:rw /share/cases/* &&  setfacl -m u:jones:r /share/cases/*
```
Для остальных юзеров запретим доступ:
```bash
[root@localhost share]# setfacl -m o::- /share/cases
```
Ну и как вы говорили на занятии, необходимо также добавить дефолтный ACL  (setfacl -d )
```bash
[root@localhost share]# setfacl -d -m g::-,g:bakerstreet:rwx,g:scotlandyard:rwx /share/cases && setfacl -d -m u:jones:r /share/cases
```
