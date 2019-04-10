---
layout: post
title: Desplegando Trident en Kubernetes
category: 2019
description: Proceso para la instalación de Trident, el orquestador de almacenamiento persistente de NetApp, sobre un clúster de Kubernetes
---

[Trident](https://github.com/NetApp/trident) es un proyecto de código abierto totalmente mantenido por NetApp. Ha sido diseñado desde cero para ayudar a solucionar los requerimientos de persistencia de las aplicaciones en contenedores.

Después de haber [Instalado kubernetes con kubeadm]({{site.baseurl}}/2019/04/05/Instalar-kubernetes-con-kubeadm.html) se hará la instalación de Trident, el cual se despliega como un pod dentro del propio clúster de kubernetes. Este pod se aprovisiona como un deployment de Kubernetes, el cual está compuesto por dos contenedores, un contenedor etcd para almacenar los datos de los volúmenes persistentes y otro contenedor con el trident-main. El contenedor etcd utiliza un volumen persistente para almacenar los datos.

### Requisitos

Para que la instalación se lleve a cabo satisfactoriamente, es necesario poder montar el volumen persistente para el contenedor etcd durante el proceso de instalación. Para ello es necesario instalar los componentes NFS e iSCSI en los nodos del clúster de kubernetes. También se conecta los iniciadores iSCSI contra la dirección IP iSCSI del SVM de la cabina.

```shell
yum install nfs-utils -y
yum install -y iscsi-initiator-utils
iscsiadm --mode discovery --op update --type sendtargets --portal 192.168.0.202
iscsiadm --mode node -l all
iscsiadm --mode session
```

También se crea un namaspace en kubernetes.

```shell
kubectl create namespace trident
```

### Instalación

Inicialmente se descarga el paquete de Trident, en este caso la versión 19.01.0, y se descomprime.

```shell
wget https://github.com/NetApp/trident/releases/download/v19.01.0/trident-installer-19.01.0.tar.gz

tar -xf trident-installer-19.01.0.tar.gz
```

En la carpeta 'sample-imput' se encuentran varios ficheros de ejemplos para backends, storage classes y persistent volumes claim. Para facilitar la instalación se copia un fichero de ejemplo de backend a la carpeta 'setup', y se edita con la información del entorno. En este caso se ha utilizado una Storage Virtual Machine de acceso NAS para este primer volumen persistente.

```shell
cp sample-input/backend-ontap-nas.json setup/backend.json

cat setup/backend.json
{
    "version": 1,
    "storageDriverName": "ontap-nas",
    "backendName": "ONTAP NAS",
    "managementLIF": "192.168.0.200",
    "dataLIF": "192.168.0.201",
    "svm": "kubernetes",
    "username": "vsadmin",
    "password": "password"
}
```

Se ejecuta el programa de instalación.

```shell

./tridentctl install -n trident
INFO Created installer service account.            serviceaccount=trident-installer
INFO Waiting for object to be created.             objectName=clusterRole
INFO Created installer cluster role.               clusterrole=trident-installer
INFO Waiting for object to be created.             objectName=clusterRoleBinding
INFO Created installer cluster role binding.       clusterrolebinding=trident-installer
INFO Created installer configmap.                  configmap=trident-installer
INFO Waiting for object to be created.             objectName=installerPod
INFO Created installer pod.                        pod=trident-installer
INFO Waiting for Trident installer pod to start.
INFO Trident installer pod started.                namespace=trident pod=trident-installer
INFO Starting Trident installation.                namespace=trident
INFO Created service account.
INFO Created cluster role.
INFO Created cluster role binding.
INFO Created Trident deployment.
INFO Waiting for Trident pod to start.
INFO Trident pod started.                          namespace=trident pod=trident-764d46d5d8-dhnll
INFO Waiting for Trident REST interface.
INFO Trident REST interface is up.                 version=19.01.0
INFO Trident installation succeeded.
INFO Waiting for Trident installer pod to finish.
INFO Trident installer pod finished.               namespace=trident pod=trident-installer
INFO Deleted installer pod.                        pod=trident-installer
INFO Deleted installer configmap.                  configmap=trident-installer
INFO In-cluster installation completed.
INFO Deleted installer cluster role binding.
INFO Deleted installer cluster role.
INFO Deleted installer service account.
```

Después de que la instalación haya finalizado satisfactoriamente, se puede verificar la versión de Trident instalada.

```shell
./tridentctl version -n trident
+----------------+----------------+
| SERVER VERSION | CLIENT VERSION |
+----------------+----------------+
| 19.01.0        | 19.01.0        |
+----------------+----------------+
```

En el sistema de almacenamiento ONTAP se ha aprovisionado automáticamente el volumen para el contenedor etcd.

```shell
ontap::> volume show -vserver kubernetes
Vserver   Volume       Aggregate    State      Type       Size  Available Used%
--------- ------------ ------------ ---------- ---- ---------- ---------- -----
kubernetes trident_trident aggr_n3_DL_Data online RW       2GB     2.00GB    0%
```

Trident se ha instalado como un deployment de kubernetes, y se puede inspeccionar el pod para ver los detalles.

```shell
kubectl get deployment -n trident
NAME      READY   UP-TO-DATE   AVAILABLE   AGE
trident   1/1     1            1           5d22h

kubectl get pod -n trident
NAME                       READY   STATUS    RESTARTS   AGE
trident-764d46d5d8-dhnll   2/2     Running   0          2m19s
```

En uno de los nodos de kubernetes se puede encontrar los dos contenedores y acceder a ellos.

```shell
docker ps
CONTAINER ID        IMAGE                   COMMAND                  CREATED             STATUS              PORTS               NAMES
e76d386fca73        643c21638c1c            "/usr/local/bin/etcd…"   5 days ago          Up 5 days                               k8s_etcd_trident-764d46d5d8-dhnll_trident_f551eabd-56fe-11e9-a678-0050569654ca_0
e047298c8a87        1a74b69a7a5d            "/usr/local/bin/trid…"   5 days ago          Up 5 days                               k8s_trident-main_trident-764d46d5d8-dhnll_trident_f551eabd-56fe-11e9-a678-0050569654ca_0
```
