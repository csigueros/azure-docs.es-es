---
title: Compatibilidad con zonas de disponibilidad para instancias de App Service multiinquilino públicas
description: Aprenda a implementar la instancia de App Service para que las aplicaciones tengan redundancia de zona.
author: seligj95
ms.topic: article
ms.date: 09/01/2021
ms.author: jordanselig
ms.custom: references_regions
ms.openlocfilehash: 1fb6959ab6e47ad631ea3cac208a3dc52ca41e41
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124832707"
---
# <a name="availability-zone-support-for-public-multi-tenant-app-service"></a>Compatibilidad con zonas de disponibilidad para instancias de App Service multiinquilino públicas

Microsoft Azure App Service se puede implementar en [Availability Zones (AZ)](../availability-zones/az-overview.md), lo que permite la [alta disponibilidad](https://en.wikipedia.org/wiki/High_availability) para las aplicaciones. Esta arquitectura también se conoce como redundancia de zona.

Una aplicación reside en un plan de App Service (ASP) y el plan de App Service existe en una sola unidad de escalado. Cuando se configura una instancia de App Service con redundancia de zona, la plataforma distribuye automáticamente las instancias de máquina virtual en el plan de App Service entre las tres zonas de la región seleccionada. Si se especifica una capacidad mayor que tres y el número de instancias es divisible entre tres, las instancias se propagarán de manera uniforme. De lo contrario, los recuentos de instancias más allá de 3*N se reparten entre las dos zonas restantes.

## <a name="requirements"></a>Requisitos

La redundancia de zona es una propiedad del plan de App Service. Estos son los requisitos o limitaciones actuales para habilitar la redundancia de zona:

- Se admite tanto Windows como Linux
- Se necesitan planes de App Service **Premium v2** o **Premium v3**
- Recuento mínimo de tres instancias
  - La plataforma aplicará este recuento mínimo en segundo plano si especifica un recuento de instancias inferior a tres.
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
- La redundancia de zona solo se puede especificar al **crear** un plan de App Service
  - Actualmente no se puede convertir un plan de App Service existente. Vea la siguiente viñeta para obtener más información sobre cómo crear un plan de App Service que admita la redundancia de zona.
- La redundancia de zona solo se admite en la parte más reciente de la superficie de App Service
  - Actualmente, si la ejecución se realiza en Pv3, es posible que ya esté en una superficie que admita la redundancia de zona. En este escenario, puede crear un plan de App Service y especificar la redundancia de zona al crearlo.
  - Si no usa Pv3 o una unidad de escalado que admita redundancia de zona, se encuentra en una región no admitida o no está seguro, siga estos pasos:
    - Cree un grupo de recursos en una región admitida.
        - Esto garantiza que el plano de control de App Service pueda encontrar una unidad de escalado en la región seleccionada que admita la redundancia de zona.
    - Cree un plan de App Service (y una aplicación) en la región que prefiera mediante el **nuevo** grupo de recursos
    - Asegúrese de que la propiedad zoneRedundant (que se describe a continuación) esté establecida en true al crear el plan de App Service.
- Se debe crear mediante [plantillas de Azure Resource Manager (ARM)](../azure-resource-manager/templates/overview.md)

Si una zona está fuera de servicio, la plataforma App Service detectará las instancias perdidas e intentará encontrar automáticamente nuevas instancias de reemplazo. Si también ha configurado la escalabilidad automática y decide que se necesitan más instancias, la escalabilidad automática también emitirá una solicitud a App Service para agregar más instancias (el comportamiento de la escalabilidad automática es independiente del de la plataforma App Service). Es importante tener en cuenta que no hay ninguna garantía de que las solicitudes de instancias adicionales en un escenario de zona fuera de servicio se realicen correctamente, ya que el rellenado de instancias perdidas se realiza en función del mejor esfuerzo. La solución recomendada consiste en aprovisionar los planes de App Service para tener en cuenta la pérdida de una zona, como se describe en la sección siguiente de este artículo.

Las aplicaciones implementadas en un plan de App Service habilitado para la redundancia de zona seguirán funcionando y proporcionarán el tráfico incluso si otras zonas de la misma región sufren una interrupción. Pero es posible que los comportamientos que no sean de tiempo de ejecución, incluidos el escalado del plan de App Service y la creación, configuración y publicación de aplicaciones, se puedan ver afectados por una interrupción en otras zonas de disponibilidad. La redundancia de zona para los planes de App Service solo garantiza un tiempo de actividad continuado para las aplicaciones implementadas.

Cuando la plataforma App Service asigna instancias a un plan de App Service con redundancia de zona, usa el [mejor esfuerzo de equilibrio de zona que ofrece la instancia de Azure Virtual Machine Scale Sets subyacente](../virtual-machine-scale-sets/virtual-machine-scale-sets-use-availability-zones.md#zone-balancing). Un plan de App Service se "equilibrará" si cada zona tiene el mismo número de máquinas virtuales o +/- 1 máquina virtual en todas las demás zonas usadas por el plan de App Service.

## <a name="how-to-deploy-a-zone-redundant-app-service"></a>Procedimiento para implementar una instancia de App Service con redundancia de zona

Actualmente, debe usar una plantilla de ARM para crear una instancia de App Service con redundancia de zona. Después de crearlo mediante la plantilla de ARM, se puede ver e interactuar con el plan de App Service desde Azure Portal y las herramientas de la CLI. Solo se necesita una plantilla de ARM para la creación inicial del plan de App Service.

Los únicos cambios necesarios en una plantilla de ARM para especificar una instancia de App Service con redundancia de zona son la nueva propiedad ***zoneRedundant** _ (obligatoria) y, opcionalmente, el recuento de instancias del plan de App Service (_*_capacity_*_) en el recurso [Microsoft.Web/serverfarms](/azure/templates/microsoft.web/serverfarms?tabs=json). Si no especifica una capacidad, el valor predeterminado de la plataforma es tres. La propiedad _*_zoneRedundant_*_ se debe establecer en _*_true_*_ y _ *_capacity_** se debe establecer en función del requisito de carga de trabajo, pero no debe ser inferior a tres. Una buena regla general para elegir la capacidad consiste en garantizar instancias suficientes para la aplicación, de modo que la pérdida de una zona de instancias deje capacidad suficiente para controlar la carga esperada.

> [!TIP]
> Para decidir la capacidad de la instancia, puede usar el cálculo siguiente:
>
> Como la plataforma distribuye las máquinas virtuales entre 3 zonas y debe tener en cuenta al menos el error de una de ellas, multiplique el número máximo de instancias de carga de trabajo por un factor de zonas/(zonas-1) o 3/2. Por ejemplo, si la carga de trabajo máxima típica necesita 4 instancias, debería aprovisionar 6 instancias: (2/3 * 6 instancias) = 4 instancias.
>

En el fragmento de código de plantilla de ARM siguiente se muestra la nueva propiedad ***zoneRedundant** _y la especificación de _ *_capacity_**.

```json
"resources": [
  {
    "type": "Microsoft.Web/serverfarms",
    "apiVersion": "2018-02-01",
    "name": "your-appserviceplan-name-here",
    "location": "West US 3",
    "sku": {
        "name": "P1v3",
        "tier": "PremiumV3",
        "size": "P1v3",
        "family": "Pv3",
        "capacity": 3
    },
    "kind": "app",
    "properties": {
        "zoneRedundant": true
    }
  }
]
```

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Información sobre cómo crear e implementar plantillas de ARM](../azure-resource-manager/templates/quickstart-create-templates-use-visual-studio-code.md)

> [!div class="nextstepaction"]
> [Plantillas de inicio rápido de ARM](https://azure.microsoft.com/resources/templates/)