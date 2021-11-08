---
title: Información general sobre el uso de vínculos privados con Azure Media Services
description: En este artículo se proporciona información general sobre el uso de vínculos privados con Azure Media Services.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: conceptual
ms.date: 10/22/2021
ms.author: inhenkel
ms.openlocfilehash: 10d27003cfa5300e097c2328deb14c0d9c1f7e93
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131091252"
---
# <a name="overview-of-using-azure-private-link-with-azure-media-services"></a>Información general sobre el uso de Azure Private Link con Azure Media Services

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

En este artículo se proporciona información general sobre el uso de vínculos privados con Azure Media Services.

## <a name="when-to-use-private-link-with-media-services"></a>Cuándo usar Private Link con Media Services

Private Link permite el acceso a Media Services desde redes privadas. Cuando se usan con los controles de acceso a la red proporcionados por Media Services, los enlaces privados pueden permitir el uso de Media Services sin exponer los puntos de conexión a la red pública de internet.

## <a name="azure-private-endpoint-and-azure-private-link"></a>El punto de conexión privado de Azure y Azure Private Link

Un [punto de conexión privado de Azure](/private-link/private-endpoint-overview) es una interfaz de red que usa una dirección IP privada de la red virtual.  Esta interfaz de red le conecta de forma privada y segura a un servicio a través de Azure Private Link.

Se puede acceder a los puntos de conexión de Media Services desde una red virtual mediante puntos de conexión privados. También se puede acceder a los puntos de conexión privados desde redes virtuales emparejadas u otras redes conectadas a la red virtual mediante una ruta rápida o VPN.

[Azure Private Link](/private-link/) permite el acceso a los puntos de conexión privados de Media Services en la red virtual sin exponerlos a la red pública de Internet. Enruta el tráfico a través de la red troncal de Microsoft.

## <a name="restricting-access"></a>Restricción del acceso

> [!Important]
> Crear un punto de conexión privado **NO** le deshabilita implícitamente el acceso a Internet.

El acceso a Internet a los puntos finales de la cuenta de Media Services puede restringirse de dos maneras:

- Restringir el acceso a todos los recursos dentro de la cuenta de Media Services.
- Restringir el acceso por separado para cada recurso mediante la lista de direcciones IP permitidas.

## <a name="media-services-endpoints"></a>Puntos de conexión de Media Services

| Punto de conexión                    | Descripción                                                               | Compatibilidad con vínculo privado | Control de acceso a Internet |
| --------------------------- | ------------------------------------------------------------------------- | --------------------- | ----------------------- |
| Punto de conexión de streaming          | Servidor de origen para streaming de vídeo y formatos multimedia en HLS y DASH | Sí                   | Lista de direcciones IP permitidas            |
| Punto de conexión de streaming con CDN | Transmisión de medios a muchos espectadores                                              | No                    | Administrado por CDN          |
| Entrega de claves                | Proporciona claves de contenido multimedia y licencias DRM a los espectadores de elementos multimedia             | Sí                   | Lista de direcciones IP permitidas            |
| Evento en directo                  | Ingesta de contenido multimedia para streaming en vivo                                  | Sí                   | Lista de direcciones IP permitidas            |

> [!NOTE]
> Las cuentas de Media Services creadas con versiones de API anteriores al 05-01-2020 también tienen un punto de conexión para el punto de conexión de API de RESTv2 heredado (desuso pendiente).  Este punto de conexión no admite vínculos privados.

## <a name="other-private-link-enabled-azure-services"></a>Otros servicios Azure habilitados para Private Link

| Servicio                | Integración de Media Services                      | Documentación de Private Link |
| ---------------------- | ----------------------------------------------- | -------------------------- |
| Azure Storage          | Se usa para almacenar elementos multimedia                             | [Uso de puntos de conexión privados para Azure Storage](/storage/common/storage-private-endpoints) |
| Azure Key Vault        | Usado para almacenar [claves administradas por el cliente](security-customer-managed-keys-portal-tutorial.md)             | [Configuración de las redes de Azure Key Vault](/key-vault/general/how-to-azure-key-vault-network-security) |
| Azure Resource Manager | Proporciona acceso a las API de Media Services          | [Uso de la API REST para crear un vínculo privado para administrar recursos de Azure](/azure-resource-manager/management/create-private-link-access-rest) |
| Event Grid             | Proporciona [notificaciones de eventos de Media Services](./monitoring/job-state-events-cli-how-to.md) | [Configuración de puntos de conexión privados para temas o dominios de Azure Event Grid](/event-grid/configure-private-endpoints)  |

## <a name="private-endpoints-are-created-on-the-media-services-account"></a>Los puntos de conexión privados se crean en la cuenta de Media Services

Los puntos de conexión privados para la entrega de claves, los puntos de conexión de streaming y los eventos en directo se crean en la cuenta Media Services en lugar de crearse individualmente.

Se crea una dirección IP privada para cada punto de conexión de streaming o evento en directo en la cuenta de Media Services cuando se crea un recurso de punto de conexión privado de Media Services. Por ejemplo, si tiene dos puntos de conexión de streaming iniciados, se debe crear un único punto de conexión privado para conectar ambos puntos de conexión de streaming a una red virtual. Los recursos se pueden conectar a varias redes virtuales al mismo tiempo.

El acceso a Internet de la cuenta de Media Services debe estar restringido, ya sea para todos los recursos de la cuenta o por separado para cada recurso.

## <a name="private-link-pricing"></a>Precios de Private Link
Para más información sobre los precios, consulte [Precios de Azure Private Link](https://azure.microsoft.com/pricing/details/private-link)

## <a name="private-link-how-tos-and-faqs"></a>Private Link y preguntas más frecuentes

- [Creación de una cuenta de Media Services y una cuenta de almacenamiento con un vínculo privado utilizando una plantilla de administración de recursos de Azure](security-private-link-arm-how-to.md)
- [Creación de un vínculo privado para un punto de conexión de streaming](security-private-link-streaming-endpoint-how-to.md)