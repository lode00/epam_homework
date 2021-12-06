### 1. Открыть инструкцию по пользованию приложением awk. Найти секцию про использование переменных окружения. Сохранить эту секцию в отдельный файл.
 ```
 [lode00@localhost ~]$  man awk | grep -C12 'ENVIRONMENT VARIABLES' | tail -14 > awk.txt && cat awk.txt 

 ENVIRONMENT VARIABLES
       The  AWKPATH  environment variable can be used to provide a list of directories that gawk
       searches when looking for files named via the -f and --file options.

       For socket communication, two special environment variables can be used  to  control  the
       number    of    retries   (GAWK_SOCK_RETRIES),   and   the   interval   between   retries
       (GAWK_MSEC_SLEEP).  The interval is in milliseconds.  On  systems  that  do  not  support
       usleep(3), the value is rounded up to an integral number of seconds. 

       If POSIXLY_CORRECT exists in the environment, then gawk behaves exactly as if --posix had
       been specified on the command line.  If --lint has been specified, gawk issues a  warning
       message to this effect. 
```
### 2.Написать скрипт, который создаёт файл "task2.txt" директорией выше своего местоположения. В случае ошибки текст ошибки записывается в err.log а пользователю выдаётся сообщение "Error."
```bash
#!/bin/bash
path2=`pwd`
cd ..
if [[ ! "$path2" == "$pwd" ]]; then
touch task2.txt &> ~/err2.log
echo "Error"
fi
```

или

```bash
#!/bin/bash
touch ../task2.txt 
if 2>err2.log; then
echo "Error"
fi
```
### 2*.Если файл уже существует, выдаётся одна ошибка, а если нет прав для его создания - другая.
```bash
#!/bin/bash
path2=`pwd`
cd ..
if [[ ! "$path2" == "$pwd" ]]; then
touch task2.txt &> ~/err2.log
fi
if [[ -f task2.txt ]]; then
echo "Файл уже существует!" » ~/err2.log
fi
```
 
### 3. Создать 2 файла: 1-й - текстовый с указанием абслютного пути до директории. 2-й - скрипт, который при выполнении выводит содержимое директории по указанному в первом файле.
```bash
#!/bin/bash
pwd > pwd.txt
new=$(cat ~/pwd.txt)
tree -a  $new
```
### 3* Скрипт выводит отдельно количество файлов и количество директорий
```bash
#!/bin/bash
pwd > pwd.txt
new=$(cat ~/pwd.txt)
tree -a  $new  |tail -1
```

### 3** Скрипт принимает любое количество записей в первом файле и обрабатывает из последовательно.
Я так понял, что сначла мы задаем наши нужные директории с помощью cat <<EOF , а дальше мы должны просто перевести как в строки наш файл и посмотреть сколько всего файлов есть в сумме для тех директорий, указанных в файле

`[lode00@localhost ~]$cat << EOF >new_text.txt`

```bash
#!/bin/bash
den="$(cat new_text.txt)"
while IFS= read -r line; do
echo "$line"
done <<< $den
tree -a $den  |tail -1
```
