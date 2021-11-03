---
title: 'Configuración de una matriz de micrófonos: servicio Voz'
titleSuffix: Azure Cognitive Services
description: Aprenda a configurar una matriz de micrófono para que el SDK de dispositivos de voz pueda usarla.
services: cognitive-services
author: mswellsi
manager: yanbo
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/01/2020
ms.author: wellsi
ms.custom: ignite-fall-2021
ms.openlocfilehash: 6fe1739006f3f5b9943076c917cc2e3ef5dc2efa
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131024569"
---
# <a name="how-to-configure-a-microphone-array"></a>Configuración de una matriz de micrófonos

En este artículo aprenderá a configurar una [matriz de micrófonos](./speech-sdk-microphone.md). Incluye la configuración del ángulo de trabajo y cómo seleccionar qué micrófono se usa para el SDK de dispositivos de voz.

El SDK de dispositivos de voz funciona mejor con una matriz de micrófonos diseñada según [nuestras pautas](./speech-sdk-microphone.md). La configuración de la matriz de micrófonos la puede proporcionar el sistema operativo o uno de los métodos siguientes.

El SDK de dispositivos de voz inicialmente admitía matrices de micrófonos realizando la selección a partir de un conjunto fijo de configuraciones.

```java
private static String DeviceGeometry = "Circular6+1"; // "Circular6+1", "Linear4",
private static String SelectedGeometry = "Circular6+1"; // "Circular6+1", "Circular3+1", "Linear4", "Linear2"
```

En Windows, el controlador de audio proporciona la configuración de la matriz de micrófonos.

Desde la versión 1.11.0, el SDK de dispositivos de voz también admite la configuración a partir de un [archivo JSON](https://aka.ms/sdsdk-micarray-json).


## <a name="windows"></a>Windows
En Windows, la información de la geometría de matrices de micrófonos se obtiene automáticamente del controlador de audio. Por lo tanto, las propiedades `DeviceGeometry`, `SelectedGeometry` y `MicArrayGeometryConfigFile` son opcionales. Usamos el [archivo JSON](https://aka.ms/sdsdk-micarray-json) proporcionado usando `MicArrayGeometryConfigFile` para obtener solamente el intervalo de formación de haces.

Si se especifica una matriz de micrófonos mediante `AudioConfig::FromMicrophoneInput`, usamos el micrófono especificado. Si no se especifica un micrófono o se llama a `AudioConfig::FromDefaultMicrophoneInput`, usamos el micrófono predeterminado, que se especifica en la configuración de sonido en Windows.
La pila de audio de Microsoft en el SDK de dispositivos de voz solo admite reducción de muestreo para frecuencias de muestreo que sean múltiplos enteros de 16 KHz.

## <a name="linux"></a>Linux
En Linux, se debe proporcionar la información de la geometría de los micrófonos. Se sigue admitiendo el uso de `DeviceGeometry` y `SelectedGeometry`. También se puede proporcionar a través del archivo JSON mediante la propiedad `MicArrayGeometryConfigFile`. De forma similar a Windows, el intervalo de formación de haces se puede proporcionar mediante el archivo JSON.

Si se especifica una matriz de micrófonos mediante `AudioConfig::FromMicrophoneInput`, usamos el micrófono especificado. Si no se especifica un micrófono o se llama a `AudioConfig::FromDefaultMicrophoneInput`, grabamos desde el dispositivo ALSA denominado *default*. De forma predeterminada, *default* apunta siempre al dispositivo 0 de la tarjeta 0, pero los usuarios pueden cambiarlo en el archivo `asound.conf`. 

La pila de audio de Microsoft en el SDK de dispositivos de voz solo admite reducción de muestreo para frecuencias de muestreo que sean múltiplos enteros de 16 KHz. Además, se admiten los siguientes formatos: flotante little endian IEEE de 32 bits, entero con signo little endian de 32 bits, entero con signo little endian de 24 bits, entero con signo little endian de 16 bits y entero con signo de 8 bits.

## <a name="android"></a>Android
Actualmente, el SDK de dispositivos de voz solo admite [Roobo v1](./speech-devices-sdk-quickstart.md?pivots=platform-android%253fpivots%253dplatform-android). El comportamiento es el mismo que el de las versiones anteriores, excepto que, ahora, la propiedad `MicArrayGeometryConfigFile` se puede usar para especificar el archivo JSON que contiene el intervalo de formación de haces.

## <a name="microphone-array-configuration-json"></a>JSON para la configuración de matrices de micrófonos

El archivo JSON para la configuración de la geometría de matrices de micrófonos seguirá el [esquema JSON](https://aka.ms/sdsdk-micarray-json). A continuación se muestran algunos ejemplos que siguen el esquema.


```json
{
    "micArrayType": "Linear",
    "geometry": "Linear4"
}
```


Or


```json
{
    "micArrayType": "Planar",
    "horizontalAngleBegin": 0,
    "horizontalAngleEnd": 360,
    "numberOfMicrophones": 4,
    "micCoord": [
        {
            "xCoord": 0,
            "yCoord": 0,
            "zCoord": 0
        },
        {
            "xCoord": 40,
            "yCoord": 0,
            "zCoord": 0
        },
        {
            "xCoord": -20,
            "yCoord": -35,
            "zCoord": 0
        },
        {
            "xCoord": -20,
            "yCoord": 35,
            "zCoord": 0
        }
    ]
}
```

Or

```json
{
    "micArrayType": "Linear",
    "horizontalAngleBegin": 70,
    "horizontalAngleEnd": 110
}
```


## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Elija el dispositivo de voz](get-speech-devices-sdk.md)
