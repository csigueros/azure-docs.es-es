---
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 11/02/2021
ms.author: aahi
ms.custom: ignite-fall-2021
ms.openlocfilehash: 882355f0c4e6a44300182ffd27eb4cc422422f5f
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131092823"
---
## <a name="prerequisites"></a>Prerrequisitos

* Una suscripción a Azure: [cree una cuenta gratuita](https://azure.microsoft.com/free/cognitive-services).
* La versión actual de [cURL](https://curl.haxx.se/).
* Un recurso de idioma. Si no tiene ninguno, puede crear uno mediante [Azure Portal](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics). Si crea un nuevo recurso, haga clic en el vínculo, siga los pasos y espere a que se implemente. A continuación, haga clic en **Ir al recurso**.

## <a name="get-your-resource-keys-and-endpoint"></a>Obtención del punto de conexión y las claves del recurso

1. Vaya a la página de información general del recurso en [Azure Portal](https://ms.portal.azure.com/#home).
2. En el menú de la izquierda, seleccione **Claves y punto de conexión**. Usará el punto de conexión y la clave para las solicitudes de API. 

    :::image type="content" source="../../../media/azure-portal-resource-credentials.png" alt-text="Captura de pantalla que muestra la página Clave y punto de conexión en Azure Portal" lightbox="../../../media/azure-portal-resource-credentials.png":::

## <a name="import-a-project"></a>Importación de un proyecto

Para empezar, puede importar un archivo JSON de CLU en el servicio. En el inicio rápido se proporciona el siguiente JSON de ejemplo que configura un par de intenciones y entidades para una aplicación de correo electrónico denominada "EmailProject". 

Cree una solicitud **POST** con la dirección URL, los encabezados y el cuerpo JSON que se incluyen a continuación para crear el proyecto.

### <a name="request-url"></a>URL de la solicitud

Use la siguiente dirección URL al crear la solicitud de API. Reemplace los valores de los marcadores de posición por sus propios valores. 

```rest
{YOUR-ENDPOINT}/language/analyze-conversation/projects/EmailProject/:import?api-version=2021-11-01-preview
```

|Marcador de posición  |Valor  | Ejemplo |
|---------|---------|---------|
|`{YOUR-ENDPOINT}`     | Punto de conexión para autenticar la solicitud de API.   | `https://<your-custom-subdomain>.cognitiveservices.azure.com` |

### <a name="headers"></a>encabezados

Use el siguiente encabezado para autenticar la solicitud. 

|Clave|Valor|
|--|--|
|`Ocp-Apim-Subscription-Key`| Clave para el recurso. Se usa para autenticar las solicitudes de API.|

### <a name="body"></a>Body

Use el siguiente código JSON de ejemplo como cuerpo.

```json
{
    "api-version": "2021-11-01-preview",
    "metadata": {
        "name": "EmailProject",
        "description": "",
        "type": "Conversation",
        "multilingual": true,
        "language": "en-us"
    },
    "assets": {
        "intents": [
            {
                "name": "Read"
            },
            {
                "name": "Delete"
            },
            {
                "name": "Attach"
            }
        ],
        "entities": [
            {
                "name": "Sender"
            },
            {
                "name": "FileName"
            },
            {
                "name": "FileType"
            }
        ],
        "examples": [
            {
                "text": "add the pdf file with the name signed contract",
                "language": "en-us",
                "intent": "Attach",
                "entities": [
                    {
                        "entity": "FileName",
                        "offset": 31,
                        "length": 15
                    },
                    {
                        "entity": "FileType",
                        "offset": 8,
                        "length": 3
                    }
                ]
            },
            {
                "text": "attach the powerpoint file",
                "language": "en-us",
                "intent": "Attach",
                "entities": [
                    {
                        "entity": "FileType",
                        "offset": 11,
                        "length": 10
                    }
                ]
            },
            {
                "text": "attach the excel file called reports q1",
                "language": "en-us",
                "intent": "Attach",
                "entities": [
                    {
                        "entity": "FileName",
                        "offset": 29,
                        "length": 10
                    },
                    {
                        "entity": "FileType",
                        "offset": 11,
                        "length": 5
                    }
                ]
            },
            {
                "text": "move this to the deleted folder",
                "language": "en-us",
                "intent": "Delete",
                "entities": []
            },
            {
                "text": "remove this one",
                "language": "en-us",
                "intent": "Delete",
                "entities": []
            },
            {
                "text": "delete this",
                "language": "en-us",
                "intent": "Delete",
                "entities": []
            },
            {
                "text": "delete my last email from martha",
                "language": "en-us",
                "intent": "Delete",
                "entities": [
                    {
                        "entity": "Sender",
                        "offset": 26,
                        "length": 6
                    }
                ]
            },
            {
                "text": "what did the email from matt say",
                "language": "en-us",
                "intent": "Read",
                "entities": [
                    {
                        "entity": "Sender",
                        "offset": 24,
                        "length": 4
                    }
                ]
            },
            {
                "text": "read john's email for me",
                "language": "en-us",
                "intent": "Read",
                "entities": [
                    {
                        "entity": "Sender",
                        "offset": 5,
                        "length": 4
                    }
                ]
            },
            {
                "text": "read the email from carol",
                "language": "en-us",
                "intent": "Read",
                "entities": [
                    {
                        "entity": "Sender",
                        "offset": 20,
                        "length": 5
                    }
                ]
            }
        ]
    }
}
```

## <a name="start-training-your-model"></a>Inicio del entrenamiento del modelo

Una vez importado el proyecto, puede empezar a entrenar un modelo. Cree una solicitud **POST** con la dirección URL, los encabezados y el cuerpo JSON que se incluyen a continuación para iniciar el entrenamiento.

### <a name="request-url"></a>URL de la solicitud

Use la siguiente dirección URL al crear la solicitud de API. Reemplace los valores de los marcadores de posición por sus propios valores. 

```rest
{YOUR-ENDPOINT}/language/analyze-conversations/projects/EmailProject/:train?api-version=2021-11-01-preview
```

|Marcador de posición  |Valor  | Ejemplo |
|---------|---------|---------|
|`{YOUR-ENDPOINT}`     | Punto de conexión para autenticar la solicitud de API.   | `https://<your-custom-subdomain>.cognitiveservices.azure.com` |

### <a name="headers"></a>encabezados

Use el siguiente encabezado para autenticar la solicitud. 

|Clave|Valor|
|--|--|
|`Ocp-Apim-Subscription-Key`| Clave para el recurso. Se usa para autenticar las solicitudes de API.|

### <a name="request-body"></a>Cuerpo de la solicitud

Use el siguiente objeto en la solicitud. El modelo se denominará `MyModel` una vez completado el entrenamiento.  

```json
{
  "modelLabel":"MyModel",
  "RunVerification":false
}
```

Una vez que envíe la solicitud de API, recibirá una respuesta `202` que indica que se ha realizado correctamente. En los encabezados de respuesta, extraiga el valor `location`. Tendrá el formato siguiente: 

```rest
{YOUR-ENDPOINT}/language/analyze-conversations/projects/EmailProject/train/jobs/{JOB-ID}?api-version=2021-11-01-preview
``` 

`JOB-ID` se usa para identificar la solicitud, ya que esta operación es asincrónica. Usará esta dirección URL en el paso siguiente para obtener el estado del entrenamiento. 

## <a name="get-training-status"></a>Get Training Status

Use la siguiente solicitud **GET** para consultar el estado del proceso de entrenamiento del modelo. Puede usar la dirección URL que recibió en el paso anterior o reemplazar los valores de los marcadores de posición siguientes por sus propios valores. 

### <a name="request-url"></a>URL de la solicitud

```rest
{YOUR-ENDPOINT}/language/analyze-conversations/projects/EmailProject/train/jobs/{JOB-ID}?api-version=2021-11-01-preview
```

|Marcador de posición  |Valor  | Ejemplo |
|---------|---------|---------|
|`{YOUR-ENDPOINT}`     | Punto de conexión para autenticar la solicitud de API.   | `https://<your-custom-subdomain>.cognitiveservices.azure.com` |
|`{JOB-ID}`     | Id. para buscar el estado del entrenamiento del modelo. Se encuentra en el valor de encabezado `location` que recibió en el paso anterior.  | `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx` |

### <a name="headers"></a>encabezados

Use el siguiente encabezado para autenticar la solicitud. 

|Clave|Valor|
|--|--|
|`Ocp-Apim-Subscription-Key`| Clave para el recurso. Se usa para autenticar las solicitudes de API.|


### <a name="response-body"></a>Cuerpo de la respuesta

Una vez que envíe la solicitud, recibirá la siguiente respuesta. Siga sondeando este punto de conexión hasta que el parámetro **status** cambie a "succeeded". 

```json
{
    "result":
    {
          "trainedModelLabel":"MyModel",
          "trainStatus":{"percentComplete":0,"elapsedTime":null},
          "evaluationStatus":{"percentComplete":0,"elapsedTime":null}
     },
    "jobId":"{JOB-ID}",
    "createdDateTime":"{CREATED-TIME}",
    "lastUpdatedDateTime":"{UPDATED-TIME}",
    "expirationDateTime":"{EXPIRATION-TIME}",
    "status":"running"
}
```

## <a name="deploy-your-model"></a>Implementación del modelo

Una vez completado el entrenamiento, puede implementar el modelo para las predicciones. 

Cree una solicitud **POST** con la dirección URL, los encabezados y el cuerpo JSON que se incluyen a continuación para empezar a implementar un modelo de reconocimiento del lenguaje conversacional.


### <a name="request-url"></a>URL de la solicitud

```rest
{YOUR-ENDPOINT}/language/analyze-conversations/projects/EmailProject/deployments/production?api-version=2021-11-01-preview
```

|Marcador de posición  |Valor  | Ejemplo |
|---------|---------|---------|
|`{YOUR-ENDPOINT}`     | Punto de conexión para autenticar la solicitud de API.   | `https://<your-custom-subdomain>.cognitiveservices.azure.com` |


### <a name="headers"></a>encabezados

Use el siguiente encabezado para autenticar la solicitud. 

|Clave|Valor|
|--|--|
|`Ocp-Apim-Subscription-Key`| Clave para el recurso. Se usa para autenticar las solicitudes de API.|


### <a name="request-body"></a>Cuerpo de la solicitud

```json
{
  "trainedModelLabel":"MyModel",
  "deploymentName":"production"
}
```

Una vez que envíe la solicitud de API, recibirá una respuesta `202` que indica que se ha realizado correctamente. En los encabezados de respuesta, extraiga el valor `location`. Tendrá el formato siguiente: 

```rest
{YOUR-ENDPOINT}/language/analyze-conversations/projects/EmailProject/deployments/production/jobs/{JOB-ID}?api-version=2021-11-01-preview
``` 

`JOB-ID` se usa para identificar la solicitud, ya que esta operación es asincrónica. Usará esta dirección URL en el paso siguiente para obtener el estado del entrenamiento.

## <a name="get-deployment-status"></a>Obtener el estado de implementación

Use la siguiente solicitud **GET** para consultar el estado del proceso de implementación del modelo. Puede usar la dirección URL que recibió en el paso anterior o reemplazar los valores de los marcadores de posición siguientes por sus propios valores. 

### <a name="request-url"></a>URL de la solicitud

```rest
{YOUR-ENDPOINT}/language/analyze-conversations/projects/EmailProject/deployments/production/jobs/{JOB-ID}?api-version=2021-11-01-preview
```

|Marcador de posición  |Valor  | Ejemplo |
|---------|---------|---------|
|`{YOUR-ENDPOINT}`     | Punto de conexión para autenticar la solicitud de API.   | `https://<your-custom-subdomain>.cognitiveservices.azure.com` |
|`{JOB-ID}`     | Id. para buscar el estado del entrenamiento del modelo. Se encuentra en el valor de encabezado `location` que recibió en el paso anterior.  | `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx` |

### <a name="headers"></a>encabezados

Use el siguiente encabezado para autenticar la solicitud. 

|Clave|Valor|
|--|--|
|`Ocp-Apim-Subscription-Key`| Clave para el recurso. Se usa para autenticar las solicitudes de API.|


### <a name="response-body"></a>Cuerpo de la respuesta

Una vez que envíe la solicitud, recibirá la siguiente respuesta. Siga sondeando este punto de conexión hasta que el parámetro **status** cambie a "succeeded". 

```json
{
    "jobId":"{JOB-ID}",
    "createdDateTime":"{CREATED-TIME}",
    "lastUpdatedDateTime":"{UPDATED-TIME}",
    "expirationDateTime":"{EXPIRATION-TIME}",
    "status":"running"
}
```

## <a name="query-model"></a>Consulta del modelo

Una vez que la implementación se realice correctamente, puede empezar a consultar el proyecto para obtener predicciones. 

Cree una solicitud **POST** con la dirección URL, los encabezados y el cuerpo JSON que se incluyen a continuación para empezar a implementar un modelo de reconocimiento del lenguaje conversacional.

### <a name="request-url"></a>URL de la solicitud

```rest
{YOUR-ENDPOINT}/language/:analyze-conversations?projectName=EmailProject&deploymentName=production&api-version=2021-11-01-preview
```

|Marcador de posición  |Valor  | Ejemplo |
|---------|---------|---------|
|`{YOUR-ENDPOINT}`     | Punto de conexión para autenticar la solicitud de API.   | `https://<your-custom-subdomain>.cognitiveservices.azure.com` |

### <a name="headers"></a>encabezados

Use el siguiente encabezado para autenticar la solicitud. 

|Clave|Valor|
|--|--|
|`Ocp-Apim-Subscription-Key`| Clave para el recurso. Se usa para autenticar las solicitudes de API.|

### <a name="request-body"></a>Cuerpo de la solicitud

```json
{
  "query":"attach a docx file"
}
```

### <a name="response-body"></a>Cuerpo de la respuesta

Una vez que envíe la solicitud, recibirá la siguiente respuesta para la predicción.

```json
{
    "query":"attach a docx file",
    "prediction": {
        "topIntent":"Attach",
        "projectKind":"conversation",
        "intents":[{"category":"Attach","confidenceScore":0.9998592},{"category":"Read","confidenceScore":0.00010551753},{"category":"Delete","confidenceScore":3.5209276E-05}],
        "entities":[{"category":"FileType","text":"docx","offset":9,"length":4,"confidenceScore":1}]
     }
}
```
