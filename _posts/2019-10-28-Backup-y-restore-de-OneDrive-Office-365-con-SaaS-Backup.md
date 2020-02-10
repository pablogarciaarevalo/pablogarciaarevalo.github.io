---
layout: post
title: Backup y restore de OneDrive de Office 365 con SaaS Backup
category: 2019
description: Ejemplo de copia de seguridad y recuperación de OneDrive for Business Office 365 con NetApp SaaS Backup
---

Tras haber completado la [configuración]({{site.baseurl}}/2019/09/02/Configuracion-de-SaaS-Backup-para-proteger-Office-365.html) de SaaS Backup simplemente hay que acceder acceder a la parte de servicios y OneDrive for Business.

La configuración está basada en tres políticas de backup: Tier 1, Tier 2 y Tier 3. Cada política tiene establecida un RTO diferente (12 horas , 18 horas y 24 horas respectívamente) y una retención configurable en cada política (1 año, 2 años, 3 años, 5 años, 7 años y para siempre). SaaS Backup se encarga de lanzar las tareas en tiempo y forma que garantize el RTO de cada política.

En la parte superior se verá un resumen de los buzones de correo protegidos, los pendientes y los que están sin proteger.

### Backup

Accediendo a los que no están protegidos se ve un listado de los discos que no tienen backup. Para hacer backup simplemente hay que seleccionar el disco del usuario que se quiera y añadir a la política deseada.

![]({{site.baseurl}}/assets/img/Backup-y-restore-de-OneDrive-Office-365-con-SaaS-Backup-001.png)

Es posible que durante el proceso inicial de configuración al añadir usuarios no se tenga conexión con el disco de OneDrive de algún usuario.

![]({{site.baseurl}}/assets/img/Backup-y-restore-de-OneDrive-Office-365-con-SaaS-Backup-002.png)

En ese caso simplemente ir a Manage Services -> Microsoft OneDrive for Business -> y darle a Sync now

![]({{site.baseurl}}/assets/img/Backup-y-restore-de-OneDrive-Office-365-con-SaaS-Backup-003.png)

Una vez añadidos los discos a la política de backup que se quiera, esperar a que se lance automáticamente el backup basado en los RTO de los Tier o darle a backup manual.

![]({{site.baseurl}}/assets/img/Backup-y-restore-de-OneDrive-Office-365-con-SaaS-Backup-004.png)

Esperar a que se complete la tarea.

![]({{site.baseurl}}/assets/img/Backup-y-restore-de-OneDrive-Office-365-con-SaaS-Backup-005.png)

### Restore

Para restaurar el contenido de un backup realizado puede hacerse desde varios sitios. El primero a nivel de disco completo.

![]({{site.baseurl}}/assets/img/Backup-y-restore-de-OneDrive-Office-365-con-SaaS-Backup-006.png)

Esto permitiría restaurar el disco entero en el mismo sitio, en otro diferente, o descargarlo como fichero ZIP.

![]({{site.baseurl}}/assets/img/Backup-y-restore-de-OneDrive-Office-365-con-SaaS-Backup-007.png)

Si se navega por el contenido del buzón, se puede ver la estructura del disco y restaurar granularmente a nivel de fichero.

![]({{site.baseurl}}/assets/img/Backup-y-restore-de-OneDrive-Office-365-con-SaaS-Backup-008.png)

y elegir donde se restaura el contenido, en este caso en el mismo disco u otro.

![]({{site.baseurl}}/assets/img/Backup-y-restore-de-OneDrive-Office-365-con-SaaS-Backup-009.png)

Esperar a que se complete la tarea.

![]({{site.baseurl}}/assets/img/Backup-y-restore-de-OneDrive-Office-365-con-SaaS-Backup-010.png)

Y sobre el OneDrive que se ha restaurado se puede ver una carpeta con el contenido restaurado.
![]({{site.baseurl}}/assets/img/Backup-y-restore-de-OneDrive-Office-365-con-SaaS-Backup-011.png)

OneDrive utiliza versiones cada vez que se hacen cambios sobre los ficheros. En este ejemplo se escribe un nuevo fichero con el mismo nombre.

![]({{site.baseurl}}/assets/img/Backup-y-restore-de-OneDrive-Office-365-con-SaaS-Backup-012.png)

Y lanzamos backup del disco.

![]({{site.baseurl}}/assets/img/Backup-y-restore-de-OneDrive-Office-365-con-SaaS-Backup-013.png)

Esperar a que se complete la tarea.

![]({{site.baseurl}}/assets/img/Backup-y-restore-de-OneDrive-Office-365-con-SaaS-Backup-014.png)

Navegando en SaaS Backup sobre el disco que se ha hecho backup se puede ver que el fichero tiene varias versiones.

![]({{site.baseurl}}/assets/img/Backup-y-restore-de-OneDrive-Office-365-con-SaaS-Backup-015.png)

Accediendo se pueden ver las versiones de cada fichero y restaurar granularmente a nivel de versión de cada fichero.

![]({{site.baseurl}}/assets/img/Backup-y-restore-de-OneDrive-Office-365-con-SaaS-Backup-016.png)



