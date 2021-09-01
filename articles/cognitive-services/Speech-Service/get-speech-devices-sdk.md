---
title: Obtener el SDK de dispositivos de voz
titleSuffix: Azure Cognitive Services
description: El servicio de voz funciona con una amplia variedad de dispositivos y orígenes de audio. Ahora puede llevar las aplicaciones de voz al siguiente nivel con hardware y software coincidente. En este artículo aprenderá a acceder al SDK de dispositivos de voz para comenzar a desarrollar.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/14/2019
ms.author: lajanuar
ms.openlocfilehash: 6d10a08d1b85cf74a7f883ad524f077db533d1f0
ms.sourcegitcommit: 05dd6452632e00645ec0716a5943c7ac6c9bec7c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/17/2021
ms.locfileid: "122253053"
---
# <a name="get-the-cognitive-services-speech-devices-sdk"></a>Obtener el SDK de dispositivos de voz de Cognitive Services

El SDK de dispositivos de voz es una biblioteca preajustada diseñada para funcionar con kits de desarrollo específicos integrados y diversas configuraciones de matrices de micrófonos.

## <a name="choose-a-development-kit"></a>Selección de un kit de desarrollo

|Dispositivos|Especificación|Descripción|Escenarios|
|--|--|--|--|
|[Azure Percept Audio DK](/azure/azure-percept/overview-azure-percept-audio)<br>[Configuración](/azure/azure-percept/quickstart-percept-dk-unboxing) / [Inicio rápido](/azure/azure-percept/quickstart-percept-audio-setup)![Azure Percept Audio Dk](./media/speech-devices-sdk/azure-percept-audio.png)|Matriz lineal de 4 micrófonos con códec XMOS. <br> Linux| Un dispositivo accesorio que agrega funcionalidades de inteligencia artificial (IA) de voz al dispositivo perimetral. Contiene un procesador de audio configurado previamente y una matriz lineal de cuatro micrófonos, lo que permite usar comandos de voz, detección de palabras clave y reconocimiento de voz a gran distancia mediante Azure Cognitive Services. Este dispositivo se suministra con Azure Percept DK, Azure Percept Studio y otros servicios de administración perimetral de Azure para integrarse fácilmente con nuestro SDK de dispositivos de voz todo en uno más eficaz y compacto.|Transcripción de conversaciones, robótica, edificios inteligentes, fabricación, agricultura|
|[Azure Kinect DK](https://azure.microsoft.com/services/kinect-dk/)<br>[Instalación](../../kinect-dk/set-up-azure-kinect-dk.md) / [Inicio rápido](./speech-devices-sdk-quickstart.md?pivots=platform-windows%253fpivots%253dplatform-windows)![Azure Kinect DK](media/speech-devices-sdk/device-azure-kinect-dk.jpg)|Matriz de 7 micrófonos, cámaras RGB y de profundidad <br>[Windows](./speech-devices-sdk-quickstart.md?pivots=platform-windows%253fpivots%253dplatform-windows)/[Linux](./speech-devices-sdk-quickstart.md?pivots=platform-linux%253fpivots%253dplatform-linux)|Un kit de desarrollo de procesos espaciales con sensores avanzados de inteligencia artificial (AI) para la creación de modernos modelos de voz y visión artificial. Combina una de las mejores matrices de micrófonos espaciales y cámaras de profundidad con una cámara de vídeo y un sensor de orientación, todo ello en un pequeño dispositivo con varios modos, opciones y SDK que se integran perfectamente con Azure Cognitive Services.|Transcripción de conversaciones, robótica, edificios inteligentes|
|[Kit de desarrollo de Urbetter](http://www.urbetter.com/products_56/278.html)![DDK de URbetter](media/speech-devices-sdk/device-urbetter.jpg)|Matriz de 7 micrófonos, ARM SOC, Wi-Fi, Ethernet, HDMI, cámara USB. <br>Linux|Un SDK de dispositivos de voz de nivel industrial que se adapta a la matriz de micrófonos de Microsoft y es compatible con E/S extendida como HDMI/Ethernet y más periféricos USB <br> [Póngase en contacto con Urbetter](http://www.urbetter.com/products_56/278.html)|Transcripción de conversaciones, educación, hospitales, robots, caja OTT, agente por voz, servicio de comidas rápidas|
|[Roobo Smart Audio Dev Kit](http://ddk.roobo.com)<br>[Configuración](speech-devices-sdk-roobo-v1.md) / [Inicio rápido](./speech-devices-sdk-quickstart.md?pivots=platform-android%253fpivots%253dplatform-android)![Roobo Smart Audio Dev Kit](media/speech-devices-sdk/device-roobo-v1.jpg)|Matriz de 7 micrófonos, ARM SOC, Wi-Fi, salida de audio, E/S. <br>[Android](./speech-devices-sdk-quickstart.md?pivots=platform-android%253fpivots%253dplatform-android)|El primer SDK de dispositivos de voz en adaptar la matriz de micrófonos de Microsoft y el SDK de procesamiento frontal para el desarrollo de escenarios de transcripción y voz de alta calidad|Transcripción de conversaciones, altavoz inteligente, agente por voz, ponible|
|Roobo Smart Audio Dev Kit 2<br>[Configuración](speech-devices-sdk-roobo-v2.md)<br>![Roobo Smart Audio Dev Kit 2](media/speech-devices-sdk/device-roobo-v2.jpg)|Matriz de 7 micrófonos, ARM SOC, Bluetooth, E/S. <br>Linux|El SDK de dispositivos de voz de segunda generación que proporciona un sistema operativo alternativo y más características en un diseño de referencia rentable.|Transcripción de conversaciones, altavoz inteligente, agente por voz, ponible|


## <a name="download-the-speech-devices-sdk"></a>Descargar el SDK de dispositivos de voz

Descargue el [SDK de dispositivos de voz](./speech-devices-sdk.md).

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Introducción al SDK de dispositivos de voz](./speech-devices-sdk-quickstart.md?pivots=platform-android)
