---
layout: post
title: Wireshark https
subtitle: Wireshark https
tags: [wireshark,network,wifi]
---

## HTTPS

**HTTPS** o Hypertext Transfer Protocol Secure puede ser uno de los protocolos más molestos de comprender desde la perspectiva del análisis de paquetes y puede resultar confuso comprender los pasos necesarios para analizar los paquetes HTTPS.


### Descripción general del tráfico HTTPS

Antes de enviar información cifrada, el cliente y el servidor deben acordar varios pasos para crear un túnel seguro.

- El cliente y el servidor acuerdan una versión de protocolo
- El cliente y el servidor seleccionan un algoritmo criptográfico
- El cliente y el servidor pueden autenticarse entre sí; este paso es opcional
- Crea un túnel seguro con una clave pública

Podemos comenzar a analizar el tráfico HTTPS mirando los paquetes para el protocolo de enlace entre el cliente y el servidor. A continuación se muestra un paquete **Client Hello** que muestra la capa de registro `SSLv2`, el tipo de protocolo de enlace y la versión `SSL`.

![1](../assets/imgs/wire/1.png)

A continuación se muestra el paquete **Server Hello** que envía información similar al paquete Client Hello, sin embargo, esta vez incluye detalles de la sesión e información del certificado `SSL`

![2](../assets/imgs/wire/2.png)

A continuación se muestra el paquete de intercambio de claves de cliente, esta parte del protocolo de enlace determinará la **clave pública** que se utilizará para cifrar más mensajes entre el cliente y el servidor.

![3](../assets/imgs/wire/3.png)

En el siguiente paquete, el servidor confirmará la clave pública y creará el túnel seguro, todo el tráfico después de este punto se cifrará según las especificaciones acordadas enumeradas anteriormente.

![4](../assets/imgs/wire/4.png)

El tráfico entre el Cliente y el Servidor ahora está encriptado y `necesitará la clave secreta` para desencriptar el flujo de datos que se envía entre los dos hosts.

![5](../assets/imgs/wire/5.png)

----

### Análisis práctico de paquetes HTTPS

Para practicar y poner manos a la obra con los paquetes HTTPS, podemos analizar el PCAP **snakeoil2_070531** y el conjunto de claves de descifrado proporcionado por Wireshark.

Primero necesitamos cargar el PCAP en Wireshark. Navegue a Archivo> Abrir y seleccione el PCAP de snakeoil2.

![6](../assets/imgs/wire/6.png)

Al observar la captura de paquetes anterior, podemos ver que todas las solicitudes están encriptadas. Mirando más de cerca los paquetes, podemos ver el protocolo de enlace **HTTPS**, así como las propias solicitudes cifradas. Echemos un vistazo más de cerca a una de las solicitudes cifradas: Paquete 11.

![7](../assets/imgs/wire/7.png)

Podemos confirmar a partir de los detalles del paquete que los datos de la aplicación están encriptados. Puede utilizar una **clave RSA** en Wireshark para ver los datos sin cifrar. Para cargar una clave RSA navegue a **Editar> Preferencias> Protocolos> TLS> [+]**. Si está utilizando una versión anterior de Wireshark, esta será `SSL` en lugar de `TLS`. Deberá completar las distintas secciones del menú con las siguientes preferencias:

Dirección IP: 127.0.0.1  
Puerto: start_tls  
Protocolo: http  
Archivo de claves: ubicación de la **clave RSA**

![8](../assets/imgs/wire/8.png)

Ahora que tenemos una **clave RSA** importada a Wireshark, si volvemos a la captura de paquetes, podemos ver que el flujo de datos ahora no está encriptado.

![9](../assets/imgs/wire/9.png)

Ahora podemos ver las solicitudes HTTP en flujos de datos sin cifrar. Si miramos más a fondo uno de los detalles del paquete, podemos ver más de cerca el flujo de datos sin cifrar. 

![10](../assets/imgs/wire/10.png)

Al observar los detalles del paquete, podemos ver información muy importante, como el `URI` de solicitud y el `User-Agent`, que puede ser muy útil en aplicaciones prácticas de Wireshark, como la búsqueda de amenazas y la administración de redes.

Ahora podemos usar otras funciones para organizar el flujo de datos, como usar la función de exportación de objetos HTTP , para acceder a esta función, navegue hasta Archivo> Exportar objetos> HTTP

![11](../assets/imgs/wire/11.png)

----

## DNS

### Descripción general de DNS

El protocolo DNS o Servicio de nombres de dominio se utiliza para resolver nombres con direcciones IP.  
Hay un par de cosas que se describen a continuación que debe tener en cuenta al analizar los paquetes DNS .

- Consulta-Respuesta
- Solo servidores DNS
- UDP

Si alguno de estos está fuera de lugar, los paquetes deben examinarse más a fondo y deben considerarse sospechosos.  
A continuación, podemos ver una captura de paquetes con múltiples consultas y respuestas de DNS .

![dns](../assets/imgs/wire/dns.png)

Mirando instantáneamente los paquetes, podemos ver lo que están consultando, esto puede ser útil cuando tiene muchos paquetes y necesita identificar rápidamente el tráfico sospechoso o inusual.

----

## Descripción general del tráfico DNS

**Consulta de DNS :**

Mirando la consulta a continuación, realmente tenemos dos bits de información que podemos usar para analizar el paquete. El primer bit de información que podemos ver es de dónde se origina la consulta, en este caso, es UDP 53, lo que significa que este paquete pasa esa verificación, si era TCP 53, entonces debe considerarse tráfico sospechoso y debe analizarse. más lejos. También podemos ver lo que está consultando, esto puede ser útil con otra información para construir una historia de lo que sucedió.

![dns2](../assets/imgs/wire/dns2.png)

Al analizar los paquetes DNS , realmente necesita comprender su entorno y si el tráfico se consideraría normal o no dentro de su entorno.

Respuesta de DNS :

A continuación vemos un paquete de respuesta, es similar al paquete de consulta, pero también incluye una respuesta que se puede usar para verificar la consulta.

![dns3](../assets/imgs/wire/dns3.png)

----

## Modelo OSI

Esta sección cubre cómo Wireshark usa capas OSI para dividir paquetes y cómo usar estas capas para análisis. Se espera que ya tenga conocimientos básicos sobre qué es el modelo OSI y cómo funciona.

![osi](../assets/imgs/wire/osi.png)

Detalles del paquete:  
Puede hacer doble clic en un paquete en captura para abrir sus detalles. Los paquetes constan de 5 a 7 capas según el modelo OSI. Los repasaremos todos en un paquete HTTP de una captura de muestra.

![osi1](../assets/imgs/wire/osi1.png)

Mirando arriba podemos ver 6 capas distintas del paquete: marco/paquete, fuente [MAC], fuente [IP], protocolo, errores de protocolo, protocolo de aplicación y datos de aplicación. A continuación, repasaremos las capas con más detalle.

**Cuadro (Capa 1)**: esto le mostrará qué cuadro / paquete está mirando, así como detalles específicos de la capa física del modelo OSI.

![osi2](../assets/imgs/wire/osi2.png)

**Fuente [MAC] (Capa 2)**: Esto le mostrará las direcciones `MAC` de origen y destino; de la capa de enlace de datos del modelo OSI.

![osi3](../assets/imgs/wire/osi3.png)

**Fuente [IP] (Capa 3)**: esto le mostrará las direcciones IPv4 de origen y destino; desde la capa de red del modelo OSI.

![osi4](../assets/imgs/wire/osi4.png)

**Protocolo (Capa 4)**: le mostrará los detalles del protocolo utilizado (UDP / TCP) junto con los puertos de origen y destino; de la capa de transporte del modelo OSI.

![osi5](../assets/imgs/wire/osi5.png)

**Protocolo de aplicación (capa 5)**: mostrará detalles específicos del protocolo que se está utilizando, como HTTP , FTP, SMB, etc. Desde la capa de aplicación del modelo OSI.

![osi6](../assets/imgs/wire/osi6.png)

