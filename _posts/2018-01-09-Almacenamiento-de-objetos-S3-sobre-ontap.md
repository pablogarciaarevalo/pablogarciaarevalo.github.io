---
layout: post
title: Almacenamiento de objetos S3 sobre ONTAP
category: 2018
description: Caso de uso y configuración de acceso de almacenamiento de objetos S3 sobre las cabinas de NetApp ONTAP usando el software open source Minio.
---

La creciente evolución de las aplicaciones monolíticas a arquitecturas de [microservicios](https://en.wikipedia.org/wiki/Microservices), hace necesaria que el acceso a los datos cambie, y se clasifiquen en datos estructurados y no estructurados para un acceso y tratamiento diferente. Este último tipo de datos (ficheros planos, imágenes, audio, video,...) está teniendo y tendrá un crecimiento de capacidad elevado formando repositorios conocidos como [lagos de datos](https://en.wikipedia.org/wiki/Data_lake).

Ni la transición a las arquitecturas de microservicios es inmediata, ni tampoco lo es la migración de los datos actuales a un formato de almacenamiento de objetos. El protocolo de [Amazon S3](https://aws.amazon.com/s3/) (Simple Storage Service) se ha convertido en un standard de acceso al almacenamiento de objetos que muchas soluciones implementan.

Las cabinas de almacenamiento [ONTAP](https://www.netapp.com/us/products/data-management-software/ontap.aspx) de NetApp no ofrecen actualmente protocolo S3 de forma nativa al igual que tampoco ofrecen [otros protocolos](https://en.wikipedia.org/wiki/List_of_file_transfer_protocols). En el caso de que el equipo de desarrollo quiera empezar a probar el acceso a los datos desde las nuevas aplicaciones con el protocolo S3 para poder valorar alternativas empresariales como [StorageGRID Webscale](https://www.netapp.com/us/products/data-management-software/object-storage-grid-sds.aspx), se puede construir muy rápidamente un servicio S3 aprovechando todas las ventajas de ONTAP con el software [Minio](https://minio.io/) en 'modo gateway' como haríamos si necesitasemos acceso a otros protocolos. Minio es un software de almacenamiento de objetos open source compatible con la API Amazon S3 que se caracteriza por su simplicidad.

### Arquitectura

La siguiente ilustración muestra una arquitectura donde conviven las aplicaciones antiguas y las nuevas que se quieren probar, accediendo a la misma cabina de almacenamiento ONTAP.

![]({{site.baseurl}}/assets/img/Almacenamiento-de-objetos-S3-en-netapp-ontap-001.png)

Las 3 capas (de arriba a abajo) de la imagen anterior tratan de representar lo siguiente: 
* A la izquierda las aplicaciones antiguas. A la derecha las aplicaciones nuevas.
* A la izquierda el acceso desde las aplicaciones antiguas se realiza a través de un export NFS. A la derecha las aplicaciones nuevas acceden, a través de un [balanceador de carga](https://en.wikipedia.org/wiki/Load_balancing_(computing)), a almacenamiento de objetos S3 que se ofrece desde dos contenedores de docker con el software Minio.
* El export NFS y el software Minio pueden acceder a datos diferentes o al mismo conjunto de datos a través de la funcionalidad [FlexClone](http://community.netapp.com/t5/Tech-OnTap-Articles/Back-to-Basics-FlexClone/ta-p/84874).

Se plantean dos casos de uso posibles para esta arquitectura que se detallarán a continuación:
1. Utilizar el mismo conjunto de datos que actualmente las aplicaciones ya tienen accediendo a ONTAP.
2. Crear nuevos volúmenes manteniendo los beneficios que proporciona ONTAP, para que posteriormente el equipo de desarrollo copie los datos que necesite.

### Configuración usando los mismos datos

En el caso de querer utilizar el mismo conjunto de datos que actualmente se ofrece a las aplicaciones por NFS, simplemente habría que realizar un FlexClone del voĺumen utilizado, montar el volumen en los servidores donde se tiene instalado el software docker, y ejecutar el software minio como contenedor. Se crea también un volumen diferente donde Minio guarda la configuración.

##### 1. FlexClone

```shell
cluster1::> volume clone create -vserver svm1 -flexclone new_app_vol -parent-volume legacy_app_vol -type RW -junction-active true -junction-path /new_app_vol
cluster1::> volume create -vserver svm1 -volume new_app_config -aggregate aggr1 -size 5g -state online -junction-path /new_app_config
```

##### 2. Montaje NFS en los hosts

```shell
$ mount 192.168.0.132:/new_app_vol /mnt/new_app_vol
$ mount 192.168.0.132:/new_app_vol /mnt/new_app_config
```

##### 3. Ejecutar minio como contenedor

```shell
$ docker run -p 9000:9000 --name minio1 -e "MINIO_ACCESS_KEY=MYS3ACCESSKEY" -e "MINIO_SECRET_KEY=MYS3SECRETKEY" -v /mnt/new_app_vol:/data -v /mnt/new_app_config:/root/.miniominio/minio server /data
```

### Configuración con nuevos volumenes

Si se desea crear un nuevo volumen para el servicio de almacenamiento de objetos S3 y después volcar la información por otros métodos, el despliegue de los volúmenes se puede delegar totalmente al equipo de desarrollo a través del plugin de docker. Se pueden ver los detalles de cómo configurar [Mi primer volume plugin de docker con ONTAP]({{site.baseurl}}/2017/12/26/Mi-primer-volume-plugin-de-docker-con-ontap.html).

##### 1. Creación de volúmenes

```shell
$ docker volume create -d netapp --name my_data -o size=100g
$ docker volume create -d netapp --name my_config -o size=5g
```

##### 2. Ejecutar minio como contenedor

```shell
$ docker run -p 9000:9000 --name minio1 -e "MINIO_ACCESS_KEY=MYS3ACCESSKEY" -e "MINIO_SECRET_KEY=MYS3SECRETKEY" -v my_data:/data -v my_config:/root/.minio minio/minio server /data
```

### Ejemplo de acceso

Para probar el acceso al almacenamiento de objetos S3 se ha instalado el software [AWS cli](https://aws.amazon.com/cli/) en un equipo Windows y también se ha montado por NFS el volumen de datos en un equipo Linux. Puesto que con esta forma de operar Minio trabaja con el sistema de ficheros que tenga el host, se puede comprobar las lecturas y escrituras de ficheros por NFS y de objetos por S3 al repositorio creado.

```shell
$ mkdir /mnt/minio
$ mount 192.168.0.132:/netappdvp_my_data /mnt/minio/
```

Desde el equipo Windows se configura el cliente AWS y se verifica el acceso sobre el host1 que efectivamente está vacío.

```posh
C:\>aws configure
AWS Access Key ID [****************]: MYS3ACCESSKEY
AWS Secret Access Key [****************]: MYS3SECRETKEY
Default region name [None]:
Default output format [None]:

C:\>aws s3 ls --endpoint-url http://host1.mydomain.local:9000

```

Se crea un bucket desde el host1 y se verifica sobre el host2 que se ha creado correctamente.

```posh
C:\>aws s3 mb s3://new-bucket --endpoint-url http://host1.mydomain.local:9000
make_bucket: new-bucket

C:\>aws s3 ls --endpoint-url http://host2.mydomain.local:9000
2017-12-28 19:09:43 new-bucket
```

Se sube un fichero sobre el host1 como objeto.

```posh
C:\>aws s3 cp ./Downloads/myFile.txt s3://new-bucket--endpoint-url http://host1.mydomain.local:9000
upload: Downloads\myFile.txt to s3://new-bucket/myFile.txt
```

Se verifica desde el NFS montado que el fichero está en el directorio del nuevo bucket.

```shell
$ ls -altr /mnt/minio/new-bucket/
total 8
drwxrwxrwx 5 root root 4096 Dec 28 19:09 ..
-rw-r--r-- 1 root root    0 Dec 28 19:12 myFile.txt
drwxr-xr-x 2 root root 4096 Dec 28 19:12 .
```

Se escribe un nuevo fichero desde el NFS montado.

```posh
$ echo 'blablabla' > /mnt/minio/new-bucket/myOtherFile.txt
$ ls -altr
total 8
drwxrwxrwx 5 root root 4096 Dec 28 19:09 ..
-rw-r--r-- 1 root root    0 Dec 28 19:12 myFile.txt
drwxr-xr-x 2 root root 4096 Dec 28 19:16 .
-rw-r--r-- 1 root root   10 Dec 28 19:16 myOtherFile.txt
```

Y se verifica el acceso desde el host1 y host2.

```posh
C:\>aws s3 ls s3://new-bucket --endpoint-url http://host1.mydomain.local:9000
2017-12-28 19:12:43          0 myFile.txt
2017-12-28 19:16:13         10 myOtherFile.txt

C:\>aws s3 ls s3://new-bucket --endpoint-url http://host2.mydomain.local:9000
2017-12-28 19:12:43          0 myFile.txt
2017-12-28 19:16:13         10 myOtherFile.txt
```
