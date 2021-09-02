---
title: Administración de cuentas v3 de Azure Media Services
description: Para iniciar la administración, el cifrado, la codificación, el análisis y el streaming de contenido multimedia en Azure, debe crear una cuenta de Media Services. En este artículo se explica cómo administrar las cuentas de Azure Media Services v3.
services: media-services
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: conceptual
ms.date: 03/29/2021
ms.author: inhenkel
ms.openlocfilehash: cbda31a3aaaf741424db174aa6a676858b27a23c
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/22/2021
ms.locfileid: "114471218"
---
# <a name="manage-azure-media-services-v3-accounts"></a>Administración de cuentas v3 de Azure Media Services

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Para iniciar la administración, el cifrado, la codificación, el análisis y el streaming de contenido multimedia en Azure, debe crear una cuenta de Media Services. Al crear una cuenta de Media Services, debe proporcionar el nombre de un recurso de cuenta de Azure Storage. La cuenta de almacenamiento especificada está asociada a su cuenta de Media Services. La cuenta de Media Services y todas las cuentas de almacenamiento asociadas deben estar en la misma suscripción de Azure. Para más información, consulte [Cuentas de almacenamiento](storage-account-concept.md).

[!INCLUDE [account creation note](./includes/note-2020-05-01-account-creation.md)]

## <a name="media-services-account-names"></a>Nombres de cuenta de Media Services

Los nombres de cuenta de Media Services deben estar compuestos totalmente de minúsculas o de números, sin espacios, y con una longitud de entre 3 y 24 caracteres. Los nombres de cuenta de Media Services deben ser únicos dentro de una ubicación de Azure.

Cuando se elimina cuenta de Media Services, el nombre de la cuenta se reserva durante un año. Durante un año después de eliminar la cuenta, solo la suscripción que contenía la cuenta original puede reutilizar el nombre de la cuenta en la misma ubicación de Azure.

Los nombres de cuenta de Media Services se usan en nombres DNS, incluidos los nombres de entrega de claves, eventos en directo y puntos de conexión de streaming. Si ha configurado firewalls o servidores proxy para permitir los nombres DNS de Media Services, asegúrese de que estas configuraciones se quitan en el plazo de un año después de eliminar una cuenta de Media Services.

## <a name="moving-a-media-services-account-between-subscriptions"></a>Traslado de una cuenta de Media Services entre suscripciones

Si necesita trasladar una cuenta de Media Services a una nueva suscripción, primero debe trasladar todo el grupo de recursos que contiene la cuenta de Media Services a la nueva suscripción. Debe trasladar todos los recursos asociados: Cuentas de Azure Storage, perfiles de Azure CDN, etc. Para obtener más información, consulte [Traslado de los recursos a un nuevo grupo de recursos o a una nueva suscripción](../../azure-resource-manager/management/move-resource-group-and-subscription.md). Al igual que con los recursos de Azure, los traslados del grupo de recursos pueden tardar algún tiempo en completarse.

### <a name="considerations"></a>Consideraciones

* Cree copias de seguridad de todos los datos en su cuenta antes de migrar a otra suscripción.
* Debe detener todos los puntos de conexión de streaming y los recursos de streaming en vivo. Los usuarios no podrán acceder a su contenido durante el traslado del grupo de recursos. 

> [!IMPORTANT]
> No inicie el punto de conexión de streaming hasta que la migración se haya completado correctamente.

### <a name="troubleshoot"></a>Solución de problemas

Si una cuenta de Media Services o una cuenta de Azure Storage asociada se "desconecta" después del traslado del grupo de recursos, intente rotar las claves de la cuenta de almacenamiento. Si la rotación de las claves de la cuenta de almacenamiento no resuelve el estado "desconectado" de la cuenta de Media Services, presente una nueva solicitud de soporte técnico desde el menú "Soporte técnico y solución de problemas" de la cuenta de Media Services.  

## <a name="next-steps"></a>Pasos siguientes

[Crear una cuenta](./account-create-how-to.md)
