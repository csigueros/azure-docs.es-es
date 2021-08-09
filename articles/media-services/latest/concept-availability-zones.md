---
title: Zonas de disponibilidad
description: Media Services admite Availability Zones, que proporciona aislamiento de errores
services: media-services
author: johndeu
manager: femila
ms.service: media-services
ms.topic: conceptual
ms.date: 05/27/2021
ms.author: johndeu
ms.openlocfilehash: d50af7acadb17fe060ede90ad93896e465d3bad6
ms.sourcegitcommit: 8bca2d622fdce67b07746a2fb5a40c0c644100c6
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/09/2021
ms.locfileid: "111746958"
---
# <a name="availability-zones"></a>Zonas de disponibilidad

Azure Media Services usa [Availability Zones](../../availability-zones/az-overview.md), que proporciona ubicaciones con aislamiento de errores dentro de la misma región de Azure. Media Services viene con redundancia de zona de manera predeterminada en las [ubicaciones disponibles](../../availability-zones/az-region.md#azure-regions-with-availability-zones) y no se requiere ninguna configuración adicional en la cuenta para habilitar esta funcionalidad.  Media Services almacena datos multimedia en las cuentas de almacenamiento asociadas.  Estas cuentas de almacenamiento deben crearse como almacenamiento con redundancia de zona (ZRS) o almacenamiento con redundancia de zona geográfica (GZRS) para proporcionar el mismo nivel de redundancia que la cuenta de Media Services. Para más información sobre cómo configurar la replicación en las cuentas de almacenamiento asociadas, consulte el artículo [Cambio en la forma en que se replican las cuentas de almacenamiento](../../storage/common/redundancy-migration.md).

## <a name="how-media-services-components-handle-an-availability-zone-fault"></a>Cómo los componentes de Media Services controlan un error de zona de disponibilidad

| Componente             | Comportamiento ante un error de zona de disponibilidad |
|-----------            |----------------------|
| Plano de control (Azure Resource Management) | Sigue funcionando con normalidad. |
| Entrega de claves            | Sigue funcionando con normalidad. |
| Trabajos                    | Los trabajos se reprograman en otra zona de disponibilidad. Habrá un retraso en el tiempo de procesamiento, ya que los trabajos de procesamiento en curso se reprograman para empezar de nuevo en la zona de disponibilidad. |
| Eventos en vivo             | El streaming y la ingesta en el evento en vivo siguen funcionando con normalidad. Actualmente no se admite la llamada a "reset" en un evento en vivo durante un error de zona de disponibilidad. Se recomienda detener y reiniciar primero el evento en vivo como reemplazo de la operación de "reset". Si un evento en vivo estaba realizando la transición al estado "En ejecución" durante un evento de zona de fuera de servicio, es posible que los clientes vean el evento en vivo bloqueado en el estado "iniciando". En este caso, se recomienda iniciar un nuevo evento en vivo y limpiar los eventos en vivo con estado "iniciando" después de que se recupere la zona.  |
| Extremos de streaming     | Sigue funcionando con normalidad. |


## <a name="high-availability-streaming-and-encoding-for-vod"></a>Streaming y codificación de alta disponibilidad para VOD

Availability Zones aumenta el aislamiento de errores en una sola región. Para proporcionar alta disponibilidad para el streaming y la codificación a petición, puede usar otros servicios de Azure para crear una arquitectura que abarque aspectos como la redundancia, el seguimiento de estado, el equilibrio de carga y la copia de seguridad y recuperación de datos. Una de estas arquitecturas se proporciona en el artículo [Alta disponibilidad con Media Services y vídeo bajo demanda](architecture-high-availability-encoding-concept.md).
El artículo y el código de ejemplo proporcionan una solución para el modo en que se pueden usar cuentas de Media Services regionales individuales para crear una arquitectura de alta disponibilidad para la aplicación de VOD.

## <a name="media-services-support-for-availability-zones-by-region"></a>Compatibilidad de Media Services con Availability Zones por región

Availability Zones solo se admite actualmente en determinadas regiones de Azure. Para más información sobre la compatibilidad de regiones de Availability Zones, consulte [Regiones de Azure con Availability Zones](../../availability-zones/az-region.md#azure-regions-with-availability-zones).

## <a name="further-reading"></a>Lecturas adicionales

Para más información sobre Availability Zones, consulte [Regiones y Availability Zones en Azure](../../availability-zones/az-overview.md).

Para más información sobre la codificación y el streaming de alta disponibilidad, consulte [Alta disponibilidad con Media Services y vídeo bajo demanda](architecture-high-availability-encoding-concept.md).

Para obtener información sobre cómo configurar correctamente la replicación de las cuentas de almacenamiento para admitir Availability Zones, consulte [Cambio en la forma en que se replican las cuentas de almacenamiento](../../storage/common/redundancy-migration.md).
