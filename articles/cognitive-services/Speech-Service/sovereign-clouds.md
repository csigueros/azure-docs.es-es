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
ms.date: 11/09/2021
ms.author: alexeyo
ms.openlocfilehash: ae288de8ae05efc22534cfaf87c42261a5e6d59a
ms.sourcegitcommit: 838413a8fc8cd53581973472b7832d87c58e3d5f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/10/2021
ms.locfileid: "132135789"
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

Para el [SDK de Voz](speech-sdk.md) en nubes soberanas, debe usar la creación de instancias "desde o con el host" de la `SpeechConfig` o la opción `--host` de la [CLI de Voz](spx-overview.md). (También puede usar la creación de instancias "desde y con el punto de conexión" y la opción de la CLI de Voz `--endpoint`).

Se debe crear una instancia de la clase `SpeechConfig` de la siguiente manera:

# <a name="c"></a>[C#](#tab/c-sharp)
```csharp
var config = SpeechConfig.FromHost(usGovHost, subscriptionKey);
```
# <a name="c"></a>[C++](#tab/cpp)
```cpp
auto config = SpeechConfig::FromHost(usGovHost, subscriptionKey);
```
# <a name="java"></a>[Java](#tab/java)
```java
SpeechConfig config = SpeechConfig.fromHost(usGovHost, subscriptionKey);
```
# <a name="python"></a>[Python](#tab/python)
```python
import azure.cognitiveservices.speech as speechsdk
speech_config = speechsdk.SpeechConfig(host=usGovHost, subscription=subscriptionKey)
```
# <a name="objective-c"></a>[Objective-C](#tab/objective-c)
```objectivec
SPXSpeechConfiguration *speechConfig = [[SPXSpeechConfiguration alloc] initWithHost:usGovHost subscription:subscriptionKey];
```
***

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

Para el [SDK de Voz](speech-sdk.md) en nubes soberanas, debe usar la creación de instancias "desde o con el host" de la `SpeechConfig` o la opción `--host` de la [CLI de Voz](spx-overview.md). (También puede usar la creación de instancias "desde y con el punto de conexión" y la opción de la CLI de Voz `--endpoint`).

Se debe crear una instancia de la clase `SpeechConfig` de la siguiente manera:

# <a name="c"></a>[C#](#tab/c-sharp)
```csharp
var config = SpeechConfig.FromHost(azCnHost, subscriptionKey);
```
# <a name="c"></a>[C++](#tab/cpp)
```cpp
auto config = SpeechConfig::FromHost(azCnHost, subscriptionKey);
```
# <a name="java"></a>[Java](#tab/java)
```java
SpeechConfig config = SpeechConfig.fromHost(azCnHost, subscriptionKey);
```
# <a name="python"></a>[Python](#tab/python)
```python
import azure.cognitiveservices.speech as speechsdk
speech_config = speechsdk.SpeechConfig(host=azCnHost, subscription=subscriptionKey)
```
# <a name="objective-c"></a>[Objective-C](#tab/objective-c)
```objectivec
SPXSpeechConfiguration *speechConfig = [[SPXSpeechConfiguration alloc] initWithHost:azCnHost subscription:subscriptionKey];
```
***

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