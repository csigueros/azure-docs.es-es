---
title: 'Nubes soberanas: Servicio de voz'
titleSuffix: Azure Cognitive Services
description: Aprenda a usar las nubes soberanas.
services: cognitive-services
author: alexeyo26
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.custom: references_regions
ms.date: 08/24/2021
ms.author: alexeyo
ms.openlocfilehash: c7c140b1ff2d3d2151ad8ce01e3ad8f64660543d
ms.sourcegitcommit: 28cd7097390c43a73b8e45a8b4f0f540f9123a6a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/24/2021
ms.locfileid: "122777896"
---
# <a name="speech-services-in-sovereign-clouds"></a>Servicios de Voz en nubes soberanas

## <a name="azure-government-united-states"></a>Azure Government (Estados Unidos)

Solo está disponible para entidades gubernamentales de EE. UU. y sus asociados. Consulte más información sobre Azure Government [aquí](../../azure-government/documentation-government-welcome.md) y [aquí](../../azure-government/compare-azure-government-global-azure.md).

- **Azure Portal:**
  - [https://portal.azure.us/](https://portal.azure.us/)
- **Regiones:**
  - US Gov: Arizona
  - US Gov - Virginia
- **Planes de tarifa disponibles:**
  - Gratis y Estándar. Consulte más detalles [aquí](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/).
- **Características admitidas:**
  - Voz a texto
    - Habla personalizada (adaptación de Modelo acústico y Modelo de lenguaje)
      - [Speech Studio](https://speech.azure.us/)
  - Texto a voz
    - Voz estándar
    - Voz neuronal
  - Traductor de voz
- **Características no admitidas:**
  - Voz personalizada
- **Idiomas admitidos:**
  - Consulte la lista de idiomas admitidos [aquí](language-support.md).

### <a name="endpoint-information"></a>Información de punto de conexión

Esta sección contiene información sobre los puntos de conexión del servicio Voz para el uso con [SDK de Voz](speech-sdk.md), [Speech-to-text REST API](rest-speech-to-text.md) y [Text-to-speech REST API](rest-text-to-speech.md).

#### <a name="speech-services-rest-api"></a>API REST de los servicios de Voz

Los puntos de conexión de la API REST de los servicios de Voz en Azure Government tienen el siguiente formato:

|  Tipo u operación de la API REST | Formato de puntos de conexión |
|--|--|
| Access token | `https://<REGION_IDENTIFIER>.api.cognitive.microsoft.us/sts/v1.0/issueToken`
| [API de REST de conversión de voz en texto v3.0](rest-speech-to-text.md#speech-to-text-rest-api-v30) | `https://<REGION_IDENTIFIER>.api.cognitive.microsoft.us/<URL_PATH>` |
| [API de REST de conversión de voz en texto para audios breves](rest-speech-to-text.md#speech-to-text-rest-api-for-short-audio) | `https://<REGION_IDENTIFIER>.stt.speech.azure.us/<URL_PATH>` |
| [Text-to-speech REST API](rest-text-to-speech.md) | `https://<REGION_IDENTIFIER>.tts.speech.azure.us/<URL_PATH>` |

Reemplace `<REGION_IDENTIFIER>` por el identificador que coincida con la región de la suscripción en la siguiente tabla:

|                     | Identificador de región |
|--|--|
| **US Gov: Arizona**  | `usgovarizona` |
| **US Gov - Virginia** | `usgovvirginia` |

#### <a name="speech-sdk"></a>SDK de voz

Para el SDK de Voz en nubes independientes, debe usar la creación de instancias "desde el host" de la clase `SpeechConfig` o la opción `--host` de la [CLI de Voz](spx-overview.md). (También puede usar la creación de instancias "desde el punto de conexión" y la opción `--endpoint` de la CLI de Voz).

Se debe crear una instancia de la clase `SpeechConfig` de la siguiente manera:
```csharp
var config = SpeechConfig.FromHost(usGovHost, subscriptionKey);
```
Se debe usar la CLI de Voz de la siguiente manera (tenga en cuenta la opción `--host`):
```dos
spx recognize --host "usGovHost" --file myaudio.wav
```
Reemplace `subscriptionKey` por su clave de recurso de Voz. Reemplace `usGovHost` por la expresión que coincida con la oferta de servicio y la región de la suscripción necesarias de esta tabla:

|  Región y oferta de servicio | Expresión de host |
|--|--|
| **US Gov: Arizona** | |
| Voz a texto | `wss://usgovarizona.stt.speech.azure.us` |
| Texto a voz | `https://usgovarizona.tts.speech.azure.us` |
| **US Gov - Virginia** | |
| Voz a texto | `wss://usgovvirginia.stt.speech.azure.us` |
| Texto a voz | `https://usgovvirginia.tts.speech.azure.us` |


## <a name="azure-china"></a>Azure China

Disponible para organizaciones con presencia empresarial en China. Para más información sobre Azure China, consulte [aquí](/azure/china/overview-operations). 


- **Azure Portal:**
  - [https://portal.azure.cn/](https://portal.azure.cn/)
- **Regiones:**
  - Este de China 2
  - Norte de China 2
- **Planes de tarifa disponibles:**
  - Gratis y Estándar. Consulte más detalles [aquí](https://www.azure.cn/pricing/details/cognitive-services/index.html).
- **Características admitidas:**
  - Voz a texto
    - Habla personalizada (adaptación de Modelo acústico y Modelo de lenguaje)
      - [Speech Studio](https://speech.azure.cn/)
  - Texto a voz
    - Voz estándar
    - Voz neuronal
  - Traductor de voz
- **Características no admitidas:**
  - Voz personalizada
- **Idiomas admitidos:**
  - Consulte la lista de idiomas admitidos [aquí](language-support.md).

### <a name="endpoint-information"></a>Información de punto de conexión

Esta sección contiene información sobre los puntos de conexión del servicio Voz para el uso con [SDK de Voz](speech-sdk.md), [Speech-to-text REST API](rest-speech-to-text.md) y [Text-to-speech REST API](rest-text-to-speech.md).

#### <a name="speech-services-rest-api"></a>API REST de los servicios de Voz

Los puntos de conexión de la API REST de los servicios de Voz en Azure China tienen el siguiente formato:

|  Tipo u operación de la API REST | Formato de puntos de conexión |
|--|--|
| Access token | `https://<REGION_IDENTIFIER>.api.cognitive.azure.cn/sts/v1.0/issueToken`
| [API de REST de conversión de voz en texto v3.0](rest-speech-to-text.md#speech-to-text-rest-api-v30) | `https://<REGION_IDENTIFIER>.api.cognitive.azure.cn/<URL_PATH>` |
| [API de REST de conversión de voz en texto para audios breves](rest-speech-to-text.md#speech-to-text-rest-api-for-short-audio) | `https://<REGION_IDENTIFIER>.stt.speech.azure.cn/<URL_PATH>` |
| [Text-to-speech REST API](rest-text-to-speech.md) | `https://<REGION_IDENTIFIER>.tts.speech.azure.cn/<URL_PATH>` |

Reemplace `<REGION_IDENTIFIER>` por el identificador que coincida con la región de la suscripción en la siguiente tabla:

|                     | Identificador de región |
|--|--|
| **Este de China 2**  | `chinaeast2` |
| **Norte de China 2**  | `chinanorth2` |

#### <a name="speech-sdk"></a>SDK de voz

Para el SDK de Voz en nubes independientes, debe usar la creación de instancias "desde el host" de la clase `SpeechConfig` o la opción `--host` de la [CLI de Voz](spx-overview.md). (También puede usar la creación de instancias "desde el punto de conexión" y la opción `--endpoint` de la CLI de Voz).

Se debe crear una instancia de la clase `SpeechConfig` de la siguiente manera:
```csharp
var config = SpeechConfig.FromHost(azCnHost, subscriptionKey);
```
Se debe usar la CLI de Voz de la siguiente manera (tenga en cuenta la opción `--host`):
```dos
spx recognize --host "azCnHost" --file myaudio.wav
```
Reemplace `subscriptionKey` por su clave de recurso de Voz. Reemplace `azCnHost` por la expresión que coincida con la oferta de servicio y la región de la suscripción necesarias de esta tabla:

|  Región y oferta de servicio | Expresión de host |
|--|--|
| **Este de China 2** | |
| Voz a texto | `wss://chinaeast2.stt.speech.azure.cn` |
| Texto a voz | `https://chinaeast2.tts.speech.azure.cn` |
| **Norte de China 2** | |
| Voz a texto | `wss://chinanorth2.stt.speech.azure.cn` |
| Texto a voz | `https://chinanorth2.tts.speech.azure.cn` |