---
layout: post
title: Poner repositorios de CentOS en RHEL
category: 2020
description: Poner repositorios de CentOS en RHEL
---
Revisar y borrar los ficheros necesarios del directorio /etc/yum.repos.d/. Crear un nuevo repositorio centos1.repo en el directorio /etc/yum.repos.d/ con el siguiente contenido:

```posh
[centos]
name=CentOS-7
baseurl=http://ftp.heanet.ie/pub/centos/7/os/x86_64/
enabled=1
gpgcheck=1
gpgkey=http://ftp.heanet.ie/pub/centos/7/os/x86_64/RPM-GPG-KEY-CentOS-7
```

Ejecutar el siguiente comando:
```posh
yum repolist
```
Revisar si se actualiza el sistema operativo o se instala el paquete que se quiere
```posh
yum update -y
yum install -y <nombre_paquete>
```