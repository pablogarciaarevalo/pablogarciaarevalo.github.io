---
layout: post
title: Cambiar nombre y dirección IP en CentOS 7
category: 2020
description: Personalizar servidor CentOS con un nuevo nombre y dirección IP
---

### Cambiar el nombre
```posh
hostnamectl set-hostname <nuevo-nombre>
```

### Cambiar la dirección IP
Editar el fichero de red:
```posh
vi /etc/sysconfig/network-scripts/<nombre-interface-red>
```
### Reiniciar
```posh
init 6
```