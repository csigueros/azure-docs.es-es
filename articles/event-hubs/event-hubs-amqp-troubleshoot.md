---
title: Solución de errores de AMQP en Azure Event Hubs | Microsoft Docs
description: Proporciona una lista de errores de AMQP que pueden aparecer al usar Azure Event Hubs y la causa de esos errores.
ms.topic: article
ms.date: 09/20/2021
ms.openlocfilehash: 7e73c85f65a6ea9edef36ab810110e576b7ea0c0
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128649404"
---
# <a name="amqp-errors-in-azure-event-hubs"></a>Errores de AMQP en Azure Event Hubs
En este artículo se indican algunos de los errores que aparecen al usar AMQP con Azure Event Hubs. Corresponden todos a comportamientos estándar del servicio. Para evitarlos, puede efectuar llamadas de envío o recepción en la conexión o el vínculo, lo que hace que estos se vuelvan a crear automáticamente.

## <a name="link-is-closed"></a>Se cierra el vínculo 
Aparecerá el siguiente error cuando la conexión de AMQP y el vínculo estén activos pero no se realicen llamadas (por ejemplo, envío o recepción) con el vínculo durante 30 minutos. Esto hace que se cierre el vínculo. La conexión sigue abierta.

" AMQP:link: detach-forced: The link 'G2:7223832:user.tenant0.cud_00000000000-0000-0000-0000-00000000000000' is force detached by the broker because of errors occurred in publisher(link164614). Detach origin: AmqpMessagePublisher.IdleTimerExpired: Idle timeout: 00:30:00. TrackingId:00000000000000000000000000000000000000_G2_B3, SystemTracker:mynamespace:Topic:MyTopic, Timestamp: 2/16/2018 11:10:40 PM "

## <a name="connection-is-closed"></a>Se cierra la conexión
Aparecerá el siguiente error en la conexión de AMQP cuando se hayan cerrado todos los vínculos de la conexión porque no ha habido ninguna actividad (inactivo) y no se haya creado un nuevo vínculo en 5 minutos.

" Error{condition=amqp:connection:forced, description='The connection was inactive for more than the allowed 300000 milliseconds and is closed by container 'LinkTracker'. TrackingId:00000000000000000000000000000000000_G21, SystemTracker:gateway5, Timestamp:2019-03-06T17:32:00', info=null} "

## <a name="link-isnt-created"></a>Vínculo no creado 
Aparecerá este error cuando se cree una nueva conexión de AMQP, pero no se cree un vínculo antes de que haya transcurrido 1 minuto.

" Error{condition=amqp:connection:forced, description='The connection was inactive for more than the allowed 60000 milliseconds and is closed by container 'LinkTracker'. TrackingId:0000000000000000000000000000000000000_G21, SystemTracker:gateway5, Timestamp:2019-03-06T18:41:51', info=null} "

## <a name="next-steps"></a>Pasos siguientes
Para obtener más información sobre AMQP, consulte la [guía del protocolo AMQP 1.0](../service-bus-messaging/service-bus-amqp-protocol-guide.md).
