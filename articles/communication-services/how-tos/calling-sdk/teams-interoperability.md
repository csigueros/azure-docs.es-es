---
title: Unión a una reunión de Teams
titleSuffix: An Azure Communication Services how-to guide
description: Use los SDK de Azure Communication Services para unirse a una reunión de Teams.
author: probableprime
ms.author: rifox
ms.service: azure-communication-services
ms.subservice: teams-interop
ms.topic: how-to
ms.date: 08/10/2021
ms.custom: template-how-to
ms.openlocfilehash: a2581efd7d3de01451012badfbfb459002b5d9a8
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128635686"
---
# <a name="join-a-teams-meeting"></a>Unirse a una reunión de Teams

[!INCLUDE [Public Preview Disclaimer](../../includes/public-preview-include-document.md)]

Los SDK de Azure Communication Services pueden permitir que los usuarios se unan a reuniones periódicas de Microsoft Teams. Aquí se explica cómo.

## <a name="prerequisites"></a>Requisitos previos

- Una cuenta de Azure con una suscripción activa. [Cree una cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 
- Un recurso de Communication Services implementado. [Cree un recurso de Communication Services](../../quickstarts/create-communication-resource.md).
- Un token de acceso de usuario para habilitar el cliente de llamada. Para más información, consulte [Inicio rápido: Creación y administración de tokens de acceso](../../quickstarts/access-tokens.md).
- Opcional: Realice el inicio rápido para [agregar llamadas de voz a la aplicación](../../quickstarts/voice-video-calling/getting-started-with-calling.md)

> [!NOTE]
> Esta API se ofrece a los desarrolladores como versión preliminar y puede cambiar en función de los comentarios que recibamos. No utilice esta API en un entorno de producción. Para usar esta API, utilice la versión beta del SDK web de llamada de ACS.

Para unirse a una reunión de Teams, use el método `join` y pase un vínculo de reunión o las coordenadas de una reunión.

Unirse mediante un vínculo de reunión:

```js
const locator = { meetingLink: '<MEETING_LINK>'}
const call = callAgent.join(locator);
```

Unirse mediante las coordenadas de reunión:

```js
const locator = {
    threadId: <thread id>,
    organizerId: <organizer id>,
    tenantId: <tenant id>,
    messageId: <message id>
}
const call = callAgent.join(locator);
```
## <a name="next-steps"></a>Pasos siguientes
- [Aprenda a administrar llamadas](./manage-calls.md)
- [Aprenda a administrar vídeo](./manage-video.md)
- [Aprenda a transferir llamadas](./transfer-calls.md)