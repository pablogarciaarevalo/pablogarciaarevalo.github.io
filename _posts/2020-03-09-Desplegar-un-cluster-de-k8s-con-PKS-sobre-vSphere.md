---
layout: post
title: Desplegar un clúster de Kubernetes con PKS sobre vSphere
category: 2020
description: Ejemplo para la conexión y despliegue de un clúster de kubernetes con PKS sobre VMware vSphere
---
En este ejemplo se muestra cómo conectar y desplegar un clúster de Kubernetes con Pivotal Container Service (PKS) sobre una plataforma VMware vSphere.

### Conectar con el servicio Bosh

Para obtener la contraseña puede accederse a la gestión web de Bosh en este enlace: [https://bosh_mgmt/api/v0/deployed/director/credentials/uaa_admin_user_credentials](https://bosh_mgmt/api/v0/deployed/director/credentials/uaa_admin_user_credentials)

```posh
myHost$ bosh -e pks login
Using environment '192.168.42.42'
 
Email (): admin
Password ():
 
Successfully authenticated with UAA
 
Succeeded
```
### Verificar los clúster de Kubernetes existentes

En este caso no hay ningún clúster de Kubernetes.

```posh
myHost$ ./pks clusters
 
PKS Version  Name  k8s Version  Plan Name  UUID  Status  Action
```
### Crear el primer clúster de Kubernetes

```posh
myHost$ ./pks create-cluster my-cluster-1 --external-hostname my-cluster-1.demolab.es --plan small
 
PKS Version:              1.6.0-build.17
Name:                     my-cluster-1
K8s Version:              1.15.5
Plan Name:                small
UUID:                     3751fef3-ec0e-415e-8e17-ee631bbfa39f
Last Action:              CREATE
Last Action State:        in progress
Last Action Description:  Creating cluster
Kubernetes Master Host:   my-cluster-1.demolab.es
Kubernetes Master Port:   8443
Worker Nodes:             3
Kubernetes Master IP(s):  In Progress
Network Profile Name:
 
Use 'pks cluster my-cluster-1' to monitor the state of your cluster
```

Verificar el clúster de Kubernetes que se está creando.

```posh
myHost$ ./pks clusters
 
PKS Version     Name          k8s Version  Plan Name  UUID                                  Status       Action
1.6.0-build.17  my-cluster-1  1.15.5       small      3751fef3-ec0e-415e-8e17-ee631bbfa39f  in progress  CREATE
```

Esperar hasta que la creación del clúster termine correctamente y mostrar los detalles.

```posh
myHost$ ./pks clusters
 
PKS Version     Name          k8s Version  Plan Name  UUID                                  Status     Action
1.6.0-build.17  my-cluster-1  1.15.5       small      3751fef3-ec0e-415e-8e17-ee631bbfa39f  succeeded  CREATE
 
myHost$ ./pks cluster my-cluster-1
 
PKS Version:              1.6.0-build.17
Name:                     my-cluster-1
K8s Version:              1.15.5
Plan Name:                small
UUID:                     3751fef3-ec0e-415e-8e17-ee631bbfa39f
Last Action:              CREATE
Last Action State:        succeeded
Last Action Description:  Instance provisioning completed
Kubernetes Master Host:   my-cluster-1.demolab.es
Kubernetes Master Port:   8443
Worker Nodes:             3
Kubernetes Master IP(s):  192.168.42.43
Network Profile Name:
```

### Añadir la resolución de nombres DNS

Para este ejemplo se ha utilizado la resolución de nombres local.
 
 ```posh
myHost$ cat /etc/hosts | grep 192.168.42.43
192.168.42.43 my-cluster-1 my-cluster-1.demolab.es
 ```

### Conectar con kubectl

```posh
myHost$ ./pks get-credentials my-cluster-1
 
Fetching credentials for cluster my-cluster-1.
Context set for cluster my-cluster-1.
 
You can now switch between clusters by using:
$kubectl config use-context <cluster-name>
```

```posh
myHost$ kubectl config use-context my-cluster-1
Switched to context "my-cluster-1".
 
myHost$ kubectl get nodes -o wide
NAME                                   STATUS   ROLES    AGE     VERSION   INTERNAL-IP     EXTERNAL-IP     OS-IMAGE             KERNEL-VERSION      CONTAINER-RUNTIME
3e362c3f-fa60-4b3e-88a8-1ecf9956250a   Ready    <none>   8m7s    v1.15.5   10.67.217.245   10.67.217.245   Ubuntu 16.04.6 LTS   4.15.0-66-generic   docker://18.9.9
d1fefe9d-f0e1-40d1-98eb-adacbbad6ffc   Ready    <none>   7m23s   v1.15.5   10.67.217.246   10.67.217.246   Ubuntu 16.04.6 LTS   4.15.0-66-generic   docker://18.9.9
eaae0493-00f2-4753-bdbd-15b42422867f   Ready    <none>   11m     v1.15.5   10.67.217.244   10.67.217.244   Ubuntu 16.04.6 LTS   4.15.0-66-generic   docker://18.9.9
```

### Verificar los servidores virtuales de PKS

```posh
myHost$ bosh -e pks vms
Using environment '10.67.217.241' as user 'admin'
 
Task 330
Task 329
Task 330 done
 
Task 329 done
 
Deployment 'pivotal-container-service-f29a70fb8ebab2d542dc'
 
Instance                                                        Process State  AZ              IPs            VM CID                                   VM Type     Active
pivotal-container-service/eea2902b-f019-45f7-b4f2-ec956aa8e37f  running        eu-netapplab-1  10.67.217.242  vm-3e0ba0ac-61fd-4fd5-8829-eb08b8959611  large.disk  true
 
1 vms
 
Deployment 'service-instance_3751fef3-ec0e-415e-8e17-ee631bbfa39f'
 
Instance                                     Process State  AZ              IPs            VM CID                                   VM Type      Active
master/f9cfdfbf-5c55-48a0-8ce3-ee3cede68757  running        eu-netapplab-1  10.67.217.243  vm-29ff4d6f-67f6-41b0-88e7-a5eb40f1b345  medium.disk  true
worker/24417563-c553-4080-b205-83de391fcad5  running        eu-netapplab-1  10.67.217.244  vm-2a549b6b-3c01-4cd7-819c-42f4590063ce  medium.disk  true
worker/65f918cc-e3aa-4887-89dc-ed678e3a0d77  running        eu-netapplab-1  10.67.217.245  vm-34542b51-c215-4a3d-ad74-6344453cb19d  medium.disk  true
worker/e43579d2-d067-469c-9859-154c4ec8cbd5  running        eu-netapplab-1  10.67.217.246  vm-1cdb2d6f-14fe-4a46-b4c7-54578a9c8769  medium.disk  true
 
4 vms
 
Succeeded
```
