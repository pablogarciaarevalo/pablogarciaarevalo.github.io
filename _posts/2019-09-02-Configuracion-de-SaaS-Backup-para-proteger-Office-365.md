---
layout: post
title: Configuración de SaaS Backup para proteger Office 365
category: 2019
description: Configuración inicial del servicio SaaS Backup para proteger Office 365
---

Tras terminar el [registro]({{site.baseurl}}/2019/08/04/Registro-de-SaaS-Backup-para-proteger-Office-365.html) de SaaS Backup, simplemente hay que hacer algunas configuraciones para personalizarlo a cada entorno de Office 365.

Lo primero que se verá es que se han completado varias tareas de sincronización de usuarios de Exchange online y sitios de Sharepoint.

![]({{site.baseurl}}/assets/img/Configuracion-de-SaaS-Backup-para-proteger-Office-365-001.png)

### Sincronización

Para sincronizar el resto de servicios hay que acceder al menú servicios, y 'settings'

![]({{site.baseurl}}/assets/img/Configuracion-de-SaaS-Backup-para-proteger-Office-365-002.png)

Se sincroniza el servicio de OneDrive.

![]({{site.baseurl}}/assets/img/Configuracion-de-SaaS-Backup-para-proteger-Office-365-003.png)

Se activa el servicio de grupos de Office 365.

![]({{site.baseurl}}/assets/img/Configuracion-de-SaaS-Backup-para-proteger-Office-365-004.png)

En la parte superior, se ha de garantizar el consentimiento de acceso con el usuario de servicio.

![]({{site.baseurl}}/assets/img/Configuracion-de-SaaS-Backup-para-proteger-Office-365-005.png)

Para cada uno de los servicios, se puede personalizar la configuración.

![]({{site.baseurl}}/assets/img/Configuracion-de-SaaS-Backup-para-proteger-Office-365-006.png)

Pueden verse los detalles de cada uno.

![]({{site.baseurl}}/assets/img/Configuracion-de-SaaS-Backup-para-proteger-Office-365-007.png)

### Configuración de la cuenta

Desde el menú 'accounts settings' se accede a la parte de la configuración de la cuenta de SaaS Backup.

![]({{site.baseurl}}/assets/img/Configuracion-de-SaaS-Backup-para-proteger-Office-365-008.png)

Se pueden crear nuevos usuarios para acceder al servicio de SaaS Backup.

![]({{site.baseurl}}/assets/img/Configuracion-de-SaaS-Backup-para-proteger-Office-365-009.png)

Y establecer a cada uno de los usuarios diferentes roles.

![]({{site.baseurl}}/assets/img/Configuracion-de-SaaS-Backup-para-proteger-Office-365-010.png)

Es posible el purgado de contenido protegido así como la liberación de licencias de SaaS Backup no utilizadas, por ejemplo, cuando se da de baja un usuario.

![]({{site.baseurl}}/assets/img/Configuracion-de-SaaS-Backup-para-proteger-Office-365-011.png)

SaaS Backup permite proteger de forma automática y desatendida usuarios que pertenecen a un grupo de Office 365. En este caso se muestra un grupo de seguridad de Office 365 que luego se utilizará para añadir los usuarios de forma automática.

![]({{site.baseurl}}/assets/img/Configuracion-de-SaaS-Backup-para-proteger-Office-365-012.png)

Y los usuarios que pertenecen al grupo.

![]({{site.baseurl}}/assets/img/Configuracion-de-SaaS-Backup-para-proteger-Office-365-013.png)

Simplemente hay que añadir ese grupo para que se puede descubrir posteriormente.

![]({{site.baseurl}}/assets/img/Configuracion-de-SaaS-Backup-para-proteger-Office-365-014.png)

Una vez añadidos, se pueden ver y borrar los grupos añadidos.

![]({{site.baseurl}}/assets/img/Configuracion-de-SaaS-Backup-para-proteger-Office-365-015.png)


### Backup y restore

Tras haber personalizado la configuración de backup, es momento de hacer backup y restore de:

- [Exchange Online]({{site.baseurl}}/2019/10/17/Backup-y-restore-de-Exchange-Office-365-con-SaaS-Backup.html) 
- [OneDrive for Business]({{site.baseurl}}/2019/10/28/Backup-y-restore-de-OneDrive-Office-365-con-SaaS-Backup.html) 
- [SharePoint Online]({{site.baseurl}}/2019/12/08/Backup-y-restore-de-SharePoint-Office-365-con-SaaS-Backup.html) 

