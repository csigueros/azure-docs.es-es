---
title: Creación de canalizaciones de datos predictivas con Azure Data Factory
description: Describe cómo crear canalizaciones predictivas con Azure Data Factory y ML Studio (clásico)
author: dcstwh
ms.author: weetok
ms.reviewer: jburchel
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/22/2018
ms.openlocfilehash: a613a48f1787c456885aabb0012efb4d71829aae
ms.sourcegitcommit: ddac53ddc870643585f4a1f6dc24e13db25a6ed6
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/18/2021
ms.locfileid: "122396852"
---
# <a name="create-predictive-pipelines-using-ml-studio-classic-and-azure-data-factory"></a>Creación de canalizaciones predictivas con ML Studio (clásico) y Azure Data Factory

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

## <a name="introduction"></a>Introducción
> [!NOTE]
> Este artículo se aplica a la versión 1 de Data Factory. Si usa la versión actual del servicio Data Factory, consulte el artículo acerca de la [transformación de datos mediante el aprendizaje automático en Data Factory](../transform-data-using-machine-learning.md).

### <a name="ml-studio-classic"></a>ML Studio (clásico)
[ML Studio (clásico)](https://azure.microsoft.com/documentation/services/machine-learning/) permite compilar, probar e implementar soluciones de análisis predictivo. Desde una perspectiva general, esto se realiza en tres pasos:

1. **Crear un experimento de entrenamiento**. Este paso se lleva a cabo mediante ML Studio (clásico). Studio (clásico) es un entorno de desarrollo visual de colaboración que se emplea para entrenar y probar un modelo de análisis predictivo con datos de entrenamiento.
2. **Convertirlo en un experimento predictivo**. Una vez que el modelo se ha entrenado con datos existentes y está listo para usarlo para puntuar nuevos datos, debe preparar y simplificar el experimento para la puntuación.
3. **Implementarlo como un servicio web**. Puede publicar el experimento de puntuación como un servicio web de Azure. Los usuarios pueden enviar datos al modelo a través de este punto de conexión de servicio web y recibir las predicciones de resultado para el modelo.

### <a name="azure-data-factory"></a>Azure Data Factory
Data Factory es un servicio de integración de datos basado en la nube que organiza y automatiza el **movimiento** y la **transformación** de los datos. Con Azure Data Factory se pueden crear soluciones de integración de datos que pueden ingerir datos de distintos almacenes de datos, transformarlos y procesarlos, y publicar los datos resultantes en los almacenes de datos.

El servicio Data Factory permite crear canalizaciones de datos que mueven y transforman datos y, después, ejecutar los procesos según una programación especificada (cada hora, diariamente, semanalmente, etc.). También proporciona excelentes visualizaciones para mostrar el linaje y las dependencias entre las canalizaciones de datos y para poder supervisar todas las canalizaciones de datos desde una única vista unificada, con el fin de facilitar la identificación de los problemas y la configuración de alertas de supervisión.

Consulte los artículos [Introducción a Azure Data Factory](data-factory-introduction.md) y [Creación de la primera canalización de datos](data-factory-build-your-first-pipeline.md) para empezar a trabajar rápidamente con el servicio Azure Data Factory.

### <a name="data-factory-and-machine-learning-studio-classic-together"></a>Data Factory y Azure Machine Learning Studio (clásico) juntos
Azure Data Factory permite crear fácilmente canalizaciones que usan un servicio web de [ML Studio (clásico)][azure-machine-learning] publicado para realizar análisis predictivos. Mediante la **actividad de ejecución de lotes** en una canalización de Azure Data Factory, puede invocar un servicio web de Studio (clásico) para realizar predicciones sobre los datos del lote. Consulte la sección Invocación de un servicio web de ML Studio mediante la actividad de ejecución por lotes para obtener detalles.

Con el tiempo, los modelos predictivos de los experimentos de puntuación de Studio (clásico) se tienen que volver a entrenar con nuevos conjuntos de datos de entrada. Puede volver a entrenar un modelo de Studio (clásico) de una canalización de Factoría de datos realizando los pasos siguientes:

1. Publicar el experimento de entrenamiento (experimento no predictivo) como un servicio web. Tiene que llevar a cabo este paso en Studio (clásico), tal como hizo para exponer el experimento predictivo como un servicio web en el escenario anterior.
2. Usar la actividad de ejecución de lotes de Studio (clásico) para invocar el servicio web para el experimento de entrenamiento. Básicamente, puede emplear la actividad de ejecución de lotes de Studio (clásico) para invocar el servicio web de aprendizaje y el servicio web de puntuación.

Cuando haya terminado el reciclaje, actualice el servicio web de puntuación (experimento predictivo expuesto como servicio web) con el modelo recién entrenado mediante la **actividad de actualización de recurso de ML Studio (clásico)** . Para obtener más información, consulte el artículo [Updating models using Update Resource Activity](data-factory-azure-ml-update-resource-activity.md) (Actualización de modelos mediante la actividad de recursos de actualización).

## <a name="invoking-a-web-service-using-batch-execution-activity"></a>Invocación de un servicio web mediante la actividad de ejecución de lotes
Azure Data Factory se usa para orquestar el procesamiento y el movimiento de datos y, posteriormente, realizar la ejecución por lotes mediante Studio (clásico). Estos son los pasos de nivel superior:

1. Crear un servicio vinculado de ML Studio (clásico). Necesita los siguientes valores:

   1. **URI de solicitud** para la API Ejecución de lotes. Para encontrar el URI de solicitud, haga clic en el vínculo **EJECUCIÓN DE LOTES** en la página de servicios web.
   2. **Clave de API** del servicio web de Studio (clásico) publicado. Para encontrar la clave de API, haga clic en el servicio web que ha publicado.
   3. Use la actividad **AzureMLBatchExecution** .

      ![Panel de Machine Learning Studio (clásico)](./media/data-factory-azure-ml-batch-execution-activity/AzureMLDashboard.png)

      ![URI del lote](./media/data-factory-azure-ml-batch-execution-activity/batch-uri.png)

### <a name="scenario-experiments-using-web-service-inputsoutputs-that-refer-to-data-in-azure-blob-storage"></a>Escenario: experimentos mediante entradas y salidas de servicios web que hacen referencia a datos de Azure Blob Storage
En este escenario, el servicio web de Studio (clásico) realiza predicciones mediante datos de un archivo de una instancia de Azure Blob Storage y almacena los resultados de predicción en el almacenamiento de blobs. El siguiente JSON define una canalización de Data Factory con una actividad AzureMLBatchExecution. La actividad tiene el conjunto de datos **DecisionTreeInputBlob** como entrada y **DecisionTreeResultBlob** como salida. **DecisionTreeInputBlob** se pasa como entrada al servicio web mediante la propiedad JSON **webServiceInput**. **DecisionTreeResultBlob** se pasa como salida al servicio web mediante la propiedad JSON **webServiceOutputs**.

> [!IMPORTANT]
> Si el servicio web toma varias entradas, use la propiedad **webServiceInputs** en lugar de usar **webServiceInput**. Consulte la sección [El servicio web requiere varias entradas](#web-service-requires-multiple-inputs) para ver un ejemplo de cómo usar la propiedad webServiceInputs.
>
> Los conjuntos de datos a los que hacen referencia las propiedades **webServiceInput**/**webServiceInputs** y **webServiceOutputs** (en **typeProperties**) también se deben incluir en las **entradas** y las **salidas** de la actividad.
>
> En el experimento de Studio (clásico), los puertos de entrada y salida del servicio web y los parámetros globales tienen nombres predeterminados ("input1", "input2") que se pueden personalizar. Los nombres que se utilizan para la configuración de globalParameters, webServiceOutputs y webServiceInputs deben coincidir exactamente con los de los experimentos. Puede ver la carga útil de la solicitud de ejemplo en la página de ayuda de ejecución de lotes del punto de conexión de Studio (clásico) para comprobar la asignación esperada.
>
>

```json
{
  "name": "PredictivePipeline",
  "properties": {
    "description": "use AzureML model",
    "activities": [
      {
        "name": "MLActivity",
        "type": "AzureMLBatchExecution",
        "description": "prediction analysis on batch input",
        "inputs": [
          {
            "name": "DecisionTreeInputBlob"
          }
        ],
        "outputs": [
          {
            "name": "DecisionTreeResultBlob"
          }
        ],
        "linkedServiceName": "MyAzureMLLinkedService",
        "typeProperties":
        {
            "webServiceInput": "DecisionTreeInputBlob",
            "webServiceOutputs": {
                "output1": "DecisionTreeResultBlob"
            }
        },
        "policy": {
          "concurrency": 3,
          "executionPriorityOrder": "NewestFirst",
          "retry": 1,
          "timeout": "02:00:00"
        }
      }
    ],
    "start": "2016-02-13T00:00:00Z",
    "end": "2016-02-14T00:00:00Z"
  }
}
```

> [!NOTE]
> Solo las entradas y salidas de la actividad AzureMLBatchExecution pueden pasarse como parámetros al servicio web. Por ejemplo, en el fragmento JSON anterior, DecisionTreeInputBlob es una entrada a la actividad AzureMLBatchExecution, que se pasa como entrada al servicio web mediante el parámetro webServiceInput.
>
>

### <a name="example"></a>Ejemplo
Este ejemplo utiliza Azure Storage para almacenar los datos de entrada y salida.

Es recomendable que siga el tutorial [Compilación de la primera canalización con Data Factory][adf-build-1st-pipeline] antes de pasar a este ejemplo. Utilice el editor de Data Factory para crear artefactos de Data Factory (servicios vinculados, conjuntos de datos, canalización) en este ejemplo.

1. Cree un **servicio vinculado** para su instancia de **Azure Storage**. Si los archivos de entrada y salida están en diferentes cuentas de almacenamiento, necesita dos servicios vinculados. Este es un ejemplo de JSON:

   ```json
   {
     "name": "StorageLinkedService",
     "properties": {
       "type": "AzureStorage",
       "typeProperties": {
         "connectionString": "DefaultEndpointsProtocol=https;AccountName= [acctName];AccountKey=[acctKey]"
       }
     }
   }
   ```

2. Cree el **conjunto de datos** de **entrada** de Azure Data Factory. A diferencia de otros conjuntos de datos de Data Factory, estos deben contener tanto los valores de **folderPath** como de **fileName**. Puede utilizar la creación de particiones para hacer que cada ejecución de lotes (cada segmento de datos) se procese o produzca archivos de entrada y salida únicos. Probablemente necesitará incluir alguna actividad ascendente para transformar la entrada en el formato de archivo CSV y colocarlo en la cuenta de almacenamiento para cada segmento. En ese caso, no incluiría la configuración de **external** y **externalData** que se muestra en el ejemplo siguiente, y su valor de DecisionTreeInputBlob sería el conjunto de datos de salida de una actividad diferente.

   ```json
   {
     "name": "DecisionTreeInputBlob",
     "properties": {
       "type": "AzureBlob",
       "linkedServiceName": "StorageLinkedService",
       "typeProperties": {
         "folderPath": "azuremltesting/input",
         "fileName": "in.csv",
         "format": {
           "type": "TextFormat",
           "columnDelimiter": ","
         }
       },
       "external": true,
       "availability": {
         "frequency": "Day",
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

   Su archivo CSV de entrada debe tener la fila de encabezado de columna. Si está usando la **actividad de copia** para crear o mover el archivo CSV a Blob Storage, debe establecer la propiedad **blobWriterAddHeader** del receptor en **true**. Por ejemplo:

   ```json
   sink:
   {
     "type": "BlobSink",
     "blobWriterAddHeader": true
     }
   ```

   si el archivo .csv no tiene la fila del encabezado, es posible que aparezca un error como el siguiente: **Error en la actividad: error de lectura de la cadena. Token inesperado: StartObject. Path '', line 1, position 1**.

3. Cree el **conjunto de datos** de **salida** de Azure Data Factory. En este ejemplo se usa la creación de particiones para crear una ruta de acceso de salida única para cada ejecución de segmento. Sin la creación de particiones, la actividad sobrescribiría el archivo.

   ```json
   {
     "name": "DecisionTreeResultBlob",
     "properties": {
       "type": "AzureBlob",
       "linkedServiceName": "StorageLinkedService",
       "typeProperties": {
         "folderPath": "azuremltesting/scored/{folderpart}/",
         "fileName": "{filepart}result.csv",
         "partitionedBy": [
           {
             "name": "folderpart",
             "value": {
               "type": "DateTime",
               "date": "SliceStart",
               "format": "yyyyMMdd"
             }
           },
           {
             "name": "filepart",
             "value": {
               "type": "DateTime",
               "date": "SliceStart",
               "format": "HHmmss"
             }
           }
         ],
         "format": {
           "type": "TextFormat",
           "columnDelimiter": ","
         }
       },
       "availability": {
         "frequency": "Day",
         "interval": 15
       }
     }
   }
   ```

4. Cree un **servicio vinculado** del tipo: **AzureMLLinkedService**; para ello, proporcione la clave de API y la dirección URL de ejecución de lotes del modelo.

   ```json
   {
     "name": "MyAzureMLLinkedService",
     "properties": {
       "type": "AzureML",
       "typeProperties": {
         "mlEndpoint": "https://[batch execution endpoint]/jobs",
         "apiKey": "[apikey]"
       }
     }
   }
   ```

5. Por último, cree una canalización que contenga una actividad **AzureMLBatchExecution** . En tiempo de ejecución, la canalización lleva a cabo los pasos siguientes:

   1. Obtiene la ubicación del archivo de entrada de los conjuntos de datos de entrada.
   2. Invoca a la API de ejecución por lotes de Studio (clásico).
   3. Copia el resultado de la ejecución por lotes en el blob proporcionado en el conjunto de datos de salida.

      > [!NOTE]
      > La actividad AzureMLBatchExecution puede tener cero o más entradas y una o más salidas.
      >
      >

      ```json
      {
        "name": "PredictivePipeline",
        "properties": {
          "description": "use AzureML model",
          "activities": [
            {
              "name": "MLActivity",
              "type": "AzureMLBatchExecution",
              "description": "prediction analysis on batch input",
              "inputs": [
                {
                  "name": "DecisionTreeInputBlob"
                }
                ],
              "outputs": [
                {
                  "name": "DecisionTreeResultBlob"
                }
                ],
              "linkedServiceName": "MyAzureMLLinkedService",
              "typeProperties":
                {
                "webServiceInput": "DecisionTreeInputBlob",
                "webServiceOutputs": {
                  "output1": "DecisionTreeResultBlob"
                }
                },
              "policy": {
                "concurrency": 3,
                "executionPriorityOrder": "NewestFirst",
                "retry": 1,
                "timeout": "02:00:00"
              }
            }
          ],
          "start": "2016-02-13T00:00:00Z",
          "end": "2016-02-14T00:00:00Z"
        }
      }
      ```

      Las fechas y horas de inicio (**start**) y de finalización (**end**) deben estar en [formato ISO](https://en.wikipedia.org/wiki/ISO_8601). Por ejemplo: 2014-10-14T16:32:41Z. La hora de la propiedad **end** es opcional. Si no especifica ningún valor para la propiedad **end**, se calcula como "**start + 48 horas**". Para ejecutar la canalización indefinidamente, especifique **9999-09-09** como valor de propiedad **end**. Para obtener más información sobre las propiedades JSON, vea [Referencia de scripting JSON](/previous-versions/azure/dn835050(v=azure.100)) .

      > [!NOTE]
      > La especificación de la entrada para la actividad AzureMLBatchExecution es opcional.
      >
      >

### <a name="scenario-experiments-using-readerwriter-modules-to-refer-to-data-in-various-storages"></a>Escenario: experimentos mediante módulos lector y escritor para hacer referencia a datos de varios almacenamientos
Otro escenario común al crear experimentos de Studio (clásico) es usar módulos Lector y Escritor. El módulo lector se usa para cargar datos en un experimento y el módulo escritor para guardar los datos de los experimentos. Para obtener información sobre los módulos lector y escritor, consulte los temas [Lector](/azure/machine-learning/studio-module-reference/import-data) y [Escritor](/azure/machine-learning/studio-module-reference/export-data) en MSDN Library.

Al usar los módulos lector y escritor, es recomendable emplear un parámetro de servicio web para cada propiedad de estos módulos. Estos parámetros web permiten configurar los valores en tiempo de ejecución. Por ejemplo, pueden crear un experimento con un módulo lector que usa una base de datos de Azure SQL: XXX.database.windows.net. Una vez implementado el servicio web, quiere permitir que los consumidores del servicio web especifiquen especificar otro servidor SQL lógico denominado YYY.database.windows.net. Puede usar un parámetro de servicio web para permitir que se configure este valor.

> [!NOTE]
> Las entradas y salidas de servicio web son diferentes de los parámetros de servicio web. En el primer escenario, ha visto cómo pueden especificarse una entrada y una salida para un servicio web de Studio (clásico). En este escenario, se pasan parámetros para un servicio web que corresponden a las propiedades de los módulos lector y escritor.
>
>

Echemos un vistazo a un escenario de uso de parámetros de servicio web. Tiene un servicio web de Studio (clásico) implementado que usa un módulo lector para leer datos de uno de los orígenes de datos compatibles con Studio (clásico) (por ejemplo: Azure SQL Database). Después de realizar la ejecución de lotes, los resultados se escriben con un módulo escritor (Azure SQL Database).  No hay entradas ni salidas de servicio web definidas en los experimentos. En este caso, se recomienda que configure los parámetros de servicio web pertinentes para los módulos lector y escritor. De esta forma, se podrán configurar los módulos lector y escritor cuando se use la actividad AzureMLBatchExecution. Los parámetros de servicio web se especifican en la sección **globalParameters** de la actividad JSON como se indica a continuación.

```json
"typeProperties": {
    "globalParameters": {
        "Param 1": "Value 1",
        "Param 2": "Value 2"
    }
}
```

También puede usar [Funciones de Factoría de datos](data-factory-functions-variables.md) para pasar valores para los parámetros de servicio web como se muestra en el siguiente ejemplo:

```json
"typeProperties": {
    "globalParameters": {
       "Database query": "$$Text.Format('SELECT * FROM myTable WHERE timeColumn = \\'{0:yyyy-MM-dd HH:mm:ss}\\'', Time.AddHours(WindowStart, 0))"
    }
}
```

> [!NOTE]
> Los parámetros de servicio web distinguen entre mayúsculas y minúsculas para garantizar que los nombres que especifica en JSON de actividad coinciden con los que muestra el servicio web.
>
>

### <a name="using-a-reader-module-to-read-data-from-multiple-files-in-azure-blob"></a>Uso de un módulo lector para leer datos de varios archivos de blob de Azure
La canalización de macrodatos con actividades como Pig y Hive puede generar uno o varios archivos de salida sin extensiones. Por ejemplo, cuando se especifica una tabla externa de Hive, los datos de dicha tabla se pueden almacenar en el almacenamiento de blobs de Azure con el siguiente nombre 000000_0. Puede usar el módulo lector en un experimento para leer varios archivos y usarlos para realizar predicciones.

Al usar el módulo Lector en un experimento de Studio (clásico), puede especificar Azure Blob como entrada. Los archivos de Azure Blob Storage pueden ser los archivos de salida (ejemplo: 000000_0) que genera un script de Pig y Hive que se ejecuta en HDInsight. El módulo de lector permite leer archivos (sin extensiones) mediante la configuración de la propiedad **Path to container, directory/blob**(Ruta de acceso al contenedor, directorio o blob). La **ruta de acceso al contenedor** apunta al contenedor y el **directorio o blob** apunta a la carpeta que contiene los archivos, tal y como se muestra en la siguiente imagen. Tenga en cuenta que el asterisco (\*) **especifica que todos los archivos de la carpeta o contenedor (es decir, data/aggregateddata/year=2014/month-6/\*)** se leen como parte del experimento.

![Propiedades de Blob de Azure](./media/data-factory-create-predictive-pipelines/azure-blob-properties.png)

### <a name="example"></a>Ejemplo
#### <a name="pipeline-with-azuremlbatchexecution-activity-with-web-service-parameters"></a>Canalización con la actividad AzureMLBatchExecution con parámetros de servicio web

```JSON
{
  "name": "MLWithSqlReaderSqlWriter",
  "properties": {
    "description": "ML Studio (classic) model with sql azure reader/writer",
    "activities": [
      {
        "name": "MLSqlReaderSqlWriterActivity",
        "type": "AzureMLBatchExecution",
        "description": "test",
        "inputs": [
          {
            "name": "MLSqlInput"
          }
        ],
        "outputs": [
          {
            "name": "MLSqlOutput"
          }
        ],
        "linkedServiceName": "MLSqlReaderSqlWriterDecisionTreeModel",
        "typeProperties":
        {
            "webServiceInput": "MLSqlInput",
            "webServiceOutputs": {
                "output1": "MLSqlOutput"
            }
              "globalParameters": {
                "Database server name": "<myserver>.database.windows.net",
                "Database name": "<database>",
                "Server user account name": "<user name>",
                "Server user account password": "<password>"
              }
        },
        "policy": {
          "concurrency": 1,
          "executionPriorityOrder": "NewestFirst",
          "retry": 1,
          "timeout": "02:00:00"
        },
      }
    ],
    "start": "2016-02-13T00:00:00Z",
    "end": "2016-02-14T00:00:00Z"
  }
}
```

En el ejemplo JSON anterior:

* El servicio web de Studio (clásico) implementado usa un módulo Lector y otro Escritor para leer y escribir datos desde y hacia una base de datos de Azure SQL Database. Este servicio web expone los cuatro parámetros siguientes:  nombre del servidor de bases de datos, nombre de base de datos, nombre de la cuenta del usuario del servidor y contraseña de la cuenta de usuario del servidor.
* Las fechas y horas de inicio (**start**) y de finalización (**end**) deben estar en [formato ISO](https://en.wikipedia.org/wiki/ISO_8601). Por ejemplo: 2014-10-14T16:32:41Z. La hora de la propiedad **end** es opcional. Si no especifica ningún valor para la propiedad **end**, se calcula como "**start + 48 horas**". Para ejecutar la canalización indefinidamente, especifique **9999-09-09** como valor de propiedad **end**. Para obtener más información sobre las propiedades JSON, vea [Referencia de scripting JSON](/previous-versions/azure/dn835050(v=azure.100)) .

### <a name="other-scenarios"></a>Otros escenarios
#### <a name="web-service-requires-multiple-inputs"></a>El servicio web requiere varias entradas
Si el servicio web toma varias entradas, use la propiedad **webServiceInputs** en lugar de usar **webServiceInput**. Los conjuntos de datos a los que hace referencia **webServiceInputs** también deben incluirse en las **entradas** de la actividad.

En el experimento de ML Studio (clásico), los puertos de entrada y salida del servicio web y los parámetros globales tienen nombres predeterminados ("input1", "input2") que se pueden personalizar. Los nombres que se utilizan para la configuración de globalParameters, webServiceOutputs y webServiceInputs deben coincidir exactamente con los de los experimentos. Puede ver la carga útil de la solicitud de ejemplo en la página de ayuda de ejecución de lotes del punto de conexión de Studio (clásico) para comprobar la asignación esperada.

```JSON
{
    "name": "PredictivePipeline",
    "properties": {
        "description": "use AzureML model",
        "activities": [{
            "name": "MLActivity",
            "type": "AzureMLBatchExecution",
            "description": "prediction analysis on batch input",
            "inputs": [{
                "name": "inputDataset1"
            }, {
                "name": "inputDataset2"
            }],
            "outputs": [{
                "name": "outputDataset"
            }],
            "linkedServiceName": "MyAzureMLLinkedService",
            "typeProperties": {
                "webServiceInputs": {
                    "input1": "inputDataset1",
                    "input2": "inputDataset2"
                },
                "webServiceOutputs": {
                    "output1": "outputDataset"
                }
            },
            "policy": {
                "concurrency": 3,
                "executionPriorityOrder": "NewestFirst",
                "retry": 1,
                "timeout": "02:00:00"
            }
        }],
        "start": "2016-02-13T00:00:00Z",
        "end": "2016-02-14T00:00:00Z"
    }
}
```

#### <a name="web-service-does-not-require-an-input"></a>Servicio web no requiere una entrada
Los servicios web de ejecución de lotes de ML Studio (clásico) se pueden usar para ejecutar cualquier flujo de trabajo, por ejemplo, scripts R o Python, que puedan no requerir entradas. O bien, el experimento se podría configurar con un módulo Lector que no expone ningún GlobalParameters. En ese caso, la actividad AzureMLBatchExecution se configuraría de la siguiente manera:

```JSON
{
    "name": "scoring service",
    "type": "AzureMLBatchExecution",
    "outputs": [
        {
            "name": "myBlob"
        }
    ],
    "typeProperties": {
        "webServiceOutputs": {
            "output1": "myBlob"
        }
     },
    "linkedServiceName": "mlEndpoint",
    "policy": {
        "concurrency": 1,
        "executionPriorityOrder": "NewestFirst",
        "retry": 1,
        "timeout": "02:00:00"
    }
},
```

#### <a name="web-service-does-not-require-an-inputoutput"></a>Servicio web no requiere entrada/salida
El servicio web de ejecución de lotes de ML Studio (clásico) podría no tener configurada ninguna salida de servicio web. En este ejemplo, no hay ninguna entrada o salida de servicio web ni tampoco hay configurado ningún GlobalParameters. Todavía hay una salida configurada en la propia actividad, pero que no se presenta como webServiceOutput.

```JSON
{
    "name": "retraining",
    "type": "AzureMLBatchExecution",
    "outputs": [
        {
            "name": "placeholderOutputDataset"
        }
    ],
    "typeProperties": {
     },
    "linkedServiceName": "mlEndpoint",
    "policy": {
        "concurrency": 1,
        "executionPriorityOrder": "NewestFirst",
        "retry": 1,
        "timeout": "02:00:00"
    }
},
```

#### <a name="web-service-uses-readers-and-writers-and-the-activity-runs-only-when-other-activities-have-succeeded"></a>Servicio web usa lectores y escritores y la actividad solo se ejecuta cuando otras actividades se realizaron correctamente
Los módulos lector y escritor del servicio web de ML Studio (clásico) se podrían configurar para ejecutarse con o sin GlobalParameters. Sin embargo, quizá quiera insertar llamadas de servicio en una canalización que use dependencias del conjunto de datos para invocar al servicio solo cuando se haya completado un procesamiento ascendente. También puede desencadenar otra acción una vez completada la ejecución por lotes con este enfoque. En ese caso, puede expresar las dependencias mediante entradas y salidas de la actividad, sin denominar a ninguna de ellas como entradas o salidas del servicio web.

```JSON
{
    "name": "retraining",
    "type": "AzureMLBatchExecution",
    "inputs": [
        {
            "name": "upstreamData1"
        },
        {
            "name": "upstreamData2"
        }
    ],
    "outputs": [
        {
            "name": "downstreamData"
        }
    ],
    "typeProperties": {
     },
    "linkedServiceName": "mlEndpoint",
    "policy": {
        "concurrency": 1,
        "executionPriorityOrder": "NewestFirst",
        "retry": 1,
        "timeout": "02:00:00"
    }
},
```

Las principales **ideas** obtenidas son:

* Si el punto de conexión del experimento usa una propiedad webServiceInput: se representa con un conjunto de datos de blobs y se incluye en las entradas de la actividad y en la propiedad webServiceInput. De lo contrario, se omite la propiedad webServiceInput.
* Si el punto de conexión del experimento utiliza webServiceOutput(s): se representan con conjuntos de datos de blobs y se incluyen en la salidas de la actividad y en la propiedad webServiceOutputs. Las salidas de la actividad y webServiceOutputs se asignan por el nombre de cada salida en el experimento. De lo contrario, se omite la propiedad webServiceOutputs.
* Si el punto de conexión del experimento expone una o más propiedades globalParameters, se proporcionan en la propiedad globalParameters como pares clave-valor. De lo contrario, se omite la propiedad globalParameters. Las claves distinguen mayúsculas de minúsculas. [Las funciones de Azure Data Factory](data-factory-functions-variables.md) se pueden usar en los valores.
* Es posible incluir conjuntos de datos adicionales en las propiedades de entradas y salidas de la actividad, sin que typeProperties de la actividad haga referencia a ellos. Estos conjunto de datos rigen la ejecución mediante el uso de las dependencias de segmento; de no ser así, la actividad AzureMLBatchExecution los omitirá.


## <a name="updating-models-using-update-resource-activity"></a>Actualización de modelos mediante la actividad de recursos de actualización
Cuando haya terminado el reciclaje, actualice el servicio web de puntuación (experimento predictivo expuesto como servicio web) con el modelo recién entrenado mediante la **actividad de actualización de recurso de ML Studio (clásico)** . Para obtener más información, consulte el artículo [Updating models using Update Resource Activity](data-factory-azure-ml-update-resource-activity.md) (Actualización de modelos mediante la actividad de recursos de actualización).

### <a name="reader-and-writer-modules"></a>Módulos Lector y Escritor
Un escenario común para el uso de parámetros de servicio web es el uso de Lectores y escritores SQL de Azure. El módulo Lector se usa para cargar datos en un experimento desde servicios de administración de datos fuera de Studio (clásico). El módulo Escritor sirve para guardar datos desde los experimentos en servicios de administración de datos fuera de Studio (clásico).

Para obtener información acerca del lector o escritor de SQL/blob de Azure, consulte los temas [Lector ](/azure/machine-learning/studio-module-reference/import-data)y [Escritor](/azure/machine-learning/studio-module-reference/export-data) en MSDN Library. El ejemplo de la sección anterior utilizó el lector de Blob de Azure y el lector de Blob de Azure. En esta sección se trata el uso del lector SQL Azure y el escritor SQL Azure.

## <a name="frequently-asked-questions"></a>Preguntas más frecuentes
**P:** Mis canalizaciones de macrodatos han generado varios archivos. ¿Puedo usar la actividad AzureMLBatchExecution para trabajar en todos los archivos?

**R:** Sí. Consulte **Uso de un módulo lector para leer datos de varios archivos de blob de Azure** para obtener más información.

## <a name="ml-studio-classic-batch-scoring-activity"></a>Actividad de puntuación por lotes de ML Studio (clásico)
Si va a usar la actividad **AzureMLBatchScoring** para la integración con ML Studio (clásico), se recomienda usar la actividad **AzureMLBatchExecution** más reciente.

La actividad AzureMLBatchExecution se introdujo en la versión de agosto de 2015 del SDK de Azure y Azure PowerShell.

Si desea continuar utilizando la actividad AzureMLBatchScoring, siga leyendo esta sección.

### <a name="ml-studio-classic-batch-scoring-activity-using-azure-storage-for-inputoutput"></a>Actividad de puntuación de lotes de ML Studio (clásico) con Azure Storage para entrada/salida

```JSON
{
  "name": "PredictivePipeline",
  "properties": {
    "description": "use AzureML model",
    "activities": [
      {
        "name": "MLActivity",
        "type": "AzureMLBatchScoring",
        "description": "prediction analysis on batch input",
        "inputs": [
          {
            "name": "ScoringInputBlob"
          }
        ],
        "outputs": [
          {
            "name": "ScoringResultBlob"
          }
        ],
        "linkedServiceName": "MyAzureMLLinkedService",
        "policy": {
          "concurrency": 3,
          "executionPriorityOrder": "NewestFirst",
          "retry": 1,
          "timeout": "02:00:00"
        }
      }
    ],
    "start": "2016-02-13T00:00:00Z",
    "end": "2016-02-14T00:00:00Z"
  }
}
```

### <a name="web-service-parameters"></a>Parámetros de servicio web
Para especificar los valores de los parámetros de un servicio web, agregue una sección **typeProperties** a la sección **AzureMLBatchScoringActivity** del JSON de canalización, como se muestra en el siguiente ejemplo:

```JSON
"typeProperties": {
    "webServiceParameters": {
        "Param 1": "Value 1",
        "Param 2": "Value 2"
    }
}
```
También puede usar [Funciones de Factoría de datos](data-factory-functions-variables.md) para pasar valores para los parámetros de servicio web como se muestra en el siguiente ejemplo:

```JSON
"typeProperties": {
    "webServiceParameters": {
       "Database query": "$$Text.Format('SELECT * FROM myTable WHERE timeColumn = \\'{0:yyyy-MM-dd HH:mm:ss}\\'', Time.AddHours(WindowStart, 0))"
    }
}
```

> [!NOTE]
> Los parámetros de servicio web distinguen entre mayúsculas y minúsculas para garantizar que los nombres que especifica en JSON de actividad coinciden con los que muestra el servicio web.
>
>

## <a name="see-also"></a>Consulte también
* [Entrada de blog de Azure: Introducción a Azure Data Factory y ML Studio (clásico)](https://azure.microsoft.com/blog/getting-started-with-azure-data-factory-and-azure-machine-learning-4/)

[adf-build-1st-pipeline]: data-factory-build-your-first-pipeline.md

[azure-machine-learning]: https://azure.microsoft.com/services/machine-learning/