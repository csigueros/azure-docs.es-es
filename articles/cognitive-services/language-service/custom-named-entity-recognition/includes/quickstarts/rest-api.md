---
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: include
ms.date: 11/02/2021
ms.author: aahi
ms.custom: ignite-fall-2021
ms.openlocfilehash: ed3824491e043ea99637604e76d83612d1ad6764
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2021
ms.locfileid: "131520437"
---
## <a name="prerequisites"></a>Prerrequisitos

* Una suscripción a Azure: [cree una cuenta gratuita](https://azure.microsoft.com/free/cognitive-services)

## <a name="create-a-new-azure-resource-and-azure-blob-storage-account"></a>Creación de un recurso de Azure y una cuenta de Azure Blob Storage

Para poder usar NER personalizado, deberá crear un recurso de idioma de Azure, que le dará las credenciales necesarias para crear un proyecto y empezar a entrenar un modelo. También necesitará una cuenta de almacenamiento de Azure, donde pueda cargar el conjunto de datos que se usará para compilar el modelo.

> [!IMPORTANT]
> Para empezar a trabajar rápidamente, se recomienda crear un recurso de idioma de Azure mediante los pasos que se indican a continuación, que le permitirán crear el recurso y configurar una cuenta de almacenamiento al mismo tiempo, lo que resulta más fácil que hacerlo luego.
>
> Si tiene un recurso existente que le gustaría usar, deberá configurar este y la cuenta de almacenamiento por separado. Consulte [el artículo sobre creación de proyectos](../../how-to/create-project.md#using-a-pre-existing-azure-resource) para obtener información.

1. Vaya a [Azure Portal](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics) y cree un recurso de idioma de Azure. Si se le pide que seleccione características adicionales, seleccione **Custom text classification & custom NER** (Clasificación de texto personalizada y NER personalizado). Al crear el recurso, asegúrese de que tiene los parámetros siguientes.

    |Requisito del recurso de Azure  |Valor obligatorio  |
    |---------|---------|
    |Location | Oeste de EE. UU. 2 u Oeste de Europa         |
    |Plan de tarifa     | Plan de tarifa estándar (**S**)        |

2. En la sección **Custom Named Entity Recognition (NER) & Custom Classification (Preview)** (Reconocimiento de entidades con nombre (NER) personalizado y clasificación personalizada [versión preliminar]), seleccione una cuenta de almacenamiento existente o elija **Create a new storage account** (Crear una cuenta de almacenamiento). Tenga en cuenta que estos valores son para este inicio rápido y no necesariamente los [valores de la cuenta de almacenamiento](/azure/storage/common/storage-account-overview) que querrá usar en entornos de producción.

    |Valor de la cuenta de almacenamiento  |Valor recomendado  |
    |---------|---------|
    | Nombre | Cualquier nombre |
    | Rendimiento | Estándar |
    | Tipo de cuenta| Storage (de uso general v1) |
    | Replicación | Almacenamiento con redundancia local (LRS)
    |Location | Cualquier ubicación más cercana a usted, para obtener la mejor latencia.        |


## <a name="upload-sample-data-to-blob-container"></a>Carga de los datos de ejemplo en el contenedor de blobs

Después de crear una cuenta de almacenamiento de Azure y vincularla al recurso de idioma, deberá cargar los archivos de ejemplo de este inicio rápido. Estos archivos se usarán más adelante para entrenar el modelo.

1. [Descargue los datos de ejemplo](https://go.microsoft.com/fwlink/?linkid=2175226) de este inicio rápido desde GitHub:

2. Vaya a la cuenta de almacenamiento de Azure en [Azure Portal](https://ms.portal.azure.com). Vaya a su cuenta y cargue los datos de ejemplo en ella.

El conjunto de datos de ejemplo proporcionado contiene 20 contratos de préstamo, cada uno con dos partes: un prestamista y un prestatario. Puede usar el archivo de etiquetas de ejemplo proporcionado para extraer información pertinente de ambas partes, una fecha de contrato, un importe del préstamo y una tasa de interés.

### <a name="get-your-resource-keys-endpoint"></a>Obtención del punto de conexión y las claves del recurso

* Vaya a la página de información general del recurso en [Azure Portal](https://ms.portal.azure.com/#home).

* En el menú de la izquierda de la pantalla, seleccione **Keys and Endpoint** (Claves y punto de conexión). Utilice el punto de conexión para las solicitudes de API y necesitará la clave para el encabezado `Ocp-Apim-Subscription-Key`.
:::image type="content" source="../../../media/azure-portal-resource-credentials.png" alt-text="Captura de pantalla que muestra la pantalla de clave y punto de conexión de un recurso de Azure." lightbox="../../../media/azure-portal-resource-credentials.png":::

## <a name="create-a-custom-ner-project"></a>Creación de un proyecto de NER personalizado

Una vez configurados el recurso y el contenedor de almacenamiento, cree un nuevo proyecto de NER personalizado. Un proyecto es un área de trabajo para crear modelos de inteligencia artificial personalizados basados en sus datos. A su proyecto solo puede acceder usted y otros usuarios que tengan acceso de colaborador al recurso de Azure que se usa.

> [!NOTE]
> El nombre del proyecto distingue mayúsculas de minúsculas para todas las operaciones.

Cree una solicitud **POST** con la dirección URL, los encabezados y el cuerpo JSON que se incluyen a continuación para crear el proyecto e importar el archivo de etiquetas.

Use la siguiente dirección URL para crear un proyecto e importar el archivo de etiquetas. Reemplace los valores de los marcadores de posición por sus propios valores. 

```rest
{YOUR-ENDPOINT}/language/analyze-text/projects/{projectName}/:import. 
```

|Marcador de posición  |Valor  | Ejemplo |
|---------|---------|---------|
|`{YOUR-ENDPOINT}`     | Punto de conexión para autenticar la solicitud de API.   | `https://<your-custom-subdomain>.cognitiveservices.azure.com` |

### <a name="parameters"></a>Parámetros

Pase el parámetro siguiente con su solicitud. 

|Clave|Explicación|Value|
|--|--|--|
|`api-version`| La versión de la API usada.| `2021-11-01-preview` |

Para pasar el parámetro, agregue `?api-version=2021-11-01-preview` al final de la dirección URL de la solicitud.

### <a name="headers"></a>encabezados

Use el siguiente encabezado para autenticar la solicitud. 

|Clave|Valor|
|--|--|
|`Ocp-Apim-Subscription-Key`| Clave para el recurso. Se usa para autenticar las solicitudes de API.|

### <a name="body"></a>Body

Utilice el siguiente código JSON en la solicitud. Reemplace los valores de los marcadores de posición por sus propios valores. Uso del archivo de etiquetas disponible en la pestaña de [datos de ejemplo](https://github.com/Azure-Samples/cognitive-services-sample-data-files)

```json
{
    "api-version": "2021-11-01-preview",
    "metadata": {
        "name": "MyProject",
        "multiLingual": true,
        "description": "Tryong out custom text classification",
        "modelType": "Extraction",
        "language": "string",
        "storageInputContainerName": "YOUR-CONTAINER-NAME",
        "settings": {}
    },
    "assets": {
        "extractors": [
        {
            "name": "Entity1"
        },
        {
            "name": "Entity2"
        }
    ],
    "documents": [
        {
            "location": "doc1.txt",
            "language": "en-us",
            "extractors": [
                {
                    "regionOffset": 0,
                    "regionLength": 500,
                    "labels": [
                        {
                            "extractorName": "Entity1",
                            "offset": 25,
                            "length": 10
                        },                    
                        {
                            "extractorName": "Entity2",
                            "offset": 120,
                            "length": 8
                        }
                    ]
                }
            ]
        },
        {
            "location": "doc2.txt",
            "language": "en-us",
            "extractors": [
                {
                    "regionOffset": 0,
                    "regionLength": 100,
                    "labels": [
                        {
                            "extractorName": "Entity2",
                            "offset": 20,
                            "length": 5
                        }
                    ]
                }
            ]
        }
    ]
    }
}
```
Para la clave de metadatos: 

|Clave  |Valor  | Ejemplo |
|---------|---------|---------|
| `modelType  `    | Tipo del modelo. | Extracción |
|`storageInputContainerName`   | Nombre del contenedor de Azure Blob Storage.   | `myContainer` |

Esta solicitud devolverá un error si:

* El recurso seleccionado no tiene el permiso adecuado para la cuenta de almacenamiento. 

## <a name="start-training-your-model"></a>Inicio del entrenamiento del modelo

Una vez creado el proyecto, puede empezar a entrenar un modelo de NER personalizado. Cree una solicitud **POST** con la dirección URL, los encabezados y el cuerpo JSON que se incluyen a continuación para empezar a entrenar un modelo de NER.
Cree una solicitud **POST** con la dirección URL, los encabezados y el cuerpo JSON que se incluyen a continuación para empezar a entrenar un modelo de clasificación de texto.

### <a name="request-url"></a>URL de la solicitud

Use la siguiente dirección URL al crear la solicitud de API. Reemplace los valores de los marcadores de posición por sus propios valores. 

```rest
{YOUR-ENDPOINT}/language/analyze-text/projects/{PROJECT-NAME}/:train
```

|Marcador de posición  |Valor  | Ejemplo |
|---------|---------|---------|
|`{YOUR-ENDPOINT}`     | Punto de conexión para autenticar la solicitud de API.   | `https://<your-custom-subdomain>.cognitiveservices.azure.com` |
|`{PROJECT-NAME}`     | Nombre del proyecto. Este valor distingue mayúsculas de minúsculas.  | `myProject` |

### <a name="parameters"></a>Parámetros

Pase el parámetro siguiente con su solicitud.

|Clave|Explicación|Value|
|--|--|--|
|`api-version`| La versión de la API usada.| `2021-11-01-preview` |

Para pasar el parámetro, agregue `?api-version=2021-11-01-preview` al final de la dirección URL de la solicitud.

### <a name="headers"></a>encabezados

Use el siguiente encabezado para autenticar la solicitud. 

|Clave|Valor|
|--|--|
|`Ocp-Apim-Subscription-Key`| Clave para el recurso. Se usa para autenticar las solicitudes de API.|

### <a name="request-body"></a>Cuerpo de la solicitud

Utilice el siguiente código JSON en la solicitud. El modelo se denominará `MyModel` una vez completado el entrenamiento.  

```json
{
  "modelLabel": "MyModel",
  "runValidation": true
}
```

|Clave  |Valor  | Ejemplo |
|---------|---------|---------|
|`modelLabel  `    | Nombre del modelo.   | MyModel |
|`runValidation`     | Valor booleano para ejecutar la validación en el conjunto de pruebas.   | True |

Una vez que envíe la solicitud de API, recibirá una respuesta `202` que indica que se ha realizado correctamente. En los encabezados de respuesta, extraiga el valor `location`. Tendrá el formato siguiente: 

```rest
{YOUR-ENDPOINT}/language/analyze-text/projects/{YOUR-PROJECT-NAME}/train/jobs/{JOB-ID}?api-version=xxxx-xx-xx-xxxxxxx
``` 

`JOB-ID` se usa para identificar la solicitud, ya que esta operación es asincrónica. Usará esta dirección URL en el paso siguiente para obtener el estado del entrenamiento. 

## <a name="get-training-status"></a>Get Training Status

Use la siguiente solicitud **GET** para consultar el estado del proceso de entrenamiento del modelo. Puede usar la dirección URL que recibió en el paso anterior o reemplazar los valores de los marcadores de posición siguientes por sus propios valores. 

```rest
{YOUR-ENDPOINT}/language/analyze-text/projects/{YOUR-PROJECT-NAME}/train/jobs/{JOB-ID}
```

|Marcador de posición  |Valor  | Ejemplo |
|---------|---------|---------|
|`{YOUR-ENDPOINT}`     | Punto de conexión para autenticar la solicitud de API.   | `https://<your-custom-subdomain>.cognitiveservices.azure.com` |
|`{PROJECT-NAME}`     | Nombre del proyecto. Este valor distingue mayúsculas de minúsculas.  | `myProject` |
|`{JOB-ID}`     | Id. para buscar el estado del entrenamiento del modelo. Se encuentra en el valor de encabezado `location` que recibió en el paso anterior.  | `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx` |

### <a name="parameters"></a>Parámetros

Pase el parámetro siguiente con su solicitud. 

|Clave|Explicación|Value|
|--|--|--|
|`api-version`| La versión de la API usada.| `2021-11-01-preview` |

Para pasar el parámetro, agregue `?api-version=2021-11-01-preview` al final de la dirección URL de la solicitud.

### <a name="headers"></a>encabezados

Use el siguiente encabezado para autenticar la solicitud. 

|Clave|Valor|
|--|--|
|`Ocp-Apim-Subscription-Key`| Clave para el recurso. Se usa para autenticar las solicitudes de API.|

### <a name="response-body"></a>Cuerpo de la respuesta

Una vez que envíe la solicitud, recibirá la siguiente respuesta. 

```json
{
  "jobs": [
    {
      "result": {
        "trainedModelLabel": "MyModel",
        "trainStatus": {
          "percentComplete": 0,
          "elapsedTime": "string"
        },
        "evaluationStatus": {
          "percentComplete": 0,
          "elapsedTime": "string"
        }
      },
      "jobId": "string",
      "createdDateTime": "2021-10-19T23:24:41.572Z",
      "lastUpdatedDateTime": "2021-10-19T23:24:41.572Z",
      "expirationDateTime": "2021-10-19T23:24:41.572Z",
      "status": "unknown",
      "errors": [
        {
          "code": "unknown",
          "message": "string"
        }
      ]
    }
  ],
  "nextLink": "string"
}
```
## <a name="deploy-your-model"></a>Implementación del modelo

Cree una solicitud **PUT** con la dirección URL, los encabezados y el cuerpo JSON que se incluyen a continuación para empezar a implementar un modelo de NER personalizado.

```rest
{YOUR-ENDPOINT}/language/analyze-text/projects/{PROJECT-NAME}/deployments/{DEPLOYMENT-NAME}
```

|Marcador de posición  |Valor  | Ejemplo |
|---------|---------|---------|
|`{YOUR-ENDPOINT}`     | Punto de conexión para autenticar la solicitud de API.   | `https://<your-custom-subdomain>.cognitiveservices.azure.com` |
|`{PROJECT-NAME}`     | Nombre del proyecto. Este valor distingue mayúsculas de minúsculas.  | `myProject` |
|`{DEPLOYMENT-NAME}`     | Nombre de la implementación. Este valor distingue mayúsculas de minúsculas.  | `prod` |

### <a name="parameters"></a>Parámetros

Pase el parámetro siguiente con su solicitud. 

|Clave|Explicación|Value|
|--|--|--|
|`api-version`| La versión de la API usada.| `2021-11-01-preview` |

Para pasar el parámetro, agregue `?api-version=2021-11-01-preview` al final de la dirección URL de la solicitud.

### <a name="headers"></a>encabezados

Use el siguiente encabezado para autenticar la solicitud. 

|Clave|Valor|
|--|--|
|`Ocp-Apim-Subscription-Key`| Clave para el recurso. Se usa para autenticar las solicitudes de API.|

### <a name="request-body"></a>Cuerpo de la solicitud

Utilice el siguiente código JSON en la solicitud. El modelo se denominará `MyModel` una vez completado el entrenamiento.  

```json
{
{
  "trainedModelLabel": "MyModel"
}
```

Una vez que envíe la solicitud de API, recibirá una respuesta `202` que indica que se ha realizado correctamente. En los encabezados de respuesta, extraiga el valor `location`. Tendrá el formato siguiente: 

```rest
{YOUR-ENDPOINT}/language/analyze-text/projects/{YOUR-PROJECT-NAME}/deployments/{DEPLOYMENT-NAME}/jobs/{JOB-ID}?api-version=xxxx-xx-xx-xxxxxxx
``` 

`JOB-ID` se usa para identificar la solicitud, ya que esta operación es asincrónica. Usará esta dirección URL en el paso siguiente para obtener el estado de publicación.

## <a name="get-the-deployment-status"></a>Obtención del estado de la implementación

Use la siguiente solicitud **GET** para consultar el estado del proceso de publicación del modelo. Puede usar la dirección URL que recibió en el paso anterior o reemplazar los valores de los marcadores de posición siguientes por sus propios valores. 

```rest
{YOUR-ENDPOINT}/language/analyze-text/projects/{YOUR-PROJECT-NAME}/deployments/{DEPLOYMENT-NAME}/jobs/{JOB-ID}
```

|Marcador de posición  |Valor  | Ejemplo |
|---------|---------|---------|
|`{YOUR-ENDPOINT}`     | Punto de conexión para autenticar la solicitud de API.   | `https://<your-custom-subdomain>.cognitiveservices.azure.com` |
|`{PROJECT-NAME}`     | Nombre del proyecto. Este valor distingue mayúsculas de minúsculas.  | `myProject` |
|`{DEPLOYMENT-NAME}`     | Nombre de la implementación. Este valor distingue mayúsculas de minúsculas.  | `prod` |
|`{JOB-ID}`     | Id. para buscar el estado del entrenamiento del modelo. Se encuentra en el valor de encabezado `location` que recibió en el paso anterior.  | `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx` |

### <a name="parameters"></a>Parámetros

Pase el parámetro siguiente con su solicitud. 

|Clave|Explicación|Value|
|--|--|--|
|`api-version`| La versión de la API usada.| `2021-11-01-preview` |

Para pasar el parámetro, agregue `?api-version=2021-11-01-preview` al final de la dirección URL de la solicitud.

### <a name="headers"></a>encabezados

Use el siguiente encabezado para autenticar la solicitud. 

|Clave|Valor|
|--|--|
|`Ocp-Apim-Subscription-Key`| Clave para el recurso. Se usa para autenticar las solicitudes de API.|


### <a name="submit-custom-ner-task"></a>Envío de una tarea de NER personalizado

Ahora que el modelo está implementado, puede empezar a enviarle tareas de reconocimiento de entidades. 

> [!NOTE]
> El nombre de proyecto distingue mayúsculas de minúsculas.

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

### <a name="response-body"></a>Cuerpo de la respuesta

La respuesta será un documento JSON con los parámetros siguientes.

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
                    "validDocumentsCount":0,
                    "erroneousDocumentsCount":0,
                    "transactionsCount":0
                }
                    }
                }
            ]
        }
    }
```

## <a name="clean-up-resources"></a>Limpieza de recursos

Cuando ya no necesite el proyecto, puede eliminarlo con la siguiente solicitud **DELETE**. Reemplace los valores de los marcadores de posición por sus propios valores.   

```rest
{YOUR-ENDPOINT}/language/text/authoring/v1.0-preview.2/projects/{PROJECT-NAME}
```

|Marcador de posición  |Valor  | Ejemplo |
|---------|---------|---------|
|`{YOUR-ENDPOINT}`     | Punto de conexión para autenticar la solicitud de API.   | `https://<your-custom-subdomain>.cognitiveservices.azure.com` |
|`{PROJECT-NAME}`     | Nombre del proyecto. Este valor distingue mayúsculas de minúsculas.  | `myProject` |

### <a name="headers"></a>encabezados

Use el siguiente encabezado para autenticar la solicitud. 

|Clave|Valor|
|--|--|
|Ocp-Apim-Subscription-Key| Clave para el recurso. Se usa para autenticar las solicitudes de API.|
