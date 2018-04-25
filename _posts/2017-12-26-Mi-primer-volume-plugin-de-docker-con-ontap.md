---
layout: post
title: Mi primer volume plugin de docker con ONTAP
category: 2017
description: Procedimiento de la instalación del plugin de Docker, los requisitos para la integración con las cabinas de almacenamiento de NetApp, y un ejemplo básico de ejecución con NFS en ONTAP
---

El software de contenedores [docker](https://www.docker.com/) habilita la integración de volúmenes con sistemas de almacenamiento de datos externos, permitiendo la existencia de datos persistentes a través de los [plugins de volúmen](https://docs.docker.com/engine/extend/plugins_volume/).

Existe un 'volume plugin' para las cabinas de [NetApp](https://store.docker.com/plugins/netapp-docker-volume-plugin-ndvp) ONTAP, E-series y SolidFire. Este plugin es open source y puede verse el código en el repositorio de [GitHub](https://github.com/NetApp/netappdvp).

### Instalación

El primer paso es la instalación del software docker en el sistema operativo correspondiente. El procedimiento para cada uno es detallado en la propia [documentación](https://docs.docker.com/engine/installation/) de Docker. En el caso de Fedora simplemente es necesario instalarlo a través del comando:

```shell
$ yum install docker
```

Antes de comenzar con la integración del plugin de Docker con ONTAP, es siempre recomendable probar el funcionamiento de docker con un contenedor simple tipo [Hello World](https://hub.docker.com/_/hello-world/) y [montar un volúmen NFS](https://library.netapp.com/ecm/ecm_download_file/ECMLP2495188) desde la cabina ONTAP que se desea utilizar.

### Requisitos

Para poder delegar el control de la gestión de los volúmenes a los aplicativos, solo la primera vez el administrador del almacenamiento debería revisar en la cabina ONTAP que:

- Exista y esté habilitado un usuario en el SVM (Storage Virtual Machine) con los permisos necesarios.

```shell
cluster1::> security login show -vserver svm1 -user-or-group-name vsadmin

                             Authentication             Acct   Is-Nsswitch
User/Group Name  Application Method    Role Name        Locked Group
---------------- ----------- --------- ---------------- ------ -----------
vsadmin          http        password  vsadmin          no     no
vsadmin          ontapi      password  vsadmin          no     no
vsadmin          ssh         password  vsadmin          no     no
```

- En el SVM que se desea utilizar existe una LIF (Logical Interface) para la gestión y una LIF para los datos.

```shell
cluster1::> network interface show -vserver svm1 -fields address,data-protocol,firewall-policy

vserver lif                     data-protocol address       firewall-policy
------- ----------------------- ------------- ------------- ---------------
svm1    svm1_admin_lif1         none          192.168.0.130 mgmt
svm1    svm1_cifs_nfs_lif1      nfs,cifs      192.168.0.135 data
```

- Los volúmenes existentes y la política de exportación NFS aplicada es correcta.

```shell
cluster1::> volume show -vserver svm1 -fields junction-path, policy

vserver volume            policy  junction-path
------- ----------------- ------- ------------------
svm1    svm1_root         default /
```

- La política aplicada tenga las reglas necesarias para que se pueda montar los volúmenes NFS.

```shell
cluster1::> export-policy rule show -vserver svm1 -policyname default
             Policy          Rule    Access   Client                RO
Vserver      Name            Index   Protocol Match                 Rule
------------ --------------- ------  -------- --------------------- ---------
svm1         default         1       nfs      0.0.0.0/0             any
```

### Ejemplo

Tras esto y tal y como se muestra en la [documentación](http://netappdvp.readthedocs.io/en/latest/quick_start.html) del volume plugin de NetApp, lo siguiente que hay que realizar es crear el directorio de trabajo para el plugin y crear un fichero [JSON](https://en.wikipedia.org/wiki/JSON).

```shell
$ mkdir /etc/netappdvp

$ cat << EOF > /etc/netappdvp/config.json
{
    "version": 1,
    "storageDriverName": "ontap-nas",
    "managementLIF": "192.168.0.101",
    "dataLIF": "192.168.0.135",
    "svm": "svm1",
    "username": "admin",
    "password": "Netapp1!",
    "aggregate": "aggr1_01"
}
EOF
```

Tras ello ya es posible la instalación del plugin de NetApp como se muestra a continuación.

```shell
$ docker plugin install netapp/ndvp-plugin:17.07 --alias netapp --grant-all-permissions
```

Se  verifica que el estado del plugin es correcto con el siguiente comando.
  
```shell
$ docker plugin ls
ID                  NAME                DESCRIPTION                          ENABLED
0f7575ad513c        netapp:latest       nDVP - NetApp Docker Volume Plugin   true
```

Y se ejecuta la creación de un volumen según se ha definido previamente en el fichero JSON:

```shell
docker volume create -d netapp --name my_data -o size=10g
```

Se puede comprobar los volúmenes existentes:

```shell
$ docker volume ls
DRIVER              VOLUME NAME
netapp:latest       my_data
```

Y ver los detalles de un volumen en concreto:

```shell
$ docker volume inspect my_data
[
    {
        "CreatedAt": "0001-01-01T00:00:00Z",
        "Driver": "netapp:latest",
        "Labels": {},
        "Mountpoint": "/var/lib/docker/plugins/0f7575ad513c26bb75843bd7650dfa2d90cb083e1136e5a716de71093b3566f9/rootfs",
        "Name": "my_data",
        "Options": {
            "size": "10g"
        },
        "Scope": "global",
        "Status": {
            "Snapshots": null
        }
    }
]
```

Desde la cabina ONTAP se puede verificar que se ha generado el volumen correctamente:

```shell
cluster1::> volume show -vserver svm1 -volume *my_data*
Vserver   Volume       Aggregate    State      Type       Size  Available Used%
--------- ------------ ------------ ---------- ---- ---------- ---------- -----
svm1      netappdvp_my_data aggr1_01 online    RW         10GB     9.50GB    5%
```

Una vez creado el volumen persistente en la cabina ONTAP, se pueden ejecutar uno o varios contenedores montando este volumen persistente desde uno o varios hosts.

En el ejemplo de a continuación se ejecuta un contenedor usando el volumen creado y montándolo sobre el path /data. Se observa que el volumen se ha montado desde dentro del contenedor.

```shell
$ docker run -it --name mycont -v my_data:/data busybox /bin/sh

$ mount | grep my_data
192.168.0.135:/netappdvp_my_data on /data type nfs (rw,relatime,vers=3,rsize=65536,wsize=65536,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,mountaddr=192.168.0.135,mountvers=3,mountport=635,mountproto=udp,local_lock=none,addr=192.168.0.135)
```