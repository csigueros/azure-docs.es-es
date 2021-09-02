---
title: Uso de puntos de conexión de servicio de red virtual con el servicio de Voz
titleSuffix: Azure Cognitive Services
description: En este artículo, se describe cómo usar el servicio de Voz con un punto de conexión de servicio de red virtual de Azure.
services: cognitive-services
author: alexeyo26
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/19/2021
ms.author: alexeyo
ms.openlocfilehash: 903ce0888aa04e7005468ca4f8aec9cf977ead5d
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/22/2021
ms.locfileid: "114490223"
---
# <a name="use-speech-service-through-a-virtual-network-service-endpoint"></a>Uso del servicio de Voz a través de un punto de conexión de Virtual Network

Los [puntos de conexión de servicio](../../virtual-network/virtual-network-service-endpoints-overview.md) de [red virtual de Azure](../../virtual-network/virtual-networks-overview.md) ayudan a proporcionar conectividad directa y segura con los servicios de Azure por medio de una ruta optimizada en la red troncal de Azure. Los puntos de conexión ayudan a proteger los recursos de servicio de Azure críticos únicamente para las redes virtuales. Los puntos de conexión de servicio permiten a las direcciones IP privadas de la red virtual acceder al punto de conexión de un servicio de Azure sin necesidad de una dirección IP pública en la red virtual.

En este artículo, se explica cómo configurar y usar puntos de conexión de servicio de red virtual con el servicio de Voz de Azure Cognitive Services.

> [!NOTE]
> Antes de comenzar, consulte [Configuración de redes virtuales de Azure Cognitive Services](../cognitive-services-virtual-networks.md).

En este artículo, también se describe [cómo quitar los puntos de conexión de servicio de red virtual más adelante, pero seguir usando el recurso de Voz](#use-a-speech-resource-that-has-a-custom-domain-name-but-that-doesnt-have-allowed-virtual-networks).

Para configurar un recurso de Voz para escenarios de punto de conexión de servicio de red virtual, debe hacer lo siguiente:
1. [Cree un nombre de dominio personalizado para el recurso de Voz](#create-a-custom-domain-name).
1. [Configure las redes virtuales y las opciones de redes para el recurso de Voz](#configure-virtual-networks-and-the-speech-resource-networking-settings).
1. [Ajuste las aplicaciones y soluciones existentes](#adjust-existing-applications-and-solutions).

> [!NOTE]
> La configuración y el uso de puntos de conexión de servicio de red virtual para el servicio de Voz es similar a la configuración y el uso de puntos de conexión privados. En este artículo, hacemos referencia a las secciones correspondientes del [artículo sobre el uso de puntos de conexión privados](speech-services-private-link.md) cuando los procedimientos son los mismos.

[!INCLUDE [](includes/speech-vnet-service-enpoints-private-endpoints.md)]

En este artículo, se describe cómo usar puntos de conexión de servicio de red virtual con el servicio de Voz. Para obtener información sobre los puntos de conexión privados, consulte [Uso del servicio de Voz mediante un punto de conexión privado](speech-services-private-link.md).

## <a name="create-a-custom-domain-name"></a>Creación de un nombre de dominio personalizado

Los puntos de conexión de servicio de red virtual requieren un [nombre de subdominio personalizado para Cognitive Services](../cognitive-services-custom-subdomains.md). Cree un dominio personalizado según las [instrucciones](speech-services-private-link.md#create-a-custom-domain-name) del artículo sobre el punto de conexión privado. Todas las advertencias de la sección también se aplican a los puntos de conexión de servicio de red virtual.

## <a name="configure-virtual-networks-and-the-speech-resource-networking-settings"></a>Configuración de las redes virtuales y las opciones de redes del recurso de Voz

Debe agregar todas las redes virtuales a las que se permite el acceso mediante el punto de conexión de servicio a las propiedades de redes del recurso de Voz.

> [!NOTE]
> Para acceder a un recurso de Voz mediante el punto de conexión de servicio de red virtual, debe habilitar el tipo de punto de conexión de servicio `Microsoft.CognitiveServices` para las subredes necesarias de la red virtual. Al hacerlo, se enruta todo el tráfico de la subred relacionado con Cognitive Services mediante la red troncal privada. Si tiene previsto acceder a cualquier otro recurso de Cognitive Services desde la misma subred, asegúrese de que estos recursos están configurados para permitir la red virtual. 
>
> Si no se agrega una red virtual como *permitida* en las propiedades de redes del recurso de Voz, no tendrá acceso al recurso de Voz mediante el punto de conexión de servicio, incluso si el punto de conexión de servicio `Microsoft.CognitiveServices` está habilitado para la red virtual. Y si el punto de conexión de servicio está habilitado pero no se permite la red virtual, el recurso de Voz no será accesible para la red virtual mediante una dirección IP pública, independientemente de cuál sea la otra configuración de seguridad de red del recurso de Voz. Esto se debe a que al habilitar el punto de conexión `Microsoft.CognitiveServices`, se enruta todo el tráfico relacionado con Cognitive Services mediante la red troncal privada y, en este caso, se debe permitir explícitamente a la red virtual acceder al recurso. Esta guía se aplica a todos los recursos de Cognitive Services, no solo a los recursos de Voz.  
  
1. Vaya a [Azure Portal](https://portal.azure.com/) e inicie sesión con su cuenta de Azure.
1. Seleccione el recurso de Voz.
1. En el grupo **Administración de recursos** del panel izquierdo, seleccione **Redes**.
1. En la pestaña **Firewalls y redes virtuales**, seleccione **Redes y puntos de conexión privados seleccionados**. 

   > [!NOTE]
   > Para usar puntos de conexión de servicio de red virtual, debe seleccionar la opción de seguridad de red **Redes y puntos de conexión privados seleccionados**. No se admiten otras opciones. Si el escenario requiere la opción **Todas las redes**, considere la posibilidad de usar [puntos de conexión privados](speech-services-private-link.md), que admiten las tres opciones de seguridad de red.

5. Seleccione **Agregar red virtual existente** o **Agregar nueva red virtual** y proporcione los parámetros necesarios. Seleccione **Agregar** para una red virtual existente o **Crear** para una nueva. Si agrega una red virtual existente, el punto de conexión de servicio `Microsoft.CognitiveServices` se habilitará automáticamente para las subredes seleccionadas. Esta operación puede tardar hasta 15 minutos. Además, consulte la nota que se encuentra al principio de esta sección.

### <a name="enabling-service-endpoint-for-an-existing-virtual-network"></a>Habilitación del punto de conexión de servicio para una red virtual existente 

Como se describe en la sección anterior, al configurar una red virtual como *permitida* para el recurso de Voz, el punto de conexión de servicio `Microsoft.CognitiveServices` se habilita automáticamente. Si más adelante lo deshabilita, deberá volver a habilitarlo manualmente para restaurar el acceso al punto de conexión de servicio para el recurso de Voz (y otros recursos de Cognitive Services):

1. Vaya a [Azure Portal](https://portal.azure.com/) e inicie sesión con su cuenta de Azure.
1. Seleccione la red virtual.
1. En el grupo **Configuración** del panel izquierdo, seleccione **Subredes**.
1. Seleccione la subred necesaria.
1. Aparece un nuevo panel en el lado derecho de la ventana. En este panel, en la sección **Puntos de conexión de servicio**, seleccione `Microsoft.CognitiveServices` en la lista **Servicios**.
1. Seleccione **Guardar**.

## <a name="adjust-existing-applications-and-solutions"></a>Ajustar aplicaciones y soluciones existentes

Un recurso de Voz que tiene un dominio personalizado interactúa con el servicio de Voz de una manera diferente. Esto es así para un recurso de Voz habilitado para un dominio personalizado, independientemente de si los puntos de conexión de servicio están configurados. La información de esta sección se aplica a ambos escenarios.

### <a name="use-a-speech-resource-that-has-a-custom-domain-name-and-allowed-virtual-networks"></a>Uso de un recurso de Voz que tiene un nombre de dominio personalizado y redes virtuales permitidas 

En este escenario, la opción **Redes y puntos de conexión privados seleccionados** está seleccionada en la configuración de redes del recurso de Voz y se permite al menos una red virtual. Este escenario es equivalente al [uso de un recurso de Voz que tiene un nombre de dominio personalizado y un punto de conexión privado habilitado](speech-services-private-link.md#adjust-an-application-to-use-a-speech-resource-with-a-private-endpoint).


### <a name="use-a-speech-resource-that-has-a-custom-domain-name-but-that-doesnt-have-allowed-virtual-networks"></a>Uso de un recurso de Voz que tiene un nombre de dominio personalizado pero no tiene redes virtuales permitidas

En este escenario, los puntos de conexión privados no están habilitados y una de estas afirmaciones es verdadera:

- La opción **Redes y puntos de conexión privados seleccionados** está seleccionada en la configuración de redes del recurso de Voz, pero no se han configurado redes virtuales permitidas.
- La opción **Todas las redes** está seleccionada en la configuración de redes del recurso de Voz.

Este escenario es equivalente al [uso de un recurso de Voz que tiene un nombre de dominio personalizado y no tiene puntos de conexión privados](speech-services-private-link.md#adjust-an-application-to-use-a-speech-resource-without-private-endpoints).


[!INCLUDE [](includes/speech-vnet-service-enpoints-private-endpoints-simultaneously.md)]


## <a name="learn-more"></a>Más información

* [Uso del servicio Voz mediante un punto de conexión privado](speech-services-private-link.md)
* [Puntos de conexión de servicio de red virtual](../../virtual-network/virtual-network-service-endpoints-overview.md)
* [Azure Private Link](../../private-link/private-link-overview.md)
* [Acerca del SDK de Voz](speech-sdk.md)
* [Speech-to-text REST API](rest-speech-to-text.md)
* [Text-to-speech REST API](rest-text-to-speech.md)
