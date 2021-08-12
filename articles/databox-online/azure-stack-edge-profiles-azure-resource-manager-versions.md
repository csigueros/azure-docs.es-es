---
title: Versiones del perfil de API del proveedor de recursos para Azure Stack Edge | Microsoft Docs
description: Obtenga información sobre las versiones de la API de Azure Resource Manager compatibles con perfiles de Azure Stack Edge.
services: databox
author: v-dalc
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 05/17/2021
ms.author: alkohli
ms.openlocfilehash: 485b1f8dccf104a9d31abb9aa79fd1ea4eb3940a
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/10/2021
ms.locfileid: "111965347"
---
# <a name="resource-provider-api-profile-versions-for-azure-stack-edge"></a>Versiones del perfil de API del proveedor de recursos para Azure Stack Edge


[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Puede encontrar el proveedor de recursos y los números de versión de cada perfil de API que utiliza Azure Stack Edge en este artículo. En las tablas de este artículo se enumeran las versiones compatibles con cada proveedor de recursos y las versiones de API de los perfiles. Cada proveedor de recursos contiene un conjunto de tipos de recursos y números de versión específicos.

El perfil de API usa tres convenciones de nomenclatura:

- **más reciente**
- **yyyy-mm-dd-hybrid**
- **yyyy-mm-dd-profile**


## <a name="overview-of-the-2019-03-01-hybrid-profile"></a>Introducción al perfil 2019-03-01-hybrid

|Proveedor de recursos                                   |Versión de la API |
|----------------------------------------------------|------------|
|Microsoft.Compute                                   |2017-12-01  |
|Microsoft.Network                                   |2017-10-01<br>VPN Gateway será 2017-10-01. |
|Microsoft.Storage (plano de datos)                      |2019-07-07  |
|Microsoft.Storage (plano de control)                   |2019-06-01  |
|Microsoft.Resources (el propio Azure Resource Manager) |2020-06-01  |
|Microsoft.Authorization (operaciones de directiva)         |2016-09-01  |

Para obtener una lista de las versiones de cada tipo de recursos de los proveedores del perfil de API, consulte [Detalles del perfil 2019-03-01-hybrid](/azure-stack/user/azure-stack-profiles-azure-resource-manager-versions?view=azs-2008&preserve-view=true#details-for-the-2019-03-01-hybrid-profile).

## <a name="details-for-the-2019-03-01-hybrid-profile"></a>Detalles del perfil 2019-03-01-hybrid

### <a name="microsoftcompute"></a>Microsoft.Compute

Las API de Azure Compute proporcionan acceso mediante programación a las máquinas virtuales y sus recursos auxiliares. Para más información, consulte [Azure Compute](/rest/api/compute/).

|Tipo de recurso               |Versión de la API |
|----------------------------|------------|
|Ubicaciones                   |2017-12-01  |
|Ubicaciones/vmSizes           |2017-12-01  |
|Virtual Machines            |2017-12-01  |
|Virtual Machines/extensiones |2017-12-01  |

### <a name="microsoftnetwork"></a>Microsoft.Network

El resultado de la llamada a las operaciones es una representación de la lista de operaciones de nube de red disponibles. Para más información, consulte la [API de REST de operación](/rest/api/operation/).

|Tipos de recursos     |Versiones de API|
|-------------------|------------|
|Interfaces de red |2017-10-01  |
|Virtual Networks   |2017-10-01  |

### <a name="microsoftresources"></a>Microsoft.Resources

Azure Resource Manager le permite implementar y administrar la infraestructura para las soluciones de Azure. Puede organizar los recursos relacionados en grupos de recursos e implementar sus recursos con plantillas JSON. Para una introducción a la implementación y la administración de recursos con Resource Manager, consulte [Introducción a Azure Resource Manager](../azure-resource-manager/management/overview.md).

|Tipos de recursos                        |Versiones de API|
|--------------------------------------|------------|
|Implementaciones                           |2020-06-01  |
|Implementaciones/operaciones                |2020-06-01  |
|Vínculos                                 |2020-06-01  |
|Ubicaciones                             |2020-06-01  |
|Operations                            |2020-06-01  |
|Proveedores                             |2020-06-01  |
|ResourceGroups                        |2020-06-01  |
|Recursos                             |2020-06-01  |
|Suscripciones                         |2018-09-01  |
|Subscriptions/locations               |2018-09-01  |
|Subscriptions/operationresults        |2020-06-01  |
|Suscripciones/proveedores               |2020-06-01  |
|Subscriptions/ResourceGroups          |2020-06-01  |
|Suscripciones/resourceGroups/recursos|2020-06-01  |
|Suscripciones/recursos               |2020-06-01  |
|Suscripciones/tagNames                |2020-06-01  |
|Suscripciones/tagNames/tagValues      |2020-06-01  |
|Inquilinos                               |2018-09-01  |

### <a name="microsoftstorage"></a>Microsoft.Storage

El proveedor de recursos de almacenamiento (SRP) le permite administrar mediante programación la cuenta de almacenamiento y las claves. Para más información, consulte [Azure Storage Resource Provider REST API reference](/rest/api/storagerp/) (Referencia de API REST del proveedor de recursos de Azure Storage).

|Tipos de recursos       |Versiones de API|
|---------------------|------------|
|CheckNameAvailability|2019-06-01  |
|Ubicaciones            |2019-06-01  |
|Ubicaciones/cuotas     |2019-06-01  |
|Operations           |2019-06-01  |
|StorageAccounts      |2019-06-01  |
|Usos               |2019-06-01  |

## <a name="next-steps"></a>Pasos siguientes

- [Administración de un dispositivo Azure Stack Edge Pro con GPU mediante Windows PowerShell](azure-stack-edge-gpu-connect-powershell-interface.md)