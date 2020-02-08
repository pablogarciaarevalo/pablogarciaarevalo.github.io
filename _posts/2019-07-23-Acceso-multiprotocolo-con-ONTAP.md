---
layout: post
title: Acceso multiprotocolo con ONTAP
category: 2019
description: Procedimiento y detalles de acceso multiprotocolo a ficheros a través de SMB y NFS con ONTAP
---

ONTAP, desde prácticamente desde sus inicios, permite el acceso nativo por los protocolos SMB y NFS simultáneamente al mismo volumen a través de los shares y los exports.

![]({{site.baseurl}}/assets/img/Acceso-multiprotocolo-con-ONTAP-001.png)

Los permisos del sistema de ficheros se aplican a nivel de volumen, y existen tres tipos de estilos de seguridad: NTFS, UNIX y mixto. Existe mucha documentación donde se detalla el comportamiento para cada uno de los estilos de seguridad [https://library.netapp.com/ecmdocs/ECMP1196891/html/GUID-0217E8D4-0DEE-420A-8DF9-E0B69FE4C427.html](https://library.netapp.com/ecmdocs/ECMP1196891/html/GUID-0217E8D4-0DEE-420A-8DF9-E0B69FE4C427.html). Un malentendido muy común es que el estilo de seguridad mixto de ONTAP es el necesario para acceder a través de los dos protocolos.

![]({{site.baseurl}}/assets/img/Acceso-multiprotocolo-con-ONTAP-002.png)

Muchos entornos no requieren una gestión de permisos complicados en los dos entornos. Normalmente el acceso a través de SMB posee una estructura de usuarios y permisos más compleja gestionada por Active Directory, y un reducido grupo de servidores con unos usuarios determinados necesitan acceder a los mismos datos pero requieren mantener los permisos. Este escenario que suele ser el más común, se soluciona implantando un estilo de seguridad NTFS a nivel de volumen y haciendo una translación de los usuarios del mundo Unix/Linux al mundo Windows para verificar el acceso. ONTAP, por defecto, hace la traslación entre ambos mundos de forma nativa en el formato miDominio\miUsuario (Windows) <> miUsuario (Linux).

A continuación se muestra este escenario descrito, con acceso al mismo volumen de datos por protocolo SMB con permisos gestionados por Active Directory, y un pequeño grupo de servidores web con PHP accediendo a la información, a través del usuario de PHP www-data. Para gestionar estos permisos se crea un usuario nuevo en el Active Directory con este usuario www-data.

![]({{site.baseurl}}/assets/img/Acceso-multiprotocolo-con-ONTAP-003.png)

Desde ONTAP se crea un volumen con estilo de seguridad NTFS pues es el estilo de seguridad más complejo a gestionar.

```posh
cluster1::> volume show -vserver svm1 -fields security-style
vserver volume   security-style
------- -------- --------------
svm1    vol_www  ntfs
```

Y se establece un share permitiendo el acceso exclusivo a ese usuario de Active Directory. En el acceso a este volumen se establecerían todos los usuarios Windows que se deseen que se tenga acceso.

```posh
cluster1::> vserver cifs share show
Vserver        Share         Path              Properties Comment  ACL
-------------- ------------- ----------------- ---------- -------- -----------
svm1           vol_www       /vol_www          browsable  -        www-data / Full Control
                                               changenotify
                                               oplocks
                                               show-previous-versions
```

En ONTAP se crea localmente este usuario www-data, y previamente el grupo al que pertenece este usuario.

```posh
cluster1::> vserver services unix-group create -name www-data -id 33
cluster1::> vserver services unix-user create -user www-data -id 33 -primary-gid 33
cluster1::> vserver services unix-user show
               User            User   Group  Full
Vserver        Name            ID     ID     Name
-------------- --------------- ------ ------ --------------------------------
svm1           nobody          65535  65535
svm1           pcuser          65534  65534
svm1           root            0      1
svm1           www-data        33     33
```

Desde los clientes Windows se monta el volumen por SMB y se verifica que se tiene acceso.

```posh
C:\Users\Administrator.DEMO>net use f: \\192.168.0.233\vol_www
The password is invalid for \\192.168.0.233\vol_www.

Enter the user name for '192.168.0.233': demo\www-data
Enter the password for 192.168.0.233:
The command completed successfully.
```

Desde los servidores Linux se crea el usuario local www-data y se habilita el acceso.

```posh
[root@rhel1 ~]# groupadd -g 33 www-data
[root@rhel1 ~]# useradd -u 33 -g 33 -d /mnt/www www-data
[root@rhel1 ~]# passwd www-data
```

Se monta el volumen por NFS desde los servidores Linux.

```posh
[root@rhel1 ~]# mkdir /mnt/www-data
[root@rhel1 ~]# mount -t nfs 192.168.0.132:/vol_www /mnt/www-data/
```

Se verifica que el usuario root no tiene acceso, pues ONTAP traslada el usuario de Linux root como el usuario DEMO\Administrator, que no tiene acceso al share.

```posh
[root@rhel1 ~]# ls -altr /mnt/www-data/
ls: cannot open directory /mnt/www-data/: Permission denied
```

Accediendo con el usuario www-data se verifica que se tiene acceso.

```posh
[www-data@rhel1 ~]$ ls -altr /mnt/www-data/
total 8
drwxrwxrwx  2 root root 4096 Jul 22 13:56 .
drwxr-xr-x. 5 root root   42 Jul 22 14:03 ..
drwxrwxrwx  3 root root 4096 Jul 22 14:05 .snapshot
```

Desde ONTAP se puede comprobar las sesiones SMB abiertas y los usuarios que la tienen abierta del mundo Windows y mundo Linux respectivamente.

```posh
cluster1::> vserver cifs session show -fields windows-user,unix-user
node        vserver session-id           connection-id windows-user  unix-user
----------- ------- -------------------- ------------- ------------- ---------
cluster1-01 svm1    14047571662698709004 3364714733    DEMO\www-data www-data
```

Desde el servidor Linux se crea un fichero en el volumen exportado por NFS y se verifica el usuario propietario.

```posh
[www-data@rhel1 ~]$ echo > /mnt/www-data/file_from_linux.txt
[www-data@rhel1 ~]$ ls -altr /mnt/www-data/
total 8
drwxr-xr-x. 5 root     root       42 Jul 22 14:03 ..
drwxrwxrwx  9 root     root     4096 Jul 23 06:05 .snapshot
drwxrwxrwx  2 root     root     4096 Jul 23 06:59 .
-rwxrwxrwx  1 www-data www-data    1 Jul 23 06:59 file_from_linux.txt
```

Desde el servidor Windows se crea un fichero en el volumen exportado por SMB y se verifica el usuario propietario.

```posh
F:\>echo > file_from_windows.txt

F:\>dir
 Volume in drive F is vol_www
 Volume Serial Number is 8024-EB3D

 Directory of F:\

07/23/2019  07:44 PM    <DIR>          .
07/23/2019  07:44 PM    <DIR>          ..
07/23/2019  06:59 AM                 1 file_from_linux.txt
07/23/2019  07:44 PM                13 file_from_windows.txt
               2 File(s)             14 bytes
               2 Dir(s)   5,098,917,888 bytes free
```

![]({{site.baseurl}}/assets/img/Acceso-multiprotocolo-con-ONTAP-004.png)

Se verifica que el usuario del mundo Windows propietario del fichero generado desde el mundo Linux es el mismo.

![]({{site.baseurl}}/assets/img/Acceso-multiprotocolo-con-ONTAP-005.png)

Se comprueba desde el servidor Linux el usuario propietario del fichero creado desde el entorno Windows.

```posh
[www-data@rhel1 ~]$ ls -altr /mnt/www-data/
total 8
drwxr-xr-x. 5 root     root       42 Jul 22 14:03 ..
drwxrwxrwx  9 root     root     4096 Jul 23 06:05 .snapshot
-rwxrwxrwx  1 www-data www-data    1 Jul 23 06:59 file_from_linux.txt
-rwxrwxrwx  1 www-data www-data    0 Jul 23 07:01 file_from_windows.txt
drwxrwxrwx  2 root     root     4096 Jul 23 07:01 .
```

Con comandos avanzados de ONTAP se puede verificar el usuario de cada fichero que coincide con los vistos desde cada uno de los entornos.

```posh
cluster1::*> vserver security file-directory show -vserver svm1 -path /vol_www/file_from_linux.txt

                Vserver: svm1
              File Path: /vol_www/file_from_linux.txt
      File Inode Number: 97
         Security Style: ntfs
        Effective Style: ntfs
         DOS Attributes: 20
 DOS Attributes in Text: ---A----
Expanded Dos Attributes: -
           UNIX User Id: 33
          UNIX Group Id: 33
         UNIX Mode Bits: 777
 UNIX Mode Bits in Text: rwxrwxrwx
                   ACLs: NTFS Security Descriptor
                         Control:0x8004
                         Owner:DEMO\www-data
                         Group:DEMO\Domain Users
                         DACL - ACEs
                           ALLOW-Everyone-0x1f01ff-(Inherited)

cluster1::*> vserver security file-directory show -vserver svm1 -path /vol_www/file_from_windows.txt

                Vserver: svm1
              File Path: /vol_www/file_from_windows.txt
      File Inode Number: 15442
         Security Style: ntfs
        Effective Style: ntfs
         DOS Attributes: 20
 DOS Attributes in Text: ---A----
Expanded Dos Attributes: -
           UNIX User Id: 33
          UNIX Group Id: 33
         UNIX Mode Bits: 777
 UNIX Mode Bits in Text: rwxrwxrwx
                   ACLs: NTFS Security Descriptor
                         Control:0x8004
                         Owner:DEMO\www-data
                         Group:DEMO\Domain Users
                         DACL - ACEs
                           ALLOW-Everyone-0x1f01ff-(Inherited)
```