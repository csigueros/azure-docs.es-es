---
title: Archivo de inclusión
description: archivo de inclusión
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: include
ms.date: 09/15/2021
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: ab3994f44ed5a77ba228d9338d40599012597a95
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128910817"
---
## <a name="message-headers"></a>Encabezados de mensaje
Estas son las propiedades que recibe en los encabezados de mensaje:

| Nombre de propiedad | Descripción |
| ------------- | ----------- | 
| aeg-subscription-name | Nombre de la suscripción de eventos. |
| aeg-delivery-count | Número de intentos realizados para el evento. |
| aeg-event-type | <p>Tipo de evento.</p><p>Puede ser uno de los siguientes valores:</p><ul><li>SubscriptionValidation</li><li>Notificación</li><li>SubscriptionDeletion</li></ul> | 
| aeg-metadata-version | <p>Versión de metadatos del evento.<p> Esta propiedad representa la versión de los metadatos en el **esquema de eventos de Event Grid** y la **versión de la especificación** en el **esquema de eventos en la nube**. </p>|
| aeg-data-version | <p>Versión de datos del evento.</p><p>Esta propiedad representa la versión de los datos en el **esquema de eventos de Event Grid** y la no se aplica en el **esquema de eventos en la nube**.</p> |
| aeg-output-event-id | Id. del evento de Event Grid. |


