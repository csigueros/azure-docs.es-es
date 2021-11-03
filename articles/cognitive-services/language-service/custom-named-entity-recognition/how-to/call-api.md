---
title: Envío de tareas de Reconocimiento de entidades con nombre (NER) personalizado
titleSuffix: Azure Cognitive Services
description: Obtenga información sobre el envío de una solicitud de Reconocimiento de entidades con nombre (NER) personalizado.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-custom-ner, ignite-fall-2021
ms.openlocfilehash: 874d7c93a4e884c5bd449c6d49d676147e673978
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131091300"
---
# <a name="deploy-a-model-and-extract-entities-from-text-using-the-runtime-api"></a>Implemente un modelo y extraiga entidades del texto mediante la API en tiempo de ejecución.

Una vez que esté satisfecho con el rendimiento del modelo, estará listo para implementarse y se usará para reconocer entidades en texto. Solo puede enviar tareas de Reconocimiento de entidades a través de la API, no desde Language Studio.

## <a name="prerequisites"></a>Prerrequisitos

* Un [proyecto creado](create-project.md) correctamente con una cuenta de Azure Blob Storage configurada
    * Datos del texto que [se ha cargado](create-project.md#prepare-training-data) en la cuenta de almacenamiento.
* [Datos etiquetados](tag-data.md)
* Un [modelo entrenado correctamente](train-model.md)
* Se han revisado los [detalles de evaluación del modelo](view-model-evaluation.md) para determinar el rendimiento del modelo.
    * (opcional) Se han [realizado mejoras](improve-model.md) en el modelo si su rendimiento no es satisfactorio.

Consulte el [ciclo de vida de desarrollo de aplicaciones](../overview.md#application-development-lifecycle) para más información.

## <a name="deploy-your-model"></a>Implementación del modelo

1. Vaya al proyecto en [Language Studio](https://aka.ms/custom-extraction).

2. Seleccione **Deploy model** (Implementar modelo) en el menú de la izquierda.

3. Seleccione el modelo que quiere implementar y elija **Deploy model** (Implementar modelo).

> [!TIP]
> Puede probar el modelo en Language Studio enviando ejemplos de texto para que se clasifiquen. 
> 1. Seleccione **Test model** (Probar modelo) en el menú de la izquierda del proyecto en Language Studio.
> 2. Seleccione el modelo que quiera probar.
> 3. Agregue el texto al cuadro de texto; también puede cargar un archivo `.txt`. 
> 4. Haga clic en **Run the test** (Ejecutar la prueba).
> 5. En la pestaña **Resultado** (Resultado), puede ver las entidades extraídas del texto y sus tipos. También puede ver la respuesta JSON en la pestaña **JSON**.

## <a name="send-a-text-classification-request-to-your-model"></a>Envío de una solicitud de clasificación de texto al modelo

# <a name="using-language-studio"></a>[Uso de Language Studio](#tab/language-studio)

### <a name="using-language-studio"></a>Uso de Language Studio

1. Una vez completada la implementación, seleccione el modelo que desea usar y, en el menú superior, haga clic en **Get prediction URL** (Obtener dirección URL de predicción) y copie la dirección URL y el cuerpo.

    :::image type="content" source="../../custom-classification/media/get-prediction-url-1.png" alt-text="run-inference" lightbox="../../custom-classification/media/get-prediction-url-1.png":::

2. En la ventana que aparece, en el gráfico dinámico **Submit** (Enviar), copie la solicitud de ejemplo en la línea de comandos.

3. Reemplace `<YOUR_DOCUMENT_HERE>` por el texto real que desea clasificar.

    :::image type="content" source="../../custom-classification/media/get-prediction-url-2.png" alt-text="run-inference-2" lightbox="../../custom-classification/media/get-prediction-url-2.png":::

4. Envíe la solicitud.

5. En el encabezado de respuesta que recibe, extraiga `jobId` de `operation-location`, que tiene el formato: `{YOUR-ENDPOINT}/text/analytics/v3.2-preview.2/analyze/jobs/<jobId}>`.

6. Copie la solicitud de recuperación y reemplace `jobId` y envíe la solicitud.

    :::image type="content" source="../../custom-classification/media/get-prediction-url-3.png" alt-text="run-inference-3" lightbox="../../custom-classification/media/get-prediction-url-3.png":::
    
 ## <a name="retrieve-the-results-of-your-job"></a>Recuperación de los resultados del trabajo

1. Seleccione **Retrieve** (Recuperar) en la misma ventana que recibió la solicitud de ejemplo que obtuvo anteriormente y copie la solicitud de ejemplo en un editor de texto. 

    :::image type="content" source="../media/get-prediction-retrieval-url.png" alt-text="Captura de pantalla que muestra la dirección URL y la solicitud de recuperación de predicción" lightbox="../media/get-prediction-retrieval-url.png":::

2. Reemplace `<OPERATION_ID>` por `jobId` (recibido en el paso anterior). 

3. Envíe la solicitud cURL `GET` en el terminal o en el símbolo del sistema. Recibirá una respuesta 202 y un código JSON similar al siguiente, si la solicitud se ha realizado correctamente.

Puede encontrar más detalles sobre los resultados en la sección siguiente.

# <a name="using-the-api"></a>[Uso de la API](#tab/api)

### <a name="using-the-api"></a>Uso de la API

### <a name="get-your-resource-keys-endpoint"></a>Obtención del punto de conexión y las claves del recurso

1. Vaya a la página de información general del recurso en [Azure Portal](https://ms.portal.azure.com/#home).

2. En el menú de la izquierda, seleccione **Claves y punto de conexión**. Utilice el punto de conexión para las solicitudes de API y necesitará la clave para el encabezado `Ocp-Apim-Subscription-Key`.

    :::image type="content" source="../../custom-classification/media/get-endpoint-azure.png" alt-text="Obtención del punto de conexión de Azure" lightbox="../../custom-classification/media/get-endpoint-azure.png":::

### <a name="submit-custom-ner-task"></a>Envío de una tarea de NER personalizado

> [!NOTE]
> El nombre del proyecto distingue mayúsculas de minúsculas.

Use esta solicitud **POST** para iniciar una tarea de extracción de entidades. Reemplace `{projectName}` por el nombre del proyecto en el que tiene el modelo que desea usar.

`{YOUR-ENDPOINT}/text/analytics/v3.2-preview.2/analyze`

#### <a name="headers"></a>encabezados

|Clave|Valor|
|--|--|
|Ocp-Apim-Subscription-Key| Clave de suscripción que proporciona acceso a esta API.|

#### <a name="body"></a>Body

```json
    {
    "displayName": "MyJobName",
    "analysisInput": {
        "documents": [
            {
                "id": "doc1", 
                "text": "Lorem ipsum dolor sit amet, consectetur adipiscing elit. Nunc tempus, felis sed vehicula lobortis, lectus ligula facilisis quam, quis aliquet lectus diam id erat. Vivamus eu semper tellus. Integer placerat sem vel eros iaculis dictum. Sed vel congue urna."
            },
            {
                "id": "doc2",
                "text": "Mauris dui dui, ultricies vel ligula ultricies, elementum viverra odio. Donec tempor odio nunc, quis fermentum lorem egestas commodo. Class aptent taciti sociosqu ad litora torquent per conubia nostra, per inceptos himenaeos."
            }
        ]
    },
    "tasks": {
        "customEntityRecognitionTasks": [      
            {
                "parameters": {
                      "project-name": "MyProject",
                      "deployment-name": "MyDeploymentName"
                      "stringIndexType": "TextElements_v8"
                }
            }
        ]
    }
}
```

|Clave|Valor de ejemplo|Descripción|
|--|--|--|
|DisplayName|"MyJobName"|Nombre del trabajo|
|Documentos|[{},{}]|Lista de documentos en los que se van a ejecutar las tareas.|
|id|"doc1"|Identificador de documento de cadena|
|text|"Lorem ipsum dolor sit amet"| El documento en formato de cadena|
|"tasks"|[]| Lista de tareas que queremos realizar.|
|--|customEntityRecognitionTasks|Identificador de la tarea que queremos realizar. |
|parámetros|[]|Lista de parámetros que se van a pasar a la tarea.|
|project-name| "MyProject"| Nombre del proyecto. El nombre del proyecto distingue mayúsculas de minúsculas.|
|deployment-name| "MyDeploymentName"| Nombre de la implementación|

#### <a name="response"></a>Response

Recibirá una respuesta 202 que indica que se ha realizado correctamente. En los **encabezados** de la respuesta, extraiga el valor de `operation-location`.
`operation-location` tiene el formato siguiente:

 `{YOUR-ENDPOINT}/text/analytics/v3.2-preview.2/analyze/jobs/<jobId>`

Usará este punto de conexión en el paso siguiente para obtener los resultados de la tarea de reconocimiento personalizado.

### <a name="get-task-status-and-results"></a>Obtención del estado y los resultados de la tarea

Use la siguiente solicitud **GET** para consultar el estado y los resultados de la tarea de reconocimiento personalizado. Puede usar el punto de conexión que recibió en el paso anterior.

`{YOUR-ENDPOINT}/text/analytics/v3.2-preview.2/analyze/jobs/<jobId>`.

#### <a name="headers"></a>encabezados

|Clave|Valor|
|--|--|
|Ocp-Apim-Subscription-Key| Clave de suscripción que proporciona acceso a esta API.|

Puede encontrar más detalles sobre los resultados en la sección siguiente.

---

#### <a name="custom-extraction-task-results"></a>Resultados de la tarea de extracción personalizada

La respuesta devuelta por la llamada de obtención de resultados será un documento JSON con los parámetros siguientes:

```json
{
    "createdDateTime": "2021-05-19T14:32:25.578Z",
    "displayName": "MyJobName",
    "expirationDateTime": "2021-05-19T14:32:25.578Z",
    "jobId": "3fa85f64-5717-4562-b3fc-2c963f66afa6",
    "lastUpdateDateTime": "2021-05-19T14:32:25.578Z",
    "status": "completed",
    "errors": [],
    "tasks": {
        "details": {
            "name": "MyJobName",
            "lastUpdateDateTime": "2021-03-29T19:50:23Z",
            "status": "completed"
        },
        "completed": 1,
        "failed": 0,
        "inProgress": 0,
        "total": 1,
        "tasks": {
    "customEntityRecognitionTasks": [
        {
            "lastUpdateDateTime": "2021-05-19T14:32:25.579Z",
            "name": "MyJobName",
            "status": "completed",
            "results": {
               "documents": [
                        {
                            "id": "doc1",
                            "entities": [
                                {
                                    "text": "Government",
                                    "category": "restaurant_name",
                                    "offset": 23,
                                    "length": 10,
                                    "confidenceScore": 0.0551877357
                                }
                            ],
                            "warnings": []
                        },
                        {
                            "id": "doc2",
                            "entities": [
                                {
                                    "text": "David Schmidt",
                                    "category": "artist",
                                    "offset": 0,
                                    "length": 13,
                                    "confidenceScore": 0.8022353
                                }
                            ],
                            "warnings": []
                        }
                    ],
                "errors": [],
                "statistics": {
                    "documentsCount":0,
                    "erroneousDocumentsCount":0,
                    "transactionsCount":0
                }
                    }
                }
            ]
        }
    }
```
