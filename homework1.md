 ## Homework # Denis Lobach
  
##  1) Внутри директории /usr/share/man (хранилище встроенной документации) находятся каталоги, разбитые по секциям разделов помощи (man1, man2, man3) и по языкам (es, fr, ru).Используя команду ls, необходимо вывести на экран все файлы, которые расположены в секционных директориях /usr/share/man/manX и содержат слово "config" в имени.Одним вызовом ls найти все файлы, содержащие слово "system" в каталогах /usr/share/man/man1 и /usr/share/man/man7 # 
  
[lode00@localhost man]$  find /usr/share/man/man?/*config*

[lode00@localhost man]$ ls /usr/share/man/man?/*config*

[lode00@localhost man]$ ls /usr/share/man/man[1,7]/*system*


## 2) Самостоятельно изучить команду find, предназначенную для поиска файлов/папок по заданным условиям (man find, find --help).Найти в директории /usr/share/man все файлы, которые содержат слово "help" в имени, найти там же все файлы, имя которых начинается на "conf".Какие действия мы можем выполнить с файлами, найденными командой find (не запуская других команд)? Приведите любой пример с комментарием.
### На самом деле все описано и так на различных сайтах (https://www.tecmint.com/35-practical-examples-of-linux-find-command/) #

Лично я пользуюсь командой find / -mmin -60, чтобы проверять какие файлы изменил.
[lode00@localhost man]$ find /usr/share/man | grep -E 'help|conf'

## 3) При помощи команд head и tail, выведите последние 2 строки файла /etc/fstab и первые 7 строк файла /etc/yum.confЧто произойдёт, если мы запросим больше строк, чем есть в файле? Попробуйте выполнить это на примере, используя команду wc (word cound) для подсчёта количества строк в файле.

[lode00@localhost man]$ { head -7 /etc/yum.conf & tail -2 /etc/fstab; } >den.txt
[lode00@localhost man]$ cat /tmp/den.txt
UUID=a0c33895-c355-4f5b-834a-42c01128c258 /boot                   xfs     defaults        0 0
/dev/mapper/centos-swap swap                    swap    defaults        0 0
[main]
cachedir=/var/cache/yum/$basearch/$releasever
keepcache=0
debuglevel=2
logfile=/var/log/yum.log
exactarch=1
obsoletes=1

[lode00@localhost man]$tail -100 /tmp/den.txt
UUID=a0c33895-c355-4f5b-834a-42c01128c258 /boot                   xfs     defaults        0 0
/dev/mapper/centos-swap swap                    swap    defaults        0 0
[main]
cachedir=/var/cache/yum/$basearch/$releasever
keepcache=0
debuglevel=2
logfile=/var/log/yum.log
exactarch=1
obsoletes=1
[lode00@localhost man]$ wc /tmp/den.txt
  9  19 297 /tmp/den.txt


## 4) Создайте в домашней директории файлы file_name1.md, file_name2.md и file_name3.md. Используя {}, переименуйте:
## file_name1.md в file_name1.textdoc
## file_name2.md в file_name2
## file_name3.md в file_name3.md.latest
## file_name1.textdoc в file_name1.txt
Да, тут накосячил прям сильно, исправил.
[lode00@localhost ~]$ touch file_name{1..3}.md &&  mv file_name1{.md,.textdoc} && mv file_name2{.md,} && mv file_name3.md{,.latest}
[lode00@localhost ~]$ mv file_name1.{textdoc,txt}

## 5) Перейдите в директорию /mnt. Напишите как можно больше различных вариантов команды cd, с помощью которых вы можете вернуться обратно в домашнюю директорию#
вашего пользователя. 
Различные относительные пути также считаются разными вариантами
1 путь сразу: [lode00@localhost mnt]$ cd /home/lode00
2 такой: 
[lode00@localhost mnt]$ cd ..
[lode00@localhost /]$ cd home
[lode00@localhost home]$ cd lode00
3 вариант [lode00@localhost mnt]$ cd
4 вариант [lode00@localhost mnt]$ cd ~

## 6) Создайте одной командой в домашней директории 3 папки new, in-process, processed. При этом in-process должна содержать в себе еще 3 папки tread0, tread1, tread2.
## Далее создайте 100 файлов формата data[[:digit:]][[:digit:]] в папке new
## Скопируйте 34 файла в tread0 и по 33 в tread1 и tread2 соответственно. Выведете содержимое каталога in-process одной командой
## После этого переместите все файлы из каталогов tread в processed одной командой. Выведете содержимое каталога in-process и processed опять же одной командой
## Сравните количество файлов в каталогах new и processed при помощи изученных ранее команд, если они равны удалите файлы из new
## ** Сравнение количества и удаление сделано при помощи условия#

[lode00@localhost ~]$ mkdir -p new in-process/tread{0..2} processed

[lode00@localhost new]$  touch $(seq -f 'a%g' 0 99)
[lode00@localhost new]$ ls
a0   a12  a16  a2   a23  a27  a30  a34  a38  a41  a45  a49  a52  a56  a6   a63  a67  a70  a74  a78  a81  a85  a89  a92  a96
a1   a13  a17  a20  a24  a28  a31  a35  a39  a42  a46  a5   a53  a57  a60  a64  a68  a71  a75  a79  a82  a86  a9   a93  a97
a10  a14  a18  a21  a25  a29  a32  a36  a4   a43  a47  a50  a54  a58  a61  a65  a69  a72  a76  a8   a83  a87  a90  a94  a98
a11  a15  a19  a22  a26  a3   a33  a37  a40  a44  a48  a51  a55  a59  a62  a66  a7   a73  a77  a80  a84  a88  a91  a95  a99

[lode00@localhost new]$ cp a{0..33} ~/in-process/tread0 && cp a{34..66} ~/in-process/tread1 && cp a{67..99} ~/in-process/tread2 && tree ~/in-proccess/

[lode00@localhost in-process]$ mv tread0/a{0..33} tread1/a{34..66} tread2/a{67..99} ~/processed/ && tree


Для сравнения кол-ва файлов и удаления если одинаковое кол-во - написал скрипт на bash:

#!/bin/bash
a=`ls -l /home/lode00/processed/ | wc | awk '{print $1}'`
b=`ls -l /home/lode00/new/ | wc | awk '{print $1}'`
if [ $a == $b ]; then
rm -rf /home/lode00/new/*
else "cодержимое каталогов разное!!"
fi


# 7.) Получить разворачивание фигурных скобок для выражения. Согласно стандартному поведению bash, стандартного для CentOS 7, скобки в приведённом ниже выражении развёрнуты не будут. Необходимо найти способ получить ожидаемый вывод.
## a=1; b=3
## echo file{$a..$b}
## Необходимо предоставить модицицированную команду, результатом которой является следующий вывод: 
## file1 file2 file3#

Исправил в самом конце.
[lode00@localhost ~]$ for x in `seq $a $b`; do echo -e  "file$x";   done
file1
file2
file3
Однако через sed работает все на ура.
[lode00@localhost ~]$ echo -e `seq $a $b | sed 's/^/file/'`
file1 file2 file3
Или через xargs, если нужно чтобы работал именно echo file{$a..$b}:
[lode00@localhost ~]$echo file{$a..$b} | xargs -I@ bash -c 'echo @'
file1 file2 file3





