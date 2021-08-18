---
title: 'Speech Devices SDK: servicio de voz'
titleSuffix: Azure Cognitive Services
description: Introducción al SDK de dispositivos de voz. El servicio de voz funciona con una amplia variedad de dispositivos y orígenes de audio. El SDK de dispositivos de voz es una biblioteca pre-ajustada que está emparejada con kits de desarrollo específicos integrados de la matriz del micrófono.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: lajanuar
ms.openlocfilehash: 2f86d64785cc0d235e4923bacb26447f7015267c
ms.sourcegitcommit: e7d500f8cef40ab3409736acd0893cad02e24fc0
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "122070751"
---
# <a name="what-is-the-speech-devices-sdk"></a>¿Qué es Speech Devices SDK?

El [servicio de voz](overview.md) funciona con una amplia variedad de dispositivos y orígenes de audio. Ahora puede llevar las aplicaciones de voz al siguiente nivel con hardware y software coincidente. El SDK de dispositivos de voz es una biblioteca pre-ajustada que está emparejada con kits de desarrollo específicos integrados de la matriz del micrófono.

El SDK de dispositivos de voz puede ayudarle a:

- Probar rápidamente nuevos escenarios de voz.
- Integrar más fácilmente el servicio de voz basado en la nube en el dispositivo.
- Crear una experiencia de usuario excepcional para sus clientes.

El SDK de dispositivos de voz consume el [SDK de voz](speech-sdk.md). Uso de nuestros algoritmos avanzados de procesamiento de audio con la matriz del micrófono del dispositivo para enviar el audio al [servicio de voz](overview.md). Ofrece un [reconocimiento de voz](speech-to-text.md) de campo lejano preciso mediante la supresión de ruido, la cancelación del eco, la formación de haces y la eliminación de la reverberación.

También puede usar el SDK de dispositivos de voz para crear dispositivos de ambiente que tengan su propia [palabra clave personalizada](./custom-keyword-basics.md). Una palabra clave personalizada proporciona una indicación de inicio de una interacción del usuario única para su marca.

El SDK de dispositivos de voz permite diversos escenarios habilitados para voz, como [asistentes de voz](./voice-assistants.md), sistemas de pedidos de comida para llevar, [transcripción de conversaciones](./conversation-transcription.md) y altavoces inteligentes. Puede responder a los usuarios con texto, hablarles con una voz predeterminada o [personalizada](./how-to-custom-voice-create-voice.md), proporcionar resultados de búsqueda, [traducir](speech-translation.md) a otros idiomas y mucho más. Estamos deseando ver lo que ha compilado.

## <a name="get-the-speech-devices-sdk"></a>Obtener el SDK de dispositivos de voz

### <a name="android"></a>Android

El SDK de dispositivos de voz para Android es compatible con [Roobo v1](speech-devices-sdk-roobo-v1.md) y dispositivos equivalentes. Para ello, descargue la versión más reciente del [SDK de dispositivos de voz de Android](https://aka.ms/sdsdk-download-android).


Si tiene otro dispositivo Android, como un teléfono o un móvil, empiece con el [SDK de voz de Android](speech-sdk.md).


### <a name="windows"></a>Windows

En el caso de Windows, la aplicación de ejemplo se proporciona como aplicación Java multiplataforma. Descargue la última versión del [SDK para dispositivos de voz de JRE](https://aka.ms/sdsdk-download-JRE).
La aplicación se crea con el paquete del SDK de Voz y el entorno de desarrollo integrado de Java de Eclipse (v4) en Windows de 64 bits. Se ejecuta en un entorno de tiempo de ejecución de Java 8 (JRE) de 64 bits.

### <a name="linux"></a>Linux

En el caso de Linux, la aplicación de ejemplo se proporciona como aplicación Java multiplataforma. Descargue la última versión del [SDK para dispositivos de voz de JRE](https://aka.ms/sdsdk-download-JRE).
La aplicación se crea con el paquete del SDK de Voz y el entorno de desarrollo integrado de Java de Eclipse (v4) en Linux de 64 bits (Ubuntu 16.04, Ubuntu 18.04, Debian 9, RHEL 8 y CentOS 8). Se ejecuta en un entorno de tiempo de ejecución de Java 8 (JRE) de 64 bits.

Se proporcionan archivos binarios adicionales para ofrecer compatibilidad con los próximos dispositivos: [Roobo v2 DDK](https://aka.ms/sdsdk-download-roobov2), [Urbetter DDK](https://aka.ms/sdsdk-download-urbetter), [GGEC Speaker](https://aka.ms/sdsdk-download-speaker), [Linux ARM32](https://aka.ms/sdsdk-download-linux-arm32) y [Linux ARM64](https://aka.ms/sdsdk-download-linux-arm64).

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Elija el dispositivo de voz](get-speech-devices-sdk.md)
> [!div class="nextstepaction"]
> [Obtenga una clave de suscripción gratuita a los servicios de Voz](overview.md#try-the-speech-service-for-free)