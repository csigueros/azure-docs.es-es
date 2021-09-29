---
title: Preferencia de enrutamiento de red
titleSuffix: Azure Storage
description: La preferencia de enrutamiento de red le permite especificar cómo se enruta el tráfico de red a la cuenta desde los clientes a través de Internet.
services: storage
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 02/11/2021
ms.author: normesta
ms.reviewer: santoshc
ms.subservice: common
ms.custom: references_regions
ms.openlocfilehash: f3a665df26e504eee1c9bea77f8bf6bf5631193f
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128591033"
---
# <a name="network-routing-preference-for-azure-storage"></a>Preferencia de enrutamiento de red para Azure Storage

Puede configurar la [preferencia de enrutamiento](../../virtual-network/routing-preference-overview.md) de red de la cuenta de Azure Storage para especificar cómo se enruta el tráfico de red a la cuenta desde los clientes a través de Internet. De forma predeterminada, el tráfico procedente de Internet se enruta al punto de conexión público de la cuenta de almacenamiento a través de la [red global de Microsoft](../../networking/microsoft-global-network.md). Azure Storage proporciona opciones adicionales para configurar la forma de enrutar el tráfico a la cuenta de almacenamiento.

La configuración de las preferencias de enrutamiento le ofrece la flexibilidad de optimizar el tráfico en función del rendimiento de la red Premium o del costo. Cuando configure una preferencia de enrutamiento, debe especificar cómo se dirigirá el tráfico al punto de conexión público de la cuenta de almacenamiento de forma predeterminada. También puede publicar puntos de conexión específicos de la ruta para la cuenta de almacenamiento.

> [!NOTE]
> Esta característica no se admite en las cuentas de almacenamiento que están configuradas para usar el nivel de rendimiento Premium o el almacenamiento con redundancia de zona (ZRS).

## <a name="microsoft-global-network-versus-internet-routing"></a>Red global de Microsoft frente al enrutamiento de Internet

De forma predeterminada, los clientes que están fuera del entorno de Azure tienen acceso a la cuenta de almacenamiento a través de la red global de Microsoft. La red global de Microsoft está optimizada para la selección de rutas de acceso de baja latencia, que ofrecen un rendimiento de red Premium con alta confiabilidad. Tanto el tráfico entrante como el saliente se enrutan a través del punto de presencia (POP) más cercano al cliente. Esta configuración de enrutamiento predeterminada garantiza que el tráfico hacia y desde la cuenta de almacenamiento atraviese la red global de Microsoft a través de la mayor parte de la ruta de acceso, lo que maximiza el rendimiento de la red.

Puede cambiar la configuración de enrutamiento de la cuenta de almacenamiento para que el tráfico entrante y saliente se enrute hacia y desde clientes que estén fuera del entorno de Azure a través del POP más cercano a la cuenta de almacenamiento. Esta ruta minimiza el recorrido del tráfico a través de la red global de Microsoft y lo lleva al ISP de tránsito lo antes posible. El uso de esta configuración de enrutamiento reduce los costos de red.

En el diagrama siguiente se muestra cómo fluye el tráfico entre el cliente y la cuenta de almacenamiento de cada preferencia de enrutamiento:

![Información general de las opciones de enrutamiento para Azure Storage](media/network-routing-preference/routing-options-diagram.png)

Para obtener más información sobre la preferencia de enrutamiento en Azure, consulte [¿Qué es la preferencia de enrutamiento?](../../virtual-network/routing-preference-overview.md).

## <a name="routing-configuration"></a>Configuración de enrutamiento

Para obtener una guía paso a paso que le muestre cómo configurar la preferencia de enrutamiento y los puntos de conexión específicos de la ruta, consulte [Configuración de la preferencia de enrutamiento de red para Azure Storage](configure-network-routing-preference.md).

Puede elegir entre la red global de Microsoft y el enrutamiento de Internet como una preferencia de enrutamiento predeterminada del punto de conexión público de la cuenta de almacenamiento. La preferencia de enrutamiento predeterminada se aplica a todo el tráfico de los clientes fuera de Azure y afecta a los puntos de conexión de Azure Data Lake Storage Gen2, Blob Storage, Azure Files y sitios web estáticos. No se admite la configuración de preferencias de enrutamiento en las colas de Azure o las tablas de Azure.

También puede publicar puntos de conexión específicos de la ruta para la cuenta de almacenamiento. Al publicar puntos de conexión específicos de la ruta, Azure Storage crea nuevos puntos de conexión públicos para la cuenta de almacenamiento que enrutan el tráfico a través de la ruta de acceso deseada. Esta flexibilidad le permite dirigir el tráfico a la cuenta de almacenamiento a través de una ruta específica sin tener que cambiar la preferencia de enrutamiento predeterminada.

Por ejemplo, la publicación de un punto de conexión específico de la ruta de Internet para "StorageAccountA" publicará los siguientes puntos de conexión para la cuenta de almacenamiento:

| Servicio de Storage        | Punto de conexión específico de la ruta                                  |
| :--------------------- | :------------------------------------------------------- |
| Blob service           | `StorageAccountA-internetrouting.blob.core.windows.net`  |
| Data Lake Storage Gen2 | `StorageAccountA-internetrouting.dfs.core.windows.net`   |
| File service           | `StorageAccountA-internetrouting.file.core.windows.net`  |
| Static Websites        | `StorageAccountA-internetrouting.web.core.windows.net`   |

Si tiene un almacenamiento con redundancia geográfica con acceso de lectura (RA-GRS) o una cuenta de almacenamiento con redundancia de zona geográfica con acceso de lectura (RA-GZRS), la publicación de puntos de conexión específicos de la ruta también crea automáticamente los puntos de conexión correspondientes en la región secundaria del acceso de lectura.

| Servicio de Storage        | Punto de conexión secundario de solo lectura específico de la ruta                        |
| :--------------------- | :----------------------------------------------------------------- |
| Blob service           | `StorageAccountA-internetrouting-secondary.blob.core.windows.net`  |
| Data Lake Storage Gen2 | `StorageAccountA-internetrouting-secondary.dfs.core.windows.net`   |
| File service           | `StorageAccountA-internetrouting-secondary.file.core.windows.net`  |
| Static Websites        | `StorageAccountA-internetrouting-secondary.web.core.windows.net`   |

Las cadenas de conexión de los puntos de conexión específicos de la ruta publicados se pueden copiar a través de [Azure Portal](https://portal.azure.com). Estas cadenas de conexión se pueden usar para la autorización de claves compartidas con todos los SDK y API de Azure Storage existentes.

## <a name="regional-availability"></a>Disponibilidad regional

La preferencia de enrutamiento de Azure Storage está disponible en las siguientes regiones:

- Centro de EE. UU. 
- EUAP del centro de EE. UU.
- Este de EE. UU. 
- Este de EE. UU. 2
- Este de EE. UU. 2 
- EUAP de Este de EE. UU. 2
- Centro-sur de EE. UU.
- Centro-Oeste de EE. UU.
- Oeste de EE. UU. 
- Oeste de EE. UU. 2 
- Centro de Francia 
- Sur de Francia 
- Norte de Alemania 
- Centro-oeste de Alemania 
- Centro-Norte de EE. UU
- Norte de Europa 
- Este de Noruega 
- Norte de Suiza
- Oeste de Suiza
- Sur de Reino Unido 
- Oeste de Reino Unido 
- Oeste de Europa 
- Centro de Emiratos Árabes Unidos
- Este de Asia 
- Sudeste de Asia 
- Japón Oriental 
- Japón Occidental 
- Oeste de la India
- Este de Australia 
- Sudeste de Australia 

Los siguientes problemas conocidos afectan a la preferencia de enrutamiento para Azure Storage:

- Las solicitudes de acceso al punto de conexión específico de la ruta correspondiente a la red global de Microsoft producen el error HTTP 404 o uno equivalente. El enrutamiento a través de la red global de Microsoft funciona según lo esperado cuando se establece como preferencia de enrutamiento predeterminada en el punto de conexión público.

## <a name="pricing-and-billing"></a>Precios y facturación

Para obtener información sobre los precios y los detalles de facturación, consulte la sección **Precios** de [¿Qué es la preferencia de enrutamiento?](../../virtual-network/routing-preference-overview.md#pricing).

## <a name="next-steps"></a>Pasos siguientes

- [¿Qué es la preferencia de enrutamiento?](../../virtual-network/routing-preference-overview.md)
- [Configuración de las preferencias de enrutamiento de red](configure-network-routing-preference.md)
- [Configuración de redes virtuales y firewalls de Azure Storage](storage-network-security.md)
- [Recomendaciones de seguridad para Blob Storage](../blobs/security-recommendations.md)
