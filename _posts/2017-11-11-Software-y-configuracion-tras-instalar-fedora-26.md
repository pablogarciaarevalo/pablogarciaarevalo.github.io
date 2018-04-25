---
layout: post
title:  Software y configuración tras instalar Fedora 26
category: 2017 
description: Programas y herramientas que instalo, y también hacer algunas personalizaciones, tras el despliegue del sistema operativo Fedora 26 en mi portátil.
---

Programas y herramientas que instalo, y también hacer algunas personalizaciones, tras el despliegue del sistema operativo Fedora 26 en mi portátil.

<!--description-->

### Actualización del sistema operativo:

```shell
$ dnf update
```

### Sublime Text

```shell
$ sudo rpm -v --import https://download.sublimetext.com/sublimehq-rpm-pub.gpg
$ sudo dnf config-manager --add-repo https://download.sublimetext.com/rpm/stable/x86_64/sublime-text.repo
$ sudo dnf install sublime-text
```

Me gustar crear un symbolic links para poder ejecutarlo por comandos.

```shell
$ sudo ln -s /opt/sublime_text/sublime_text /usr/local/bin/sublime
```

E instalar el [Package Control](https://packagecontrol.io/installation#st3)

### Docker

```shell
$ sudo dnf install docker
$ sudo systemctl enable docker
$ sudo systemctl start docker
```

### AWS cli

```shell
$ pip install awscli --user
```

### Ansible:

Dejar preparado el módulo [Ansible for NetApp](http://docs.ansible.com/ansible/latest/list_of_storage_modules.html#netapp)

```shell
$ pip install ansible --user
$ pip install netapp-lib --user
```

Los siguientes comandos muestran que son necesarios más paquetes y la instalación de los mismos.

```shell
$ python -c "from netapp_lib.api.zapi import zapi" && echo $?
$ python -c "from netapp_lib.api.zapi import errors" && echo $?
$ pip install lxml
$ pip install oslo_log --ignore-installed --user
```

### Spotify

```shell
$ dnf config-manager --add-repo=http://negativo17.org/repos/fedora-spotify.repo
$ dnf install spotify
```

### Jekyll

```shell
$ sudo dnf install ruby-devel  
$ sudo dnf install rubygems-devel
$ sudo gem install jekyll
$ gem install bundler	gi
$ sudo ln -s /usr/local/bin/jekyll /usr/bin/jekyll
$ gem install redis-stat
```

### VLC

```shell
$ su -
$ dnf install https://download1.rpmfusion.org/free/fedora/rpmfusion-free-release-$(rpm -E %fedora).noarch.rpm
$ dnf install vlc
```

### Montaje del disco duro interno

Tengo Fedora ejecutándose en un USB externo 3.0 por lo que monto el disco duro interno donde tengo el Windows

```shell
### /etc/fstab
...
/dev/sda2		/mnt/win		ntfs-3g	defaults	0 0
```

### Webex

Desafortunadamente no está soportado el software Webex en Fedora 26. He conseguido poder acceder a sesiones con los siguientes comandos aunque no puedo compartir mi escritorio.

```shell
$ sudo dnf install icedtea-web java-1.8.0-openjdk pangox-compat.i686 libXmu.i686 mesa-libEGL.i686 gtk2.i686 libpng.i686 alsa-lib.i686 libXtst.i686 libart_lgpl.i686

$ sudo /usr/sbin/alternatives --install /usr/lib64/mozilla/plugins/libjavaplugin.so libjavaplugin.so.x86_64 /usr/lib64/IcedTeaPlugin.so 200000
```

Hay un test para ver que funciona [aquí](http://www.webex.com/test-meeting.html).

### Cisco VPN

```shell
$ tar zxv < anyconnect-linux64-4.5.00058-predeploy-k9.tar.gz
$ cd anyconnect-linux64-4.5.00058
$ cd vpn
$ sudo ./vpn_install.sh
(Accept the license agreement)
$ cd ../dart
$ sudo ./dart_install.sh
(Accept the license agreement)
```

Siguiendo los pasos anteriores, la ejecución me falla con el siguiente error:

```shell
$ /opt/cisco/anyconnect/bin/vpnui 
error while loading shared libraries: libpangox-1.0.so.0: cannot open shared object file: No such file or directory
```

Puede resolverse copiando la siguiente librería en el mismo path

```shell
$ cd /usr/lib64
$ cp libpangoxft-1.0.so.0 libpangox-1.0.so.0
```


### Chrome

Instalar la última versión de [Chrome](https://www.google.com/chrome/browser/desktop/index.html).

### Postman

Instalar la última versión de [Postman](https://www.getpostman.com/apps).

### Citrix Receiver

Un buen artículo de Ken Fallon está [aquí](http://kenfallon.com/installing-citrix-on-fedora-21/).

