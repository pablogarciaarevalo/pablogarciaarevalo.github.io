---
layout: post
title: Acceso Rest API al servicio Active IQ
category: 2020
description: Explicación de los pasos necesarios para acceder al servicio Active IQ programáticamente a través de Rest API
---

Active IQ de NetApp proporciona información a través de una variedad de widgets y paneles. El [servicio de API](https://mysupport.netapp.com/myautosupport/dist/index.html#/apiservices) permite al usuario acceder programáticamente a estos datos e integrarlos en el flujo de trabajo que se desea. Se requieren los siguientes tres pasos para el acceso a llamadas API.

* Autorización
* Autenticación
* Uso de llamadas API

![]({{site.baseurl}}/assets/img/Acceso-Rest-API-al-servicio-Active-IQ-001.png)

## Autorización

Para conseguir acceso al [servicio de API](https://mysupport.netapp.com/myautosupport/dist/index.html#/apiservices) hay que acceder al portal, identificarse con el usuario de la web de NetApp y solicitar autorización de acceso al servicio. El proceso de validación puede tardar un par de días y llegará por email la aceptación.

## Autenticación

Hay dos tipos de tokens para la autenticación del servicio API:

* Access Token: El token de acceso se utiliza para autenticar las solicitudes de API. Este token aduca después de 1 hora. Se puede crear un nuevo token de acceso utilizando la API en / tokens / accessToken.

* Refresh Token: El token de actualización se utiliza para generar un nuevo token de acceso y un token de actualización programáticamente. Este token caduca después de 7 días.

Accediendo al segundo paso del [servicio de API](https://mysupport.netapp.com/myautosupport/dist/index.html#/apiservices) se pueden descargar y copiar ambos tokens para empezar a utilizar las llamadas Rest API.

![]({{site.baseurl}}/assets/img/Acceso-Rest-API-al-servicio-Active-IQ-002.png)

Una vez descargadas, existe una propia llamada Rest API para refrescar el token de acceso usando el token de refresco. La sintaxis es la siguiente:

```shell
curl -X POST "https://api.activeiq.netapp.com/v1/tokens/accessToken" \
-H "accept: application/json" -H "Content-Type: application/json" \
-d "{ \"refresh_token\": \"$refresh_token\"}"
```

A continuación se muestra un ejemplo, donde se puede ver que la llamada Rest API devuelve un nuevo token de acceso y un nuevo token de refresco.

```shell
curl -X POST "https://api.activeiq.netapp.com/v1/tokens/accessToken" -H "accept: application/json" -H "Content-Type: application/json" -d "{ \"refresh_token\": \"eyJhbGciOiJSUzUxMiIsInR5cCI6IkpXVCIsIng1dCI6IndKZlQ2NHpqemQxWk4yS1duX3FYSmpvOHByMCIsImtpZCI6Im9yYWtleSJ9.eyJzdWIiOm51bGwsIm9yYWNsZS5vYXV0aC51c2VyX29yaWdpbl9pZF90eXBlIjoiTERBUF9VSUQiLCJvcmFjbGUub2F1dGgudXNlcl9vcmlnaW5faWQiOiJncGFibG8iLCJpc3MiOiJ3d3cub3JhY2xlLmV4YW1wbGUuY29tIiwib3JhY2xlLm9hdXRoLnJ0LnR0YyI6InJlc291cmNlX2FjY2Vzc190ayIsIm9yYWNsZS5vYXV0aC5zdmNfcF9uIjoiT0F1dGhTZXJ2aWNlUHJvZmlsZSIsImlhdCI6MTU3NTI4NjYyOCwib3JhY2xlLm9hdXRoLnRrX2NvbnRleHQiOiJyZWZyZXNoX3Rva2VuIiwiZXhwIjoxNTc1ODkxNDI4LCJwcm4iOm51bGwsImp0aSI6ImU5NmZkNDVlLTJhZWMtNDAzZS04MzZiLTc0NDNkNDkyZDkzYyIsIm9yYWNsZS5vYXV0aC5zY29wZSI6IkFzdXBVc2VyUHJvZmlsZS5tZSIsIm9yYWNsZS5vYXV0aC5jbGllbnRfb3JpZ2luX2lkIjoiTXlBc3VwQVBJIiwidXNlci50ZW5hbnQubmFtZSI6IkRlZmF1bHREb21haW4iLCJvcmFjbGUub2F1dGguaWRfZF9pZCI6IjEyMzQ1Njc4LTEyMzQtMTIzNC0xMjM0LTEyMzQ1Njc4OTAxMiJ9.TowktHB5YSYxKYxpDsTlw27mTS7qK0T8e7j5yRZ5qdQqafkUlYBYvXTXs8QMSuzNC4jfQRNdPryiY5BQQCLOiE6olJQC2WFVzSGqzC6pdZZxPiHS9EqDN6n6Mgabz_yo2vWeodMS5mhxdpmBufowJEM46pYZVLof_ubjxQjBONM\"}" | jq .

{
  "access_token": "eyJhbGciOiJSUzUxMiIsInR5cCI6IkpXVCIsIng1dCI6IndKZlQ2NHpqemQxWk4yS1duX3FYSmpvOHByMCIsImtpZCI6Im9yYWtleSJ9.eyJ1aWQiOiJncGFibG8iLCJzdWIiOiJncGFibG8iLCJvcmFjbGUub2F1dGgudXNlcl9vcmlnaW5faWRfdHlwZSI6IkxEQVBfVUlEIiwib3JhY2xlLm9hdXRoLnVzZXJfb3JpZ2luX2lkIjoiZ3BhYmxvIiwiaXNzIjoid3d3Lm9yYWNsZS5leGFtcGxlLmNvbSIsImxhc3RuYW1lIjoiR2FyY2lhIEFyZXZhbG8iLCJmaXJzdG5hbWUiOiJQYWJsbyIsIm9yYWNsZS5vYXV0aC5zdmNfcF9uIjoiT0F1dGhTZXJ2aWNlUHJvZmlsZSIsImlhdCI6MTU3NTI4NjYyOCwib3JhY2xlLm9hdXRoLnBybi5pZF90eXBlIjoiTERBUF9VSUQiLCJvcmFjbGUub2F1dGgudGtfY29udGV4dCI6InJlc291cmNlX2FjY2Vzc190ayIsImV4cCI6MTU3NTI5MDIyOCwicHJuIjoiZ3BhYmxvIiwiaXNfaW50ZXJuYWwiOiJ0cnVlIiwianRpIjoiMjIwOTM1ZmUtMjNkNS00ZGJlLWJjMjAtNjlhYmU0OTQzYmM2Iiwib3JhY2xlLm9hdXRoLnNjb3BlIjoiQXN1cFVzZXJQcm9maWxlLm1lIiwiY29tbW9ubmFtZSI6IlBhYmxvIEdhcmNpYSBBcmV2YWxvIiwib3JhY2xlLm9hdXRoLmNsaWVudF9vcmlnaW5faWQiOiJNeUFzdXBBUEkiLCJvcmFjbGUub2F1dGguaWRfZF9pZCI6IjEyMzQ1Njc4LTEyMzQtMTIzNC0xMjM0LTEyMzQ1Njc4OTAxMiIsInVzZXIudGVuYW50Lm5hbWUiOiJEZWZhdWx0RG9tYWluIn0.gZyG03_s7Bwig43ICB1i10tOue2ZqalFA2lbQUFcWtVIN67VcgAjTzaIRSO9zzwoFxhPjlrXuhZPvV4SvjVNRRR0HdUOTHGmXf-hvi8PdLnfpUU6AWO80PGy8Nid5v8YpLudygMjG_eGrtTwyeRmtnHUNqLTIqmfHs1eglejkQU",
  "refresh_token": "eyJhbGciOiJSUzUxMiIsInR5cCI6IkpXVCIsIng1dCI6IndKZlQ2NHpqemQxWk4yS1duX3FYSmpvOHByMCIsImtpZCI6Im9yYWtleSJ9.eyJzdWIiOm51bGwsIm9yYWNsZS5vYXV0aC51c2VyX29yaWdpbl9pZF90eXBlIjoiTERBUF9VSUQiLCJvcmFjbGUub2F1dGgudXNlcl9vcmlnaW5faWQiOiJncGFibG8iLCJpc3MiOiJ3d3cub3JhY2xlLmV4YW1wbGUuY29tIiwib3JhY2xlLm9hdXRoLnJ0LnR0YyI6InJlc291cmNlX2FjY2Vzc190ayIsIm9yYWNsZS5vYXV0aC5zdmNfcF9uIjoiT0F1dGhTZXJ2aWNlUHJvZmlsZSIsImlhdCI6MTU3NTI4NjYyOCwib3JhY2xlLm9hdXRoLnRrX2NvbnRleHQiOiJyZWZyZXNoX3Rva2VuIiwiZXhwIjoxNTc1ODkxNDI4LCJwcm4iOm51bGwsImp0aSI6ImU5NmZkNDVlLTJhZWMtNDAzZS04MzZiLTc0NDNkNDkyZDkzYyIsIm9yYWNsZS5vYXV0aC5zY29wZSI6IkFzdXBVc2VyUHJvZmlsZS5tZSIsIm9yYWNsZS5vYXV0aC5jbGllbnRfb3JpZ2luX2lkIjoiTXlBc3VwQVBJIiwidXNlci50ZW5hbnQubmFtZSI6IkRlZmF1bHREb21haW4iLCJvcmFjbGUub2F1dGguaWRfZF9pZCI6IjEyMzQ1Njc4LTEyMzQtMTIzNC0xMjM0LTEyMzQ1Njc4OTAxMiJ9.TowktHB5YSYxKYxpDsTlw27mTS7qK0T8e7j5yRZ5qdQqafkUlYBYvXTXs8QMSuzNC4jfQRNdPryiY5BQQCLOiE6olJQC2WFVzSGqzC6pdZZxPiHS9EqDN6n6Mgabz_yo2vWeodMS5mhxdpmBufowJEM46pYZVLof_ubjxQjBONM"
}
```

## Uso de llamadas API

Una vez conseguidos los tokens en el paso anterior, se puede ver el [catálogo de servicios](https://mysupport.netapp.com/myautosupport/dist/index.html#/apidocs/serviceList) que se pueden consultar a través de Rest API.

![]({{site.baseurl}}/assets/img/Acceso-Rest-API-al-servicio-Active-IQ-003.png)

En el siguiente repositorio pueden consultarse ejemplos de llamadas Rest API al servicio NetApp Active IQ: [https://github.com/netappspain/Active-IQ-Rest-API](https://github.com/netappspain/Active-IQ-Rest-API)

