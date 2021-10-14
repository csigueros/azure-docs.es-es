---
title: Llamada a Read API
titleSuffix: Azure Cognitive Services
description: Obtenga información sobre cómo llamar a Read API y configurar su comportamiento en detalle.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 03/31/2021
ms.author: pafarley
ms.openlocfilehash: ed19ddae799a743d3bc8e51c550e4a054ad42cf7
ms.sourcegitcommit: f29615c9b16e46f5c7fdcd498c7f1b22f626c985
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/04/2021
ms.locfileid: "129426758"
---
# <a name="call-the-read-api"></a>Llamada a la API Read

En esta guía, aprenderá a llamar a Read API para extraer texto de imágenes. Conocerá las distintas maneras en que puede configurar el comportamiento de esta API para satisfacer sus necesidades.

En esta guía se da por supuesto que ya se ha <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision"  title="creado un recurso Computer Vision"  target="_blank">create a Computer Vision resource</a> y se ha obtenido una clave de suscripción y una dirección URL del punto de conexión. Si no lo ha hecho, para empezar consulte un [inicio rápido](../quickstarts-sdk/client-library.md).

## <a name="determine-how-to-process-the-data-optional"></a>Determinación de cómo procesar los datos (opcional)

### <a name="specify-the-ocr-model"></a>Especificación del modelo de OCR

De manera predeterminada, el servicio usa el modelo de GA más reciente para extraer texto. A partir de Read 3.2, un parámetro `model-version` permite elegir entre los modelos de GA y versión preliminar de una versión de API determinada. El modelo que se especifique se va a usar para extraer texto con la operación de lectura.

Al usar la operación de lectura, emplee los valores siguientes en el parámetro `model-version` opcional.

|Valor| Modelo usado |
|:-----|:----|
| No proporcionado | modelo e idiomas de GA más recientes |
| latest | modelo e idiomas de GA más recientes|
| 2021-09-30-preview | modelo de versión preliminar con las características e idiomas de versión preliminar adicionales. Incluye todas las mejoras en el modelo de GA anterior.
| 2021-04-12 | GA específico de fecha, actualmente igual a la versión más reciente |

### <a name="input-language"></a>Idioma de entrada

De manera predeterminada, el servicio extrae todo el texto de las imágenes o documentos, incluidos los idiomas mixtos. La [operación de lectura](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2/operations/5d986960601faab4bf452005) tiene un parámetro de solicitud opcional para el lenguaje. Proporcione un código de idioma únicamente si quiere forzar a que el documento se procese como ese idioma específico. De lo contrario, el servicio puede devolver texto incompleto e incorrecto.

### <a name="natural-reading-order-output-latin-languages-only"></a>Salida de orden de lectura natural (solo idiomas procedentes del latín)

De manera predeterminada, el servicio genera las líneas de texto en el orden de izquierda a derecha. Opcionalmente, con el parámetro de solicitud `readingOrder`, use `natural` si quiere obtener una salida de orden de lectura más natural, como se muestra en el ejemplo siguiente. Esta característica solo es compatible con los idiomas procedentes del latín.

:::image type="content" source="../Images/ocr-reading-order-example.png" alt-text="Ejemplo de orden de lectura de OCR" border="true" :::

### <a name="select-pages-or-page-ranges-for-text-extraction"></a>Selección de las páginas o de los intervalos de páginas para la extracción de texto

De manera predeterminada, el servicio extrae texto de todas las páginas de los documentos. Opcionalmente, use el parámetro de solicitud `pages` para especificar números de página o intervalos de páginas para extraer texto solo de esas páginas. En el ejemplo siguiente se muestra un documento con 10 páginas, con texto extraído para ambos casos: todas las páginas (1-10) y las páginas seleccionadas (3-6).

:::image type="content" source="../Images/ocr-select-pages.png" alt-text="Salida de páginas seleccionadas" border="true" :::

## <a name="submit-data-to-the-service"></a>Envío de datos al servicio

Envíe una imagen local o una imagen remota a Read API. Si la imagen es local, coloque los datos binarios de la imagen en el cuerpo de la solicitud HTTP. Si la imagen es remota, especifique la dirección URL de la misma asignando al cuerpo de la solicitud un formato como el siguiente: `{"url":"http://example.com/images/test.jpg"}`.

La [llamada a Read](https://centraluseuap.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2/operations/5d986960601faab4bf452005) de Read API toma como entrada una imagen o un documento PDF y extrae texto de forma asincrónica.

`https://{endpoint}/vision/v3.2/read/analyze[?language][&pages][&readingOrder]`

La llamada se devuelve un campo de encabezado de respuesta denominado `Operation-Location`. El valor `Operation-Location` es una dirección URL que contiene el identificador de la operación que se va a usar en el paso siguiente.

|Encabezado de respuesta| Valor de ejemplo |
|:-----|:----|
|Operation-Location | `https://cognitiveservice/vision/v3.2/read/analyzeResults/49a36324-fc4b-4387-aa06-090cfbf0064f` |

> [!NOTE]
> **Facturación** 
>
> La página de [precios de Computer Vision](https://azure.microsoft.com/pricing/details/cognitive-services/computer-vision/) incluye el plan de tarifa de Read. Cada imagen o página analizada cuenta como una transacción. Si llama a la operación con un documento PDF o TIFF con 100 páginas, la operación de Read la contabilizará como 100 transacciones para su facturación. Si realizó 50 llamadas a la operación, y cada llamada envió un documento con 100 páginas, se facturarán 50x100 = 5000 transacciones.


## <a name="get-results-from-the-service"></a>Obtención de resultados a partir del servicio

El segundo paso es llamar a la operación [Get Read Results](https://centraluseuap.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2/operations/5d9869604be85dee480c8750). Esta operación toma como entrada el identificador de operación que la operación de lectura ha creado.

`https://{endpoint}/vision/v3.2/read/analyzeResults/{operationId}`

Devuelve una respuesta JSON que contiene un campo de **estado** con los siguientes valores posibles.

|Value | Significado |
|:-----|:----|
| `notStarted`| la operación no se ha iniciado. |
| `running`| la operación se está procesando. |
| `failed`| Se produjeron errores en la operación. |
| `succeeded`| La operación se ha realizado correctamente. |

Llamará a esta operación de forma iterativa hasta que se devuelva con el valor **correcto**. Use un intervalo de 1 a 2 segundos para evitar superar la tasa de solicitudes por segundo (RPS).

> [!NOTE]
> El nivel gratuito limita la tasa de solicitudes a 20 llamadas por minuto. El nivel de pago permite 10 solicitudes por segundo (RPS) que pueden aumentar a petición. Anote tanto el identificador como la región de los recursos de Azure y abra una incidencia de soporte técnico de soporte técnico de Azure o póngase en contacto con el equipo de su cuenta para solicitar una tasa de solicitudes por segundo (RPS) mayor.

Cuando el campo **estado** tiene el valor `succeeded` (correcto), la respuesta JSON contiene el contenido de texto extraído de la imagen o el documento. La respuesta en JSON mantiene las agrupaciones de líneas originales de palabras reconocidas. Incluye las líneas de texto extraídas y las coordenadas del cuadro de límite correspondientes. Cada línea de texto incluye todas las palabras extraídas, con las coordenadas y las puntuaciones de confianza respectivas.

> [!NOTE]
> Los datos enviados a la operación `Read` se cifran temporalmente y se almacenan en reposo durante un corto tiempo y después se eliminan. Esto permite que las aplicaciones recuperen el texto extraído como parte de la respuesta del servicio.

### <a name="sample-json-output"></a>Salida de JSON de ejemplo

Consulte el siguiente ejemplo de una respuesta JSON correcta:

```json
{
  "status": "succeeded",
  "createdDateTime": "2021-02-04T06:32:08.2752706+00:00",
  "lastUpdatedDateTime": "2021-02-04T06:32:08.7706172+00:00",
  "analyzeResult": {
    "version": "3.2",
    "readResults": [
      {
        "page": 1,
        "angle": 2.1243,
        "width": 502,
        "height": 252,
        "unit": "pixel",
        "lines": [
          {
            "boundingBox": [
              58,
              42,
              314,
              59,
              311,
              123,
              56,
              121
            ],
            "text": "Tabs vs",
            "appearance": {
              "style": {
                "name": "handwriting",
                "confidence": 0.96
              }
            },
            "words": [
              {
                "boundingBox": [
                  68,
                  44,
                  225,
                  59,
                  224,
                  122,
                  66,
                  123
                ],
                "text": "Tabs",
                "confidence": 0.933
              },
              {
                "boundingBox": [
                  241,
                  61,
                  314,
                  72,
                  314,
                  123,
                  239,
                  122
                ],
                "text": "vs",
                "confidence": 0.977
              }
            ]
          }
        ]
      }
    ]
  }
}
```

### <a name="handwritten-classification-for-text-lines-latin-languages-only"></a>Clasificación manuscrita de líneas de texto (solo para idiomas procedentes del latín)

La respuesta incluye la clasificación de si cada línea de texto es de estilo manuscrito o no, junto con una puntuación de confianza. Esta característica solo es compatible con los idiomas procedentes del latín. En el ejemplo siguiente se muestra la clasificación manuscrita del texto de la imagen.

:::image type="content" source="../Images/ocr-handwriting-classification.png" alt-text="Ejemplo de clasificación de escritura a mano OCR" border="true" :::

## <a name="next-steps"></a>Pasos siguientes

- Comience a trabajar con los [inicios rápidos de la biblioteca cliente o la API REST de OCR (Read)](../quickstarts-sdk/client-library.md).
- Obtenga información sobre la [API REST de Read 3.2](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2/operations/5d986960601faab4bf452005).
