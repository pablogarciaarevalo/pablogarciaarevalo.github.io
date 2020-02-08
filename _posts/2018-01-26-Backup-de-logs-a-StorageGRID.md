---
layout: post
title: Backup de logs a StorageGRID
category: 2018
description: Ejemplo de utilización de StorageGRID Webscale como repositorio de backup de logs de sistemas.
---

Uno de los muchos escenarios para los que hay que estar más protegidos que antes debido a la normativa [GDPR](
https://www.eugdpr.org/) es en la protección de los logs de los sistemas ante posibles auditorías por vulnerabilidades. Esto es uno de los casos de uso mas comunes para el almacenamiento de objetos, el cual puede ser repositorio de este contenido.

Una vez se haya hecho la [configuración de S3cmd con StorageGRID]({{site.baseurl}}/2018/01/23/Configuracion-de-s3cmd-con-StorageGRID.html) tal y como se explicó previamente, ya se puede hacer uso del mismo. A continuación se detallan los escenarios posibles y el funcionamiento de S3cmd en la sincronización de la carpeta de logs C:\myLogs\ contra el bucket 'my-bucket' de StorageGRID. Esta sincronización siempre será realizada unidireccionalmente a modo de copia con el siguiente comando:

```posh
python s3cmd sync --acl-public --recursive --delete-removed --force C:\myLogs\ s3://my-bucket
```

### Situación inicial

Se dispone de una carpeta de logs vacía y de un bucket vacío en StorageGRID.

```posh
C:\>dir C:\myLogs\
 Volume in drive C has no label.
 Volume Serial Number is 1E97-4BBC

 Directory of C:\myLogs

01/17/2018  06:24 AM    <DIR>          .
01/17/2018  06:24 AM    <DIR>          ..
               0 File(s)              0 bytes
               2 Dir(s)  25,212,268,544 bytes free

C:\>python s3cmd ls s3://my-bucket
```

En un momento dado el aplicativo empieza a trabajar guardando ficheros de logs y carpetas necesarias en el directorio establecido.

```posh
C:\>type nul > C:\myLogs\log1.txt
C:\>type nul > C:\myLogs\log2.txt
C:\>mkdir C:\myLogs\folder1
C:\>type nul > C:\myLogs\folder1\anotherLog.txt
```

Se sincroniza y se observa que se transfieren todos los ficheros generados.

```posh
C:\>python s3cmd sync --acl-public --recursive --delete-removed --force C:\myLogs\ s3://my-bucket
WARNING: Module python-magic is not available. Guessing MIME types based on file extensions.
upload: 'C:\myLogs\folder1\anotherLog.txt' -> 's3://my-bucket/folder1/anotherLog.txt' (0 bytes in 0.0 seconds, -1.00 B/s) [1 of 3]
upload: 'C:\myLogs\log1.txt' -> 's3://my-bucket/log1.txt' (0 bytes in 0.0 seconds, -1.00 B/s) [2 of 3]
upload: 'C:\myLogs\log2.txt' -> 's3://my-bucket/log2.txt' (0 bytes in 0.1 seconds, -1.00 B/s) [3 of 3]
```

Se muestra el contenido del bucket que se corresponde con el contenido de la carpeta de logs del servidor.

```posh
C:\>python s3cmd ls s3://my-bucket
                       DIR   s3://my-bucket/folder1/
2018-01-17 07:33         0   s3://my-bucket/log1.txt
2018-01-17 07:33         0   s3://my-bucket/log2.txt
```

### Creación de nuevos ficheros

En el momento que el servidor genere un nuevo fichero de log y se ejecute el comando de sincronización de S3cmd, se observa que solo se copia ese fichero.

```posh
C:\>type nul > C:\myLogs\log3.txt

C:\>python s3cmd sync --acl-public --recursive --delete-removed --force C:\myLogs\ s3://my-bucket
WARNING: Module python-magic is not available. Guessing MIME types based on file extensions.
upload: 'C:\myLogs\log3.txt' -> 's3://my-bucket/log3.txt' (0 bytes in 0.1 seconds, -1.00 B/s) [1 of 1]
```

Al mostrar el contenido del bucket se verifica que se ha transferido correctamente la información.

```posh
C:\>python s3cmd ls s3://my-bucket
                       DIR   s3://my-bucket/folder1/
2018-01-17 07:33         0   s3://my-bucket/log1.txt
2018-01-17 07:33         0   s3://my-bucket/log2.txt
2018-01-17 07:35         0   s3://my-bucket/log3.txt
```

### Sin ningún cambio

Si se ejecuta el comando de sincronización de S3cmd sin haber habido ningún cambio, no se produce ninguna transferencia.

```posh
C:\>python s3cmd sync --acl-public --recursive --delete-removed --force C:\myLogs\ s3://my-bucket
C:\>
```

### Actualización de un fichero existente

Se actualiza un fichero existente que cambia el contenido del mismo.

```posh
C:\>@echo Updating the file> C:\myLogs\log1.txt

C:\>dir C:\myLogs\
 Volume in drive C has no label.
 Volume Serial Number is 1E97-4BBC

 Directory of C:\myLogs

01/17/2018  07:35 AM    <DIR>          .
01/17/2018  07:35 AM    <DIR>          ..
01/17/2018  07:33 AM    <DIR>          folder1
01/17/2018  07:36 AM                25 log1.txt
01/17/2018  07:32 AM                 0 log2.txt
01/17/2018  07:35 AM                 0 log3.txt
               3 File(s)             25 bytes
               3 Dir(s)  25,212,268,544 bytes free
```

Se sincroniza y se observa que se transfieren solo el fichero actualizado.

```posh
C:\>python s3cmd sync --acl-public --recursive --delete-removed --force C:\myLogs\ s3://my-bucket
WARNING: Module python-magic is not available. Guessing MIME types based on file extensions.
upload: 'C:\myLogs\log1.txt' -> 's3://my-bucket/log1.txt' (25 bytes in 0.1 seconds, 320.51 B/s) [1 of 1]
Done. Uploaded 25 bytes in 1.0 seconds, 25.00 B/s.

C:\>python s3cmd ls s3://my-bucket
                       DIR   s3://my-bucket/folder1/
2018-01-17 07:37        25   s3://my-bucket/log1.txt
2018-01-17 07:33         0   s3://my-bucket/log2.txt
2018-01-17 07:35         0   s3://my-bucket/log3.txt
```

### Borrado de un fichero

Se borra un fichero existente.

```posh
C:\>del C:\myLogs\log2.txt
```

Se sincroniza y se observa que se borra el objeto.

```posh
C:\>python s3cmd sync --acl-public --recursive --delete-removed --force C:\myLogs\ s3://my-bucket
delete: 's3://my-bucket/log2.txt'

C:\>python s3cmd ls s3://my-bucket
                       DIR   s3://my-bucket/folder1/
2018-01-17 07:37        25   s3://my-bucket/log1.txt
2018-01-17 07:35         0   s3://my-bucket/log3.txt
```

