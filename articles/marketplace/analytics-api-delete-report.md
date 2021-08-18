---
title: Eliminación de API de informe
description: Use esta API para eliminar todos los registros de informes y de ejecución de informes para los informes de análisis de marketplace comercial.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: smannepalle
ms.author: smannepalle
ms.reviewer: sroy
ms.date: 3/08/2021
ms.openlocfilehash: 9cb8f12061e53a89fa0380d717c0ad947515ab5f
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121747245"
---
# <a name="delete-report-api"></a>Eliminación de API de informe

En la ejecución, esta API elimina todos los registros de ejecución de informes y de informes.

**Sintaxis de la solicitud**

| Método | URI de solicitud |
| ------------ | ------------- |
| Delete | `https://api.partnercenter.microsoft.com/insights/v1/cmp/ScheduledReport/{Report ID}` |
|||

**Encabezado de solicitud**

| Encabezado | Tipo | Descripción |
| ------------ | ------------- | ------------- |
| Authorization | string | Necesario. El token de acceso de Azure AD con el formato `Bearer <token>`. |
| Tipo de contenido | string | `Application/JSON` |
||||

**Parámetro de ruta de acceso**

Ninguno

**Parámetro de consulta**

| Nombre de parámetro | Obligatorio | string | Descripción |
| ------------ | ------------- | ------------- | ------------- |
| `reportId` | Sí | string | Id. del informe que se está modificando |
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
| `ReportId` | UUID único del informe eliminado. |
| `ReportName` | Nombre dado al informe durante la creación. |
| `Description` | Descripción proporcionada durante la creación del informe. |
| `QueryId` | Id. de consulta pasado en el momento en que se creó el informe. |
| `Query` | Texto de la consulta que se ejecutará para este informe. |
| `User` | Identificador de usuario usado para crear el informe |
| `CreatedTime` | Hora de creación del informe. El formato de la hora es aaaa-MM-ddTHH:mm:ssZ. |
| `ModifiedTime` | Hora en que se modificó el informe por última vez. El formato de la hora es aaaa-MM-ddTHH:mm:ssZ. |
| `StartTime` | Hora a la que se iniciará la ejecución del informe. El formato de la hora es aaaa-MM-ddTHH:mm:ssZ. |
| `ReportStatus` | Estado de ejecución del informe. Los valores posibles son en pausa, activo e inactivo. |
| `RecurrenceInterval` | Intervalo de periodicidad proporcionado durante la creación del informe. |
| `RecurrenceCount` | Recuento de periodicidad proporcionado durante la creación del informe. |
| `CallbackUrl` | Dirección URL de devolución de llamada proporcionada en la solicitud. |
| `Format` | Formato de los archivos de informe. |
|||
