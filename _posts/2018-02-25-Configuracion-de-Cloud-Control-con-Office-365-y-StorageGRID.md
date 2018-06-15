---
layout: post
title: Configuración de Cloud Control con Office 365 y StorageGRID
category: 2018
description: Procedimiento en la configuración de Cloud Control con Microsoft Office 365 para el uso de StorageGRID como repositorio de backup.
---

Office 365 de Microsoft es uno de los servicios Cloud en modalidad SaaS mas utilizados. Si bien este tipo de modalidad cloud SaaS proporcionan a las empresas muchas ventajas respecto a las aplicaciones tradicionales, la eliminación de las políticas de backup de sus datos no es una de ellas. Esten los datos en una infraestructura on-premises o Cloud pública en cualquiera de sus modalidades, es responsabilidad de las empresas la protección e integridad de los datos. Tal y como explica la [web de Microsoft](https://products.office.com/en-us/business/office-365-online-data-portability): 

**“It’s your data. You own your data and retain all rights, title, and interest in the data your store with Office 365.”**

[Cloud Control](https://cloud.netapp.com/cloud-control) de NetApp es el servicio que permite proteger los servicios Cloud SaaS de amenazas o borrados accidentales. Permite proteger los servicios Outlook, OneDrive y SharePoint de Microsoft Office 365 hacia un almacenamiento de objetos en cloud público como Amazon S3 o Azure, y Cloud privado como [StorageGRID](https://www.netapp.com/us/products/data-management-software/object-storage-grid-sds.aspx)

### Requisitos

En el caso de querer proteger los servicios de Office 365 sobre el almacenamiento de StorageGRID, son necesarios los siguientes requisitos:

- El acceso al almacenamiento de StorageGRID tiene que estar disponible a través de Internet.
- El acceso a StorageGRID necesita de un nombre de dominio con un certificado, en el caso del ejemplo sgws.mydomain.com
- TLS 1.2
- Puerto 8082 abierto y redireccionado
- Un bucket ya creado, en el caso del ejemplo cc-pablogarciaarevalo

Si bien no es un requisito, una buena práctica es toda plataforma de backup es la utilización de un usuario específico. En este ejemplo se ha utilizado el usuario cloudcontrol del dominio garciaarevalo.onmicrosoft.com

![]({{site.baseurl}}/assets/img/Configuracion-de-Cloud-Control-con-Office-365-y-StorageGRID-001.png)

### Configuración

Para realizar la configuración, simplemente hay que acceder a la web [https://cloud.netapp.com/cloud-control](https://cloud.netapp.com/cloud-control) y darse de alta 'Sign Up'.

![]({{site.baseurl}}/assets/img/Configuracion-de-Cloud-Control-con-Office-365-y-StorageGRID-002.png)

Tras completar la información requerida, se nos devuelve a la dirección de correo las credenciales necesarias. Con ellas ya es posible acceder.

![]({{site.baseurl}}/assets/img/Configuracion-de-Cloud-Control-con-Office-365-y-StorageGRID-003.png)

Se aceptan los permisos necesarios para la protección y recuperación del servicio.

![]({{site.baseurl}}/assets/img/Configuracion-de-Cloud-Control-con-Office-365-y-StorageGRID-004.png)

Se continua con el Wizard de configuración.

![]({{site.baseurl}}/assets/img/Configuracion-de-Cloud-Control-con-Office-365-y-StorageGRID-005.png)

Se seleccionan los servicios que se quieren proteger.

![]({{site.baseurl}}/assets/img/Configuracion-de-Cloud-Control-con-Office-365-y-StorageGRID-006.png)

Se selecciona la licencia. Es posible comenzar con una licencia de pruebas y después actualizar a la licencia definitiva.

![]({{site.baseurl}}/assets/img/Configuracion-de-Cloud-Control-con-Office-365-y-StorageGRID-007.png)

Se configura el almacenamiento destino del backup. En el caso del ejemplo se configurará contra un almacenamiento On-premises para proteger los datos del cloud público en una infraestructura propia.

![]({{site.baseurl}}/assets/img/Configuracion-de-Cloud-Control-con-Office-365-y-StorageGRID-008.png)

Se verifica y se guarda la configuración.

![]({{site.baseurl}}/assets/img/Configuracion-de-Cloud-Control-con-Office-365-y-StorageGRID-009.png)

A continuación se puede ver un ejemplo de la sencillez del entorno, donde se observan los servicios protegidos y los que no lo están, y el estado de las últimas tareas.

![]({{site.baseurl}}/assets/img/Configuracion-de-Cloud-Control-con-Office-365-y-StorageGRID-010.png)
