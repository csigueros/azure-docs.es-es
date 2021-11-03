---
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: quickstart
ms.date: 11/02/2021
ms.author: aahi
ms.custom: ignite-fall-2021
ms.openlocfilehash: a13a627cfe30121b84ecb18fb1af6b52a396bbcc
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131091101"
---
[Documentación de referencia](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1/)


## <a name="prerequisites"></a>Prerrequisitos

* La versión actual de [cURL](https://curl.haxx.se/).
* Una vez que tenga la suscripción de Azure, <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics"  title="Creación de un recurso de idioma"  target="_blank">cree un recurso de idioma</a> en Azure Portal para obtener la clave y el punto de conexión. Una vez que se implemente, haga clic en **Ir al recurso**.
    * Necesitará la clave y el punto de conexión del recurso que cree para conectar la aplicación a la API. En una sección posterior de este mismo inicio rápido pegará la clave y el punto de conexión en el código siguiente.
    * Puede usar el plan de tarifa gratis (`F0`) para probar el servicio y actualizarlo más adelante a un plan de pago para producción.

> [!NOTE]
> * En los siguientes ejemplos de BASH se usa el carácter de continuación de línea `\`. Si la consola o el terminal usan un carácter de continuación de línea diferente, use ese carácter.
> * Puede encontrar ejemplos específicos del lenguaje en [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code).
> * Vaya a Azure Portal y busque la clave y el punto de conexión del recurso del idioma que creó en los requisitos previos. Los encontrará en la página de **clave y punto de conexión** del recurso, en **Administración de recursos**. A continuación, reemplace las cadenas del código siguiente por su clave y punto de conexión.
Para llamar a la API, necesita la siguiente información:


|parámetro  |Descripción  |
|---------|---------|
|`-X POST <endpoint>`     | Especifica el punto de conexión para acceder a la API.        |
|`-H Content-Type: application/json`     | Tipo de contenido para enviar datos JSON.          |
|`-H "Ocp-Apim-Subscription-Key:<key>`    | Especifica la clave para acceder a la API.        |
|`-d <documents>`     | JSON que contiene los documentos que desea enviar.         |

Los siguientes comandos de cURL se ejecutan desde un shell de BASH. Edite estos comandos con sus propios valores de nombre de recurso, clave de recurso y JSON.


## <a name="text-analytics-for-health"></a>Text Analytics for Health

[!INCLUDE [REST API quickstart instructions](../../../includes/rest-api-instructions.md)]

```bash
curl -i -X POST https://your-text-analytics-endpoint-here/text/analytics/v3.1/entities/health/jobs \
-H "Content-Type: application/json" \
-H "Ocp-Apim-Subscription-Key: your-text-analytics-key-here" \
-d '{ documents: [{ id: "1", language:"en", text: "Subject was administered 100mg remdesivir intravenously over a period of 120 min"}]}'
```

Obtenga el valor de `operation-location` del encabezado de respuesta. El valor será similar a la dirección URL siguiente:

```rest
https://your-resource.cognitiveservices.azure.com/text/analytics/v3.1/entities/health/jobs/12345678-1234-1234-1234-12345678
```

Para obtener los resultados de la solicitud, use el siguiente comando cURL. Asegúrese de reemplazar `my-job-id` por el valor de identificador numérico que recibió del encabezado de respuesta `operation-location` anterior:

```bash
curl -X GET  https://your-text-analytics-endpoint-here/text/analytics/v3.1/entities/health/jobs/my-job-id \
-H "Content-Type: application/json" \
-H "Ocp-Apim-Subscription-Key: your-text-analytics-key-here"
```


### <a name="json-response"></a>Respuesta JSON

```json
{
   "jobId":"12345678-1234-1234-1234-12345678",
   "lastUpdateDateTime":"2021-08-02T21:32:43Z",
   "createdDateTime":"2021-08-02T21:32:04Z",
   "expirationDateTime":"2021-08-03T21:32:04Z",
   "status":"succeeded",
   "errors":[
      
   ],
   "results":{
      "documents":[
         {
            "id":"1",
            "entities":[
               {
                  "offset":25,
                  "length":5,
                  "text":"100mg",
                  "category":"Dosage",
                  "confidenceScore":1.0
               },
               {
                  "offset":31,
                  "length":10,
                  "text":"remdesivir",
                  "category":"MedicationName",
                  "confidenceScore":1.0,
                  "name":"remdesivir",
                  "links":[
                     {
                        "dataSource":"UMLS",
                        "id":"C4726677"
                     },
                     {
                        "dataSource":"DRUGBANK",
                        "id":"DB14761"
                     },
                     {
                        "dataSource":"GS",
                        "id":"6192"
                     },
                     {
                        "dataSource":"MEDCIN",
                        "id":"398132"
                     },
                     {
                        "dataSource":"MMSL",
                        "id":"d09540"
                     },
                     {
                        "dataSource":"MSH",
                        "id":"C000606551"
                     },
                     {
                        "dataSource":"MTHSPL",
                        "id":"3QKI37EEHE"
                     },
                     {
                        "dataSource":"NCI",
                        "id":"C152185"
                     },
                     {
                        "dataSource":"NCI_FDA",
                        "id":"3QKI37EEHE"
                     },
                     {
                        "dataSource":"NDDF",
                        "id":"018308"
                     },
                     {
                        "dataSource":"RXNORM",
                        "id":"2284718"
                     },
                     {
                        "dataSource":"SNOMEDCT_US",
                        "id":"870592005"
                     },
                     {
                        "dataSource":"VANDF",
                        "id":"4039395"
                     }
                  ]
               },
               {
                  "offset":42,
                  "length":13,
                  "text":"intravenously",
                  "category":"MedicationRoute",
                  "confidenceScore":0.99
               },
               {
                  "offset":73,
                  "length":7,
                  "text":"120 min",
                  "category":"Time",
                  "confidenceScore":0.98
               }
            ],
            "relations":[
               {
                  "relationType":"DosageOfMedication",
                  "entities":[
                     {
                        "ref":"#/results/documents/0/entities/0",
                        "role":"Dosage"
                     },
                     {
                        "ref":"#/results/documents/0/entities/1",
                        "role":"Medication"
                     }
                  ]
               },
               {
                  "relationType":"RouteOfMedication",
                  "entities":[
                     {
                        "ref":"#/results/documents/0/entities/1",
                        "role":"Medication"
                     },
                     {
                        "ref":"#/results/documents/0/entities/2",
                        "role":"Route"
                     }
                  ]
               },
               {
                  "relationType":"TimeOfMedication",
                  "entities":[
                     {
                        "ref":"#/results/documents/0/entities/1",
                        "role":"Medication"
                     },
                     {
                        "ref":"#/results/documents/0/entities/3",
                        "role":"Time"
                     }
                  ]
               }
            ],
            "warnings":[
               
            ]
         }
      ],
      "errors":[
         
      ],
      "modelVersion":"2021-05-15"
   }
}
```
