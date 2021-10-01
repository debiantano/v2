---
layout: post
title: Apache Virtual Host
subtitle: "Apache Virtual Host"
tags: [apache]
---

**Apache Virtual Host**: se puede definir como un método para ejecutar varios servidores web en un solo host. Al utilizar este método, una computadora puede albergar miles de sitios web. Los servidores web Apache se han convertido en uno de los métodos de servicio web más populares, ya que son extremadamente flexibles y predominantes.

## Configuración

### Crear estructura del directorio

Crear una estructura de directorios que alojará los datos del sitio que vamos a proporcionar.

```
❯ mkdir /sbin/test
```

```
❯ cat index.html
<h1>Test</h1>
<marquee>Virtual Hosting</marquee>
```

### Editar ports.conf

Edite el archivo `/etc/apache2/ports.conf` y agreguo `Listen 127.0.0.1:9090` antes de `Listen 80`.

```
Listen 127.0.0.1:9090
Listen 80

<IfModule ssl_module>
    Listen 443
</IfModule>

<IfModule mod_gnutls.c>
    Listen 443
</IfModule>
```

### Crear archivo para host virtual

Creo un archivo `test.conf` en la ruta `/etc/apache2/sites-available`.

**DocumentRoot**: Ruta de la página web  
**Servername**: Nombre de host  
**AllowOverride**: Tipos de directivas permitidas en archivos .htaccess  
**AllowEncodedSlashes:** Determina si se permite el paso de separadores de ruta codificados en URL.  
**FollowSymlinks**: El servidor seguirá enlaces simbólicos en este directorio.  

```
<VirtualHost 127.0.0.1:9090>
    DocumentRoot /sbin/test/
    Servername localhost

    AllowEncodedSlashes NoDecode
    <Directory "/sbin/test/">
        Require all granted
        AllowOverride All
        options FollowSymlinks MultiViews
    </Directory>
</VirtualHost>
```

### Habilitar los nuevos archivos

> a2ensite: habilita o deshabilita un sitio/host virtual apache2.

```
> sudo a2ensite test.conf
> sudo systemctl reload apache2 reload
```

### Comprobamos el host

```
❯ curl -s http://127.0.0.1:9090

<h1>Test</h1>
<marquee>Virtual Hosting</marquee>

```
