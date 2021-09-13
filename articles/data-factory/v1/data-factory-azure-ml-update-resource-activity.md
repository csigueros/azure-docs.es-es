---
title: Actualización de los modelos de Machine Learning con Azure Data Factory
description: En este artículo se describe cómo crear canalizaciones predictivas con Azure Data Factory v1 y ML Studio (clásico).
author: dcstwh
ms.author: weetok
ms.reviewer: jburchel
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/22/2018
ms.openlocfilehash: 42a1318ffb4c0063875939c8d3633ea513818ba4
ms.sourcegitcommit: ddac53ddc870643585f4a1f6dc24e13db25a6ed6
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/18/2021
ms.locfileid: "122397075"
---
# <a name="updating-ml-studio-classic-models-using-update-resource-activity"></a>Actualización de los modelos de ML Studio (clásico) mediante la actividad de actualización de recurso

> [!div class="op_single_selector" title1="Actividades de transformación"]
> * [Actividad de Hive](data-factory-hive-activity.md) 
> * [Actividad de Pig](data-factory-pig-activity.md)
> * [Actividad MapReduce](data-factory-map-reduce.md)
> * [Actividad de streaming de Hadoop](data-factory-hadoop-streaming-activity.md)
> * [Actividad de Spark](data-factory-spark.md)
> * [Actividad de ejecución por lotes de ML Studio (clásico)](data-factory-azure-ml-batch-execution-activity.md)
> * [Actividad de actualización de recurso de ML Studio (clásico)](data-factory-azure-ml-update-resource-activity.md)
> * [Actividad de procedimiento almacenado](data-factory-stored-proc-activity.md)
> * [Actividad U-SQL de Data Lake Analytics](data-factory-usql-activity.md)
> * [Actividad personalizada de .NET](data-factory-use-custom-activities.md)


> [!NOTE]
> Este artículo se aplica a la versión 1 de Data Factory. Si utiliza la versión actual del servicio Data Factory, consulte [actualización de los modelos de aprendizaje automático en Data Factory](../update-machine-learning-models.md).

Este artículo complementa el artículo de integración principal Azure Data Factory - ML Studio (clásico): [Creación de canalizaciones predictivas con ML Studio (clásico) y Azure Data Factory](data-factory-azure-ml-batch-execution-activity.md). Si aún no lo ha hecho, revise el artículo principal antes de leer este artículo. 

## <a name="overview"></a>Información general
Con el tiempo, los modelos predictivos de los experimentos de puntuación de ML Studio (clásico) se tienen que volver a entrenar con nuevos conjuntos de datos de entrada. Después de terminar con el nuevo entrenamiento, tendrá que actualizar el servicio web de puntuación con el modelo de Aprendizaje automático que volvió a entrenar. Los pasos más comunes para habilitar el nuevo entrenamiento y actualizar los modelos de Studio (clásico) mediante los servicios web son:

1. Crear un experimento en [ML Studio (clásico)](https://studio.azureml.net).
2. Cuando esté satisfecho con el modelo, use ML Studio (clásico) para publicar servicios web para el **experimento de entrenamiento** y el **experimento predictivo o de puntuación**.

En la tabla siguiente se describen los servicios web empleados en este ejemplo.  Consulte [Nuevo entrenamiento de modelos de ML Studio (clásico) mediante programación](../../machine-learning/classic/retrain-machine-learning-model.md) para más información.

- **Servicio web de entrenamiento**: recibe datos de entrenamiento y genera modelos entrenados. El resultado del nuevo entrenamiento es un archivo .ilearner en Blob Storage de Azure. El **punto de conexión predeterminado** se crea automáticamente para cuando publique el experimento de entrenamiento como un servicio web. Se pueden crear más puntos de conexión, pero el ejemplo usa solo el predeterminado.
- **Servicio web de puntuación**: recibe ejemplos de datos sin etiqueta y realiza predicciones. El resultado de la predicción puede presentarse en diversas formas, como un archivo .csv o las filas de una base de datos de Azure SQL Database, según la configuración del experimento. El punto de conexión predeterminado se crea automáticamente cuando se publica el experimento predictivo como un servicio web. 

La siguiente imagen muestra la relación entre los puntos de conexión de entrenamiento y de puntuación de ML Studio (clásico).

![servicios Web](./media/data-factory-azure-ml-batch-execution-activity/web-services.png)

Puede invocar el **servicio web de entrenamiento** a través de la **actividad de ejecución por lotes de ML Studio (clásico)** . La invocación de un servicio web de entrenamiento es lo mismo que invocar un servicio web de ML Studio (clásico) (servicio web de puntuación) para puntuar datos. Las secciones anteriores abarcan cómo invocar un servicio web de ML Studio (clásico) a partir de una canalización de Azure Data Factory. 

Puede invocar el **servicio web de puntuación** a través de la **actividad de actualización de recurso de ML Studio (clásico)** para actualizar el servicio web con el modelo recién entrenado. Los ejemplos siguientes proporcionan definiciones de servicios vinculados: 

## <a name="scoring-web-service-is-a-classic-web-service"></a>El servicio web de puntuación es un servicio web clásico
Si el servicio web de puntuación es un **servicio web clásico**, cree el segundo **punto de conexión no predeterminado y actualizable** mediante Azure Portal. Para conocer los pasos necesarios para ello, consulte el artículo [Creación de puntos de conexión](../../machine-learning/classic/create-endpoint.md). Después de crear el punto de conexión actualizable no predeterminado, realice los siguientes pasos:

* Haga clic en **EJECUCIÓN DE LOTES** para obtener el valor del URI para la propiedad JSON **mlEndpoint**.
* Haga clic en vínculo **ACTUALIZAR RECURSO** para obtener el valor de URI para la propiedad JSON **updateResourceEndpoint**. La clave de API está en la página de punto de conexión (en la esquina inferior derecha).

![punto de conexión actualizable](./media/data-factory-azure-ml-batch-execution-activity/updatable-endpoint.png)

En el ejemplo siguiente se proporciona una definición de JSON de ejemplo para el servicio vinculado de AzureML. El servicio vinculado utiliza apiKey para la autenticación.  

```json
{
    "name": "updatableScoringEndpoint2",
    "properties": {
        "type": "AzureML",
        "typeProperties": {
            "mlEndpoint": "https://ussouthcentral.services.azureml.net/workspaces/xxx/services/--scoring experiment--/jobs",
            "apiKey": "endpoint2Key",
            "updateResourceEndpoint": "https://management.azureml.net/workspaces/xxx/webservices/--scoring experiment--/endpoints/endpoint2"
        }
    }
}
```

## <a name="scoring-web-service-is-azure-resource-manager-web-service"></a>El servicio web de puntuación es el servicio web Azure Resource Manager 
Si el servicio web es el nuevo tipo de servicio web que expone un punto de conexión de Azure Resource Manager, no es preciso agregar el segundo punto de conexión **no predeterminado**. En el servicio vinculado, **updateResourceEndpoint** tiene el formato: 

```
https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resource-group-name}/providers/Microsoft.MachineLearning/webServices/{web-service-name}?api-version=2016-05-01-preview. 
```

Puede obtener valores para los marcadores de posición en la dirección URL al consultar el servicio web en el [Portal de servicios web de ML Studio (clásico)](https://services.azureml.net/). El nuevo tipo de punto de conexión del recurso de actualización requiere un token AAD (Azure Active Directory). Especifique **servicePrincipalId** y **servicePrincipalKey** en el servicio vinculado Studio (clásico). Consulte [Uso del portal para crear una aplicación de Active Directory y una entidad de servicio con acceso a los recursos](../../active-directory/develop/howto-create-service-principal-portal.md). Esta es la definición de un servicio vinculado AzureML de ejemplo: 

```json
{
    "name": "AzureMLLinkedService",
    "properties": {
        "type": "AzureML",
        "description": "The linked service for AML web service.",
        "typeProperties": {
            "mlEndpoint": "https://ussouthcentral.services.azureml.net/workspaces/0000000000000000000000000000000000000/services/0000000000000000000000000000000000000/jobs?api-version=2.0",
            "apiKey": "xxxxxxxxxxxx",
            "updateResourceEndpoint": "https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myRG/providers/Microsoft.MachineLearning/webServices/myWebService?api-version=2016-05-01-preview",
            "servicePrincipalId": "000000000-0000-0000-0000-0000000000000",
            "servicePrincipalKey": "xxxxx",
            "tenant": "mycompany.com"
        }
    }
}
```

El escenario siguiente proporciona más detalles. Tiene un ejemplo para volver a entrenar y actualizar modelos de Studio (clásico) a partir de una canalización de Azure Data Factory.

## <a name="scenario-retraining-and-updating-a-studio-classic-model"></a>Escenario: entrenamiento y actualización de un modelo de Studio (clásico)
Esta sección proporciona una canalización de ejemplo que usa la **actividad de ejecución por lotes de ML Studio** para volver a entrenar un modelo. La canalización usa también la **actividad de actualización de recurso de ML Studio (clásico)** para actualizar el modelo en el servicio web de puntuación. La sección también proporciona fragmentos JSON para todos los servicios vinculados, conjuntos de datos y canalización en el ejemplo.

Esta es la vista de diagrama de la canalización de ejemplo. Como se puede apreciar, la actividad de ejecución de lotes de Studio (clásico) toma la entrada de entrenamiento y genera una salida de entrenamiento (archivo iLearner). La actividad de actualización de recurso de Studio (clásico) toma esta salida de entrenamiento y actualiza el modelo en el punto de conexión de servicio web de puntuación. La Actividad de recursos de actualización no produce ningún resultado. El placeholderBlob es simplemente un conjunto de datos de salida ficticio que el servicio de Azure Data Factory necesita para ejecutar la canalización.

![diagrama de canalización](./media/data-factory-azure-ml-batch-execution-activity/update-activity-pipeline-diagram.png)

### <a name="azure-blob-storage-linked-service"></a>Servicio vinculado de almacenamiento de blobs de Azure:
Azure Storage contiene los siguientes datos:

* datos de aprendizaje. Los datos de entrada para el servicio web de entrenamiento de Studio (clásico).  
* archivo iLearner. La salida del servicio web de entrenamiento de Studio (clásico). Este archivo también es la entrada para la actividad Actualizar recurso.  

Esta es la definición de JSON de ejemplo del servicio vinculado:

```JSON
{
    "name": "StorageLinkedService",
      "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=name;AccountKey=key"
        }
    }
}
```

### <a name="training-input-dataset"></a>Conjunto de datos de entrada de entrenamiento:
El siguiente conjunto de datos representa los datos de entrenamiento de entrada para el servicio web de entrenamiento de Studio (clásico). La actividad de ejecución de lotes de Studio (clásico) toma este conjunto de datos como entrada.

```JSON
{
    "name": "trainingData",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "folderPath": "labeledexamples",
            "fileName": "labeledexamples.arff",
            "format": {
                "type": "TextFormat"
            }
        },
        "availability": {
            "frequency": "Week",
            "interval": 1
        },
        "policy": {          
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

### <a name="training-output-dataset"></a>Conjunto de datos de salida de entrenamiento:
El siguiente conjunto de datos representa el archivo iLearner de salida del servicio web de entrenamiento de ML Studio (clásico). La actividad de ejecución por lotes de ML Studio (clásico) produce este conjunto de datos. Este conjunto de datos es también la entrada para la actividad de actualización de recurso de ML Studio (clásico).

```JSON
{
    "name": "trainedModelBlob",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "folderPath": "trainingoutput",
            "fileName": "model.ilearner",
            "format": {
                "type": "TextFormat"
            }
        },
        "availability": {
            "frequency": "Week",
            "interval": 1
        }
    }
}
```

### <a name="linked-service-for-studio-classic-training-endpoint"></a>Servicio vinculado al punto de conexión de entrenamiento de Studio (clásico)
El siguiente fragmento JSON define un servicio vinculado de Studio (clásico) que apunta al punto de conexión predeterminado del servicio web de entrenamiento.

```JSON
{    
    "name": "trainingEndpoint",
      "properties": {
        "type": "AzureML",
        "typeProperties": {
            "mlEndpoint": "https://ussouthcentral.services.azureml.net/workspaces/xxx/services/--training experiment--/jobs",
              "apiKey": "myKey"
        }
      }
}
```

En **ML Studio (clásico)** , haga lo siguiente para obtener los valores de **mlEndpoint** y **apiKey**:

1. Haga clic en **SERVICIOS WEB** en el menú de la izquierda.
2. En la lista de servicios web, haga clic en el **servicio web de entrenamiento** .
3. Haga clic en Copiar junto al cuadro de texto **Clave de API** . Pegue la clave del portapapeles en el editor de JSON de Data Factory.
4. En **ML Studio (clásico)** , haga clic en el vínculo **EJECUCIÓN DE LOTES**.
5. Copie el **URI de solicitud** de la sección **Solicitar** y péguelo en el editor de JSON de Data Factory.   

### <a name="linked-service-for-studio-classic-updatable-scoring-endpoint"></a>Servicio vinculado para el punto de conexión de puntuación actualizable de Studio (clásico):
El siguiente fragmento JSON define un servicio vinculado de Studio (clásico) que apunta al punto de conexión no predeterminado y actualizable del servicio web de puntuación.  

```JSON
{
    "name": "updatableScoringEndpoint2",
    "properties": {
        "type": "AzureML",
        "typeProperties": {
            "mlEndpoint": "https://ussouthcentral.services.azureml.net/workspaces/00000000eb0abe4d6bbb1d7886062747d7/services/00000000026734a5889e02fbb1f65cefd/jobs?api-version=2.0",
            "apiKey": "sooooooooooh3WvG1hBfKS2BNNcfwSO7hhY6dY98noLfOdqQydYDIXyf2KoIaN3JpALu/AKtflHWMOCuicm/Q==",
            "updateResourceEndpoint": "https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Default-MachineLearning-SouthCentralUS/providers/Microsoft.MachineLearning/webServices/myWebService?api-version=2016-05-01-preview",
            "servicePrincipalId": "fe200044-c008-4008-a005-94000000731",
            "servicePrincipalKey": "zWa0000000000Tp6FjtZOspK/WMA2tQ08c8U+gZRBlw=",
            "tenant": "mycompany.com"
        }
    }
}
```

### <a name="placeholder-output-dataset"></a>Conjunto de datos de salida de marcador de posición:
La actividad de actualización de recurso de Studio (clásico) no genera ningún resultado. Sin embargo, Azure Data Factory requiere un conjunto de datos de salida para impulsar la programación de una canalización. Por lo tanto, utilizamos un conjunto de datos ficticio o un marcador de posición en este ejemplo.  

```JSON
{
    "name": "placeholderBlob",
    "properties": {
        "availability": {
            "frequency": "Week",
            "interval": 1
        },
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "folderPath": "any",
            "format": {
                "type": "TextFormat"
            }
        }
    }
}
```

### <a name="pipeline"></a>Canalización
La canalización tiene dos actividades: **AzureMLBatchExecution** y **AzureMLUpdateResource**. La actividad de ejecución por lotes de ML Studio (clásico) toma los datos de entrenamiento como entrada y genera como salida un archivo iLearner. La actividad invoca el servicio web de entrenamiento (el experimento de entrenamiento expuesto como servicio web) con los datos de entrenamiento de entrada y recibe el archivo ilearner desde el servicio web. El placeholderBlob es simplemente un conjunto de datos de salida ficticio que el servicio de Azure Data Factory necesita para ejecutar la canalización.

![diagrama de canalización](./media/data-factory-azure-ml-batch-execution-activity/update-activity-pipeline-diagram.png)

```JSON
{
    "name": "pipeline",
    "properties": {
        "activities": [
            {
                "name": "retraining",
                "type": "AzureMLBatchExecution",
                "inputs": [
                    {
                        "name": "trainingData"
                    }
                ],
                "outputs": [
                    {
                        "name": "trainedModelBlob"
                    }
                ],
                "typeProperties": {
                    "webServiceInput": "trainingData",
                    "webServiceOutputs": {
                        "output1": "trainedModelBlob"
                    }              
                 },
                "linkedServiceName": "trainingEndpoint",
                "policy": {
                    "concurrency": 1,
                    "executionPriorityOrder": "NewestFirst",
                    "retry": 1,
                    "timeout": "02:00:00"
                }
            },
            {
                "type": "AzureMLUpdateResource",
                "typeProperties": {
                    "trainedModelName": "Training Exp for ADF ML [trained model]",
                    "trainedModelDatasetName" :  "trainedModelBlob"
                },
                "inputs": [
                    {
                        "name": "trainedModelBlob"
                    }
                ],
                "outputs": [
                    {
                        "name": "placeholderBlob"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1,
                    "retry": 3
                },
                "name": "AzureML Update Resource",
                "linkedServiceName": "updatableScoringEndpoint2"
            }
        ],
        "start": "2016-02-13T00:00:00Z",
           "end": "2016-02-14T00:00:00Z"
    }
}
```