---
layout: post
title: Instalar kubernetes con kudeadm
category: 2019
description: Detalle del proceso de instalación de kubernetes usando kubeadm en CentOS 7
---

A continuación de muestras los pasos para la instalación de kuberenetes sobre tres servidores CentOS 7 con una instalación mínima realizada usando kubeadm. El objetivo de esta instalación es disponer de un clúster pequeño para entornos de pruebas.

### Requisitos

Hay que verificar que los tres servidores tienen visibilidad entre ambos y con resolución de nombres. Las siguientes tareas se realizan en los tres servidores.

Se deshabilita el firewall al ser un entorno de pruebas.

```shell
systemctl disable firewalld
systemctl stop firewalld
systemctl status firewalld
```
Se deshabilita el SElinux.

```shell
setenforce 0
sed -i 's/SELINUX=enforcing/SELINUX=disabled/g' /etc/selinux/config
```

Se modifican variables del sysctl.

```shell
cat <<EOF > /etc/sysctl.d/k8s.conf
net.bridge.bridge-nf-call-ip6tables = 1
net.bridge.bridge-nf-call-iptables = 1
EOF
sysctl -p
```

De deshabilita el SWAP y se comentan las líneas de swap en el fichero fstab.

```shell
swapoff -a
vi /etc/fstab
```

### Instalación de docker

```shell
yum install -y yum-utils device-mapper-persistent-data lvm2
 
yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo

yum update
yum install -y docker-ce-18.06.1.ce

mkdir /etc/docker

cat > /etc/docker/daemon.json <<EOF
{
"exec-opts": ["native.cgroupdriver=systemd"],
"log-driver": "json-file",
"log-opts": {
"max-size": "100m"
},
"storage-driver": "overlay2",
"storage-opts": [
"overlay2.override_kernel_check=true"
]
}
EOF

mkdir -p /etc/systemd/system/docker.service.d

systemctl daemon-reload
systemctl restart docker
systemctl enable docker
```

### Instalación de kuberentes

Se configuran los repositorios para poder descargar los paquetes.

```shell
cat <<EOF > /etc/yum.repos.d/kubernetes.repo
[kubernetes]
name=Kubernetes
baseurl=https://packages.cloud.google.com/yum/repos/kubernetes-el7-x86_64
enabled=1
gpgcheck=1
repo_gpgcheck=1
gpgkey=https://packages.cloud.google.com/yum/doc/yum-key.gpg https://packages.cloud.google.com/yum/doc/rpm-package-key.gpg
exclude=kube*
EOF
```

Se instalan unas versiones en concreto de los paquetes de kubernetes.

```shell
yum install -y kubelet-1.13.5 kubeadm-1.13.5 kubectl-1.13.5 --disableexcludes=kubernetes
systemctl enable kubelet 
systemctl start kubelet
```

Se inicializa el clúster ejecutando el siguientes comando **solo desde el master**.

```shell
kubeadm init
```

Se ejecutan los siguientes comandos tal y como muestra la salida del comando de inicialización.

```shell
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
```

### Unión de nodos al clúster

Desde el resto de nodos, se ejecuta el siguientes comando para unirlos al clúster.

```shell
kubeadm join 192.168.0.11:6443 --token x0hs5a.z7cjeel7326m08hb --discovery-token-ca-cert-hash sha56:3827b8400d6030b41b3677a27f017e42ad52e5c252730bc57e975451c47350bd
```

### Gestión del clúster

Se verifica el estado del clúster.

```shell
kubectl get pods --all-namespaces
kubectl get nodes
```

Se configura la red a utilizar dentro de Kubernetes. En este caso se instala Wave.

```shell
export kubever=$(kubectl version | base64 | tr -d '\n')
kubectl apply -f "https://cloud.weave.works/k8s/net?k8s-version=$kubever"
```

Se verifica el estado del clúster

```shell
kubectl get pods --all-namespaces
```



