---
title: Creación de una Private Link para un punto de conexión de streaming
description: En este artículo se muestra cómo usar un vínculo privado con un punto de conexión de streaming. Va a crear un recurso de punto de conexión privado que es un vínculo entre una red virtual y un punto de conexión de streaming. Esta implementación crea una dirección IP de interfaz de red dentro de la red virtual. El vínculo privado permite conectar la interfaz de red de la red privada al punto de conexión de streaming de la Media Services privada. También va a crear zonas DNS que pasan las direcciones IP privadas.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: how-to
ms.date: 10/22/2021
ms.author: inhenkel
ms.openlocfilehash: 3175925fe8d5273ec5f9bb0220b439a01df5ef11
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131093270"
---
# <a name="create-a-private-link-for-a-streaming-endpoint"></a>Creación de una Private Link para un punto de conexión de streaming

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

En este artículo se muestra cómo usar un vínculo privado con un punto de conexión de streaming. Suponemos que ya conoce cómo crear un [grupo de recursos de Azure](/azure-resource-manager/management/manage-resource-groups-portal), una [cuenta de servicios multimedia](account-create-how-to.md) y una [red virtual de Azure](/virtual-network/quick-create-portal).

Va a crear un recurso de punto de conexión privado que es un vínculo entre una red virtual y un punto de conexión de streaming. Esta implementación crea una dirección IP de interfaz de red dentro de la red virtual. El vínculo privado permite conectar la interfaz de red de la red privada al punto de conexión de streaming de la Media Services privada. También va a crear zonas DNS que pasan las direcciones IP privadas.

La red virtual creada para este recorrido es solo para ayudar con el ejemplo.  Se supone que tiene una red virtual existente que usará para producción.

> [!NOTE]
> A medida que siga los pasos, asigne un nombre similar a los recursos para que puedan entenderse fácilmente como con un propósito similar.  Por ejemplo, *privatelink1stor para* la cuenta de almacenamiento y *privatelink1mi* para la identidad administrada.

## <a name="create-a-resource-group-and-a-media-services-account"></a>Creación de un grupo de recursos y una Media Services recursos

1. Cree un grupo de recursos de Azure.
1. Cree una cuenta de Media Services.  Al crear la cuenta, se crea un punto de conexión de streaming predeterminado. La creación de una identidad administrada es necesaria durante el proceso de instalación.
1. Cree una red virtual de Azure con la configuración predeterminada.

En este momento, no hay nada en la red virtual, la cuenta de Media Services tiene un punto de conexión orientado a Internet que incluye un punto de conexión de streaming orientado a Internet, entrega de claves y eventos en directo.  El paso siguiente hará que el punto de conexión de streaming sea privado.

## <a name="start-the-streaming-endpoint"></a>Inicio del punto de conexión de streaming

1. Vaya a la Media Services que ha creado.  
1. Seleccione **Puntos de conexión de streaming** en el menú. Aparecerá la pantalla Puntos de conexión de streaming.
1. Seleccione el punto de conexión de streaming predeterminado que creó al configurar la cuenta Media Services usuario.  Aparecerá la pantalla de punto de conexión de streaming predeterminada.
1. Seleccione **Inicio**. Aparecerán las opciones de inicio.
1. Seleccione **Ninguno** en la lista desplegable del plan de tarifa de CDN.
1. Seleccione **Inicio**.  Se iniciará el punto de conexión de streaming. El punto de conexión sigue orientado a Internet.

## <a name="create-a-private-endpoint"></a>Creación de un punto de conexión privado

1. Vuelva a la cuenta Media Services usuario.
1. Seleccione **Redes** en el menú.
1. Seleccione la pestaña **Conexiones de punto de conexión privado**.  Aparecerá la pantalla de conexión del punto de conexión privado.
1. Seleccione **Agregar un punto de conexión privado.** Aparecerá la pantalla Crear un punto de conexión privado.
1. En el campo **Nombre**, asigne al punto de conexión privado un nombre como *privatelinkpe.*
1. En la lista desplegable **Región**, seleccione una región como *Oeste de EE. UU. 2.*
1. Seleccione **Siguiente: Resource** (Siguiente: Recurso). Aparecerá la pantalla Recurso.

## <a name="assign-the-private-endpoint-to-a-resource"></a>Asignación del punto de conexión privado a un recurso

1. En los botones de radio **Métodos de conexión**, seleccione el botón de radio *Conectarse a un recurso de Azure en mi directorio*.
1. En la lista desplegable **Tipo de recurso**, seleccione *Microsoft.Media/mediaservices*.
1. En la lista desplegable **Recurso**, seleccione la cuenta de Media Services que creó.
1. En la lista desplegable **Recurso secundario de destino**, seleccione el punto de conexión de streaming que creó.

## <a name="deploy-the-private-endpoint-to-the-virtual-network"></a>Implementación del punto de conexión privado en la red virtual

1. En la lista desplegable **Red virtual**, seleccione la red virtual que creó.
1. Seleccione en la lista desplegable **Subred**, la subred con la que desea trabajar.
1. Permanezca en esta pantalla.

## <a name="create-dns-zones-to-use-with-the-private-endpoint"></a>Creación de zonas DNS para usarlas con el punto de conexión privado

Para usar el punto de conexión de streaming dentro de la red virtual, cree zonas DNS privadas. Puede usar el mismo nombre DNS y recuperar la dirección IP privada del punto de conexión de streaming.

1. En la misma pantalla, para la configuración de **media-azure-net**, seleccione el grupo de recursos que creó en la lista desplegable **Grupo de recursos**.
1. Para la configuración de **privatelink-media-azure-net**, seleccione el mismo grupo de recursos en la lista desplegable **Grupo de recursos**.
1. Seleccione **Siguiente: Etiquetas**. Si desea agregar etiquetas a los recursos, haga esto aquí.
1. Seleccione **Siguiente: Revisar y crear**. Aparecerá la pantalla Revisar y crear.
1. Revise la configuración y asegúrese de que son correctas.
1. Seleccione **Crear**. Aparece la pantalla de implementación del punto de conexión privado.

Mientras la implementación está en curso, también está creando una [plantilla Azure Resource Manager (ARM).](/azure-resource-manager/templates/overview) Puede usar plantillas de ARM para automatizar la implementación. Para ver la plantilla, seleccione **Plantilla** en el menú.

## <a name="clean-up-resources"></a>Limpieza de recursos

Si no planea usar los recursos creados en este ejercicio, basta con eliminar el grupo de recursos. Si no elimina los recursos, se le cobrará por ellos.