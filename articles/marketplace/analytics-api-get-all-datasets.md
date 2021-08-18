---
title: API de obtención de todos los conjuntos de datos
description: Use esta API para obtener todos los conjuntos de datos disponibles para el análisis del marketplace comercial.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: smannepalle
ms.author: smannepalle
ms.reviewer: sroy
ms.date: 3/08/2021
ms.openlocfilehash: 487c1b4ad58eb17fc90bb78f0dbc4346de8a62d3
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121747239"
---
# <a name="get-all-datasets-api"></a>API de obtención de todos los conjuntos de datos

La API Get all datasets obtiene todos los conjuntos de datos disponibles. Los conjuntos de datos muestran las tablas, las columnas, las métricas y los intervalos de tiempo.

**Sintaxis de la solicitud**

| **Método** | **URI de solicitud** |
| --- | --- |
| GET | `https://api.partnercenter.microsoft.com/insights/v1/cmp/ScheduledDataset?datasetName={Dataset Name}` |

**Encabezado de solicitud**

| **Header** | **Tipo** | **Descripción** |
| --- | --- | --- |
| Authorization | string | Necesario. Token de acceso de Azure Active Directory (Azure AD) con el formato `Bearer <token>` |
| Content-Type | string | `Application/JSON` |

**Parámetro de ruta de acceso**

Ninguno

**Parámetro de consulta**

| **Nombre de parámetro** | **Tipo** | **Obligatorio** | **Descripción** |
| --- | --- | --- | --- |
| `datasetName` | string | No | Filtro para obtener detalles de un solo conjunto de datos |

**Carga de solicitud**

Ninguno

**Glosario**

Ninguno

**Respuesta**

La carga de respuesta tiene la estructura siguiente:

Código de respuesta: 200, 400, 401, 403, 404, 500

Ejemplo de carga de respuesta:

```json
{
   "Value":[
      {
         "DatasetName ":"string",
         "SelectableColumns":[
            "string"
         ],
         "AvailableMetrics":[
            "string"
         ],
         "AvailableDateRanges ":[
            "string"
         ]
      }
   ],
   "TotalCount":int,
   "Message":"<Error Message>",
   "StatusCode": int
}
```

**Glosario**

En esta tabla se definen los elementos clave de la respuesta:

| **Parámetro** | **Descripción** |
| --- | --- |
| `DatasetName` | Nombre del conjunto de datos que define este objeto de matriz. |
| `SelectableColumns` | Columnas sin procesar que se pueden especificar en las columnas de selección. |
| `AvailableMetrics` | Nombres de columna de agregación/métrica que se pueden especificar en las columnas de selección. |
| `AvailableDateRanges` | Intervalo de fechas que se puede usar en las consultas de informe para el conjunto de datos. |
| `NextLink` | Vínculo a la página siguiente si se paginan los datos. |
| `TotalCount` | Número de conjuntos de datos en la matriz de valores. |
| `StatusCode` | Código de resultado. Los valores posibles son 200, 400, 401, 403 y 500. |
