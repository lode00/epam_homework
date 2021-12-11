 
### Awk 

*  What is the most frequent browser?
```bash 
[lode00@localhost home]$ sudo awk -F"\"" '{print $6}' access.log | sort | uniq -dc | sort -nr | head -1
```
* Show total amount of data which server has provided for each unique ip (i.e. 100500 bytes for 1.2.3.4; 9001 bytes for 5.4.3.2 and so on)
```bash
[lode00@localhost home]$ awk 'BEGIN{ips[$1]=0}{ips[$1]+=$10}END{for (key in ips) print ips[key]" bytes for "key}' access.log | sort
``` 
### Sed

*  Change all browsers to "lynx"
```bash 
[lode00@localhost home]$ sed {s/\"[^\"]*\"/\"lynx\"/3} access.log | head -n3
```
