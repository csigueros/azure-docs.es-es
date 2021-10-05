---
title: Transferencia de llamadas
titleSuffix: An Azure Communication Services how-to guide
description: Use SDK de Azure Communication Services para transferir llamadas.
author: probableprime
ms.author: rifox
ms.service: azure-communication-services
ms.subservice: calling
ms.topic: how-to
ms.date: 08/10/2021
ms.custom: template-how-to
ms.openlocfilehash: e68c81a88c972cc05ffafea0d94e82c84de4268f
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128699641"
---
# <a name="transfer-calls"></a>Transferencia de llamadas

[!INCLUDE [Public Preview Disclaimer](../../includes/public-preview-include-document.md)]

Durante una llamada activa, puede transferirla a otra persona o número. Veamos cómo hacerlo. 

## <a name="prerequisites"></a>Requisitos previos

- Una cuenta de Azure con una suscripción activa. [Cree una cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 
- Un recurso de Communication Services implementado. [Cree un recurso de Communication Services](../../quickstarts/create-communication-resource.md).
- Un token de acceso de usuario para habilitar el cliente de llamada. Para más información, consulte [Inicio rápido: Creación y administración de tokens de acceso](../../quickstarts/access-tokens.md).
- Opcional: Realice el inicio rápido para [agregar llamadas de voz a la aplicación](../../quickstarts/voice-video-calling/getting-started-with-calling.md)

[!INCLUDE [Transfer calls Client-side JavaScript](./includes/transfer-calls/transfer-calls-web.md)]

## <a name="next-steps"></a>Pasos siguientes
- [Aprenda a administrar llamadas](./manage-calls.md)
- [Aprenda a administrar vídeo](./manage-video.md)
- [Aprenda a grabar llamadas](./record-calls.md)
- [Aprenda a transcribir llamadas](./call-transcription.md)