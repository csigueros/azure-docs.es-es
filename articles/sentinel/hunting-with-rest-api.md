---
title: Administración de consultas de búsqueda y Live Stream en Azure Sentinel mediante la API de REST | Microsoft Docs
description: En este artículo se describe cómo las características de búsqueda de Azure Sentinel le permiten aprovechar las ventajas de la API REST de Log Analytics para administrar consultas de búsqueda y Live Stream.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: reference
ms.custom: mvc, ignite-fall-2021
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/06/2020
ms.author: yelevin
ms.openlocfilehash: 264e99b15676807c54f400cb4431f53c06cae8bd
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131037119"
---
# <a name="manage-hunting-and-livestream-queries-in-azure-sentinel-using-rest-api"></a>Administración de consultas de búsqueda y Live Stream en Azure Sentinel mediante la API de REST

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Azure Sentinel, que se integra en parte en Log Analytics de Azure Monitor, le permite usar la API REST de Log Analytics para administrar consultas de búsqueda y Live Stream. En este documento se muestra cómo crear y administrar consultas de búsqueda mediante la API REST.  Las consultas creadas de este modo se mostrarán en la interfaz de usuario de Azure Sentinel.

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

En este ejemplo se muestra cómo crear o actualizar una consulta de búsqueda para un área de trabajo de Azure Sentinel determinada.  En el caso de una consulta de Live Stream, reemplace *“Category”: “Hunting Queries”* por *“Category”: “Livestream Queries”* en el **cuerpo de la consulta**: 

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

En este ejemplo se muestra cómo eliminar una consulta de búsqueda o Live Stream para un área de trabajo de Azure Sentinel determinada:

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

En este artículo, aprendió a administrar consultas de búsqueda y Live Stream en Azure Sentinel mediante la API de Log Analytics. Para más información sobre Azure Sentinel, consulte los siguientes artículos:

- [Búsqueda de amenazas con Azure Sentinel, versión preliminar](hunting.md)
- [Uso de cuadernos de Jupyter Notebook para buscar amenazas de seguridad](notebooks.md)
