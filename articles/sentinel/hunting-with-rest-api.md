---
title: Administración de consultas de búsqueda y Live Stream en Microsoft Sentinel mediante la API de REST | Microsoft Docs
description: En este artículo se describe cómo las características de búsqueda de Microsoft Sentinel le permiten aprovechar las ventajas de la API de REST de Log Analytics para administrar consultas de búsqueda y Live Stream.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: reference
ms.custom: mvc, ignite-fall-2021
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/09/2021
ms.author: yelevin
ms.openlocfilehash: 4b479fbe2782cb7316cee565f51d7b6170c17579
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/17/2021
ms.locfileid: "132721031"
---
# <a name="manage-hunting-and-livestream-queries-in-microsoft-sentinel-using-rest-api"></a>Administración de consultas de búsqueda y Live Stream en Microsoft Sentinel mediante la API de REST

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Microsoft Sentinel, que se integra en parte en Log Analytics de Azure Monitor, le permite usar la API de REST de Log Analytics para administrar consultas de búsqueda y Live Stream. En este documento se muestra cómo crear y administrar consultas de búsqueda mediante la API REST.  Las consultas creadas de este modo se mostrarán en la interfaz de usuario de Microsoft Sentinel.

Consulte la referencia de la API REST definitiva para más detalles sobre la [API de búsquedas guardadas](/rest/api/loganalytics/savedsearches).

## <a name="api-examples"></a>Ejemplos de API

En los ejemplos siguientes, reemplace estos marcadores de posición por el reemplazo indicado en la tabla siguiente:

| Marcador de posición | Reemplazar por |
|-|-|
| **{subscriptionId}** | Nombre de la suscripción a la que se aplica la consulta de búsqueda o Live Stream. |
| **{resourceGroupName}** | Nombre del grupo de recursos al que se aplica la consulta de búsqueda o Live Stream. |
| **{savedSearchId}** | Identificador único (GUID) para cada consulta de búsqueda. |
| **{WorkspaceName}** | Nombre del área de trabajo de Log Analytics que es el destino de la consulta. |
| **{DisplayName}** | Nombre para mostrar de su elección para la consulta. |
| **{Description}** | Descripción de la consulta de búsqueda o Live Stream. |
| **{Tactics}** | Tácticas de MITRE ATT&CK pertinentes que se aplican a la consulta. |
| **{Query}** | Expresión de consulta para la consulta. |
|  

### <a name="example-1"></a>Ejemplo 1

En este ejemplo se muestra cómo crear o actualizar una consulta de búsqueda para un área de trabajo de Microsoft Sentinel determinada.  En el caso de una consulta de Live Stream, reemplace *“Category”: “Hunting Queries”* por *“Category”: “Livestream Queries”* en el **cuerpo de la consulta**: 

#### <a name="request-header"></a>Encabezado de solicitud

```http
PUT https://management.azure.com/subscriptions/{subscriptionId} _
    /resourcegroups/{resourceGroupName} _
    /providers/Microsoft.OperationalInsights/workspaces/{workspaceName} _
    /savedSearches/{savedSearchId}?api-version=2020-03-01-preview
```

#### <a name="request-body"></a>Cuerpo de la solicitud

```json
{
"properties": {
    “Category”: “Hunting Queries”,
    "DisplayName": "HuntingRule02",
    "Query": "SecurityEvent | where EventID == \"4688\" | where CommandLine contains \"-noni -ep bypass $\"",
    “Tags”: [
        { 
        “Name”: “Description”,
        “Value”: “Test Hunting Query”
        },
        { 
        “Name”: “Tactics”,
        “Value”: “Execution, Discovery”
        }
        ]        
    }
}
```

### <a name="example-2"></a>Ejemplo 2

En este ejemplo se muestra cómo eliminar una consulta de búsqueda o Live Stream para un área de trabajo de Microsoft Sentinel determinada:

```http
DELETE https://management.azure.com/subscriptions/{subscriptionId} _
       /resourcegroups/{resourceGroupName} _
       /providers/Microsoft.OperationalInsights/workspaces/{workspaceName} _
       /savedSearches/{savedSearchId}?api-version=2020-03-01-preview
```

### <a name="example-3"></a>Ejemplo 3

En este ejemplo se muestra cómo recuperar una consulta de búsqueda o Live Stream para un área de trabajo determinada:

```http
GET https://management.azure.com/subscriptions/{subscriptionId} _
    /resourcegroups/{resourceGroupName} _
    /providers/Microsoft.OperationalInsights/workspaces/{workspaceName} _
    /savedSearches/{savedSearchId}?api-version=2020-03-01-preview
```

## <a name="next-steps"></a>Pasos siguientes

En este artículo, aprendió a administrar consultas de búsqueda y Live Stream en Microsoft Sentinel mediante la API de Log Analytics. Para obtener más información sobre Microsoft Sentinel, consulte los siguientes artículos:

- [Búsqueda de amenazas con Azure Sentinel, versión preliminar](hunting.md)
- [Uso de cuadernos de Jupyter Notebook para buscar amenazas de seguridad](notebooks.md)
