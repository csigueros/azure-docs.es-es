---
title: Obtención de API de consultas de informe
description: Use esta API para obtener todas las consultas que están disponibles para su uso en los informes de análisis comerciales de marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: smannepalle
ms.author: smannepalle
ms.reviewer: sroy
ms.date: 3/08/2021
ms.openlocfilehash: f3a6351f726a5b1bd408764280ad2487cf7ecd95
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121747240"
---
# <a name="get-report-queries-api"></a>Obtención de API de consultas de informe

La API de obtención de consultas de informe obtiene todas las consultas que están disponibles para su uso en informes. Obtiene todas las consultas del sistema y las definidas por el usuario de forma predeterminada.

**Sintaxis de la solicitud**

| **Método** | **URI de solicitud** |
| --- | --- |
| GET | `https://api.partnercenter.microsoft.com/insights/v1/cmp/ScheduledQueries?queryId={QueryID}&queryName={QueryName}&includeSystemQueries={include_system_queries}&includeOnlySystemQueries={include_only_system_queries}` |
|||

**Encabezado de solicitud**

| **Header** | **Tipo** | **Descripción** |
| --- | --- | --- |
| Authorization | string | Necesario. Token de acceso de Azure Active Directory (Azure AD) con el formato `Bearer <token>` |
| Content-Type | string | `Application/JSON` |
||||

**Parámetro de ruta de acceso**

Ninguno

**Parámetro de consulta**

| **Nombre del parámetro** | **Tipo** | **Obligatorio** | **Descripción** |
| --- | --- | --- | --- |
| `queryId` | string | No | Filtrar para obtener los detalles de solo las consultas con el id. dado en el argumento |
| `queryName` | string | No | Filtrar para obtener los detalles de solo las consultas con el nombre dado en el argumento |
| `IncludeSystemQueries` | boolean | No | Incluir consultas predefinidas por el sistema en la respuesta |
| `IncludeOnlySystemQueries` | boolean | No | Incluir solo las consultas del sistema en la respuesta |
|||||

**Carga de la solicitud**:

Ninguno

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
      "QueryId": "string",
      "Name": "string",
      "Description": "string",
      "Query": "string",
      "Type": "string",
      "User": "string",
      "CreatedTime": "string",
      "ModifiedTime": "string"
    }
  ],
  "TotalCount": 0,
  "Message": "string",
  "StatusCode": 0
}
```

**Glosario**

En esta tabla se describen las definiciones clave de los elementos de la respuesta.

| **Parámetro** | **Descripción** |
| --- | --- |
| `QueryId` | UUID único de la consulta. |
| `Name` | Nombre asignado a la consulta en el momento de la creación de la consulta. |
| `Description` | Descripción proporcionada durante la creación de la consulta. |
| `Query` | Cadena de consulta del informe. |
| `Type` | Se establece en `userDefined` para las consultas creadas por el usuario y en `system` para las consultas predefinidas por el sistema. |
| `User` | Id. de usuario que creó la consulta. |
| `CreatedTime` | Hora de creación de la consulta. |
| `TotalCount` | Número de conjuntos de datos en la matriz de valores. |
| `StatusCode` | Código de resultado. Los valores posibles son 200, 400, 401, 403 y 500. |
|||
