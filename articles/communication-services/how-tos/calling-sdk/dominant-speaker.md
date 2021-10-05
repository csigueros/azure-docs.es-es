---
title: Obtención de los hablantes activos
titleSuffix: An Azure Communication Services how-to guide
description: Use SDK de Azure Communication Services para representar a los hablantes activos en una llamada.
author: probableprime
ms.author: rifox
ms.service: azure-communication-services
ms.subservice: calling
ms.topic: how-to
ms.date: 08/10/2021
ms.custom: template-how-to
ms.openlocfilehash: cb2391db2f24eaf4dca4e0463ab508b27361fae0
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128699503"
---
# <a name="get-active-speakers-within-a-call"></a>Obtención de los hablantes activos dentro de una llamada

[!INCLUDE [Public Preview Disclaimer](../../includes/public-preview-include-document.md)]

Durante una llamada activa, es posible que desee obtener una lista de los hablantes activos para representarlos o mostrarlos de forma diferente. Esta es la manera de hacerlo.

## <a name="prerequisites"></a>Requisitos previos

- Una cuenta de Azure con una suscripción activa. [Cree una cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 
- Un recurso de Communication Services implementado. [Cree un recurso de Communication Services](../../quickstarts/create-communication-resource.md).
- Un token de acceso de usuario para habilitar el cliente de llamada. Para más información, consulte [Inicio rápido: Creación y administración de tokens de acceso](../../quickstarts/access-tokens.md).
- Opcional: Realice el inicio rápido para [agregar llamadas de voz a la aplicación](../../quickstarts/voice-video-calling/getting-started-with-calling.md)

[!INCLUDE [Dominant Speaker JavaScript](./includes/dominant-speaker/dominant-speaker-web.md)]

## <a name="next-steps"></a>Pasos siguientes
- [Aprenda a administrar vídeo](./manage-video.md)
- [Aprenda a administrar llamadas](./manage-calls.md)
- [Aprenda a grabar llamadas](./record-calls.md)