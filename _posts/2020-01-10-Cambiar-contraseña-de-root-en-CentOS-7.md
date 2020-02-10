---
layout: post
title: Cambiar contraseña de root en CentOS 7
category: 2020
description: Resetear o reiniciar la contraseña de root en CentOS 7
---

1. Seleccionar la opción editar en el menú de arranque de grub
2. Seleccionar la opción editar (e)
3. Ir a la línea de Linux 16 y cambiar 'ro' por 'rw init=/sysroot/bin/sh'
4. Selecciona Control+x para comenzar en modo single user
5. Accede al sistema con el comando
```posh
chroot /sysroot
```
6. Reiniciar la contrasela
```posh
passwd root
```
7. Actualizar la información de SElinux
```posh
touch /.autorelabel
```
8. Salir de chroot
```posh
exit
```
9. Reiniciar 'reboot'
```posh
reboot
```

