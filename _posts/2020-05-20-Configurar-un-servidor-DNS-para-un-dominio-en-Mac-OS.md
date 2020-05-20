---
layout: post
title: Configurar un servidor DNS para un dominio en Mac OS
category: 2020
description: Comandos para establecer un servidor DNS en concreto para un dominio específico en un equipo Mac OS
---

Mac OS tiene el concepto de resolvers, que permiten establecer los servidores DNS y sus opciones para un determinado nombre de dominio. Puede consultarse la configuración actual con el comando scutil.

```shell
scutil --dns
```

Para añadir un nuevo resolver, hay que crear la carpeta /etc/resolver, y crear un fichero con el nombre del dominio y una entrada con la dirección IP del servidor DNS para ese nombre de dominio. Este es un ejemplo:

```shell
sudo mkdir /etc/resolver
sudo echo 'nameserver 192.168.42.2' > /etc/resolver/pablogarciaarevalo.com
```
Tras esto, con el comando scutil anterior puede verificarse la entrada del nuevo resolver. A partir de aquí, podremos resolver los nombres del dominio especificado desde el equipo Mac OS.
