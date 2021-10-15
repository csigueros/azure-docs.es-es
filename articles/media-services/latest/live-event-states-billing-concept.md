---
title: Estados y facturación de eventos en directo en Azure Media Services
description: En este tema se proporciona información general sobre los estados y facturación de los eventos en directo en Azure Media Services.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: conceptual
ms.date: 10/26/2020
ms.author: inhenkel
ms.openlocfilehash: 961c563a12a3edf4df5c898f0c8ee8f1a5b2daf0
ms.sourcegitcommit: 7bd48cdf50509174714ecb69848a222314e06ef6
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/02/2021
ms.locfileid: "129390128"
---
# <a name="live-event-states-and-billing"></a>Estados y facturación de eventos en directo

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

En Azure Media Services, un evento en directo comienza la facturación tan pronto como su estado realiza la transición a **En ejecución** o **En espera**. Se le facturará incluso si no hay ningún flujo de vídeo a través del servicio. Para detener la facturación del evento en directo, debe pararlo. La transcripción en directo se factura igual que el evento en directo.

Si **LiveEventEncodingType** en el [evento en directo](/rest/api/media/liveevents) está establecido en Estándar o Premium1080p, Media Services cerrará automáticamente cualquier evento en directo que tenga aún el estado **En ejecución** doce horas después de que se pierda la fuente de entrada y si no hay ninguna **salida en directo** en ejecución. Sin embargo, se le facturará por el tiempo que el evento en directo haya estado en el estado **En ejecución**.

> [!NOTE]
> Los eventos en directo de tránsito (básico o estándar) NO se cierran automáticamente y se deben detener explícitamente a través de la API para evitar una facturación excesiva.

## <a name="states"></a>States

El evento en directo puede estar en uno de los siguientes estados.

|State|Descripción|
|---|---|
|**Stopped**| Este es el estado inicial del evento en directo después de su creación (a menos que haya establecido el inicio automático en true). No se produce ninguna facturación en este estado. El evento en directo no puede recibir ninguna entrada. |
|**Starting** (iniciándose)| El evento en directo se está iniciando y los recursos se están asignando. No se produce ninguna facturación en este estado.  Si se produce un error, el evento en directo vuelve al estado Detenido.|
| **Asignando** | Se llamó a la acción de asignación en el evento en directo y los recursos se están aprovisionando para este. Una vez que esta operación se realiza correctamente, el evento en directo cambia al estado En espera.
|**En espera**| Los recursos de eventos en directo se han aprovisionado y el evento está listo para iniciarse. La facturación comienza en este estado.  La mayoría de las propiedades todavía se pueden actualizar, pero no se permite la ingesta ni la transmisión durante este estado.
|**Ejecución**| Se han asignado los recursos del evento en directo, se han generado las direcciones URL de ingesta y visualización previa y puede recibir transmisiones en vivo. En este momento, la facturación está activa. Debe llamar explícitamente a Stop en el recurso del evento en directo para evitar que continúe la facturación.|
|**Stopping** (Deteniéndose)| El evento en directo se detendrá y los recursos se desaprovisionarán. No se produce facturación en este estado transitorio. |
|**Eliminando**| El evento en directo se eliminará. No se produce facturación en este estado transitorio. |

Al crear el evento en directo puede elegir habilitar las transcripciones en directo. Si lo hace, se le facturarán las transcripciones en directo cuando el evento en directo se encuentre en el estado **En ejecución**. Tenga en cuenta que se le facturará aunque no haya ningún flujo de audio a través del evento en directo.

## <a name="next-steps"></a>Pasos siguientes

- [Introducción al streaming en vivo](stream-live-streaming-concept.md)
- [Tutorial de Live Streaming](stream-live-tutorial-with-api.md)
