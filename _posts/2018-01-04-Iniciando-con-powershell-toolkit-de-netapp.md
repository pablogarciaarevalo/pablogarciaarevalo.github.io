---
layout: post
title: Iniciando con Powershell toolkit de NetApp
category: 2018
description: Pasos y ejemplo para iniciarse con la Powershell toolkit de NetApp
---

La [PowerShell Toolkit](https://mysupport.netapp.com/tools/info/ECMLP2310788I.html?productID=61926) de NetApp unifica todos los módulos de Powershell en un único paquete. Actualmente soporta las cabinas de almacenamiento ONTAP en todas sus variantes (FAS, AFF, Cloud ONTAP) y cabinas E-Series.

Tras instalar el paquete con un 'siguiente, siguiente, siguiente' y abrir una nueva consola de Powershell, es interesante ejecutar el siguiente comando que abrirá un archivo HTML con toda la información de la integración con NetApp, detalles de cada uno de los cmdlets, y ejemplos de uso.

```posh
PS C:\> show-nchelp
```

A continuación se muestra un primer script de ejemplo tipo [Hello World](https://en.wikipedia.org/wiki/%22Hello,_World!%22_program) en el que se listan los volúmenes de una cabina ONTAP que no son root y que no tienen la funcionalidad de auto crecimiento habilitado.

### Código de ejemplo

Primero se ha creado un simple script que pregunta por la contraseña de una cabina y la almacena en un fichero de texto de forma cifrada para evitar tener contraseñas en claro.

```posh
# script name: create_securestring_file.ps1

$clusterName = Read-Host "What is the cluster's name?"
$password = Read-Host 'What is your password?' -AsSecureString

$PathFile = ".\$clusterName.txt"
$password | convertfrom-securestring | out-file $PathFile
```

El siguiente script se conecta a una cabina ONTAP y lista los volúmenes que no sean root y que no tengan habilitado el autosize.

```posh
# script name:  check_nonroot_vols_without_autosize.ps1

$clusterName = "cluster1"
$username = "admin"
$PathFile = ".\$clusterName.txt"

# Get the secured password from the cluster
$Secstr = cat $PathFile | convertto-securestring
$Cred = new-object -typename System.Management.Automation.PSCredential -argumentlist $username, $Secstr

# Connect to cluster
Write-Host "Connecting to cluster $destinationClusterName ... " -NoNewLine
$conn = Connect-NcController -Name $clusterName -HTTPS -Credential $Cred
if ($conn -eq $null)
    {
    Write-Host "Connection to host $destinationClusterName failed!" -foregroundcolor "red"
    break
    }
else {
    Write-Host "Done." -foregroundcolor "green"
}

Get-NcVol | ?{ ($_.VolumeStateAttributes.IsVserverRoot -eq $false) -and ($_.VolumeAutosizeAttributes.IsEnabled -eq $false) }
```

### Ejecución del ejemplo

Por último se muestra la ejecución de los dos ejemplos de código mostrado anteriormente.

```posh
PS C:\> .\create_secure_password.ps1
What is the clusters name?: cluster1
What is your password?: ********
PS C:\>
PS C:\> .\check_nonroot_vols_without_autosize.ps1
Connecting to cluster  ... Done.

Name                      State       TotalSize  Used  Available Dedupe Aggregate                 Vserver
----                      -----       ---------  ----  --------- ------ ---------                 -------
myvol2                    online         1.0 GB    5%   972.3 MB False  aggr2_cluster1_01         svm1
svm1_vol01                online         1.0 GB    5%   971.4 MB False  aggr1_cluster1_01         svm1
```


