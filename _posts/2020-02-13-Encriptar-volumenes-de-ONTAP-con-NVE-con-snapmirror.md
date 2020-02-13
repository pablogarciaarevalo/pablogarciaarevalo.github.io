---
layout: post
title: Encriptar volúmenes de ONTAP con NVE con snapmirror
category: 2020
description: Pasos para encriptar volúmenes de ONTAP que estén replicados con SnapMirror usando NVE 
---
NetApp Volume Encryption (NVE) es una tecnología software que permite encriptar los datos de volúmenes en sistemas de almacenamiento ONTAP. En el caso de que el tipo de replicación de SnapMirror sea tipo DP, los pasos para poder encriptar los volúmenes origen y destino son los siguientes:

### Parar la replicación

```posh
destino> snapmirror quiesce -destination-path svm_destino:volumen_destino

destino> snapmirror break -destination-path svm_destino:volumen_destino
```
### Encriptar los volúmenes origen y destino

```posh
origen> volume encryption conversion start -vserver svm_origen -volume volumen_origen

destino> volume encryption conversion start -vserver svm_destino -volume volumen_destino
```
### Sincronizar la replicación

```posh
destino> snapmirror resync -destination-path svm_destino:volumen_destino
```
