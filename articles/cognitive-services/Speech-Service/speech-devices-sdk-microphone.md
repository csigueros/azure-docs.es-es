---
title: Recomendaciones de matriz de micrófonos del SDK de dispositivos de voz
titleSuffix: Azure Cognitive Services
description: Recomendaciones para la matriz de micrófonos del SDK de dispositivos de voz. Se recomiendan las geometrías de matriz para su uso con la pila de audio de Microsoft.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/16/2019
ms.author: pafarley
ms.openlocfilehash: e7fbe41da74f112e6081ef9abc8ce1601c9491d7
ms.sourcegitcommit: f2d0e1e91a6c345858d3c21b387b15e3b1fa8b4c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/07/2021
ms.locfileid: "123535210"
---
# <a name="speech-devices-sdk-microphone-array-recommendations"></a>Recomendaciones de matriz de micrófonos del SDK de dispositivos de voz

En este artículo, aprenderá a diseñar una matriz de micrófonos para el SDK de dispositivos de voz.

El SDK de dispositivos de voz funciona mejor con una matriz de micrófonos que se ha diseñado según las directrices siguientes, incluida la selección de geometría y componentes del micrófono. También se proporcionan instrucciones sobre los aspectos de integración y eléctricos que se deben tener en cuenta.

## <a name="microphone-geometry"></a>Geometría del micrófono

Se recomiendan las geometrías de matriz siguientes para su uso con la pila de audio de Microsoft. La ubicación de las fuentes de sonido y el rechazo del ruido ambiental se han mejorado con un número mayor de micrófonos con dependencias de aplicaciones y escenarios de usuario específicos, y el factor de forma del dispositivo.

| Micrófonos y geometría | Matriz circular | Matriz circular | Matriz lineal | Matriz lineal |
| --- | -------------- | --- | ------------ | --- |
|     | <img src="media/speech-devices-sdk/7-mic-c.png" alt="7 mic circular array" width="150"/> | <img src="media/speech-devices-sdk/4-mic-c.png" alt="4 mic circular array" width="150"/> | <img src="media/speech-devices-sdk/4-mic-l.png" alt="4 mic linear array" width="150"/> | <img src="media/speech-devices-sdk/2-mic-l.png" alt="2 mic linear array" width="150"/> |
| \# Micrófonos | 7 | 4 | 4 | 2 |
| Geometría | 6 externo, 1 centro, Radio = 42,5 mm, Espaciado uniforme | 3 exterior, 1 centro, Radio = 42,5 mm, Espaciado uniforme | Longitud = 120 mm, Espaciado = 40 mm | Espaciado = 40 mm |

Los canales de micrófono deben ordenarse según la numeración representada para cada matriz y subir desde 0. La pila de audio de Microsoft requerirá un flujo de referencia adicional de reproducción de audio para realizar la cancelación del eco.

## <a name="component-selection"></a>Selección de componentes

Los componentes de micrófono deben seleccionarse para reproducir con precisión una señal libre de ruido y distorsión.

Las propiedades recomendadas al seleccionar micrófonos son:

| Parámetro | Recomendado |
| --------- | ----------- |
| SNR | \>= 65 dB (señal de 1 kHz a 94 dBSPL, ruido ponderado con A) |
| Coincidencia de amplitud | ± 1 dB a 1 kHz |
| Coincidencia de fase | ± 2° a 1 kHz |
| Punto de sobrecarga acústico (AOP) | \>= 120 dBSPL (THD = 10 %) |
| Velocidad de bits | 24 bits como mínimo |
| Frecuencia de muestreo | Mínimo 16 kHz\* |
| Respuesta de frecuencia | \+ 3 dB, máscara flotante 8000-200 Hz\* |
| Confiabilidad | Intervalo de temperatura de almacenamiento: entre -40° C y 70° C<br />Intervalo de temperatura de funcionamiento: entre -20° C y 55° C |

\*_Pueden ser necesarias velocidades de muestreo más altas o "más amplias" en aplicaciones de comunicaciones de alta calidad (VoIP)_

Una buena selección de componentes debe ir acompañada de una buena integración electroacústica para evitar reducir el rendimiento de los componentes usados. Los casos de uso único también pueden necesitar requisitos adicionales (por ejemplo, intervalos de temperatura de funcionamiento).

## <a name="microphone-array-integration"></a>Integración de la matriz de micrófonos

El rendimiento de la matriz de micrófono cuando se integra en un dispositivo diferirá de la especificación del componente. Es importante asegurarse de que los micrófonos están bien adaptados después de la integración. Por tanto, el rendimiento del dispositivo medido después de cualquier ganancia fija o EQ debe cumplir las siguientes recomendaciones:

| Parámetro          | Recomendado                                        |
| ------------------ | -------------------------------------------------- |
| SNR                | \> 63 dB (señal de 1 kHz a 94 dBSPL, ruido ponderado con A) |
| Sensibilidad de salida | -26 dBFS/Pa a 1 kHz (recomendado)                  |
| Coincidencia de amplitud | ± 2 dB, 200-8000 Hz                                |
| THD %\*             | ≤ 1 %, 200-8000 Hz, 94 dBSPL, quinto orden             |
| Respuesta de frecuencia | ± 6 dB, máscara flotante a 200-8000 Hz\*\*              |

\*\*_Se requiere un altavoz de baja distorsión para medir THD (por ejemplo, Neumann KH120)_ .

\*\*_Pueden ser necesarios intervalos de frecuencia "más amplios" para las aplicaciones de comunicaciones de alta calidad (VoIP)_ .

## <a name="speaker-integration-recommendations"></a>Recomendaciones de integración del altavoz

Como la cancelación del eco es necesaria para los dispositivos de reconocimiento de voz que contienen altavoces, se proporcionan recomendaciones adicionales para la integración y la selección de altavoces.

| Parámetro | Recomendado |
| --------- | ----------- |
| Consideraciones sobre la linealidad | Sin procesamiento no lineal después de la referencia del altavoz, de lo contrario se requiere una secuencia de referencia de bucle invertido basada en hardware |
| Bucle invertido de altavoz | Se proporciona mediante WASAPI, API privadas, complemento ALSA personalizado (Linux), o se proporciona mediante el canal de firmware |
| THD % | Bandas de tercios de octava, mínimo quinto orden, reproducción 70 dBA a 0,8 m ≤ 6,3 %, 315-500 Hz ≤ 5 %, 630-5000 Hz |
| Acoplamiento de eco a los micrófonos | \> TCLw -10 dB mediante el método ITU-T G.122 anexo B.4, normalizado a nivel de micrófono<br />TCLw = TCLwmeasured \+ (nivel medido - sensibilidad de salida objetivo)<br />TCLw = TCLwmeasured \+ (nivel medido - (-26)) |

## <a name="integration-design-architecture"></a>Arquitectura de diseño de integración

Las siguientes directrices sobre arquitectura son necesarias al integrar micrófonos en un dispositivo:

| Parámetro | Recomendación |
| --------- | -------------- |
| Similitud del puerto de micrófono | Todos los puertos de micrófono tienen la misma longitud en la matriz |
| Dimensiones del puerto de micrófono | Tamaño de puerto Ø0,8-1,0 mm. Longitud de puerto/diámetro de puerto \< 2 |
| Sellado de micrófono         | Juntas de sellado implementadas uniformemente en la pila. Se recomienda una relación de compresión del \> 70 % para juntas de espuma. |
| Confiabilidad del micrófono     | Se debe usar una malla para evitar el polvo y el acceso (entre el PCB de los micrófonos de puerto inferior y la junta de sellado/cubierta superior). |
| Aislamiento del micrófono       | Juntas de goma y desacoplamiento de la vibración a través de la estructura, en especial para aislar las vías de vibración debido a los altavoces integrados. |
| Reloj de muestreo      | El audio del dispositivo debe estar libre de vibración y cortes con desviación baja. |
| Funcionalidad de registro   | El dispositivo debe poder grabar secuencias sin procesar de canales individuales de manera simultánea. |
| USB                 | Todos los dispositivos de entrada de audio USB deben establecer descriptores de acuerdo con la [especificación Rev3 de dispositivos de audio USB](https://www.usb.org/document-library/usb-audio-devices-rev-30-and-adopters-agreement). |
| Geometría del micrófono | Los controladores deben implementar correctamente [descriptores de geometría de matriz de micrófonos](/windows-hardware/drivers/audio/ksproperty-audio-mic-array-geometry). |
| Detectabilidad     | Los dispositivos no deben tener algoritmos de procesamiento de audio no lineales basados en hardware, firmware o software de terceros indetectables o incontrolables, ni hacia ni desde el dispositivo. |
| Formato de captura      | Los formatos de captura deben usar una frecuencia de muestreo mínima de 16 kHz y una profundidad recomendada de 24 bits. |

## <a name="electrical-architecture-considerations"></a>Consideraciones sobre la arquitectura eléctrica

Si procede, las matrices pueden estar conectadas a un host USB (por ejemplo, un SoC que ejecuta la pila de audio de Microsoft) y las interfaces a servicios de voz u otras aplicaciones.

Los componentes de hardware, como la conversión de PDM a TDM, deben garantizar que se conserva el rango dinámico y el SNR de los micrófonos dentro de los remuestradores.

Se debe admitir audio USB 2.0 de alta velocidad dentro de cualquier MCU de audio con el fin de proporcionar el ancho de banda necesario para hasta siete canales a frecuencias de muestreo y profundidades de bits mayores.

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Conozca el SDK de dispositivos de voz](speech-devices-sdk.md).