---
layout: post
title: Configuración de S3cmd con StorageGRID
category: 2018
description: Pasos necesarios para la instalación y configuración del software S3cmd en Microsoft Windows y la integración con el sistema de almacenamiento de objetos StorageGRID Webscale de NetApp
---

[S3cmd](http://s3tools.org/s3cmd) es un cliente open-source gratuito por línea de comandos, basado en Python, capaz de trabajar con el protocolo [S3](https://aws.amazon.com/documentation/s3/). Todo el código fuente de este software puede encontrarse en [GitHub](https://github.com/s3tools/s3cmd).

### Requisitos

Para poder instalar S3cmd en Windows es necesario tener instalado previamente los siguientes componentes:

* Python 2.6 o superior
* Paquete python-dateutil

La instalación de este último paquete puede realizarse con el siguiente comando:
```posh
C:\pip install python-dateutil
```

Si se necesita hacer la instalación en un sistema sin conexión a Internet, es necesario descargar previamente los paquetes de python [six](https://pypi.python.org/pypi/six) y [dateutil](https://pypi.python.org/pypi/python-dateutil). Una vez descomprimidos, la instalación se realiza a través del comando [pip](https://en.wikipedia.org/wiki/Pip_(package_manager)).

```posh
C:\six-1.11.0>pip install .

C:\python-dateutil-2.6.0>pip install .
```

### Instalación

No es necesaria una instalación de software si no simplemente hay que descargarse la última versión del software [S3cmd](https://github.com/s3tools/s3cmd/archive/master.zip) y descomprimirlo en la carpeta que se quiera.

### Configuración

La configuración con el StorgeGRID se realiza con el parámetro --configure y estableciendo los valores determinados. Todos los valores y la salida pertenecen al mismo comando, pero a continuación se muestra dividido para poder comentarlo mejor.

```posh
C:\>python s3cmd --configure
ERROR: Option --preserve is not yet supported on MS Windows platform. Assuming --no-preserve.
ERROR: Option --progress is not yet supported on MS Windows platform. Assuming --no-progress.

Enter new values or accept defaults in brackets with Enter.
Refer to user manual for detailed description of all options.
...
```
Se teclean las claves de acceso y secretas que se han obtenido dentro del tenant existente.
```posh
...
Access key and Secret key are your identifiers for Amazon S3. Leave them empty for using the env variables.
Access Key: MYS3ACCESSKEY
Secret Key: MYS3SECRETKEY
...
```
Se deja la región por defecto pues no aplica en StorageGRID.
```posh
...
Default Region [US]:
...
```
Se pone el nombre o dirección IP del acceso al StorageGRID, normalmente un balanceador de carga o el propio API Gateway, y el puerto.
```posh
...
Use "s3.amazonaws.com" for S3 Endpoint and not modify it to the target Amazon S3
.
S3 Endpoint [s3.amazonaws.com]: 192.168.0.11:8082
...
```
Se proporciona el nombre del bucket al que se quiere conectar.
```posh
...
Use "%(bucket)s.s3.amazonaws.com" to the target Amazon S3. "%(bucket)s" and "%(location)s" vars can be used
if the target S3 system supports dns based buckets.
DNS-style bucket+hostname:port template for accessing a bucket [%(bucket)s.s3.amazonaws.com]: my-bucket
...
```
Si se desea se puede encriptar con GPG.
```posh
...
Encryption password is used to protect your files from reading by unauthorized persons while in transfer to S3
Encryption password:
Path to GPG program:
...
```
Actualmente el acceso al StorageGRID es siempre a través de HTTPS
```posh
...
When using secure HTTPS protocol all communication with Amazon S3servers is protected from 3rd party eavesdropping. This method is slower than plain HTTP, and can only be proxied with Python 2.7 or newer
Use HTTPS protocol [Yes]:

On some networks all internet access must go through a HTTP proxy.
Try setting it here if you cant connect to S3 directly
HTTP Proxy server name:

New settings:
  Access Key: Y9VT3ZLSZ22T48E9SE9Q
  Secret Key: gqcDvrRvUlGanlvzvHz316aQmm+oo+IATqdrOBgP
  Default Region: US
  S3 Endpoint: 192.168.0.11:8082
  DNS-style bucket+hostname:port template for accessing a bucket: my-bucket
  Encryption password:
  Path to GPG program: None
  Use HTTPS protocol: True
  HTTP Proxy server name:
  HTTP Proxy server port: 0
...
```
Se revisa la configuración y se acepta.
```posh
...
Test access with supplied credentials? [Y/n] Y
Please wait, attempting to list all buckets...
Success. Your access key and secret key worked fine :-)

Now verifying that encryption works...
Not configured. Never mind.
...
```
Se puede guardar la configuración para futuros usos.
```posh
...
Save settings? [y/N] y
Configuration saved to 'C:\Users\myuser\AppData\Roaming\s3cmd.ini'
```

### Ejemplo

Una vez configurado podemos mostrar los buckets que pertenecen al Tenant al que tenemos acceso.

```posh
C:\>python s3cmd ls
2018-01-16 06:20  s3://my-bucket
2018-01-16 06:12  s3://my-logs
```

Y también mostrar los objetos dentro de un bucket determinado.

```posh
C:\>python s3cmd ls s3://my-bucket
2018-01-16 06:27         0   s3://my-bucket/test_file.txt
```

Por último se muestra un ejemplo de las operaciones típicas del almacenamiento de objetos: PUT y GET.

```posh
C:\>python s3cmd put my_file.txt s3://my-bucket
WARNING: Module python-magic is not available. Guessing MIME types based on file extensions.
upload: 'my_file.txt' -> 's3://my-bucket/my_file.txt' (0 bytes in 0.1 seconds, -1.00 B/s) [1 of 1]

C:\>python s3cmd ls s3://my-bucket
2018-01-17 11:09         0   s3://my-bucket/my_file.txt

C:\>python s3cmd get s3://my-bucket/my_file.txt copied_file.txt
download: 's3://my-bucket/my_file.txt' -> 'copied_file.txt' (0 bytes in 0.0 seconds, -1.00 B/s)
```
