---
layout: post
title: Mi primer PlayBook de Ansible con SolidFire
category: 2018
description: Procedimiento de la instalación del software Ansible, los requisitos para la integración con las cabinas de almacenamiento de SolidFire, y un ejemplo básico de la ejecución de un PlayBook.
---

De igual forma que se ha detallado el procedimiento para poder ejecutar [Mi primer PlayBook de Ansible con ONTAP]({{site.baseurl}}/2017/12/17/Mi-primer-playbook-de-ansible-con-ontap.html), en este caso se realizará lo mismo para las cabinas de almacenamiento de NetApp [SolidFire](https://www.netapp.com/us/products/storage-systems/all-flash-array/solidfire-web-scale.aspx).

Los detalles de los módulos están en este [enlace](http://docs.ansible.com/ansible/latest/list_of_storage_modules.html#netapp) que proporcionan toda la información de los input necesarios para ejecutar cada módulo.

### Instalación

En el caso de las cabinas SolidFire, los requisitos necesarios para trabajar con Ansible son:
- Ansible 2.3
- Paquete solidfire-sdk-python

Para instalar el paquete necesario simplemente hay que ejecutar el siguiente comando:

```shell
$ pip install solidfire-sdk-python
```

### Ejemplo

El siguiente código YAML muestra el ejemplo básico para la creación un volumen sobra una cabina SolidFire. El código está actualizado en el repositorio de [GitHub](https://github.com/pablogarciaarevalo/ansible_playbooks/blob/master/example_netapp_solidfire.yml):


```yaml
---
# This playbook deploys a NetApp volume on SolidFire

- name: example netapp solidfire
  hosts: localhost
  gather_facts: no
  connection: local

  vars:
    netapp_hostname: 192.168.0.10
    netapp_username: admin
    netapp_password: password
  tasks:  
    - name: Create Volume
      sf_volume_manager:
        hostname: "{{ netapp_hostname }}"
        username: "{{ netapp_username }}"
        password: "{{ netapp_password }}"
        state: present
        name: AnsibleVol
        qos: {minIOPS: 1000, maxIOPS: 20000, burstIOPS: 50000}
        account_id: 3
        enable512e: False
        size: 1
        size_unit: gb

```

La ejecución de este código YAML simplemente se realizaría con el siguiente comando:

```shell
$ ansible-playbook example_netapp_solidfire.yml
 [WARNING]: Could not match supplied host pattern, ignoring: all
 [WARNING]: provided hosts list is empty, only localhost is available

PLAY [example netapp solidfire] ****************************************

TASK [Create Volume] ***************************************************
changed: [localhost]

PLAY RECAP *************************************************************
localhost              : ok=1    changed=1    unreachable=0    failed=0
```

Y se puede revisar que se ha creado satisfactoriamente el volumen dentro de la cabina de SolidFire.

```posh
PS C:\> Get-SFVolume -Name AnsibleVol

VolumeID           : 27
Name               : AnsibleVol
AccountID          : 3
CreateTime         : 2018-01-13T16:46:22Z
Status             : active
Access             : readWrite
Enable512e         : False
Iqn                : iqn.2010-01.com.solidfire:n6o7.ansiblevol.27
ScsiEUIDeviceID    : 6e366f370000001bf47acc0100000000
ScsiNAADeviceID    : 6f47acc1000000006e366f370000001b
Qos                : SolidFire.Element.Api.VolumeQOS
VolumeAccessGroups : {}
VolumePairs        : {}
DeleteTime         :
PurgeTime          :
SliceCount         : 1
TotalSize          : 1000341504
BlockSize          : 4096
VirtualVolumeID    :
Attributes         : {}

```


