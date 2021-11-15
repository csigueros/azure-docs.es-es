---
title: 'Análisis de opiniones: LUIS'
titleSuffix: Azure Cognitive Services
description: Si el análisis de sentimiento está configurado, la respuesta JSON de LUIS incluye el análisis de sentimiento.
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 10/28/2021
ms.openlocfilehash: 2c80e23335de38929f8ca0fc8cf0b04f835d9e10
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2021
ms.locfileid: "131446645"
---
# <a name="sentiment-analysis"></a>análisis de opiniones
Si el análisis de sentimiento está configurado, la respuesta JSON de LUIS incluye el análisis de sentimiento. Obtenga más información sobre el análisis de sentimiento en la documentación del [servicio de lenguaje](../language-service/index.yml).

LUIS usa la versión 2 de la API. 

Análisis de sentimiento se configura al publicar la aplicación. Vea [cómo publicar una aplicación](./luis-how-to-publish-app.md) para obtener más información.

## <a name="resolution-for-sentiment"></a>Resolución de opiniones

Los datos de opinión son una puntuación entre 1 y 0 que indica el valor de opinión positiva (más cercano a 1) o negativa (más cercano a 0) de los datos.

#### <a name="english-language"></a>[Lengua inglesa](#tab/english)

Cuando la referencia cultural es `en-us`, la respuesta es:

```JSON
"sentimentAnalysis": {
  "label": "positive",
  "score": 0.9163064
}
```

#### <a name="other-languages"></a>[Otros idiomas](#tab/other-languages)

Para todas las demás referencias culturales, la respuesta es:

```JSON
"sentimentAnalysis": {
  "score": 0.9163064
}
```
* * *

## <a name="next-steps"></a>Pasos siguientes

Más información acerca del [punto de conexión de predicción de V3](luis-migration-api-v3.md).
