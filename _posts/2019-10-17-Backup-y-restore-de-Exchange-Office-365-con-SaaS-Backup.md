---
layout: post
title: Backup y restore de Exchange de Office 365 con SaaS Backup
category: 2019
description: Ejemplo de copia de seguridad y recuperación de Exchange Online Office 365 con NetApp SaaS Backup
---

Tras haber completado la [configuración]({{site.baseurl}}/2019/09/02/Configuracion-de-SaaS-Backup-para-proteger-Office-365.html) de SaaS Backup simplemente hay que acceder acceder a la parte de servicios y Exchange Online.

La configuración está basada en tres políticas de backup: Tier 1, Tier 2 y Tier 3. Cada política tiene establecida un RTO diferente (12 horas , 18 horas y 24 horas respectívamente) y una retención configurable en cada política (1 año, 2 años, 3 años, 5 años, 7 años y para siempre). SaaS Backup se encarga de lanzar las tareas en tiempo y forma que garantize el RTO de cada política.

En la parte superior se verá un resumen de los buzones de correo protegidos, los pendientes y los que están sin proteger.

![]({{site.baseurl}}/assets/img/Backup-y-restore-de-Exchange-Office-365-con-SaaS-Backup-001.png)

### Backup

Accediendo a los buzones que no están protegidos se ve en la pestaña 'user' un listado de los buzones que no tienen backup.

![]({{site.baseurl}}/assets/img/Backup-y-restore-de-Exchange-Office-365-con-SaaS-Backup-002.png)

SaaS Backup permite proteger también buzones compartidos. Se han creado dos buzones compartidos en Office 365 al que tienen acceso tres usuarios existentes.

![]({{site.baseurl}}/assets/img/Backup-y-restore-de-Exchange-Office-365-con-SaaS-Backup-003.png)

En la pestaña shared se han descubierto los buzones compartidos vistos anteriormente.

![]({{site.baseurl}}/assets/img/Backup-y-restore-de-Exchange-Office-365-con-SaaS-Backup-004.png)

Para hacer backup simplemente hay que seleccionar el usuario que se quiere proteger y añadirlo a la política / tier de backup que se quiera. El backup de la política se realizará respetando el RTO de la misma.

![]({{site.baseurl}}/assets/img/Backup-y-restore-de-Exchange-Office-365-con-SaaS-Backup-005.png)

Adicionalmente se puede realizar un backup manualmente sobre todo el Tier, que realizará un backup sobre todos los buzones que estén asignados a esa política de backup.

![]({{site.baseurl}}/assets/img/Backup-y-restore-de-Exchange-Office-365-con-SaaS-Backup-006.png)

La tarea de backup ejecutará un trabajo donde se puede ver el progreso.

![]({{site.baseurl}}/assets/img/Backup-y-restore-de-Exchange-Office-365-con-SaaS-Backup-007.png)

Y finalmente se verá el estado del mismo.

![]({{site.baseurl}}/assets/img/Backup-y-restore-de-Exchange-Office-365-con-SaaS-Backup-008.png)

Adicionalmente se puede hacer un backup manual sobre un buzón en concreto.

![]({{site.baseurl}}/assets/img/Backup-y-restore-de-Exchange-Office-365-con-SaaS-Backup-009.png)

### Añadir usuarios automáticamente

SaaS Backup permite automatizar el proceso de alta de nuevos usuarios dentro de las políticas de backup, de tal forma que cuando se crean nuevos usuarios en Office 365, se configure automáticamente la protección de sus buzones. Para ello, en el menú Servicios > Exchange Online > Unprotected se realiza una búsqueda basada en un Security Group.

![]({{site.baseurl}}/assets/img/Backup-y-restore-de-Exchange-Office-365-con-SaaS-Backup-010.png)

Se selecciona el grupo de seguridad al que pertenecen los usuarios que se desean proteger automáticamente.

![]({{site.baseurl}}/assets/img/Backup-y-restore-de-Exchange-Office-365-con-SaaS-Backup-011.png)

Se crea una regla y se selecciona la política de backup / tier a donde se añadirán los buzones automáticamente.

![]({{site.baseurl}}/assets/img/Backup-y-restore-de-Exchange-Office-365-con-SaaS-Backup-012.png)

Como ejemplo se accede al grupo de securidad que se tiene configurado.

![]({{site.baseurl}}/assets/img/Backup-y-restore-de-Exchange-Office-365-con-SaaS-Backup-013.png)

Y se añade un nuevo usuario 'User 5'.

![]({{site.baseurl}}/assets/img/Backup-y-restore-de-Exchange-Office-365-con-SaaS-Backup-014.png)

En el siguiente escaneo que hace SaaS Backup se puede ver el usuario añadido pero sin proteger.

![]({{site.baseurl}}/assets/img/Backup-y-restore-de-Exchange-Office-365-con-SaaS-Backup-015.png)

SaaS Backup automáticamente sincroniza los usuarios cada cierto tiempo. Pero para que se pueda observar el proceso, se sincroniozan manualmente los usuarios dentro de la gestión de servicios.

![]({{site.baseurl}}/assets/img/Backup-y-restore-de-Exchange-Office-365-con-SaaS-Backup-016.png)

Y se observa que el usuario se ha añadido automáticamente a la política de backup y está en estado pendiente de ser protegido.

![]({{site.baseurl}}/assets/img/Backup-y-restore-de-Exchange-Office-365-con-SaaS-Backup-017.png)

### Restore

Para restaurar el contenido de un backup realizado puede hacerse desde varios sitios. El primero a nivel de buzón completo.

![]({{site.baseurl}}/assets/img/Backup-y-restore-de-Exchange-Office-365-con-SaaS-Backup-018.png)

Esto permitiría restaurar el buzón entero en el mismo buzón, en otro diferente, o descargarlo como fichero PST.

![]({{site.baseurl}}/assets/img/Backup-y-restore-de-Exchange-Office-365-con-SaaS-Backup-019.png)

Si se navega por el contenido del buzón, se puede ver la estructura del buzón: email, tareas, calendario, contactos,... y ver el histórico de backup. 

![]({{site.baseurl}}/assets/img/Backup-y-restore-de-Exchange-Office-365-con-SaaS-Backup-020.png)

Y ver los puntos en los que se ha hecho backup.

![]({{site.baseurl}}/assets/img/Backup-y-restore-de-Exchange-Office-365-con-SaaS-Backup-021.png)

Navengando se puede restaurar con granularidad de a nivel de correo electrónico, tarea, contacto o cita de calendario.

![]({{site.baseurl}}/assets/img/Backup-y-restore-de-Exchange-Office-365-con-SaaS-Backup-022.png)

y elegir donde se restaura el contenido, en este caso en el mismo buzón de correo u otro.

![]({{site.baseurl}}/assets/img/Backup-y-restore-de-Exchange-Office-365-con-SaaS-Backup-023.png)

Una vez lanzado se verifica el estado de la tarea de restauración

![]({{site.baseurl}}/assets/img/Backup-y-restore-de-Exchange-Office-365-con-SaaS-Backup-024.png)

Y desde el Outlook del buzón que se ha restaurado puede verse una carpeta con el contenido restaurado.

![]({{site.baseurl}}/assets/img/Backup-y-restore-de-Exchange-Office-365-con-SaaS-Backup-025.png)
