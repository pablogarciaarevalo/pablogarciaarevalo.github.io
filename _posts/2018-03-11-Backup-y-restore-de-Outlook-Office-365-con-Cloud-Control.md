---
layout: post
title: Backup y restore de Outlook Office 365 con Cloud Control
category: 2018
description: Ejemplo de configuración, copia de seguridad y recuperación de Outlook Office 365 con NetApp Cloud Control
---

Después de haber realizado la [configuración de Cloud Control con Office 365 y StorageGRID]({{site.baseurl}}/2018/02/25/Configuracion-de-Cloud-Control-con-Office-365-y-StorageGRID.html) se muestra a continuación el procedimiento para respaldar y recuperar información del Outlook de Office 365.

### Requisitos

Al usar un usuario de servicio para realizar el backup, es necesario establecer permisos para que este usuario pueda acceder a la información a respaldar. A continuación es muestra el procedimiento para permitir estos permisos de forma manual, si bien es posible realizarlo de forma automática. Para ello es necesario acceder al [Exchange Admin Center](https://outlook.office365.com/ecp/) y editar al 'Discovery Management' en la parte de permisos.

![]({{site.baseurl}}/assets/img/Backup-y-restore-de-Outlook-Office-365-con-Cloud-Control-001.png)

Simplemente hay que añadir el usuario de servicio creado en el role 'ApplicationImpersonation'.

![]({{site.baseurl}}/assets/img/Backup-y-restore-de-Outlook-Office-365-con-Cloud-Control-002.png)

### Configuración

Navegando a través del portal de Cloud Control por Servicios > Microsoft Exchange Online > Mailboxes se listan los buzones y el estado de la protección de los mismos. Para establecerles una política de backup simplemente hay que seleccionar los que se desee y seleccionar el Tier en la pestaña Groups.

![]({{site.baseurl}}/assets/img/Backup-y-restore-de-Outlook-Office-365-con-Cloud-Control-003.png)

Desde el nivel superior del Cloud Control (Servicios > Microsoft Exchange Online) se muestran las diferentes políticas de backup y el estado de las mismas.

![]({{site.baseurl}}/assets/img/Backup-y-restore-de-Outlook-Office-365-con-Cloud-Control-004.png)

### Backup

Los backups se pueden ejecutar bajo demanda además de la propia planificación de horas que tienen las políticas.

![]({{site.baseurl}}/assets/img/Backup-y-restore-de-Outlook-Office-365-con-Cloud-Control-005.png)

Tras finalizar la tarea se observa el estado de la misma.

![]({{site.baseurl}}/assets/img/Backup-y-restore-de-Outlook-Office-365-con-Cloud-Control-006.png)

Conectándose al bucket S3 del StorageGRID se puede ver el backup que se acaba de realizar en el formato propio de Cloud Control con extensión ntap.

![]({{site.baseurl}}/assets/img/Backup-y-restore-de-Outlook-Office-365-con-Cloud-Control-007.png)

### Restore

Para probar la restauración se borra un email del buzón de Outlook Office 365.

![]({{site.baseurl}}/assets/img/Backup-y-restore-de-Outlook-Office-365-con-Cloud-Control-008.png)

Navegando a través del portal de Cloud Control, se accede a la carpeta respaldada del Outlook de este usuario donde se encuentra el email borrado.

![]({{site.baseurl}}/assets/img/Backup-y-restore-de-Outlook-Office-365-con-Cloud-Control-009.png)

Se selecciona la ubicación de la restauración.

![]({{site.baseurl}}/assets/img/Backup-y-restore-de-Outlook-Office-365-con-Cloud-Control-010.png)

Tras finalizar la tarea se observa el estado de la misma.

![]({{site.baseurl}}/assets/img/Backup-y-restore-de-Outlook-Office-365-con-Cloud-Control-011.png)

Como consecuencia el usuario observa el email recuperado.

![]({{site.baseurl}}/assets/img/Backup-y-restore-de-Outlook-Office-365-con-Cloud-Control-012.png)
