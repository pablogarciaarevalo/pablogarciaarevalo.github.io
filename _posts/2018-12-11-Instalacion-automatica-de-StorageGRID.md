---
layout: post
title: Instalación automática de StorageGRID
category: 2018
description: Procedimiento para la instalación automática y desatendida de la solución software de almacenamiento de objetos StorageGRID de NetApp
---

Una de las ventajas de la solución software de almacenamiento de objetos (Software Defined Storage) es la posibilidad de automatizar su despliegue sobre, por ejemplo, una plataforma VMware vSphere.

### Requisitos

Es necesario tener instalado previamente el software para Windows [VMware PowerCLI](https://my.vmware.com/web/vmware/details?downloadGroup=PCLI650R1&productId=614).

### Instalación

A continuación se muestran los pasos para descargar e importar el módulo de Powershell que reside en GitHub.

```posh
PS C:\> git clone https://github.com/NetApp-StorageGRID/Windows-Deployment-Tools.git
PS C:\> cd Windows-Deployment-Tools
PS C:\> Import-Module .\install-storagegrid.psm1
```

Se ha de crear un fichero de configuración con los valores del entorno a desplegar. A continuación puede encontrarse un ejemplo básico ideal para pruebas de concepto, con un nodo de administración, un nodo de gateway y tres nodos de almacenamiento, que cuenta con una única red y que se desplega sobre el mismo datastore en modo thin provisionig.

```shell
SOURCE                   = C:\StorageGRID-Webscale-11.1.1\vsphere
VCENTER                  = 192.168.0.10
PATH                     = /Mi_Datacenter/Mi_Cluster/Mi_App
USERNAME                 = administrator@vsphere.local
PASSWORD                 = password

OVFTOOL_ARGUMENTS        = --powerOn --noSSLVerify --diskMode=thin --datastore=Mi_Datastore

[dc1-adm1]
  NODE_TYPE = VM_Admin_Node
  ADMIN_ROLE = Primary
  GRID_NETWORK_CONFIG = STATIC
  GRID_NETWORK_TARGET = My_vNetwork
  GRID_NETWORK_IP = 192.168.0.11
  GRID_NETWORK_MASK = 255.255.255.0
  GRID_NETWORK_GATEWAY = 192.168.0.1

[dc1-sn1]
  NODE_TYPE = VM_Storage_Node
  ADMIN_IP = 192.168.0.11
  GRID_NETWORK_CONFIG = STATIC
  GRID_NETWORK_TARGET = My_vNetwork
  GRID_NETWORK_IP = 192.168.0.12
  GRID_NETWORK_MASK = 255.255.255.0
  GRID_NETWORK_GATEWAY = 192.168.0.1

[dc1-sn2]
  NODE_TYPE = VM_Storage_Node
  ADMIN_IP = 192.168.0.11
  GRID_NETWORK_CONFIG = STATIC
  GRID_NETWORK_TARGET = My_vNetwork
  GRID_NETWORK_IP = 192.168.0.13
  GRID_NETWORK_MASK = 255.255.255.0
  GRID_NETWORK_GATEWAY = 192.168.0.1

[dc1-sn3]
  NODE_TYPE = VM_Storage_Node
  ADMIN_IP = 192.168.0.11
  GRID_NETWORK_CONFIG = STATIC
  GRID_NETWORK_TARGET = My_vNetwork
  GRID_NETWORK_IP = 192.168.0.14
  GRID_NETWORK_MASK = 255.255.255.0
  GRID_NETWORK_GATEWAY = 192.168.0.1

[dc1-gw1]
  NODE_TYPE = VM_API_Gateway
  ADMIN_IP = 192.168.0.11
  GRID_NETWORK_CONFIG = STATIC
  GRID_NETWORK_TARGET = My_vNetwork
  GRID_NETWORK_IP = 192.168.0.15
  GRID_NETWORK_MASK = 255.255.255.0
  GRID_NETWORK_GATEWAY = 192.168.0.1

```

### Despliegue

Con el siguiente comando se comienza el despliegue del sistema de almacenamiento de objetos StorageGRID.

```posh
PS C:\> Deploy-StorageGRID -ConfigFile .\config.ini
```

![]({{site.baseurl}}/assets/img/Instalacion-automatica-de-StorageGRID-001.png)

### Configuración NTP

Para la configuración final es requisito tener un servicio de tiempo preciso. En caso de no disponer de uno o no tener facilidad en las conexiones de redes (puerto UDP 123) hacia el exterior, se puede ejecutar el siguiente contenedor con un servidor NTP local sin requisitos de conectividad exterior.

```shell
$ docker run --name ntpd -d -p 123:123/udp pablogarciaarevalo/ntpd-local
```

### Asistente de instalación

Una vez termine el despliegue, accediendo a la IP del nodo de administración se completa el asistente de instalación.

![]({{site.baseurl}}/assets/img/Instalacion-automatica-de-StorageGRID-002.png)

