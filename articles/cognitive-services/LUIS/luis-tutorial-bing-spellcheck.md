---
title: 'Corrección de palabras mal escritas: LUIS'
titleSuffix: Azure Cognitive Services
description: Corrija palabras incorrectas en expresiones mediante la adición de Bing Spell Check API V7 a las consultas de punto de conexión de LUIS.
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 01/12/2021
ms.openlocfilehash: 904a29278ea3efdabe6ed72892d2e14ccbf2e0e3
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124740752"
---
# <a name="correct-misspelled-words-with-bing-resource"></a>Corrección de palabras mal escritas con el recurso de Bing

La API de predicción V3 ahora es compatible con [Bing Spellcheck API](/bing/search-apis/bing-spell-check/overview). Para agregar la revisión ortográfica a la aplicación, incluya la clave del recurso de búsqueda de Bing en el encabezado de las solicitudes. Puede usar esta característica con un recurso de Bing existente, si ya tiene uno, o [crear otro](https://portal.azure.com/#create/Microsoft.BingSearch). 

Ejemplo de salida de predicción de una consulta mal escrita:

```json
{
  "query": "bouk me a fliht to kayro",
  "prediction": {
    "alteredQuery": "book me a flight to cairo",
    "topIntent": "book a flight",
    "intents": {
      "book a flight": {
        "score": 0.9480589
      }
      "None": {
        "score": 0.0332136229
      }
    },
    "entities": {}
  }
}
```

Las correcciones de ortografía se realizan antes que la predicción de expresiones de usuario de LUIS. Puede ver cualquier cambio en la expresión original (incluida la ortografía) en la respuesta.

## <a name="create-bing-search-resource"></a>Creación de un recurso de Bing Search

Para crear un recurso de Bing Search en Azure Portal, siga estas instrucciones:

1. Inicie sesión en [Azure Portal](https://portal.azure.com).

2. Haga clic en **Crear un recurso** en la esquina superior izquierda.

3. En el cuadro de búsqueda, escriba `Bing Search V7` y seleccione el servicio.

4. Aparece un panel de información a la derecha con información, incluido el Aviso legal. Haga clic en **Crear** para iniciar el proceso de creación de la suscripción.

> [!div class="mx-imgBorder"]
> ![Recurso de la API Bing Spell Check V7](./media/luis-tutorial-bing-spellcheck/bing-search-resource-portal.png)

5. En el panel siguiente, escriba la configuración del servicio. Espere a que finalice el proceso de creación del servicio.

6. Una vez creado el recurso, vaya a la hoja **Keys and Endpoint** (Claves y punto de conexión) de la izquierda. 

7. Copie una de las claves que se van a agregar al encabezado de la solicitud de predicción. Solo necesita una de las dos claves.

<!--
## Using the key in LUIS test panel
There are two places in LUIS to use the key. The first is in the [test panel](luis-interactive-test.md#view-bing-spell-check-corrections-in-test-panel). The key isn't saved into LUIS but instead is a session variable. You need to set the key every time you want the test panel to apply the Bing Spell Check API v7 service to the utterance. See [instructions](luis-interactive-test.md#view-bing-spell-check-corrections-in-test-panel) in the test panel for setting the key.
-->


## <a name="adding-the-key-to-the-endpoint-url"></a>Agregar la clave a la dirección URL del punto de conexión
Para cada consulta en la que desee aplicar la corrección ortográfica, la consulta de punto de conexión necesita la clave de recurso de Bing Spellcheck que se pasa en el parámetro del encabezado de la consulta. Es posible que tenga un bot de chat que llama a LUIS o puede llamar directamente a la API de punto de conexión de LUIS. Con independencia de cómo se llame al punto de conexión, cada llamada debe incluir la información necesaria en la solicitud del encabezado para que las correcciones ortográficas funcionen correctamente. Debe establecer el valor con **mkt-bing-spell-check-key** en el valor de clave.

|Clave del encabezado|Valor de encabezado|
|--|--|
|`mkt-bing-spell-check-key`|Claves que se encuentran en la hoja **Keys and Endpoint** (Claves y punto de conexión) del recurso|

## <a name="send-misspelled-utterance-to-luis"></a>Enviar expresiones incorrectas a LUIS
1. Agregue una expresión incorrecta a la consulta de predicción que va a enviar, como "How far is the mountainn?". En inglés, `mountain`, con una `n`, es la ortografía correcta.

2. LUIS responde con un resultado JSON para `How far is the mountain?`. Si Bing Spell Check API v7 detecta un error ortográfico, el campo `query` de la respuesta JSON de la aplicación de LUIS contiene la consulta original y el campo `alteredQuery` contiene la consulta corregida enviada a LUIS.

```json
{
  "query": "How far is the mountainn?",
  "alteredQuery": "How far is the mountain?",
  "topScoringIntent": {
    "intent": "Concierge",
    "score": 0.183866
  },
  "entities": []
}
```

## <a name="ignore-spelling-mistakes"></a>Omitir los errores ortográficos

Si no desea usar el servicio de Bing Search API v7, debe agregar la ortografía correcta y la incorrecta.

Hay dos soluciones:

* Etiquete las expresiones de ejemplo que tengan todas las diferentes ortografías para que LUIS pueda aprender la correcta, así como los errores tipográficos. Esta opción requiere más esfuerzo de etiquetado que el uso de un corrector ortográfico.
* Cree una lista de frases con todas las variaciones de la palabra. Con esta solución, no es necesario etiquetar las variaciones de palabras en las expresiones de ejemplo.


> [!div class="nextstepaction"]
> [Más información sobre las expresiones de ejemplo](./luis-how-to-add-entities.md)
