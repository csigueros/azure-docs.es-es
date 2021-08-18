---
title: Actualización de la API de informe
description: Use esta API con los parámetros de informes de análisis del marketplace comercial.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: smannepalle
ms.author: smannepalle
ms.reviewer: sroy
ms.date: 3/08/2021
ms.openlocfilehash: 35c01bf83d4c5ecf98a49a17fce62bfd757be122
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121748308"
---
# <a name="update-report-api"></a>Actualización de la API de informe

Esta API ayuda a modificar un parámetro de informe.

**Sintaxis de la solicitud**

| Método | URI de la solicitud |
| ------------ | ------------- |
| PUT | `https://api.partnercenter.microsoft.com/insights/v1/cmp/ScheduledReport/{Report ID}` |
|||

**Encabezado de solicitud**

| Encabezado | Tipo | Descripción |
| ------------ | ------------- | ------------- |
| Authorization | string | Necesario. Token de acceso de Azure Active Directory (Azure AD) con el formato `Bearer <token>` |
| Content-Type | string | `Application/JSON` |
||||

**Parámetro de ruta de acceso**

Ninguno

**Parámetro de consulta**

| Nombre de parámetro | Obligatorio | Tipo | Descripción |
| ------------ | ------------- | ------------- | ------------- |
| `reportId` | Sí | string | Identificador del informe que se va a modificar |
|||||

**Carga de solicitud**

```json
{
  "ReportName": "string",
  "Description": "string",
  "StartTime": "string",
  "RecurrenceInterval": 0,
  "RecurrenceCount": 0,
  "Format": "string",
  "CallbackUrl": "string"
}
```

**Glosario**

En esta tabla se proporcionan las definiciones clave de los elementos de la carga útil de solicitud.

| Parámetro | Obligatorio | Descripción | Valores permitidos |
| ------------ | ------------- | ------------- | ------------- |
| `ReportName` | Sí | Nombre que se va a asignar al informe | string |
| `Description` | No | Descripción del informe creado | string |
| `StartTime` | Sí | Marca de tiempo después de la que comenzará la generación del informe | string |
| `RecurrenceInterval` | No | Frecuencia (en horas) con la que se debe generar el informe. El valor mínimo es 4. | integer |
| `RecurrenceCount` | No | Número de informes que se van a generar. El valor predeterminado es indefinido. | integer |
| `Format` | Sí | Formato del archivo exportado. El valor predeterminado es CSV. | CSV/TSV |
| `CallbackUrl` | Sí | Dirección URL de devolución de llamada HTTPS a la que se llamará en la generación de informes. | string |
|||||

**Glosario**

Ninguno

**Respuesta**

La carga de respuesta tiene la estructura siguiente:

Código de respuesta: 200, 400, 401, 403, 404, 500

Carga de respuesta:

```json
{
  "Value": [
    {
      "ReportId": "string",
      "ReportName": "string",
      "Description": "string",
      "QueryId": "string",
      "Query": "string",
      "User": "string",
      "CreatedTime": "string",
      "ModifiedTime": "string",
      "StartTime": "string",
      "ReportStatus": "string",
      "RecurrenceInterval": 0,
      "RecurrenceCount": 0,
      "CallbackUrl": "string",
      "Format": "string"
    }
  ],
  "TotalCount": 0,
  "Message": "string",
  "StatusCode": 0
}
```

**Glosario**

| Parámetro | Description |
| ------------ | ------------- |
| `ReportId` | Identificador único universal (UUID) del informe que se actualiza |
| `ReportName` | Nombre dado al informe en la carga útil de solicitud |
| `Description` | Descripción proporcionada durante la creación del informe. |
| `QueryId` | Id. de consulta pasado en el momento en que se creó el informe. |
| `Query` | Texto de la consulta que se ejecutará para este informe. |
| `User` | Identificador de usuario usado para crear el informe |
| `CreatedTime` | Hora de creación del informe. El formato de la hora es aaaa-MM-ddTHH:mm:ssZ. |
| `ModifiedTime` | Hora en que se modificó el informe por última vez. El formato de la hora es aaaa-MM-ddTHH:mm:ssZ. |
| `StartTime` | Hora a la que se iniciará la ejecución del informe. El formato de la hora es aaaa-MM-ddTHH:mm:ssZ. |
| `ReportStatus` | Estado de la ejecución del informe. Los valores posibles son en pausa, activo e inactivo. |
| `RecurrenceInterval` | Intervalo de periodicidad proporcionado durante la creación del informe. |
| `RecurrenceCount` | Recuento de periodicidad proporcionado durante la creación del informe. |
| `CallbackUrl` | Dirección URL de devolución de llamada proporcionada en la solicitud. |
| `Format` | Formato de los archivos de informe. |
|||
