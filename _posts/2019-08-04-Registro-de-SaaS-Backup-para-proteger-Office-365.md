---
layout: post
title: Registro de SaaS Backup para proteger Office 365
category: 2019
description: Procedimiento para darse de alta en el servicio SaaS Backup de NetApp.
---

Office 365 de Microsoft es uno de los servicios Cloud en modalidad SaaS mas utilizados. Si bien este tipo de modalidad cloud SaaS proporcionan a las empresas muchas ventajas respecto a las aplicaciones tradicionales, la eliminación de las políticas de backup de sus datos no es una de ellas. Esten los datos en una infraestructura on-premises o Cloud pública en cualquiera de sus modalidades, es responsabilidad de las empresas la protección e integridad de los datos. 

[SaaS Backup](https://cloud.netapp.com/saas-backup) de NetApp es el servicio que permite proteger los servicios Cloud SaaS de amenazas o borrados accidentales. Permite proteger los servicios Outlook, OneDrive, SharePoint y OneNote de Microsoft Office 365 y de SalesForce hacia un almacenamiento de objetos en cloud público como Amazon S3 o Azure, y Cloud privado como [StorageGRID](https://www.netapp.com/us/products/data-management-software/object-storage-grid-sds.aspx)

### Requisitos

En el caso de querer proteger los servicios de Office 365 sobre el almacenamiento de StorageGRID, son necesarios los siguientes requisitos:

- El acceso al almacenamiento de StorageGRID tiene que estar disponible a través de Internet.
- El acceso a StorageGRID necesita de un nombre de dominio con un certificado, en el caso del ejemplo sgws.mydomain.com
- TLS 1.2
- Puerto 8082 abierto y redireccionado
- Un bucket ya creado

Si bien no es un requisito, una buena práctica es toda plataforma de backup es la utilización de un usuario específico. Por temas de seguridad se recomienda habilitar la autenticación múltiple (MFA) propia de Azure.

![]({{site.baseurl}}/assets/img/Registro-de-SaaS-Backup-para-proteger-Office-365-001.png)


Para poder proteger el servicio de Exchange Online es necesario habilitar el User impersonation desde el [centro de administración de Exchange](https://outlook.office365.com/ecp).

![]({{site.baseurl}}/assets/img/Registro-de-SaaS-Backup-para-proteger-Office-365-002.png)

### Registro

Todos los servicios cloud de NetApp cuentan con la posibilidad de probarlos durante cierto tiempo. 
Simplemente se accede al servicio que se quiere probar, en este caso SaaS Backup, y se accede al enlace de Free Trial.

![]({{site.baseurl}}/assets/img/Registro-de-SaaS-Backup-para-proteger-Office-365-003.png)

Tras completar la información requerida, se nos envía a la dirección de correo una clave para el acceso Trial. Simplemente hay que acceder a la web del [registro de SaaS Backup](https://saasbackup.netapp.com/register/selection) y acceder usando las credenciales de Office 365 de la cuenta de servicio que se ha creado anteriormente. Es recomendable usar un navegador en modo incógnito para evitar acceder con la cuenta que no se desea.

![]({{site.baseurl}}/assets/img/Registro-de-SaaS-Backup-para-proteger-Office-365-004.png)

Se aceptan los permisos necesarios para la protección y recuperación del servicio.

![]({{site.baseurl}}/assets/img/Registro-de-SaaS-Backup-para-proteger-Office-365-005.png)

Y se comienza con el Wizard de configuración.

![]({{site.baseurl}}/assets/img/Registro-de-SaaS-Backup-para-proteger-Office-365-006.png)

Se seleccionan los servicios que se quieren proteger.

![]({{site.baseurl}}/assets/img/Registro-de-SaaS-Backup-para-proteger-Office-365-007.png)

Se selecciona la licencia. Es posible comenzar con una licencia de pruebas y después actualizar a la licencia definitiva.

![]({{site.baseurl}}/assets/img/Registro-de-SaaS-Backup-para-proteger-Office-365-008.png)

Se configura el almacenamiento destino del backup. Es posible elegir que el almacenamiento lo proporcione el mismo servicio SaaS Backup (se contrata a NetApp tanto el servicio como el almacenamiento donde se guarda el backup), o utilizar el almacenamiento propio de cada empresa (se contrata solo el servicio, y por tanto el coste por usuario de SaaS Backup es más barato). El almacenamiento destino de backup es un almacenamiento de objetos.

En el caso que el almacenamiento lo proporcione el mismo servicio (SaaS Backup Provided Storage) se permite elegir guardarlo en un Blob Storage de Azure propiedad de NetApp, permitiendo elegir la región de Azure.

![]({{site.baseurl}}/assets/img/Registro-de-SaaS-Backup-para-proteger-Office-365-009.png)

O también se puede escoger guardalo en un bucket de AWS propieda de NetApp, permitiendo elegir la región de AWS.

![]({{site.baseurl}}/assets/img/Registro-de-SaaS-Backup-para-proteger-Office-365-010.png)

En el caso que el almacenamiento lo proporcione la empresa (Bring your own storage) se permite que el repositorio de backup sea un almacenamiento de objetos de nube pública de Azure Blob Storage.

![]({{site.baseurl}}/assets/img/Registro-de-SaaS-Backup-para-proteger-Office-365-011.png)

Un almacenamiento de objetos de nube pública sobre un bucket de AWS.

![]({{site.baseurl}}/assets/img/Registro-de-SaaS-Backup-para-proteger-Office-365-012.png)

O un almacenamiento de objetos de nube privada basado en NetApp [StorageGRID](https://www.netapp.com/us/products/data-management-software/object-storage-grid-sds.aspx)

![]({{site.baseurl}}/assets/img/Registro-de-SaaS-Backup-para-proteger-Office-365-013.png)

Se verifica y se guarda la configuración.

![]({{site.baseurl}}/assets/img/Registro-de-SaaS-Backup-para-proteger-Office-365-014.png)

Se ha completado satisfactoriamente el registro al servicio SaaS Backup.

![]({{site.baseurl}}/assets/img/Registro-de-SaaS-Backup-para-proteger-Office-365-015.png)

Una vez completado el registro, simplemente hay que hacer algunas tareas de [configuración]({{site.baseurl}}/2019/09/02/Configuracion-de-SaaS-Backup-para-proteger-Office-365.html).
