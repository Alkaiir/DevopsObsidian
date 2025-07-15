
## Изменить владельца файла

  
Изменить владельца файла или директории можно с помощью команды _**chown**_:  
  

```
[user@testhost ~]$ chown user:user temp
[user@testhost ~]$ ls -l temp
-rw-rw-r-- 1 user user 31 Nov 26 11:09 temp
```

  
В параметр этой команде нужно отдать нового владельца и группу (опционально), разделенных двоеточием. Также при изменении владельца директории может быть полезна опция _-R_ — тогда владельцы изменятся и у всего содержимого директории.  
  

## Изменить права доступа файла

  
Эта задача решается с помощью команды _**chmod**_. В качестве примера приведу установку прав «владельцу разрешено чтение, запись и исполнение, группе разрешено чтение и запись, всем остальным — ничего»:  
  

```
[user@testhost ~]$ ls -l temp
-rw-rw-r-- 1 user user 31 Nov 26 11:09 temp
[user@testhost ~]$ chmod 760 temp
[user@testhost ~]$ ls -l temp
-rwxrw---- 1 user user 31 Nov 26 11:09 temp
```

  
Первая 7 (это 0b111 в битовом представлении) в параметре означает «все права для владельца», вторая 6 (это 0b110 в битовом представлении) — «чтение и запись», ну и 0 — это ничего для остальных. Битовая маска состоит из трёх битов: самый младший («правый») бит отвечает за исполнение, следующий за ним («средний») — за запись, и самый старший («левый») — за чтение.  
Также можно выставлять права с помощью специальных символов (_мнемонический синтаксис_). Например, в следующем примере сначала убираются права на исполнение для текущего пользователя, а затем возвращаются обратно:  
  

```
[user@testhost ~]$ ls -l temp
-rwxrw---- 1 user user 31 Nov 26 11:09 temp
[user@testhost ~]$ chmod -x temp
[user@testhost ~]$ ls -l temp
-rw-rw---- 1 user user 31 Nov 26 11:09 temp
[user@testhost ~]$ chmod +x temp
[user@testhost ~]$ ls -l temp
-rwxrwx--x 1 user user 31 Nov 26 11:09 temp
```

  
У этой команды есть много вариантов использования, поэтому советую прочитать про неё подробнее (особенно про мнемонический синтаксис, например, [здесь](https://vps.ua/wiki/change-permissions/)).  
  

## Вывести содержимое бинарного файла

  
Это можно сделать с помощью утилиты _**hexdump**_. Ниже приведены примеры её использования.  
  

```
[user@testhost ~]$ cat temp
Content of a file.
Lalalala...
[user@testhost ~]$ hexdump -c temp
0000000   C   o   n   t   e   n   t       o   f       a       f   i   l
0000010   e   .  \n   L   a   l   a   l   a   l   a   .   .   .  \n
000001f
[user@testhost ~]$ hexdump -x temp
0000000    6f43    746e    6e65    2074    666f    6120    6620    6c69
0000010    2e65    4c0a    6c61    6c61    6c61    2e61    2e2e    000a
000001f
[user@testhost ~]$ hexdump -C temp
00000000  43 6f 6e 74 65 6e 74 20  6f 66 20 61 20 66 69 6c  |Content of a fil|
00000010  65 2e 0a 4c 61 6c 61 6c  61 6c 61 2e 2e 2e 0a     |e..Lalalala....|
0000001f
```

  
С помощью этой утилиты можно вывести данные и в других форматах, однако наиболее часто могут пригодиться именно такие варианты её использования.  
  

## Искать файлы

  
Найти файл по части имени в дереве каталогов можно с помощью команды _**find**_:  
  

```
[user@testhost ~]$ find test_dir/ -name "*le*"
test_dir/file_1
test_dir/file_2
test_dir/subdir/file_3
```

  
Также доступны другие опции и фильтры поиска. Например, так можно найти файлы в папке _test_, созданные более 5 дней назад:  
  

```
[user@testhost ~]$ ls -ltr test
total 0
-rw-rw-r-- 1 user user 0 Nov 26 10:46 temp_clone
-rw-rw-r-- 1 user user 0 Dec  4 10:39 created_today
[user@testhost ~]$ find test/ -type f -ctime +5
test/temp_clone
```

  

## Искать текст в файлах

  
Справиться с этой задачей поможет команда _**grep**_. У неё есть множество вариантов использования, здесь в качестве примера указан самый простой.  
  

```
[user@testhost ~]$ grep -nr "content" test_dir/
test_dir/file_1:1:test content for file_1
test_dir/file_2:1:test content for file_2
test_dir/subdir/file_3:1:test content for file_3
```

  
Один из популярных способов использования команды _grep_ — использование её в конвейере (_pipe_):  
  

```
[user@testhost ~]$ sudo tail -f /var/log/test.log | grep "ERROR"
```

  
Опция _-v_ позволяет сделать эффект _grep_'а обратным — будут выводиться только строки, не содержащие паттерн, переданный в _grep_.  
  

## Смотреть установленные пакеты

  
Универсальной команды нет, потому что всё зависит от дистрибутива Linux и используемого пакетного менеджера. Скорее всего вам поможет одна из следующих команд:  
  

```
yum list installed
apt list --installed
zypper se —installed-only
pacman -Qqe
dpkg -l
rpm -qa
```

  

## Посмотреть, сколько места занимает дерево директорий

  
Один из вариантов использования команды _**du**_:  
  

```
[user@testhost ~]$ du -h -d 1 test_dir/
8,0K test_dir/subdir
20K test_dir/
```

  
Можно менять значение параметра _-d_, чтобы получать более подробную информацию о дереве директорий. Также можно использовать команду в комбинации с _sort_:  
  

```
[user@testhost ~]$ du -h -d 1 test_dir/ | sort -h
8,0K test_dir/subdir
16K test_dir/subdir_2
36K test_dir/
[user@testhost ~]$ du -h -d 1 test_dir/ | sort -h -r
36K test_dir/
16K test_dir/subdir_2
8,0K test_dir/subdir
```

  
Опция _-h_ у команды _sort_ позволяет сортировать размеры, записанные в human readable формате (например, 1K, 2G), опция _-r_ позволяет отсортировать данные в обратном порядке.  
  

## «Найти и заменить» в файле, в файлах в директории

  
Данная операция выполняется с помощью утилиты _**sed**_ (без флага _g_ в конце заменится только первое вхождение «old-text» в строке):  
  

```
sed -i 's/old-text/new-text/g' input.txt
```

  
Можно использовать её для нескольких файлов сразу:  

```
[user@testhost ~]$ cat test_dir/file_*
test content for file_1
test content for file_2
[user@testhost ~]$ sed -i 's/test/edited/g' test_dir/file_*
[user@testhost ~]$ cat test_dir/file_*
edited content for file_1
edited content for file_2
```

  

## Вывести колонку из вывода

  
Справиться с этой задачей поможет _**awk**_. В данном примере выводится вторая колонка вывода команды `_ps ux_`:  
  

```
[user@testhost ~]$ ps ux | awk '{print $2}'
PID
11023
25870
25871
25908
25909
```

  
При этом надо иметь ввиду, что _awk_ обладает гораздо более богатым функционалом, так что при необходимости работы с текстом в командной строке стоит почитать об этой команде подробнее.  
  

## Узнать IP адрес по имени хоста

  
С этим поможет одна из следующих команд:  
  

```
[user@testhost ~]$ host ya.ru
ya.ru has address 87.250.250.242
ya.ru has IPv6 address 2a02:6b8::2:242
ya.ru mail is handled by 10 mx.yandex.ru.

[user@testhost ~]$ dig +short ya.ru
87.250.250.242

[user@testhost ~]$ nslookup ya.ru
Server: 8.8.8.8
Address: 8.8.8.8#53

Non-authoritative answer:
Name: ya.ru
Address: 87.250.250.242
```

  

## Сетевая информация

  
Можно использовать _**ifconfig**_:  
  

```
[user@testhost ~]$ ifconfig
eth0: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 47.89.93.67  netmask 255.255.224.0  broadcast 47.89.95.255
        inet6 fd90::302:57ff:fe79:1  prefixlen 64  scopeid 0x20<link>
        ether 04:01:57:79:00:01  txqueuelen 1000  (Ethernet)
        RX packets 11912135  bytes 9307046034 (8.6 GiB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 14696632  bytes 2809191835 (2.6 GiB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0


lo: flags=73<UP,LOOPBACK,RUNNING>  mtu 65536
        inet 127.0.0.1  netmask 255.0.0.0
        inet6 ::1  prefixlen 128  scopeid 0x10<host>
        loop  txqueuelen 0  (Local Loopback)
        RX packets 10  bytes 866 (866.0 B)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 10  bytes 866 (866.0 B)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0
```

  
А можно и _**ip**_:  
  

```
[user@testhost ~]$ ip a
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host
       valid_lft forever preferred_lft forever
2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP group default qlen 1000
    link/ether 04:01:57:79:00:01 brd ff:ff:ff:ff:ff:ff
    inet 47.89.93.67/19 brd 47.89.95.255 scope global eth0
       valid_lft forever preferred_lft forever
    inet6 fd90::302:57ff:fe79:1/64 scope link
       valid_lft forever preferred_lft forever
3: ip_vti0: <NOARP> mtu 1500 qdisc noop state DOWN group default
    link/ipip 0.0.0.0 brd 0.0.0.0
```

  
При этом, если, например, вас интересует только IPv4, то можно добавить опцию _-4_:  
  

```
[user@testhost ~]$ ip -4 a
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP group default qlen 1000
    inet 47.89.93.67/19 brd 47.89.95.255 scope global eth0
       valid_lft forever preferred_lft forever
```

  

## Посмотреть открытые порты

  
Для этого используют утилиту _**netstat**_. Например, чтобы посмотреть все слушающие TCP и UDP порты с отображением PID'а процесса, слушающего порт, и с числовым представлением порта, нужно использовать ее со следующими опциями:  
  

```
[user@testhost ~]$ netstat -lptnu
```

  

## Информация о системе

  
Получить данную информацию можно с помощью команды _**uname**_.  
  

```
[user@testhost ~]$ uname -a
Linux alexander 3.10.0-123.8.1.el7.x86_64 #1 SMP Mon Sep 22 19:06:58 UTC 2014 x86_64 x86_64 x86_64 GNU/Linux
```

  
Чтобы понять, в каком формате производится вывод, можно обратиться к _help_'у данной команды:  
  

```
[user@testhost ~]$ uname --help
Использование: uname [КЛЮЧ]…
Печатает определенные сведения о системе.  Если КЛЮЧ не задан,
подразумевается -s.

  -a, --all          напечатать всю информацию, в следующем порядке,
                       кроме -p и -i, если они неизвестны:
  -s, --kernel-name  напечатать имя ядра
  -n, --nodename     напечатать имя машины в сети
  -r, --release      напечатать номер выпуска операционной системы
  -v, --kernel-version     напечатать версию ядра
  -m, --machine            напечатать тип оборудования машины
  -p, --processor          напечатать тип процессора или «неизвестно»
  -i, --hardware-platform  напечатать тип аппаратной платформы или «неизвестно»
  -o, --operating-system   напечатать имя операционной системы
      --help     показать эту справку и выйти
      --version  показать информацию о версии и выйти
```

  

## Информация о памяти

  
Чтобы понять, сколько оперативной памяти занято или свободно, можно воспользоваться командой _**free**_.  
  

```
[user@testhost ~]$ free -h
              total        used        free      shared  buff/cache   available
Mem:           3,9G        555M        143M         56M        3,2G        3,0G
Swap:            0B          0B          0B
```

  

## Информация о файловых системах (свободное место на дисках)

  
Команда _**df**_ позволяет посмотреть, сколько места свободно и занято на примонтированных файловых системах.  
  

```
[user@testhost ~]$ df -hT
Файловая система Тип      Размер Использовано  Дост Использовано% Cмонтировано в
/dev/vda1        ext4        79G          21G   55G           27% /
devtmpfs         devtmpfs   2,0G            0  2,0G            0% /dev
tmpfs            tmpfs      2,0G            0  2,0G            0% /dev/shm
tmpfs            tmpfs      2,0G          57M  1,9G            3% /run
tmpfs            tmpfs      2,0G            0  2,0G            0% /sys/fs/cgroup
tmpfs            tmpfs      396M            0  396M            0% /run/user/1001
```

  
Опция _-T_ указывает, что нужно выводить тип файловой системы.  
  

## Информация о задачах и различной статистике по системе

  
Для этого используется команда _**top**_. Она способна вывести разную информацию: например, топ процессов по использованию оперативной памяти или топ процессов по использованию процессорного времени. Также она выводит информацию о памяти, CPU, uptime и LA (load average).  
  

```
[user@testhost ~]$ top | head -10
top - 17:19:13 up 154 days,  6:59,  3 users,  load average: 0.21, 0.21, 0.27
Tasks: 2169 total,   2 running, 2080 sleeping,   0 stopped,   0 zombie
Cpu(s):  1.7%us,  0.7%sy,  0.0%ni, 97.5%id,  0.0%wa,  0.0%hi,  0.1%si,  0.0%st
Mem:  125889960k total, 82423048k used, 43466912k free, 16026020k buffers
Swap:        0k total,        0k used,        0k free, 31094516k cached

    PID USER      PR  NI  VIRT  RES  SHR S %CPU %MEM    TIME+  COMMAND
  25282 user      20   0 16988 3936 1964 R  7.3  0.0   0:00.04 top
   4264 telegraf  20   0 2740m 240m  22m S  1.8  0.2  23409:39 telegraf
   6718 root      20   0 35404 4768 3024 S  1.8  0.0   0:01.49 redis-server
```

  
Эта утилита обладает богатым функционалом, так что если вам надо часто ей пользоваться, лучше ознакомиться с её документацией.  
  

## Дамп сетевого трафика

  
Для перехвата сетевого трафика в Linux используется утилита _**tcpdump**_. Чтобы сдампить трафик на порте 12345, можно воспользоваться следующей командой:  
  

```
[user@testhost ~]$ sudo tcpdump -i any -A port 12345
```

  
Опция _-A_ говорит о том, что мы ходим видеть вывод в ASCII (поэтому это хорошо для текстовых протоколов), _-i any_ указывает, что нас не интересует сетевой интерфейс, _port_ — трафик какого порта дампить. Вместо _port_ можно использовать _host_, либо комбинацию _host_ и _port_ (_host A and port X_). И еще полезной может оказаться опция _-n_ — не конвертировать адреса в хостнеймы в выводе.  
Что если трафик бинарный? Тогда нам поможет опция _-X_ — выводить данные в hex и ASCII:  
  

```
[user@testhost ~]$ sudo tcpdump -i any -X port 12345
```

  
При этом надо учитывать, что в обоих вариантах использования будут выводиться IP пакеты, поэтому в начале каждого из них будут бинарные заголовки IP и TCP. Вот пример вывода для запроса "_123_" посланного в сервер, слушающий порт 12345:  
  

```
[user@testhost ~]$ sudo tcpdump -i any -X port 12345
tcpdump: verbose output suppressed, use -v or -vv for full protocol decode
listening on any, link-type LINUX_SLL (Linux cooked), capture size 262144 bytes
14:27:13.224762 IP localhost.49794 > localhost.italk: Flags [P.], seq 2262177478:2262177483, ack 3317210845, win 342, options [nop,nop,TS val 3196604972 ecr 3196590131], length 5
    0x0000:  4510 0039 dfb6 4000 4006 5cf6 7f00 0001  E..9..@.@.\.....
    0x0010:  7f00 0001 c282 3039 86d6 16c6 c5b8 9edd  ......09........
    0x0020:  8018 0156 fe2d 0000 0101 080a be88 522c  ...V.-........R,
    0x0030:  be88 1833 3132 330d 0a00 0000 0000 0000  ...3123.........
    0x0040:  0000 0000 0000 0000 00                   .........
```