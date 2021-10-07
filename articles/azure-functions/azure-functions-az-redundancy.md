---
title: Compatibilidad con las zonas de disponibilidad de Azure Functions en los planes elásticos Premium
description: Aprenda a usar la redundancia de zona de disponibilidad con Azure Functions para aplicaciones de funciones de alta disponibilidad en planes elásticos Premium.
ms.topic: conceptual
ms.author: johnguo
ms.date: 09/07/2021
ms.custom: references_regions
ms.openlocfilehash: 796f4db37429e73dd1c3d50dc71b0a599977d861
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128699793"
---
# <a name="azure-functions-support-for-availability-zone-redundancy"></a>Compatibilidad con la redundancia de zonas de disponibilidad de Azure Functions

La compatibilidad con las zonas de disponibilidad (AZ) de Azure Functions ahora está disponible en los planes elásticos Premium y en los planes dedicados (App Service). Las aplicaciones de Azure Functions con redundancia de zona equilibrarán automáticamente sus instancias entre las zonas de disponibilidad para mejorar la disponibilidad. En este documento, se explica la compatibilidad con la redundancia de zona en los planes elásticos Premium de Functions. Para más información sobre la redundancia de zona en planes dedicados, consulte [esta página](../app-service/how-to-zone-redundancy.md).

## <a name="overview"></a>Información general

Una [zona de disponibilidad](../availability-zones/az-overview.md#availability-zones) es una oferta de alta disponibilidad que protege las aplicaciones y los datos de los errores que se producen en el centro de datos. Las zonas de disponibilidad son ubicaciones físicas exclusivas dentro de una región de Azure. Cada zona de disponibilidad consta de uno o varios centros de datos equipados con alimentación, refrigeración y redes independientes. Para garantizar la resistencia, todas las regiones habilitadas tienen, como mínimo, tres zonas independientes. Si quiere conseguir una alta disponibilidad en la arquitectura de sus aplicaciones, puede situar los recursos de proceso, almacenamiento, red y datos dentro de una zona y replicarlos en otras.

Las aplicaciones de funciones con redundancia de zona distribuirán automáticamente la carga de las instancias en las que se ejecutan entre las zonas de disponibilidad de la región. En el caso de las aplicaciones de los planes elásticos Premium con redundancia de zona, aunque la aplicación se escale o se reduzca horizontalmente, las instancias en las que se ejecutan seguirán distribuyéndose uniformemente entre las zonas de disponibilidad.

## <a name="requirements"></a>Requisitos

> [!IMPORTANT]
> Cuando seleccione una [cuenta de almacenamiento](storage-considerations.md#storage-account-requirements) para la aplicación de funciones, asegúrese de usar una [cuenta de almacenamiento con redundancia de zona (ZRS)](../storage/common/storage-redundancy.md#zone-redundant-storage). De lo contrario, si se produce una interrupción en alguna zona, Functions puede registrar un comportamiento inesperado por su dependencia de Storage. 

- Se admite Windows y Linux.
- Debe hospedarse en un plan de hospedaje [elástico Premium](functions-premium-plan.md) o dedicado. Encontrará instrucciones sobre la redundancia de zona con los planes de hospedaje dedicados (App Service) [aquí](../app-service/how-to-zone-redundancy.md).
  - La compatibilidad con las zonas de disponibilidad (AZ) no está disponible actualmente en las aplicaciones de funciones de los planes de [consumo](consumption-plan.md).
- En recuento mínimo de instancias en los planes con redundancia de zona debe ser tres.
- En las aplicaciones de funciones de los planes elásticos Premium, el recuento mínimo de [instancias que estén siempre listas](functions-premium-plan.md#always-ready-instances) también debe ser tres.
- Se puede habilitar en cualquiera de las regiones siguientes:
  - Oeste de EE. UU. 2
  - Oeste de EE. UU. 3
  - Centro de EE. UU.
  - Este de EE. UU.
  - Este de EE. UU. 2
  - Centro de Canadá
  - Sur de Brasil
  - Norte de Europa
  - Oeste de Europa
  - Centro-oeste de Alemania
  - Centro de Francia
  - Sur de Reino Unido 2
  - Japón Oriental
  - Sudeste de Asia
  - Este de Australia
- En este momento, debe crearse con una [plantilla de ARM](../azure-resource-manager/templates/index.yml).

## <a name="how-to-deploy-a-function-app-on-a-zone-redundant-premium-plan"></a>Implementación de una aplicación de funciones en un plan Premium con redundancia de zona

Para crear por primera vez un plan elástico Premium con redundancia de zona de Functions, la implementación debe hacerse utilizando [plantillas de ARM](../azure-resource-manager/templates/quickstart-create-templates-use-visual-studio-code.md). Posteriormente, una vez creado correctamente, podrá ver el plan de Functions e interactuar con él mediante Azure Portal y las herramientas de la CLI. Las plantillas de ARM solo se necesitan para crear el plan de Functions por primera vez. Encontrará una guía sobre el hospedaje de Functions en planes Premium [aquí](functions-infrastructure-as-code.md#deploy-on-premium-plan). Una vez creado e implementado el plan con redundancia de zona, cualquier aplicación de funciones que esté hospedada en el nuevo plan tendrá redundancia de zona. 

Las únicas propiedades que debe tener en cuenta al crear un plan de Functions con redundancia de zona son la nueva propiedad **zoneRedundant** y los campos de recuento de instancias del plan de Functions (**capacity**). La propiedad **zoneRedundant** debe estar establecida en **true**, mientras que la propiedad **capacity** debe establecerse en función de los requisitos de las cargas de trabajo, pero nunca en un valor inferior a tres. Elegir la capacidad adecuada depende de diversos factores y de las estrategias de alta disponibilidad y tolerancia a errores. Una buena estrategia es garantizar instancias suficientes para la aplicación, de modo que la pérdida de una zona de instancias deje capacidad suficiente para administrar la carga esperada.

> [!IMPORTANT]
> Las aplicaciones de funciones de Azure hospedadas en un plan elástico Premium con redundancia de zona de Functions deben tener un mínimo de tres [instancias que estén siempre listas](functions-premium-plan.md#always-ready-instances). De este modo, tendrá la seguridad de que una aplicación de funciones con redundancia de zona siempre tendrá suficientes instancias para cubrir las necesidades de, al menos, un trabajo por zona.

A continuación, se muestra un fragmento de código de una plantilla de ARM para un plan Premium de Functions con redundancia de zona, donde puede verse el nuevo campo **zoneRedundant** y la especificación de **capacity**.

```
    "resources": [
        {
            "type": "Microsoft.Web/serverfarms",
            "apiVersion": "2021-01-15",
            "name": "your_plan_name_here",
            "location": "Central US",
            "sku": {
                "name": "EP3",
                "tier": "ElasticPremium",
                "size": "EP3",
                "family": "EP", 
                "capacity": 3
            },
            "kind": "elastic",
            "properties": {
                "perSiteScaling": false,
                "elasticScaleEnabled": true,
                "maximumElasticWorkerCount": 20,
                "isSpot": false,
                "reserved": false,
                "isXenon": false,
                "hyperV": false,
                "targetWorkerCount": 0,
                "targetWorkerSizeId": 0, 
                "zoneRedundant": true
            }
        }
    ]
```

Para más información, consulte [Automatización de la implementación de recursos para una aplicación de funciones en Azure Functions](functions-infrastructure-as-code.md).
