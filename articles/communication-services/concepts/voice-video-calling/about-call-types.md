---
title: Conceptos de voz y vídeo en Azure Communication Services
titleSuffix: An Azure Communication Services concept document
description: Obtenga información sobre los tipos de llamada de Communication Services.
author: probableprime
manager: chpalm
services: azure-communication-services
ms.author: rifox
ms.date: 06/30/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.subservice: calling
ms.openlocfilehash: 9fd2de1bdcd89f027db9a3fd74ef40111f540ced
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128635971"
---
# <a name="voice-and-video-concepts"></a>Conceptos de voz y vídeo

[!INCLUDE [SDP Plan B Deprecation Notice](../../includes/plan-b-sdp-deprecation.md)]

Puede usar Azure Communication Services para hacer y recibir llamadas de voz o videollamadas individuales o de grupo. Las llamadas se pueden hacer a otros dispositivos conectados a Internet y en teléfonos convencionales. Puede usar los SDK para JavaScript, Android o iOS de Communication Services para crear aplicaciones que permitan a los usuarios hablar entre sí en conversaciones privadas o en discusiones grupales. Azure Communication Services admite hacer y recibir llamadas desde servicios o bots.

## <a name="call-types-in-azure-communication-services"></a>Tipos de llamada en Azure Communication Services

Hay varios tipos de llamadas que puede hacer en Azure Communication Services. El tipo de llamadas que haga determinará el esquema de señales, los flujos de tráfico multimedia y el modelo de precios.

### <a name="voice-over-ip-voip"></a>Llamada de voz sobre IP (VoIP)

Cuando un usuario de la aplicación llama a otro usuario de la aplicación a través de una conexión de datos o Internet, la llamada se hace a través de voz sobre IP (VoIP). En este caso, las señales y los elementos multimedia fluyen a través de Internet.

### <a name="public-switched-telephone-network-pstn"></a>Red telefónica conmutada (RTC)

Cada vez que los usuarios interactúan con un número de teléfono tradicional, las llamadas se facilitan mediante llamadas de voz de RTC (red telefónica conmutada). Para hacer y recibir llamadas RTC, tiene que agregar funcionalidades de telefonía a su recurso de Azure Communication Services. En este caso, las señales y los elementos multimedia usan una combinación de tecnologías basadas en IP y en RTC para conectar a los usuarios.

### <a name="one-to-one-call"></a>Llamadas de uno a uno

Una llamada de uno a uno en Azure Communication Services se produce cuando uno de los usuarios se conecta con otro usuario mediante uno de nuestros SDK. La llamada puede ser VoIP o RTC.

### <a name="group-call"></a>Llamada grupal

Una llamada grupal en Azure Communication Services se produce cuando tres o más participantes se conectan entre sí. Cualquier combinación de usuarios con conexión VoIP o RTC puede estar presente en una llamada grupal. Una llamada de uno a uno se puede convertir en una llamada grupal si se agregan más participantes a la llamada. Uno de estos participantes puede ser un bot.

### <a name="supported-video-standards"></a>Estándares de vídeo compatibles
Se admite H.264 (MPEG-4).

### <a name="video-quality"></a>Calidad de vídeo
En los SDK nativos (iOS y Android), se admite hasta Full HD 1080p. Para el SDK web (JS), se admite el Standard HD 720p. La calidad depende del ancho de banda disponible.

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Introducción a las llamadas](../../quickstarts/voice-video-calling/getting-started-with-calling.md)

Para más información, consulte los siguientes artículos.
- Familiarización con los [flujos de llamada](../call-flows.md) generales
- [Tipos de número de teléfono](../telephony-sms/plan-solution.md)
- Más información sobre las [funcionalidades de Calling SDK](../voice-video-calling/calling-sdk-features.md).
