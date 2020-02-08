---
layout: post
title: ONTAP as a DR para servidor web en docker
category: 2019
description: Caso de uso de despliegue de ONTAP en AWS para recuperación ante desastres de un servidor web basado en contenedores docker
---

Uno de los varios casos de uso de Cloud Volumes ONTAP es la posibilidad de disponer en una nube pública del mismo sistema de almacenamiento ONTAP que se tiene on-premises. Utilizando para recuperación ante desastres permite reducir costes al poder tenerse el sistema de almacenamiento en la nube pública encendido unicamente durante el tiempo que se ejecuta diariamente la replicación.

La arquitectura compuesta por contenedores y ONTAP permite desacoplar la capa de la aplicación y la de los datos permitiendo separar el ciclo de vida de ambos.

### Ejecutándo un servidor web con almacenamiento persistente sobre un volumen de ONTAP

En este ejemplo sencillo se dispone en una cabina ONTAP on-premises de un volumen donde se almacenará, a través de NFS, el contenido de un servicio web.

```shell
clusterlab::> volume show  -volume vol_website
Vserver   Volume       Aggregate    State      Type       Size  Available Used%
--------- ------------ ------------ ---------- ---- ---------- ---------- -----
capacity_server vol_website aggr_n4_DL_Data online RW      1GB    972.6MB    5%
```

Desde el servidor Linux se monta el punto de montaje y se crea contenido simple html.

```shell
[root@docker]# mkdir -p /mnt/mi_website
[root@docker]# mount -t nfs 10.67.216.20:/vol_website /mnt/mi_website/

[root@vm-docker2 mi_website]# vi /mnt/mi_website/index.html
<html>
<body>
Hola !!! Esta es mi website
</body>
</html>
```

Se levanta el servicio web a través de un contenedor apache redirigiendo la carpeta de contenido web al volumen persistente que reside sonre ONTAP.

```shell
[root@docker]# docker run -dit --name mi_website -p 8080:80 -v /mnt/mi_website/:/usr/local/apache2/htdocs/ httpd

Unable to find image 'httpd:latest' locally
latest: Pulling from library/httpd
6ae821421a7d: Pull complete
0ceda4df88c8: Pull complete
24f08eb4db68: Pull complete
c3c78f43bd0f: Pull complete
bd71aacd8d1e: Pull complete
Digest: sha256:d12c036427f436978f2d4397ad2bd6b5b8f7b03003b7a1da084eb228ef25b7d2
Status: Downloaded newer image for httpd:latest
a4d54577c05abfd9d8c5e3bfb2a1339598f3079295b264e901293f679eb7578d
```

```shell
[root@docker]# docker ps
CONTAINER ID        IMAGE               COMMAND              CREATED             STATUS              PORTS                  NAMES
a4d54577c05a        httpd               "httpd-foreground"   10 seconds ago      Up 9 seconds        0.0.0.0:8080->80/tcp   mi_website
```

Puede comprobarse como se puede acceder al contenido web desde el puerto apropiado.

![]({{site.baseurl}}/assets/img/ONTAP-as-a-DR-para-servidor-web-en-docker-001.png)

### Replicando los datos del servicio web

Al haber desacoplado el aplicativo de los datos con el uso de docker, simplemente se ha de replicar los datos hacia otro sistema. Tras haber [Desplegado AWS Cloud Volumes ONTAP]({{site.baseurl}}/2019/01/29/Desplegando-AWS-Cloud-Volumes-ONTAP.html), se utilizará este sistema aprovechando la replicación eficiente de ONTAP llamada SnapMirror.

Accediendo a OnCommand Cloud Manager se ha de añadir el sistema ONTAP on-premises. Para ello habrá sido necesario previamente establecer la conexión via VPN o Direct Connect entre la red física y el VPC de AWS.

![]({{site.baseurl}}/assets/img/ONTAP-as-a-DR-para-servidor-web-en-docker-002.png)

Se selecciona un sistema ONTAP físico

![]({{site.baseurl}}/assets/img/ONTAP-as-a-DR-para-servidor-web-en-docker-003.png)

Se define la dirección IP de gestión del clúster y las credenciales de acceso.

![]({{site.baseurl}}/assets/img/ONTAP-as-a-DR-para-servidor-web-en-docker-004.png)

OnCommand Cloud Manager descubre y cataloga el sistema de almacenamiento ONTAP físico.

![]({{site.baseurl}}/assets/img/ONTAP-as-a-DR-para-servidor-web-en-docker-005.png)

Para replicar contenido de una nube privada a una nube pública, simplemente se tiene que arrastrar una nube sobre otra.

![]({{site.baseurl}}/assets/img/ONTAP-as-a-DR-para-servidor-web-en-docker-006.png)

A partir de aquí completa la información necesaria en forma de asistente. A continuación se establecen las direcciones IPs, LIFs, de réplica a utilizar.

![]({{site.baseurl}}/assets/img/ONTAP-as-a-DR-para-servidor-web-en-docker-007.png)

Se selecciona el volumen o volumenes que se quiere replicar.

![]({{site.baseurl}}/assets/img/ONTAP-as-a-DR-para-servidor-web-en-docker-008.png)

El tipo de disco donde se va a almacenar el volumen replicado.

![]({{site.baseurl}}/assets/img/ONTAP-as-a-DR-para-servidor-web-en-docker-009.png)

El límite de transferencia de la réplica.

![]({{site.baseurl}}/assets/img/ONTAP-as-a-DR-para-servidor-web-en-docker-010.png)

La política de replicación.

![]({{site.baseurl}}/assets/img/ONTAP-as-a-DR-para-servidor-web-en-docker-011.png)

La programación de la replicación.

![]({{site.baseurl}}/assets/img/ONTAP-as-a-DR-para-servidor-web-en-docker-012.png)

Se revisa la configuración y se acepta.

![]({{site.baseurl}}/assets/img/ONTAP-as-a-DR-para-servidor-web-en-docker-013.png)

Una vez que termina la replica se tendrán los datos de la nube privada y la nube pública sincronizados.

![]({{site.baseurl}}/assets/img/ONTAP-as-a-DR-para-servidor-web-en-docker-014.png)

Se podrán ver los detalles de los datos replicados.

![]({{site.baseurl}}/assets/img/ONTAP-as-a-DR-para-servidor-web-en-docker-015.png)

### Replicando el aplicativo del servicio web

Para poder prestar el servicio en AWS hay que juntar el aplicativo y los datos. Una vez que los datos están replicados, con la tecnología de docker es muy sencillo replicar el aplicativo. Para ello se despliega una instancia Linux desde una AMI de AWS.

![]({{site.baseurl}}/assets/img/ONTAP-as-a-DR-para-servidor-web-en-docker-016.png)

Una vez está disponible se verifica el Security Group

![]({{site.baseurl}}/assets/img/ONTAP-as-a-DR-para-servidor-web-en-docker-017.png)

Y se permite acceso a los puertos necesarios del aplicativo, en este ejemplo el servicio web y ssh para la gestión.

![]({{site.baseurl}}/assets/img/ONTAP-as-a-DR-para-servidor-web-en-docker-018.png)


### Ejecutándo el servicio replicado en AWS

Tras comprobar los NFS export policy de Cloud Volumes ONTAP, conectándose a la instancia Linux de AWS se monta el volumen replicado y se lista el contenido de la aplicación.

```shell
[root@ip-172-30-14-247 ~]# mkdir -p /mnt/mi_website
# Hay que crear/modificar los export policy
[root@ip-172-30-14-247 ~]# mount -t nfs 172.30.14.246:/vol_website /mnt/mi_website/
[root@ip-172-30-14-247 ~]# cat /mnt/mi_website/index.html
<html>
<body>
Hola !!! Esta es mi mi_website
</body>
</html>
```

De igual forma, con el mismo comando, que se instancian los contenedores web on-premises, se instancian el contenedor web en AWS

```shell
[root@ip-172-30-14-247 ~]# docker run -dit --name mi_website -p 8080:80 -v /mnt/mi_website/:/usr/local/apache2/htdocs/ httpd
Unable to find image 'httpd:latest' locally
latest: Pulling from library/httpd
6ae821421a7d: Pull complete
0ceda4df88c8: Pull complete
24f08eb4db68: Pull complete
c3c78f43bd0f: Pull complete
bd71aacd8d1e: Pull complete
Digest: sha256:d12c036427f436978f2d4397ad2bd6b5b8f7b03003b7a1da084eb228ef25b7d2
Status: Downloaded newer image for httpd:latest
5bcda071f7773f68865fb6a703af5490e3b8504ec6ba4a045aa4f60f57b25417


[root@ip-172-30-14-247 ~]# docker ps
CONTAINER ID        IMAGE               COMMAND              CREATED             STATUS              PORTS                  NAMES
5bcda071f777        httpd               "httpd-foreground"   15 seconds ago      Up 14 seconds       0.0.0.0:8080->80/tcp   mi_website
```

Esto permite visualizar el mismo servicio con los mismos datos de forma eficiente.

![]({{site.baseurl}}/assets/img/ONTAP-as-a-DR-para-servidor-web-en-docker-019.png)


