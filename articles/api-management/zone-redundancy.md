---
title: Compatibilidad con la zona de disponibilidad para Azure API Management
description: Aprenda a habilitar la redundancia de zona para mejorar la resistencia de la instancia de servicio de Azure API Management en una región.
author: dlepow
ms.service: api-management
ms.topic: how-to
ms.date: 08/11/2021
ms.author: danlep
ms.custom: references_regions
ms.openlocfilehash: d43d6e7b04e117e4144356ac22ea2858408405df
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128582849"
---
# <a name="availability-zone-support-for-azure-api-management"></a>Compatibilidad con la zona de disponibilidad para Azure API Management 

En este artículo se muestra cómo habilitar la redundancia de zona para su instancia de API Management mediante Azure Portal. La [redundancia de zona](../availability-zones/az-overview.md#availability-zones) proporciona resistencia y alta disponibilidad a una instancia de servicio en una región específica de Azure (ubicación). Con la redundancia de zona, la puerta de enlace y el plano de control de la instancia de API Management (API de administración, portal para desarrolladores, configuración de Git) se replican en centros de datos que se encuentran en zonas separadas físicamente, lo que les otorga resistencia a los errores de zona. 

API Management también admite las [implementaciones en varias regiones](api-management-howto-deploy-multi-region.md); esto ayuda a reducir la latencia de las solicitudes que perciben los consumidores de la API distribuidos geográficamente y, además, mejora la disponibilidad del componente de puerta de enlace si una región se queda sin conexión. Combinar las zonas de disponibilidad para la redundancia dentro de una región, y las implementaciones en varias regiones para mejorar la disponibilidad de la puerta de enlace si se produce una interrupción regional, ayuda a mejorar tanto la confiabilidad como el rendimiento de la instancia de API Management.

[!INCLUDE [premium.md](../../includes/api-management-availability-premium.md)]

## <a name="supported-regions"></a>Regiones admitidas

La configuración de API Management para la redundancia de zona se admite actualmente en las siguientes regiones de Azure.

* Este de Australia
* Sur de Brasil
* Centro de Canadá
* Centro de la India (*)
* Centro de EE. UU.
* Este de EE. UU.
* Este de EE. UU. 2
* Centro de Francia
* Centro-oeste de Alemania
* Japón Oriental
* Centro de Corea del Sur (*)
* Norte de Europa
* Este de Noruega (*)
* Norte de Sudáfrica (*)
* Centro-sur de EE. UU.
* Sudeste de Asia
* Sur de Reino Unido
* Oeste de Europa
* Oeste de EE. UU. 2
* Oeste de EE. UU. 3

> [!IMPORTANT]
> Las regiones con * tienen acceso restrictivo en una suscripción de Azure para habilitar la compatibilidad con Zona de disponibilidad. Trabaje junto a su representante de ventas o al cliente de Microsoft.

## <a name="prerequisites"></a>Requisitos previos

* Si todavía no ha creado una instancia del servicio API Management, consulte [Creación de una instancia del servicio API Management](get-started-create-service-instance.md). Seleccione el nivel de servicio Premium.
* Si la instancia de API Management se implementó en una [red virtual](api-management-using-with-vnet.md), asegúrese de configurar una red virtual, una subred y una dirección IP pública en todas las nuevas ubicaciones en las que planea habilitar la redundancia de zona.

## <a name="enable-zone-redundancy---portal"></a>Habilitación de la redundancia de zona a través del portal

En el portal también tiene la opción de habilitar la redundancia de zona cuando agregue una ubicación al servicio API Management o cuando actualice la configuración de una ubicación existente.

1. En Azure Portal, vaya al servicio API Management y seleccione **Ubicaciones** en el menú.
1. Seleccione una ubicación existente o **+ Agregar** en la barra superior. La ubicación debe [admitir zonas de disponibilidad](#supported-regions).
1. Seleccione el número de **[unidades](upgrade-and-scale.md)** de escalado de la ubicación.
1. En **Zonas de disponibilidad**, seleccione una o varias zonas. El número de unidades seleccionadas debe distribuirse uniformemente entre las zonas de disponibilidad. Por ejemplo, si seleccionó tres unidades, seleccione tres zonas para que cada zona hospede una unidad.
1. Si la instancia de API Management se implementa en una [red virtual](api-management-using-with-vnet.md), configure los valores de red virtual en la ubicación seleccionando una red virtual, una subred y una dirección IP pública existentes que estén disponibles en la ubicación. En el caso de una ubicación existente, la red virtual y la subred deben configurarse desde la hoja Red virtual.
1. Seleccione **Aplicar** y, luego, **Guardar**.

:::image type="content" source="media/zone-redundancy/add-location-zones.png" alt-text="Habilitación de la redundancia de zona":::

> [!IMPORTANT]
> La dirección IP pública de la ubicación se modifica cuando habilita, agrega o quita zonas de disponibilidad. Al actualizar zonas de disponibilidad en una región con valores de configuración de red, debe configurar un recurso de dirección IP pública diferente al que configuró anteriormente.

> [!NOTE]
> El cambio puede tardar entre 15 y 45 minutos en aplicarse a la instancia de API Management.

## <a name="next-steps"></a>Pasos siguientes

* Obtenga información acerca de cómo [implementar una instancia de servicio de Azure API Management en varias regiones de Azure](api-management-howto-deploy-multi-region.md).
* También puede habilitar la redundancia de zona con una [plantilla de Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.apimanagement/api-management-simple-zones).
* Obtenga más información acerca de los [servicios de Azure compatibles con zonas de disponibilidad](../availability-zones/az-region.md).
* Obtenga más información sobre el desarrollo de la [confiabilidad](/azure/architecture/framework/resiliency/app-design) en Azure.
