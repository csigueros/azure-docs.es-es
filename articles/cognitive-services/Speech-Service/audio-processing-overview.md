---
title: 'Procesamiento de audio: servicio de voz'
titleSuffix: Azure Cognitive Services
description: Información general sobre el procesamiento de audio y las funcionalidades de Microsoft Audio Stack.
services: cognitive-services
author: hasyashah
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/17/2021
ms.author: hasshah
ms.custom: devx-track-csharp, ignite-fall-2021
ms.openlocfilehash: f0bc80a9c248b9171a89bead1971cb7d5f4ce0fe
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131092854"
---
# <a name="audio-processing"></a>Procesamiento de audio

El procesamiento de audio hace referencia a las mejoras aplicadas a una secuencia de audio con el objetivo de mejorar la calidad del audio. Un conjunto de mejoras combinadas se suele denominar pila de procesamiento de audio. El objetivo de mejorar la calidad del audio se puede segmentar aún más en diferentes escenarios, como el procesamiento de voz y las telecomunicaciones. Entre los ejemplos de mejoras comunes se incluyen el control automático de ganancia (AGC), la supresión del ruido y la cancelación del eco acústico (AEC).

Los distintos escenarios o casos de uso requieren diferentes optimizaciones que influyen en el comportamiento de la pila de procesamiento de audio. Por ejemplo, en escenarios de telecomunicaciones, como las llamadas telefónicas, es aceptable que se produzcan pequeñas distorsiones en la señal de audio después de aplicar el procesamiento. Esto se debe a que los seres humanos pueden seguir comprendiendo la voz con alta precisión. Sin embargo, es inaceptable y molesto que una persona escuche el eco de su propia voz. Esto contrasta con los escenarios de procesamiento de voz, donde el audio distorsionado puede afectar negativamente a la precisión de un modelo de reconocimiento de voz con aprendizaje automático, pero es aceptable tener niveles leves de eco residual.

## <a name="microsoft-audio-stack"></a>Microsoft Audio Stack

Microsoft Audio Stack es un conjunto de mejoras optimizadas para escenarios de procesamiento de voz. Incluye ejemplos como el reconocimiento de palabras clave y el reconocimiento de voz. Consta de varias mejoras o componentes que funcionan en la señal de audio de entrada:

* **Supresión del ruido**: reduzca el nivel de ruido de fondo.
* **Con forma de haz**: localice el origen del sonido y optimice la señal de audio mediante varios micrófonos.
* **Anulación de la reverberación**: reduzca las reflexiones del sonido de las superficies del entorno.
* **Cancelación del eco acústico**: suprima el audio que se reproduce fuera del dispositivo mientras la entrada del micrófono está activa.
* **Control automático de ganancia**: ajuste dinámicamente el nivel de voz de las personas para tener en cuenta las personas que hablan suave, las largas distancias o los micrófonos no calibrados.

[ ![Diagrama de bloques de las mejoras de Microsoft Audio Stack.](media/audio-processing/mas-block-diagram.png) ](media/audio-processing/mas-block-diagram.png#lightbox)

Microsoft Audio Stack potencia una amplia gama de productos de Microsoft:
* **Windows**: Microsoft Audio Stack es la canalización de procesamiento de voz predeterminada cuando se usa la categoría Audio de voz. 
* **Dispositivos de Microsoft Teams Displays y de Sala de Microsoft Teams**: los dispositivos de Microsoft Teams Displays y de Sala de Microsoft Teams usan Microsoft Audio Stack para permitir experiencias basadas en voz y manos libres de alta calidad con Cortana.

### <a name="pricing"></a>Precios

El uso de Microsoft Audio Stack con el SDK de Voz no tiene ningún costo.

### <a name="minimum-requirements-to-use-microsoft-audio-stack"></a>Requisitos mínimos para usar Microsoft Audio Stack

Microsoft Audio Stack se puede usar en cualquier producto o aplicación que pueda cumplir los siguientes requisitos:
* **Audio sin formato**: Microsoft Audio Stack requiere audio sin formato (es decir, sin procesar) como entrada para producir los mejores resultados. Proporcionar audio que ya se ha procesado limita la capacidad de la pila de audio para realizar mejoras de alta calidad.
* **Geometrías de micrófono**: se requiere información de geometría sobre cada micrófono del dispositivo para realizar correctamente todas las mejoras que ofrece Microsoft Audio Stack. La información incluye el número de micrófonos, su organización física y coordenadas. Se admiten hasta 16 canales de micrófono de entrada. 
* **Bucle invertido o audio de referencia**: se requiere un canal de audio que represente el audio que se reproduce fuera del dispositivo para realizar la cancelación del eco acústico. 
* **Formato de entrada**: Microsoft Audio Stack admite la reducción de muestreo para frecuencias de muestreo que sean múltiplos enteros de 16 kHz. Se requiere una velocidad de muestreo mínima de 16 kHz. Además, se admiten los siguientes formatos: flotante little endian IEEE de 32 bits, entero con signo little endian de 32 bits, entero con signo little endian de 24 bits, entero con signo little endian de 16 bits y entero con signo de 8 bits.

## <a name="next-steps"></a>Pasos siguientes

* [Obtenga más información sobre la integración del SDK de Voz de Microsoft Audio Stack.](audio-processing-speech-sdk.md)
