---
layout: post
title: Mi primer PlayBook de Ansible con ONTAP
category: 2017
description: Procedimiento de la instalación del software Ansible, los requisitos para la integración con las cabinas de almacenamiento de ONTAP, y un ejemplo básico de la ejecución de un PlayBook.
---

[Ansible](https://www.ansible.com/) es una herramienta que permite la gestión de configuraciones, la provisión, el despliegue y la orquestación. Ansible no requiere de la instalación de agentes o clientes en los equipos que gestiona y está basado en ficheros [YAML](https://en.wikipedia.org/wiki/YAML).

Desde la versión 2.3 de Ansible, varios módulos de NetApp ya son parte del proyecto de forma nativa. Dentro del proyecto existen módulos para las cabinas de NetApp ONTAP, E-series y SolidFire. Para ver más información es interesante acceder a este contenido [https://www.ansible.com/integrations/infrastructure/netapp](https://www.ansible.com/integrations/infrastructure/netapp). Los detalles de los módulos están en este enlace [http://docs.ansible.com/ansible/latest/list_of_storage_modules.html#netapp](http://docs.ansible.com/ansible/latest/list_of_storage_modules.html#netapp) que proporcionan toda la información de los input necesarios para ejecutar cada módulo.

### Instalación

En el caso de las cabinas ONTAP, los requisitos necesarios para trabajar con Ansible son:
- Una cabina física o virtual con ONTAP versión 8.3 o superior
- Ansible 2.3
- Paquete netapp-lib (2015.9.25)

Para instalarlo simplemente hay que ejecutar los siguientes comandos:

```shell
$ pip install ansible --user
$ pip install netapp-lib --user
```

En mi caso ha sido necesario instalar algun paquete adicional. Los siguientes comandos muestran que son necesarios más paquetes y la instalación de los mismos.

```shell
$ python -c "from netapp_lib.api.zapi import zapi" && echo $?
$ python -c "from netapp_lib.api.zapi import errors" && echo $?
$ pip install lxml
$ pip install oslo_log --ignore-installed --user
```

### Ejemplo

Y el siguiente código YAML muestra el ejemplo básico para la creación un SVM (Storage Virtual Machine) sobra una cabina ONTAP. El código está actualizado en el repositorio de [GitHub](https://github.com/pablogarciaarevalo/ansible_playbooks/blob/master/example_netapp_ontap.yml):


```yaml
---
# This playbook deploys a NetApp SVM on ONTAP

- name: example netapp ontap
  hosts: localhost
  gather_facts: no
  connection: local

  vars:
    netapp_hostname: 192.168.0.10
    netapp_username: admin
    netapp_password: password
  tasks:
    - name: SVM Manager
      na_cdot_svm:
        state: present
        name: ansibleVServer
        root_volume: vol1
        root_volume_aggregate: aggrSATA_n1
        root_volume_security_style: ntfs
        hostname: "{{ netapp_hostname }}"
        username: "{{ netapp_username }}"
        password: "{{ netapp_password }}"

    - name: Volume Manager
      na_cdot_volume:
        state: present
        name: ansibleVolume
        aggregate_name: aggrSATA_n1
        size: 20
        size_unit: mb
        vserver: ansibleVServer
        hostname: "{{ netapp_hostname }}"
        username: "{{ netapp_username }}"
        password: "{{ netapp_password }}"

```

La ejecución de este código YAML simplemente se realizaría con el siguiente comando:

```shell
$ ansible-playbook example_netapp_ontap.yml

 [WARNING]: Could not match supplied host pattern, ignoring: all
 [WARNING]: provided hosts list is empty, only localhost is available

PLAY [example netapp ontap] ********************************************
TASK [SVM Manager] *****************************************************
changed: [localhost]

TASK [Volume Manager] **************************************************
changed: [localhost]

PLAY RECAP *************************************************************

```

Y se puede revisar que se ha creado satisfactoriamente el SVM en ONTAP.

```shell
cluster1::> vserver show -vserver *ansible*
                               Admin      Operational Root
Vserver     Type    Subtype    State      State       Volume     Aggregate
----------- ------- ---------- ---------- ----------- ---------- ----------
ansibleVServer data default    running    running     vol1       aggrSATA_n1

```


