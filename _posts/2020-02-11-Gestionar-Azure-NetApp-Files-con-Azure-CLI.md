---
layout: post
title: Gestionar Azure NetApp Files con Azure CLI
category: 2020
description: Procedimiento para gestionar las cuentas, pools, volúmenes y snapshots de Azure NetApp Files con Azure CLI
---
Azure NetApp Files es el servicio gestionado empresarial de ficheros de alto rendmiento de Microsoft Azure. Desde la suscripción de Azure es necesaria crear una cuenta de Azure NetApp para poder crear un Pool de almacenamiento. Cada uno de los Pool es de un tipo: Standard, Premium o Ultra, que proporciona hasta 16 MiB/s, 64 MiB/s o 128 MiB/s de throughput respectivamente por cada 1 TiB de volúmen. La capacidad mínima de cada Pool es de 4 TiB. Desde cada Pool se pueden crear los volúmenes que se quiera, con una capacidad mínima de 100 GiB y máxima de 100 TiB.

![]({{site.baseurl}}/assets/img/Gestionar-Azure-NetApp-Files-con-Azure-CLI-001.png)

Previo a ninguna acción actualizo Azure CLI a la última versión: 

```posh
Invoke-WebRequest -Uri https://aka.ms/installazurecliwindows \
   -OutFile .\AzureCLI.msi; Start-Process msiexec.exe \
   -Wait -ArgumentList '/I AzureCLI.msi /quiet'
```
### Gestión de la cuenta

Tras loguearse con las credenciales, se crea una cuenta de Azure NetApp y se comprueba su estado.

```posh
az login

$RESOURCE_GROUP="Mi_Grupo_Recurso"
$ANF_ACCOUNT_NAME="Mi_Cuenta_ANF"
$LOCATION="eastus"

az netappfiles account create --resource-group $RESOURCE_GROUP \
   --location $LOCATION --account-name $ANF_ACCOUNT_NAME

az netappfiles account list --resource-group $RESOURCE_GROUP
```

### Gestión de pool

Una vez creada la cuenta ya es posible crear un Pool de almacenamiento. En el ejemplo se crea un Pool de 4 TiB de capacidad de tipo Standard. Después se lista para comprobar su estado.

```posh
az netappfiles pool create --resource-group $RESOURCE_GROUP \
   --location $LOCATION --account-name $ANF_ACCOUNT_NAME \
   --pool-name anf_standard --size 4 --service-level Standard

az netappfiles pool list -g $RESOURCE_GROUP --account-name $ANF_ACCOUNT_NAME
```

### Gestión de volúmenes

Y sobre el pool de almacenamiento que se acaba de crear previamente, se crean los volúmenes de ficheros que se desean con la capacidad apropiada. En el ejemplo se crea un volúmen de 100 GiB para ser accedido a través de NFS.

```posh
az netappfiles volume create -g $RESOURCE_GROUP 
   --account-name $ANF_ACCOUNT_NAME --pool-name anf_standard \
   --name mi_volumen --location $LOCATION --service-level Standard \
   --usage-threshold 100 --file-path "mi_volumen" --vnet mi_red_virtual \
   --subnet default --protocol-types NFSv3

az netappfiles volume list -g $RESOURCE_GROUP \
   --account-name $ANF_ACCOUNT_NAME --pool-name anf_standard
```

### Gestión de snapshots

Para crear un Snapshot de un volumen existente se realiza con los siguientes comandos:

```posh
az netappfiles snapshot create -g $RESOURCE_GROUP \
   --account-name $ANF_ACCOUNT_NAME --pool-name anf_standard \
   --volume-name mi_volumen --name mi_snapshot1 -l $LOCATION

az netappfiles snapshot list -g $RESOURCE_GROUP \
   --account-name $ANF_ACCOUNT_NAME --pool-name anf_standard \
   --volume-name mi_volumen
```
