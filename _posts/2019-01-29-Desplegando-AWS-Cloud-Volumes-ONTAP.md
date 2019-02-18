---
layout: post
title: Desplegando AWS Cloud Volumes ONTAP
category: 2019
description: Despliegue de Cloud Volumes ONTAP sobre AWS (Amazon Web Services)
---

Cloud Volumes ONTAP es la solución Cloud en modalidad IaaS que permite disponer de un sistema de almacenamiento enterprise dentro de los principales proveedores cloud públicos. En este ejemplo se va a mostrar el procedimiento para su despliegue en AWS Amazon Web Services.

### OnCommand Cloud Manager

OnCommand Cloud Manager (OCCM) es el servicio que permite, entre otras cosas, el despliegue sencillo de Cloud Volumes ONTAP. Para desplegar OCCM simplemente hay que buscarlo en el buscar la AMI en el MarketPlace de AWS.

![]({{site.baseurl}}/assets/img/2019-01-29-Desplegando-AWS-Cloud-Volumes-ONTAP-001.png)

Y lanzarlo.

![]({{site.baseurl}}/assets/img/2019-01-29-Desplegando-AWS-Cloud-Volumes-ONTAP-002.png)

#### Instalación

Se mantiene el tamaño de instancia que viene por defecto.

![]({{site.baseurl}}/assets/img/2019-01-29-Desplegando-AWS-Cloud-Volumes-ONTAP-003.png)

Se modifican los parámetros necesarios.

![]({{site.baseurl}}/assets/img/2019-01-29-Desplegando-AWS-Cloud-Volumes-ONTAP-004.png)

Se mantiene la configuración del almacenamiento por defecto.

![]({{site.baseurl}}/assets/img/2019-01-29-Desplegando-AWS-Cloud-Volumes-ONTAP-005.png)

Se añaden los tags que se quieran.

![]({{site.baseurl}}/assets/img/2019-01-29-Desplegando-AWS-Cloud-Volumes-ONTAP-006.png)

Y se mantiene el grupo de seguridad que la AMI crea por defecto.

![]({{site.baseurl}}/assets/img/2019-01-29-Desplegando-AWS-Cloud-Volumes-ONTAP-007.png)

Se selecciona la clave que se desea.

![]({{site.baseurl}}/assets/img/2019-01-29-Desplegando-AWS-Cloud-Volumes-ONTAP-008.png)

Transcurrido cierto tiempo, la instancia virtual de OnCommand Cloud Manager está preparada para su uso.

![]({{site.baseurl}}/assets/img/2019-01-29-Desplegando-AWS-Cloud-Volumes-ONTAP-009.png)

### Cloud Volumes ONTAP

### Instalación

Accediendo por https a la dirección IP del OnCommand Cloud Manager, se tiene acceso a la consola que permite descubrir e instanciar sistemas ONTAP.

![]({{site.baseurl}}/assets/img/2019-01-29-Desplegando-AWS-Cloud-Volumes-ONTAP-011.png)

Actualmente se permite crear una instancia Cloud Volumes ONTAP tanto en AWS como en Azure en modalidad de instancia simple o doble. En este ejemplo se utilizará la instancia simple de AWS.

![]({{site.baseurl}}/assets/img/2019-01-29-Desplegando-AWS-Cloud-Volumes-ONTAP-012.png)

Se rellenan los datos de las claves de seguridad.

![]({{site.baseurl}}/assets/img/2019-01-29-Desplegando-AWS-Cloud-Volumes-ONTAP-013.png)

Se establece un nombre para la instancia y la contraseña de acceso.

![]({{site.baseurl}}/assets/img/2019-01-29-Desplegando-AWS-Cloud-Volumes-ONTAP-014.png)

Por defecto el proceso selecciona la misma subnet del VPC donde se ha desplegado la instancia de OnCommand Cloud Manager.

![]({{site.baseurl}}/assets/img/2019-01-29-Desplegando-AWS-Cloud-Volumes-ONTAP-015.png)

Se permite la encriptación de datos.

![]({{site.baseurl}}/assets/img/2019-01-29-Desplegando-AWS-Cloud-Volumes-ONTAP-016.png)

Y se permite añadir una licencia de ONTAP que se pueda tener disponible.

![]({{site.baseurl}}/assets/img/2019-01-29-Desplegando-AWS-Cloud-Volumes-ONTAP-017.png)

Se puede escoger entre diferentes formatos preconfigurados en función del caso de uso que se desea utilizar. 

![]({{site.baseurl}}/assets/img/2019-01-29-Desplegando-AWS-Cloud-Volumes-ONTAP-018.png)

Y permite dar de alta la instancia en el sitema de alerta de NetApp.

![]({{site.baseurl}}/assets/img/2019-01-29-Desplegando-AWS-Cloud-Volumes-ONTAP-019.png)

Si se desea se puede crear en el propio asistente un volumen de datos listo para ser consumido.

![]({{site.baseurl}}/assets/img/2019-01-29-Desplegando-AWS-Cloud-Volumes-ONTAP-020.png)

Se revisa la configuración y se termina el asistente.

![]({{site.baseurl}}/assets/img/2019-01-29-Desplegando-AWS-Cloud-Volumes-ONTAP-021.png)

### Verificación

Tras terminar el proceso se crea una nube en el que se va a poder monitorizar el proceso de instalación.

![]({{site.baseurl}}/assets/img/2019-01-29-Desplegando-AWS-Cloud-Volumes-ONTAP-022.png)

Una vez terminado queda la instancia de Cloud Volumes ONTAP disponible para ser utilizada.

![]({{site.baseurl}}/assets/img/2019-01-29-Desplegando-AWS-Cloud-Volumes-ONTAP-023.png)

Desde el punto de vista de AWS se trata de una instancia virtual EC2.

![]({{site.baseurl}}/assets/img/2019-01-29-Desplegando-AWS-Cloud-Volumes-ONTAP-024.png)

Si bien se trata del sistema operativo completo ONTAP con todas sus funciones ejecutándose en Amazon Web Services.

![]({{site.baseurl}}/assets/img/2019-01-29-Desplegando-AWS-Cloud-Volumes-ONTAP-025.png)

