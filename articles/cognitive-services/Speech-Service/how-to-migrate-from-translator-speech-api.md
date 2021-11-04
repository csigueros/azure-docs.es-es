---
title: Migración de Translator Speech API al servicio de voz
titleSuffix: Azure Cognitive Services
description: Obtenga información sobre cómo migrar las aplicaciones de Translator Speech API al servicio de voz.
services: cognitive-services
author: eric-urban
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/21/2020
ms.author: eur
ms.openlocfilehash: 8c22c803d97610f1bcdd1833aa006cf16dc925fd
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2021
ms.locfileid: "131510723"
---
# <a name="migrate-from-the-translator-speech-api-to-the-speech-service"></a>Migración de Translator Speech API al servicio de voz

Use este artículo para migrar las aplicaciones de Microsoft Translator Speech API al [servicio de voz](index.yml). Esta guía describe las diferencias entre Translator Speech API y el servicio de voz, y sugiere estrategias para migrar las aplicaciones.

> [!NOTE]
> El servicio de voz no aceptará su clave de suscripción de Translator Speech API. Deberá crear una nueva suscripción al servicio de voz.

## <a name="comparison-of-features"></a>Comparación de características

| Característica                                           | Translator Speech API                                  | Servicio de voz | Detalles                                                                                                                                                                                                                                                                            |
|---------------------------------------------------|-----------------------------------------------------------------|------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Traducción a texto                               | :heavy_check_mark:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| Traducción a voz                             | :heavy_check_mark:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| Punto de conexión global                                   | :heavy_check_mark:                                              | :heavy_minus_sign:                 | El servicio de voz no ofrece un punto de conexión global. Un punto de conexión global puede dirigir automáticamente el tráfico al punto de conexión regional más cercano, reduciendo la latencia en la aplicación.                                                    |
| Puntos de conexión regionales                                | :heavy_minus_sign:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| Límite de tiempo de conexión                             | 90 minutos                                               | Ilimitado con SDK. 10 minutos con una conexión WebSockets                                                                                                                                                                                                                                                                                   |
| Clave de autenticación de encabezado                                | :heavy_check_mark:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| Varios idiomas que se traducen en una sola solicitud | :heavy_minus_sign:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| SDK disponibles                                    | :heavy_minus_sign:                                              | :heavy_check_mark:                 | Vea la [Documentación del servicio de voz](index.yml) para obtener información de los SDK disponibles.                                                                                                                                                    |
| Conexiones de WebSockets                            | :heavy_check_mark:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| API Languages                                     | :heavy_check_mark:                                              | :heavy_minus_sign:                 | El servicio de Voz admite el mismo conjunto de idiomas que se describe en el artículo de [referencia de idiomas de Translator](). |
| Marcador y filtro de obscenidad                       | :heavy_minus_sign:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| .WAV/PCM como entrada                                 | :heavy_check_mark:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| Otros tipos de archivo como entrada                         | :heavy_minus_sign:                                              | :heavy_minus_sign:                 |                                                                                                                                                                                                                                                                                    |
| Resultados parciales                                   | :heavy_check_mark:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| Información de control de tiempo                                       | :heavy_check_mark:                                              | :heavy_minus_sign:                 |                                                                                                                                                                 |
| Id. de correlación                                    | :heavy_check_mark:                                              | :heavy_minus_sign:                 |                                                                                                                                                                                                                                                                                    |
| Modelos de voz personalizados                              | :heavy_minus_sign:                                              | :heavy_check_mark:                 | El Servicio de voz ofrece modelos de voz personalizados que le permiten personalizar el reconocimiento de voz con el vocabulario único de su organización.                                                                                                                                           |
| Modelos de traducción personalizados                         | :heavy_minus_sign:                                              | :heavy_check_mark:                 | Si se suscribe a Microsoft Translator Text API, podrá utilizar [Traductor personalizado](https://www.microsoft.com/translator/business/customization/), lo que le permitirá emplear sus propios datos para conseguir traducciones más precisas.                                                 |

## <a name="migration-strategies"></a>Estrategias de migración

Si su organización o usted tienen aplicaciones en desarrollo o producción que usan la API Translator Speech, deben actualizarlas para que usen el servicio de voz. Vea la documentación del [servicio de voz](index.yml) para obtener información sobre SDK disponibles, ejemplos de código y tutoriales. Tenga en cuenta lo siguiente cuando esté migrando:

* El servicio de voz no ofrece un punto de conexión global. Determine si la aplicación funciona eficazmente con un único punto de conexión regional para todo su tráfico. Si no lo hace, use la ubicación geográfica para determinar el punto de conexión más eficaz.

* Si la aplicación usa conexiones de larga duración y no puede usar un SDK disponible, puede usar una conexión de WebSockets. Administre el límite de tiempo de espera de 10 minutos volviendo a conectarse en los momentos adecuados.

* Si la aplicación utiliza el servicio de Translator para y la API Translator Speech habilitar los modelos de traducción personalizada, puede agregar los identificadores "Categoría" directamente mediante el servicio de Voz.

* A diferencia de la API Translator Speech, el servicio de voz puede completar las traducciones en varios idiomas en una sola solicitud.

## <a name="next-steps"></a>Pasos siguientes

* [Prueba gratuita del servicio de voz](overview.md#try-the-speech-service-for-free)
* [Inicio rápido: Reconocimiento de voz en una aplicación de UWP mediante el SDK de Voz](./get-started-speech-to-text.md?pivots=programming-language-csharp&tabs=uwp)

## <a name="see-also"></a>Consulte también

* [¿Qué es el servicio Voz?](overview.md)
* [Documentación del servicio de voz y del SDK de voz](./speech-devices-sdk-quickstart.md?pivots=platform-android)
