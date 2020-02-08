---
layout: post
title: Configuración de S3FS con StorageGRID
category: 2018
description: Pasos necesarios para la instalación y configuración del software S3FS en Linux y la integración con el sistema de almacenamiento de objetos StorageGRID Webscale de NetApp
---

[S3FS](https://github.com/s3fs-fuse/s3fs-fuse) es un cliente open-source gratuito por línea de comandos que permite montar un bucket [S3](https://aws.amazon.com/documentation/s3/) como si se tratase de un sistema de ficheros, preservando el formato nativo de los objetos al igual que lo hace la herramienta [S3CMD]({{site.baseurl}}/2018/01/22/Configuracion-de-s3cmd-con-StorageGRID.html).

### Instalación

En la documentación oficial de S3FS se dispone de los pasos necesarios para la instalación de diferentes sistemas operativos. A acontinuación se muestran los pasos para un sistema CentOS 7.

```shell
sudo yum install automake fuse fuse-devel gcc-c++ git libcurl-devel libxml2-devel make openssl-devel

git clone https://github.com/s3fs-fuse/s3fs-fuse.git
cd s3fs-fuse
./autogen.sh
./configure
make
sudo make install
```

### Configuración

Una vez instalado hay que almacenar las claves S3 en un fichero de credenciales.

```shell
echo MYS3ACCESSKEY:MYS3SECRETKEY >  ~/.passwd-s3fs
chmod 600  ~/.passwd-s3fs
```

Y crear un punto de montaje donde montar el bucket S3.

```shell
mkdir /mnt/s3fs
```

### Ejemplo

Tras ello hay que montar el bucket S3 sobre el punto de montaje deseado utilizando el software S3FS.

```shell
s3fs s3fs-bucket /mnt/s3fs \
-o passwd_file=~/.passwd-s3fs \
-o url=https://my_gateway_storagegrid.mydomain.com:8082 \
-o sigv2  -o no_check_certificate \
-o use_path_request_style
```

Tras copiar varios ficheros al directorio, se pueden listar estos ficheros de forma habitual.

```shell
[root@Linux1 ~]# cd /mnt/s3fs/
[root@Linux1 s3fs]# ls -al
total 219
drwx------. 1 root root      0 Dec 31  1969 .
drwxr-xr-x. 4 root root     28 Oct  3 01:29 ..
-rw-------. 1 root root   1044 Oct  3 02:20 anaconda-ks.cfg
-rwxr-xr-x. 1 root root 221483 Oct  3 02:20 configure
-rw-r--r--. 1 root root     10 Oct  3 01:39 file1.txt
```

Y nos podemos conectar con el cliente nativo de AWS S3 y listar el contenido del bucket para verificar la consistencia de los ficheros guardados.

```shell
[root@Linux1]# cat ~/.aws/credentials
[storagegrid]
aws_access_key_id = MYS3ACCESSKEY
aws_secret_access_key = MYS3SECRETKEY

aws s3 ls s3://s3fs-bucket/ --profile storagegrid --endpoint-url  https://my_gateway_storagegrid.mydomain.com:8082 --no-verify-ssl
2018-10-03 02:20   1044 s3://s3fs-bucket/anaconda-ks.cfg
2018-10-03 02:20 221483 s3://s3fs-bucket/configure
2018-10-03 01:39     10 s3://s3fs-bucket/file1.txt
```
