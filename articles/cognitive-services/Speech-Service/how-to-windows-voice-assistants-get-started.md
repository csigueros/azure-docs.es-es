---
title: 'Asistentes de voz en Windows: introducción'
titleSuffix: Azure Cognitive Services
description: Los pasos para empezar a desarrollar un agente de voz de Windows, incluida una referencia al inicio rápido del código de ejemplo.
services: cognitive-services
author: cfogg6
manager: trrwilson
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: travisw
ms.openlocfilehash: 1d9b5e861b35ade3cf391e2a0ace52722821a158
ms.sourcegitcommit: d2738669a74cda866fd8647cb9c0735602642939
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/13/2021
ms.locfileid: "113650626"
---
# <a name="getting-started-with-voice-assistants-on-windows"></a>Introducción a los asistentes de voz en Windows

En esta guía se explican los pasos necesarios para empezar a desarrollar un asistente de voz en Windows.

## <a name="set-up-your-development-environment"></a>Configurado su entorno de desarrollo

Para empezar a desarrollar un asistente de voz para Windows, deberá asegurarse de que tiene el entorno de desarrollo adecuado.

- **Visual Studio:** Será preciso instalar [Microsoft Visual Studio 2017](https://visualstudio.microsoft.com/), Community Edition, o cualquier versión superior
- **Versión de Windows**: un equipo con una compilación del anillo anticipado de Windows Insider de Windows y la versión de Windows Insider de Windows SDK.  Este código de ejemplo se comprueba como en funcionamiento en la compilación de versión de Windows Insider 19025.vb_release_analog.191112-1600 con Windows SDK 19018.  Cualquier compilación o SDK por encima de las versiones especificadas debería ser compatible.
- **Herramientas de desarrollo de UWP**: carga de trabajo de la Plataforma universal de Windows en Visual Studio.  Consulte la página [Prepárate](/windows/uwp/get-started/get-set-up) de UWP para preparar el equipo para el desarrollo de aplicaciones para UWP.
- **Un micrófono activo y una salida de audio**.

## <a name="obtain-resources-from-microsoft"></a>Obtención de recursos de Microsoft

Algunos recursos necesarios para un agente de voz completamente personalizado en Windows requerirán recursos de Microsoft. El [ejemplo del asistente de voz de UWP](windows-voice-assistants-faq.yml#the-uwp-voice-assistant-sample) proporciona versiones de ejemplo de estos recursos para el desarrollo y las pruebas iniciales, por lo que esta sección no es necesaria para el desarrollo inicial.

- **Modelo de palabras clave:** la activación por voz requiere un modelo de palabras clave de Microsoft con el formato de archivo .bin. El archivo .bin que se proporciona en el ejemplo del asistente de voz de UWP se entrena con la palabra clave *Contoso*.
- **Token de característica de acceso limitado:** dado que las API de ConversationalAgent proporcionan acceso al audio del micrófono, están protegidas con restricciones de la característica de acceso limitado.  Para usar una característica de acceso limitado, tendrá que obtener un token de característica de acceso limitado conectado a la identidad del paquete de la aplicación de Microsoft.

## <a name="establish-a-dialog-service"></a>Establecimiento de un servicio de diálogo

Para obtener una experiencia completa del asistente de voz, la aplicación necesitará un servicio de diálogo que:

- Detecte una palabra clave en un archivo de audio determinado.
- Escuche la entrada del usuario y la convierta en texto.
- Proporcione el texto a un bot.
- Traslade la respuesta de texto del bot a una salida de audio.

Estos son los requisitos para crear un servicio de diálogo básico mediante Direct Line Speech.

- **Suscripción de Servicios de voz:** suscripción de Cognitive Speech Services para conversiones de voz a texto y de texto a voz. Pruebe los Servicios de voz gratis [aquí](./overview.md#try-the-speech-service-for-free).
- **Bot de Bot Framework:**  Un bot creado con Bot Framework, versión 4.2 o superior, que esté suscrito a [Direct Line Speech](./direct-line-speech.md) para habilitar la entrada y salida de voz. [Esta guía](./tutorial-voice-enable-your-bot-speech-sdk.md) contiene instrucciones paso a paso para crear un "bot de eco" y suscribirlo a Direct Line Speech. También puede acudir [aquí](https://blog.botframework.com/2018/05/07/build-a-microsoft-bot-framework-bot-with-the-bot-builder-sdk-v4/) para ver los pasos para crear un bot personalizado y, a continuación, seguir los mismos pasos que se describen [aquí](./tutorial-voice-enable-your-bot-speech-sdk.md) para suscribirse a Direct Line Speech, pero con el nuevo bot en lugar del "bot de eco".

## <a name="try-out-the-sample-app"></a>Prueba de la aplicación de ejemplo

Con la clave de suscripción de Speech Services y el id. de bot del bot de eco, está listo para probar el [ejemplo del asistente de voz de UWP](windows-voice-assistants-faq.yml#the-uwp-voice-assistant-sample). Siga las instrucciones del archivo Léame para ejecutar la aplicación y escribir sus credenciales.

## <a name="create-your-own-voice-assistant-for-windows"></a>Creación de un asistente de voz propio para Windows

Una vez que haya recibido el token de la característica de acceso limitado y el archivo bin de Microsoft, puede empezar a crear su propio asistente de voz en Windows.

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Lea la guía de implementación del asistente de voz](windows-voice-assistants-implementation-guide.md).