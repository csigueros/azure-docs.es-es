---
title: Personalización de una aptitud de AML en conjuntos de aptitudes
titleSuffix: Azure Cognitive Search
description: Amplíe las funcionalidades de los conjuntos de aptitudes de Azure Cognitive Search con modelos de Azure Machine Learning.
manager: nitinme
author: mattmsft
ms.author: magottei
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/12/2020
ms.openlocfilehash: 529be70de61fe9adb025ee801bd417bc860ef5e6
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121736328"
---
# <a name="aml-skill-in-an-azure-cognitive-search-enrichment-pipeline"></a>Aptitud de AML en una canalización de enriquecimiento de Azure Cognitive Search

> [!IMPORTANT] 
> Esta aptitud está en versión preliminar pública en los [términos de uso complementarios](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). La [API de REST de versión preliminar](/rest/api/searchservice/index-preview) admite esta aptitud.

La aptitud **AML** permite ampliar el enriquecimiento de IA con un modelo personalizado de [Azure Machine Learning](../machine-learning/overview-what-is-azure-machine-learning.md) (AML). Una vez que un modelo de AML se ha [entrenado e implementado](../machine-learning/concept-azure-machine-learning-architecture.md#workspace), una aptitud de **AML** lo integra en el enriquecimiento con IA.

Como en las aptitudes integradas, una aptitud de **AML** tiene entradas y salidas. Las entradas se envían al servicio AML implementado como un objeto JSON, lo que genera una carga JSON como respuesta junto con un código de estado correcto. Se espera que la respuesta tenga las salidas especificadas por la aptitud de **AML**. Cualquier otra respuesta se considera un error y no se realiza ningún enriquecimiento.

> [!NOTE]
> El indexador realizará dos reintentos para ciertos códigos de estado HTTP estándar devueltos desde el servicio AML. Estos códigos de estado HTTP son:
> * `503 Service Unavailable`
> * `429 Too Many Requests`

## <a name="prerequisites"></a>Requisitos previos

* Un [área de trabajo de AML](../machine-learning/concept-workspace.md)
* Un [destino de proceso AML de Azure Kubernetes Service](../machine-learning/concept-compute-target.md) en esta área de trabajo con un [modelo implementado](../machine-learning/how-to-deploy-azure-kubernetes-service.md).
  * El [destino de proceso debe tener SSL habilitado](../machine-learning/how-to-secure-web-service.md#deploy-on-azure-kubernetes-service). Azure Cognitive Search solo permite el acceso los puntos de conexión **https**.
  * No se pueden usar certificados autofirmados.

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Custom.AmlSkill

## <a name="skill-parameters"></a>Parámetros de la aptitud

Los parámetros distinguen mayúsculas de minúsculas. Los parámetros que decida usar dependen de la [autenticación que requiera el servicio AML, si hay alguna](#WhatSkillParametersToUse).

| Nombre de parámetro | Descripción |
|--------------------|-------------|
| `uri` | (Necesario cuando [no hay autenticación ni autenticación de claves](#WhatSkillParametersToUse)) El [URI de puntuación del servicio AML](../machine-learning/how-to-consume-web-service.md) al que se enviará la carga _JSON_. Solo se permite el esquema de URI **https**. |
| `key` | (Se requiere para la [autenticación de clave](#WhatSkillParametersToUse)) La [clave del servicio AML](../machine-learning/how-to-consume-web-service.md#authentication-with-keys). |
| `resourceId` | (Necesario para la [autenticación de tokens](#WhatSkillParametersToUse)). El identificador de recursos de Azure Resource Manager del servicio AML. Debe tener el formato subscriptions/{guid}/resourceGroups/{nombre-grupo-recursos}/Microsoft.MachineLearningServices/workspaces/{nombre_área_trabajo}/services/{nombre_servicio}. |
| `region` | (Opcional para la [autenticación de tokens](#WhatSkillParametersToUse)). La [región](https://azure.microsoft.com/global-infrastructure/regions/) en la que se implementa el servicio AML. |
| `timeout` | (Opcional) Cuando se especifica, indica el tiempo de expiración del cliente http que hace la llamada API. Debe tener el formato de un valor "dayTimeDuration" XSD (subconjunto restringido de un valor de [duración ISO 8601](https://www.w3.org/TR/xmlschema11-2/#dayTimeDuration) ). Por ejemplo, `PT60S` para 60 segundos. Si no se establece, se elige el valor predeterminado de 30 segundos. El tiempo de expiración se puede establecer en un máximo de 230 segundos y un mínimo de 1. |
| `degreeOfParallelism` | (Opcional) Cuando se especifica, indica el número de llamadas que realizará el indexador en paralelo al punto de conexión que ha proporcionado. Puede reducir este valor si el punto de conexión no puede con una carga demasiado elevada de solicitudes o aumentarlo si el punto de conexión es capaz de aceptar más solicitudes y desea un aumento en el rendimiento del indexador.  Si no se establece, se usa un valor predeterminado de 5. degreeOfParallelism se puede establecer en un máximo de 10 y un mínimo de 1.

<a name="WhatSkillParametersToUse"></a>

## <a name="what-skill-parameters-to-use"></a>Qué parámetros de la aptitud se van a usar

Los parámetros de la aptitud de AML necesarios dependen de la autenticación que use el servicio AML, si hay alguno. Los servicios AML proporcionan tres opciones de autenticación:

* [Autenticación basada en claves](../machine-learning/how-to-authenticate-web-service.md#key-based-authentication) Se proporciona una clave estática para autenticar las solicitudes de puntuación de aptitudes de AML.
  * Use los parámetros _uri_ y _key_.
* [Autenticación basada en tokens](../machine-learning/how-to-authenticate-web-service.md#token-based-authentication) El servicio AML se [implementa mediante la autenticación basada en tokens](../machine-learning/how-to-authenticate-web-service.md#token-based-authentication). A la [identidad administrada](../active-directory/managed-identities-azure-resources/overview.md) del servicio Azure Cognitive Search se le ha concedido el [rol de lector](../machine-learning/how-to-assign-roles.md) en el área de trabajo del servicio AML. La aptitud de AML usa entonces la identidad administrada del servicio Azure Cognitive Search para autenticarse en el servicio AML, sin necesidad de claves estáticas.
  * Use el parámetro _resourceId_.
  * Si el servicio Azure Cognitive Search está en una región diferente del área de trabajo AML, use el parámetro _region_ para establecer la región en la que se implementó el servicio AML.
* Sin autenticación. No se requiere autenticación para usar el servicio AML.
  * Utilice el parámetro _uri_.

## <a name="skill-inputs"></a>Entradas de la aptitud

No hay entradas "predefinidas" para esta aptitud. Puede elegir uno o más campos que ya estarían disponibles en el momento de la ejecución de esta aptitud como entradas y la carga de _JSON_ enviada al servicio AML tendrá distintos campos.

## <a name="skill-outputs"></a>Salidas de la aptitud

No hay salidas "predefinidas" para esta aptitud. En función de la respuesta que devuelva el servicio AML, agregue campos de salida para poder seleccionarlos desde la respuesta de _JSON_.

## <a name="sample-definition"></a>Definición de ejemplo

```json
  {
    "@odata.type": "#Microsoft.Skills.Custom.AmlSkill",
    "description": "A sample model that detects the language of sentence",
    "uri": "https://contoso.count-things.com/score",
    "context": "/document",
    "inputs": [
      {
        "name": "text",
        "source": "/document/content"
      }
    ],
    "outputs": [
      {
        "name": "detected_language_code"
      }
    ]
  }
```

## <a name="sample-input-json-structure"></a>Estructura de JSON de entrada de ejemplo

Esta estructura _JSON_ representa la carga que se enviará al servicio AML. Los campos de nivel superior de la estructura se corresponderán a los "nombres" especificados en la sección `inputs` de la definición de la aptitud. El valor de esos campos provendrá del `source` de esos campos (que podrían ser de un campo del documento o posiblemente de otra aptitud).

```json
{
  "text": "Este es un contrato en Inglés"
}
```

## <a name="sample-output-json-structure"></a>Estructura JSON de salida de ejemplo

La salida corresponde a la respuesta devuelta desde el servicio AML. El servicio AML solo debe devolver una carga _JSON_ (comprobada mediante el examen del encabezado de respuesta de `Content-Type`) y debe ser un objeto en el que los campos son enriquecimientos que coinciden con los "nombres" de la `output` y cuyo valor se considere el enriquecimiento.

```json
{
    "detected_language_code": "es"
}
```

## <a name="inline-shaping-sample-definition"></a>Definición de ejemplo de formas insertadas

```json
  {
    "@odata.type": "#Microsoft.Skills.Custom.AmlSkill",
    "description": "A sample model that detects the language of sentence",
    "uri": "https://contoso.count-things.com/score",
    "context": "/document",
    "inputs": [
      {
        "name": "shapedText",
        "sourceContext": "/document",
        "inputs": [
            {
              "name": "content",
              "source": "/document/content"
            }
        ]
      }
    ],
    "outputs": [
      {
        "name": "detected_language_code"
      }
    ]
  }
```

## <a name="inline-shaping-input-json-structure"></a>Estructura JSON de entrada de formas insertadas

```json
{
  "shapedText": { "content": "Este es un contrato en Inglés" }
}
```

## <a name="inline-shaping-sample-output-json-structure"></a>Estructura JSON de salida de ejemplo de formas insertadas

```json
{
    "detected_language_code": "es"
}
```

## <a name="error-cases"></a>Casos de error
Además de un servicio AML no disponible o del envío de códigos de estado no correcto, los siguientes se consideran como casos con errores:

* Si el servicio AML devuelve un código de estado correcto, pero la respuesta indica que no es `application/json`, la respuesta se considera como no válida y no se realizará ningún enriquecimiento.
* Si el servicio AML devuelve JSON no válido

En los casos en que el servicio AML no está disponible o devuelve un error HTTP, se agregará un error descriptivo con todos los detalles disponibles sobre el error HTTP al historial de ejecución del indexador.

## <a name="see-also"></a>Consulte también

+ [Definición de un conjunto de aptitudes](cognitive-search-defining-skillset.md)
+ [Solución de problemas con la implementación de Docker de modelos con Azure Kubernetes Service y Azure Container Instances](../machine-learning/how-to-troubleshoot-deployment.md)