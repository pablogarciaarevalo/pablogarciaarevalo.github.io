---
layout: post
title: Backup y restore de SharePoint de Office 365 con SaaS Backup
category: 2019
description: Ejemplo de copia de seguridad y recuperación de SharePoint Online Office 365 con NetApp SaaS Backup
---

Tras haber completado la [configuración]({{site.baseurl}}/2019/09/02/Configuracion-de-SaaS-Backup-para-proteger-Office-365.html) de SaaS Backup simplemente hay que acceder acceder a la parte de servicios y OneDrive for Business.

La configuración está basada en tres políticas de backup: Tier 1, Tier 2 y Tier 3. Cada política tiene establecida un RTO diferente (12 horas , 18 horas y 24 horas respectívamente) y una retención configurable en cada política (1 año, 2 años, 3 años, 5 años, 7 años y para siempre). SaaS Backup se encarga de lanzar las tareas en tiempo y forma que garantize el RTO de cada política.

En este caso se ha creado un sitio de SharePoint nuevo de ejemplo.

![]({{site.baseurl}}/assets/img/Backup-y-restore-de-SharePoint-Office-365-con-SaaS-Backup-001.png)

Una vez creado se accede a la parte de servicios y configuración 'settings'

![]({{site.baseurl}}/assets/img/Backup-y-restore-de-SharePoint-Office-365-con-SaaS-Backup-002.png)

Se sincronizan los sitios de SharePoint Online.

![]({{site.baseurl}}/assets/img/Backup-y-restore-de-SharePoint-Office-365-con-SaaS-Backup-003.png)

Esperar a que se complete la tarea.

![]({{site.baseurl}}/assets/img/Backup-y-restore-de-SharePoint-Office-365-con-SaaS-Backup-004.png)

En la parte superior se verá un resumen de los buzones de correo protegidos, los pendientes y los que están sin proteger.

![]({{site.baseurl}}/assets/img/Backup-y-restore-de-SharePoint-Office-365-con-SaaS-Backup-005.png)

### Backup

Accediendo a los portales que no están protegidos se ve un listado de los portales que no tienen backup. Para hacer backup simplemente hay que seleccionar el portal que se quiere proteger y añadirlo a la política / tier de backup que se quiera. El backup de la política se realizará respetando el RTO de la misma.

![]({{site.baseurl}}/assets/img/Backup-y-restore-de-SharePoint-Office-365-con-SaaS-Backup-006.png)

Adicionalmente se puede realizar un backup manualmente sobre todo el Tier, que realizará un backup sobre todos los portales que estén asignados a esa política de backup.

![]({{site.baseurl}}/assets/img/Backup-y-restore-de-SharePoint-Office-365-con-SaaS-Backup-007.png)

Esperar a que se complete la tarea.

![]({{site.baseurl}}/assets/img/Backup-y-restore-de-SharePoint-Office-365-con-SaaS-Backup-008.png)

### Restore

Para restaura entrar en los sitios protegidos.

![]({{site.baseurl}}/assets/img/Backup-y-restore-de-SharePoint-Office-365-con-SaaS-Backup-009.png)

Para restaurar el contenido de un backup realizado puede hacerse desde varios sitios. El primero a nivel de portal completo.

![]({{site.baseurl}}/assets/img/Backup-y-restore-de-SharePoint-Office-365-con-SaaS-Backup-010.png)

Esto permitiría restaurar el portal entero en el mismo sitio, en otro diferente, o descargarlo como fichero ZIP.

![]({{site.baseurl}}/assets/img/Backup-y-restore-de-SharePoint-Office-365-con-SaaS-Backup-011.png)

Si se navega por el contenido del portal, se puede ver la estructura del portal y restaurar granularmente a nivel de fichero.

![]({{site.baseurl}}/assets/img/Backup-y-restore-de-SharePoint-Office-365-con-SaaS-Backup-012.png)

En este ejemplo se elimina un subsitio.

![]({{site.baseurl}}/assets/img/Backup-y-restore-de-SharePoint-Office-365-con-SaaS-Backup-013.png)

Que desaparece de la página principal.

![]({{site.baseurl}}/assets/img/Backup-y-restore-de-SharePoint-Office-365-con-SaaS-Backup-014.png)

Restaurando el subsitio que se acaba de eliminar.

![]({{site.baseurl}}/assets/img/Backup-y-restore-de-SharePoint-Office-365-con-SaaS-Backup-015.png)

Se elige que se restaure en su ubicación original.

![]({{site.baseurl}}/assets/img/Backup-y-restore-de-SharePoint-Office-365-con-SaaS-Backup-016.png)

Esperar a que se complete la tarea.

![]({{site.baseurl}}/assets/img/Backup-y-restore-de-SharePoint-Office-365-con-SaaS-Backup-017.png)

Se verifica que el portal de SharePoint se muestra como estaba originalmente.

![]({{site.baseurl}}/assets/img/Backup-y-restore-de-SharePoint-Office-365-con-SaaS-Backup-018.png)
