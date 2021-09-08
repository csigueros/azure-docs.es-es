---
title: Authentication
titleSuffix: Azure Cognitive Services
description: 'Hay tres maneras de autenticar una solicitud a un recurso de Azure Cognitive Services: una clave de suscripción, un token de portador o una suscripción multiservicio. En este artículo aprenderá sobre cada método y cómo realizar una solicitud.'
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 07/22/2021
ms.author: erhopf
ms.openlocfilehash: e81b2121e8b4f71040014d3e211177dc8656b556
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/22/2021
ms.locfileid: "114447624"
---
# <a name="authenticate-requests-to-azure-cognitive-services"></a>Autenticación de solicitudes en Azure Cognitive Services

Cada solicitud a una instancia de Azure Cognitive Services debe incluir un encabezado de autenticación. Este encabezado pasa una clave de suscripción o token de acceso, que se utiliza para validar su suscripción a un servicio o grupo de servicios. En este artículo, aprenderá sobre tres maneras de autenticar una solicitud y los requisitos para cada una.

* Autenticación con una clave de suscripción a un [servicio único](#authenticate-with-a-single-service-subscription-key) o a [varios servicios](#authenticate-with-a-multi-service-subscription-key)
* Autenticación con un [token](#authenticate-with-an-authentication-token)
* Autenticación con [Azure Active Directory (AAD)](#authenticate-with-azure-active-directory)

## <a name="prerequisites"></a>Requisitos previos

Antes de hacer una solicitud, necesita una cuenta de Azure y una suscripción a Azure Cognitive Services. Si ya tiene una cuenta, siga adelante y vaya a la sección siguiente. Si no tiene una cuenta, tenemos una guía para que la configure en unos minutos: [Creación de una cuenta de Cognitive Services para Azure](cognitive-services-apis-create-account.md).

Puede obtener la clave de suscripción en [Azure Portal](cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) después de [crear la cuenta](https://azure.microsoft.com/free/cognitive-services/).

## <a name="authentication-headers"></a>Encabezados de autenticación

Revisemos rápidamente los encabezados de autenticación disponibles para su uso con Azure Cognitive Services.

| Encabezado | Descripción |
|--------|-------------|
| Ocp-Apim-Subscription-Key | Utilice este encabezado para autenticarse con una clave de suscripción para un servicio específico o una clave de suscripción a varios servicios. |
| Ocp-Apim-Subscription-Region | Este encabezado solo es necesario cuando se utiliza una clave de suscripción multiservicio con el [servicio de Traductor](./Translator/reference/v3-0-reference.md). Utilice este encabezado para especificar la región de la suscripción. |
| Authorization | Utilice este encabezado si está usando un token de autenticación. En las secciones siguientes se describen los pasos necesarios para realizar un intercambio de tokens. El valor proporcionado sigue este formato: `Bearer <TOKEN>`. |

## <a name="authenticate-with-a-single-service-subscription-key"></a>Autenticación con una clave de suscripción a un servicio único

La primera opción es autenticar una solicitud con una clave de suscripción para un servicio específico, como Traductor. Las claves están disponibles en Azure Portal para cada recurso que se ha creado. Para utilizar una clave de suscripción para autenticar una solicitud, esta se debe pasar como encabezado `Ocp-Apim-Subscription-Key`.

En estas solicitudes de ejemplo se muestra cómo utilizar el encabezado `Ocp-Apim-Subscription-Key`. Tenga en cuenta que, cuando utilice este ejemplo, deberá incluir una clave de suscripción válida.

Esta es una llamada de ejemplo a Bing Web Search API:
```cURL
curl -X GET 'https://api.cognitive.microsoft.com/bing/v7.0/search?q=Welsch%20Pembroke%20Corgis' \
-H 'Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY' | json_pp
```

Esta es una llamada de ejemplo al servicio Traductor:
```cURL
curl -X POST 'https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=de' \
-H 'Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY' \
-H 'Content-Type: application/json' \
--data-raw '[{ "text": "How much for the cup of coffee?" }]' | json_pp
```

En el siguiente vídeo se explica cómo se usa una clave de Cognitive Services.

## <a name="authenticate-with-a-multi-service-subscription-key"></a>Autenticación con una clave de suscripción a varios servicios

> [!WARNING]
> En este momento, la clave de varios servicios no admite: QnA Maker, Immersive Reader, Personalizer ni Anomaly Detector.

Esta opción también utiliza una clave de suscripción para autenticar las solicitudes. La principal diferencia es que una clave de suscripción no está asociada a un servicio específico, sino que se puede utilizar una única clave para autenticar solicitudes de varios servicios de Cognitive Services. Consulte los [precios de Cognitive Services](https://azure.microsoft.com/pricing/details/cognitive-services/) para más información sobre la disponibilidad regional, características admitidas y precios.

La clave de suscripción se proporciona en cada solicitud como encabezado `Ocp-Apim-Subscription-Key`.

[![Clave de suscripción a varios servicios para Cognitive Services](./media/index/single-key-demonstration-video.png)](https://www.youtube.com/watch?v=psHtA1p7Cas&feature=youtu.be)

### <a name="supported-regions"></a>Regiones admitidas

Cuando utilice la clave de suscripción a varios servicios para realizar una solicitud a `api.cognitive.microsoft.com`, debe incluir la región en la dirección URL. Por ejemplo: `westus.api.cognitive.microsoft.com`.

Cuando utilice la clave de suscripción a varios servicios con Traductor, debe especificar la región de suscripción con el encabezado `Ocp-Apim-Subscription-Region`.

Se admite la autenticación de varios servicios en estas regiones:

- `australiaeast`
- `brazilsouth`
- `canadacentral`
- `centralindia`
- `eastasia`
- `eastus`
- `japaneast`
- `northeurope`
- `southcentralus`
- `southeastasia`
- `uksouth`
- `westcentralus`
- `westeurope`
- `westus`
- `westus2`
- `francecentral`
- `koreacentral`
- `northcentralus`
- `southafricanorth`
- `uaenorth`
- `switzerlandnorth`


### <a name="sample-requests"></a>Solicitudes de ejemplo

Esta es una llamada de ejemplo a Bing Web Search API:

```cURL
curl -X GET 'https://YOUR-REGION.api.cognitive.microsoft.com/bing/v7.0/search?q=Welsch%20Pembroke%20Corgis' \
-H 'Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY' | json_pp
```

Esta es una llamada de ejemplo al servicio Traductor:

```cURL
curl -X POST 'https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=de' \
-H 'Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY' \
-H 'Ocp-Apim-Subscription-Region: YOUR_SUBSCRIPTION_REGION' \
-H 'Content-Type: application/json' \
--data-raw '[{ "text": "How much for the cup of coffee?" }]' | json_pp
```

## <a name="authenticate-with-an-authentication-token"></a>Autenticación con un token de autenticación

Algunos servicios de Azure Cognitive Services aceptan, y en algunos casos requieren, un token de autenticación. Actualmente, estos servicios admiten tokens de autenticación:

* Text Translation API
* Servicios Voz: Speech-to-text REST API
* Servicios Voz: Text-to-speech REST API

>[!NOTE]
> QnA Maker también utiliza el encabezado de autorización, pero requiere una clave de punto de conexión. Para más información, consulte [QnA Maker: Get answer from knowledge base](./qnamaker/quickstarts/get-answer-from-knowledge-base-using-url-tool.md) (QnA Maker: Obtención de respuesta de Knowledge Base).

>[!WARNING]
> Los servicios que admiten los tokens de autenticación pueden cambiar con el tiempo. Consulte la referencia de la API de un servicio antes de utilizar este método de autenticación.

Tanto las claves de suscripción de servicio único como las de varios servicios se pueden intercambiar por tokens de autenticación. Los token de autenticación tienen una validez de 10 minutos.

Los tokens de autenticación se incluyen en una solicitud como encabezado `Authorization`. El valor del token proporcionado debe ir precedido de `Bearer`, por ejemplo: `Bearer YOUR_AUTH_TOKEN`.

### <a name="sample-requests"></a>Solicitudes de ejemplo

Use esta dirección URL para intercambiar una clave de suscripción para un token de autenticación: `https://YOUR-REGION.api.cognitive.microsoft.com/sts/v1.0/issueToken`.

```cURL
curl -v -X POST \
"https://YOUR-REGION.api.cognitive.microsoft.com/sts/v1.0/issueToken" \
-H "Content-type: application/x-www-form-urlencoded" \
-H "Content-length: 0" \
-H "Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY"
```

Estas regiones de varios servicios admiten el intercambio de tokens:

- `australiaeast`
- `brazilsouth`
- `canadacentral`
- `centralindia`
- `eastasia`
- `eastus`
- `japaneast`
- `northeurope`
- `southcentralus`
- `southeastasia`
- `uksouth`
- `westcentralus`
- `westeurope`
- `westus`
- `westus2`

Después de obtener un token de autenticación, deberá pasarlo en cada solicitud como encabezado `Authorization`. Esta es una llamada de ejemplo al servicio Traductor:

```cURL
curl -X POST 'https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=de' \
-H 'Authorization: Bearer YOUR_AUTH_TOKEN' \
-H 'Content-Type: application/json' \
--data-raw '[{ "text": "How much for the cup of coffee?" }]' | json_pp
```

[!INCLUDE [](../../includes/cognitive-services-azure-active-directory-authentication.md)]

## <a name="see-also"></a>Consulte también

* [¿Qué es Cognitive Services?](./what-are-cognitive-services.md)
* [Precios de Cognitive Services](https://azure.microsoft.com/pricing/details/cognitive-services/)
* [Subdominios personalizados](cognitive-services-custom-subdomains.md)
