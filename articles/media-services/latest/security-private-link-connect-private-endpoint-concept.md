---
title: Introducción a las conexiones de punto de conexión privado
description: Este artículo es una introducción a las conexiones de punto de conexión privado con Media Services.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: conceptual
ms.date: 10/22/2021
ms.author: inhenkel
ms.openlocfilehash: 346630425cd6a77c7ebf18b6f16a1bbd1bcab705
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131091249"
---
# <a name="private-endpoint-connections-overview"></a>Introducción a las conexiones de punto de conexión privado

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Este artículo es una introducción a las conexiones de punto de conexión privado con Media Services.

## <a name="clients-using-vnet"></a>Clientes con red virtual

Los clientes de una red virtual que usa el punto de conexión privado deben usar el mismo nombre DNS para conectarse a Media Services que los clientes que se conectan a los puntos de conexión de Media Services públicos. Media Services se basa en la resolución DNS para enrutar automáticamente las conexiones desde la red virtual a los puntos de conexión de Media Services a través de un vínculo privado.

> [!IMPORTANT]
> Use los mismos nombres DNS para los puntos de conexión de Media Services cuando use puntos de conexión privados, como haría normalmente. No se conecte a los puntos de conexión de Media Services con la dirección URL del subdominio de vínculo privado.

De manera predeterminada, Media Services crea una [zona DNS privada](/dns/private-dns-overview) conectada a la red virtual con las actualizaciones necesarias para los puntos de conexión privados. Sin embargo, si usa su propio servidor DNS, puede que tenga que realizar cambios adicionales en la configuración de DNS. En la sección Cambios de DNS que aparece a continuación se describen las actualizaciones necesarias para los puntos de conexión privados.

## <a name="dns-changes-for-private-endpoints"></a>Cambios de DNS en puntos de conexión privados

Al crear un punto de conexión privado, el registro del recurso **CNAME de DNS** de los puntos de conexión de Media Services se actualiza a un alias de un subdominio con el prefijo `privatelink`. De manera predeterminada, también se crea una zona DNS privada correspondiente al subdominio `privatelink`, con los registros de recursos A de DNS de los puntos de conexión privados.

Cuando se resuelve un nombre DNS de Media Services desde fuera de la red virtual con el punto de conexión privado, se resuelve en el punto de conexión público del punto de conexión de Media Services. Cuando se resuelve desde la red virtual que hospeda el punto de conexión privado, la dirección URL de Media Services se resuelve en la dirección IP del punto de conexión privado.

Por ejemplo, los registros de recursos DNS de un punto de conexión de streaming de `MediaAccountA` de Media Services, cuando se resuelven desde fuera de la red virtual que hospeda el punto de conexión privado, son:

| Nombre | Tipo | Valor |
| ---- | ---- | ----- |
| mediaaccounta-uswe1.streaming.media.azure.net | CNAME | mediaaccounta-uswe1.streaming.privatelink.media.azure.net |
|mediaaccounta-uswe1.streaming.privatelink.media.azure.net | CNAME | `<Streaming Endpoint public endpoint>` |
| `<Streaming Endpoint public endpoint>` | CNAME | `<Streaming Endpoint internal endpoint>` |
| `<Streaming Endpoint internal endpoint>` | A | `<Streaming Endpoint public IP address>` |

Puede denegar o restringir el acceso público de Internet a los puntos de conexión de Media Services mediante listas de IP permitidas o si deshabilita el acceso de red público para todos los recursos de la cuenta.

Los registros de recursos DNS del punto de conexión de streaming de ejemplo de "MediaAccountA", si los resuelve un cliente de la red virtual que hospeda el punto de conexión privado, son:

| Nombre | Tipo | Valor |
| ---- | ---- | ----- |
| mediaaccounta-uswe1.streaming.media.azure.net | CNAME | mediaaccounta-uswe1.streaming.privatelink.media.azure.net |
|mediaaccounta-uswe1.streaming.privatelink.media.azure.net | A | `<Streaming Endpoint public endpoint>`, por ejemplo "10.0.0.9 |

Este enfoque permite el acceso al punto de conexión de Media Services con el mismo nombre DNS para los clientes de la red virtual que hospeda los puntos de conexión privados. Hace lo mismo para los clientes de fuera de la red virtual.

Si está usando un servidor DNS personalizado en la red, los clientes deben resolver el FQDN del punto de conexión de Media Services en la dirección IP del punto de conexión privado. Configure el servidor DNS para delegar el subdominio de vínculo privado en la zona DNS privada de la red virtual, o configure los registros A de  `mediaaccounta-usw22.streaming.privatelink.media.azure.net` con la dirección IP del punto de conexión privado.

> [!TIP]
> Si usa un servidor DNS personalizado o local, debe configurarlo para resolver el nombre del punto de conexión de Media Services del subdominio de vínculo privado en la dirección IP del punto de conexión privado. Para ello, delegue el subdominio de vínculo privado en la zona DNS privada de la red virtual, o bien configure la zona DNS en el servidor DNS y agregue los registros A de DNS.

Los nombres de zona DNS recomendados para los puntos de conexión privados de los servicios de almacenamiento, y los subrecursos de destino del punto de conexión asociados, son:

| Punto de conexión de Media Services | Id. de grupo de vínculo privado | Nombre de zona DNS |
| ----------------------- | --------------------- | ------------- |
| Punto de conexión de streaming | streamingendpoint | privatelink.media.azure.net |
| Entrega de claves | streamingendpoint | privatelink.media.azure.net |
| Evento en directo | liveevent | privatelink.media.azure.net |

Para obtener más información sobre la configuración de un servidor DNS propio para que admita puntos de conexión privados, vea los siguientes artículos:

- [Resolución de nombres de recursos en redes virtuales de Azure](/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances#name-resolution-that-uses-your-own-dns-server)
- [Configuración de DNS para puntos de conexión privados](/private-link/private-endpoint-overview#dns-configuration)

## <a name="public-network-access-flag"></a>Marca de acceso de red pública

La marca `publicNetworkAccess` de la cuenta de Media Services se puede usar para permitir o bloquear el acceso a puntos de conexión de Media Services desde la red pública de Internet. Cuando `publicNetworkAccess` está deshabilitada, las solicitudes a cualquier punto de conexión de Media Services desde la red pública de Internet se bloquean; las solicitudes a puntos de conexión privados se siguen permitiendo.  

## <a name="service-level-ip-allowlists"></a>Listas de direcciones IP permitidas de nivel de servicio

Si `publicNetworkAccess` está habilitada, se permiten solicitudes desde la red pública de Internet, sujetas a listas de direcciones IP permitidas de nivel de servicio. Si `publicNetworkAccess` está deshabilitada, las solicitudes desde la red pública de Internet se bloquean, independientemente de la configuración de la lista de direcciones IP permitidas. Las listas de direcciones IP permitidas solo se aplican a las solicitudes de la red pública de Internet; las solicitudes a puntos de conexión privados no se filtran por las listas de direcciones IP permitidas.
