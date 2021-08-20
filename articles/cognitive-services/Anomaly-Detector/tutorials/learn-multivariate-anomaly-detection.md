---
title: 'Tutorial: Información sobre la detección de anomalías multivariante en una hora'
titleSuffix: Azure Cognitive Services
description: Un tutorial completo de detección de anomalías multivariante.
services: cognitive-services
author: juaduan
manager: conhua
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: tutorial
ms.date: 06/27/2021
ms.author: juaduan
ms.openlocfilehash: bdebf242dcea8e7640d68afcb21d7ba7e2e49576
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/16/2021
ms.locfileid: "114292637"
---
# <a name="tutorial-learn-multivariate-anomaly-detection-in-one-hour"></a>Tutorial: Información sobre la detección de anomalías multivariante en una hora

Anomaly Detector con detección de anomalías multivariante (MVAD) es una herramienta avanzada de inteligencia artificial para detectar anomalías de un grupo de métricas de forma **no supervisada**.

En general, puede seguir estos pasos para usar MVAD:

  1. Cree un recurso de Anomaly Detector que admita MVAD en Azure.
  1. Prepare sus datos.
  1. Entrene un modelo MVAD.
  1. Consulte el estado del modelo.
  1. Detecte anomalías con el modelo MVAD entrenado.
  1. Recupere e interprete los resultados de la inferencia.

En este tutorial, hará lo siguiente:

> [!div class="checklist"]
> * Comprender cómo preparar los datos en un formato correcto.
> * Comprender cómo entrenar e inferencia con MVAD.
> * Comprender los parámetros de entrada y cómo interpretar la salida en los resultados de la inferencia.

## <a name="1-create-an-anomaly-detector-resource-that-supports-mvad"></a>1. Cree un recurso de Anomaly Detector que admita MVAD.

* Cree una suscripción de Azure si no tiene una: [Crear una de forma gratuita](https://azure.microsoft.com/free/cognitive-services)
* Cuando tenga la suscripción de Azure, [cree de un recurso de Anomaly Detector](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector) en Azure Portal para obtener la clave y el punto de conexión de la API.

> [!NOTE]
> Durante la fase de versión preliminar, MVAD solo está disponible en regiones limitadas. Agregue [Novedades de Anomaly Detector](../whats-new.md) como marcador para mantenerse al día con los lanzamientos de regiones de MVAD. También puede presentar un problema GitHub o ponerse en contacto con nosotros en [AnomalyDetector@microsoft.com](mailto:AnomalyDetector@microsoft.com) para solicitar regiones específicas.

## <a name="2-data-preparation"></a>2. Preparación de datos

A continuación, debe preparar los datos de entrenamiento (y los datos de inferencia).

[!INCLUDE [mvad-data-schema](../includes/mvad-data-schema.md)]

### <a name="tools-for-zipping-and-uploading-data"></a>Herramientas para comprimir y cargar datos

En esta sección, se comparten algunas herramientas y código de ejemplo que puede copiar y editar para agregarlos a su propia lógica de aplicación que se ocupa de los datos de entrada de MVAD.

#### <a name="compressing-csv-files-in-nix"></a>Compresión de archivos CSV en \*nix

```bash
zip -j series.zip series/*.csv
```

#### <a name="compressing-csv-files-in-windows"></a>Compresión de archivos CSV en Windows

* Vaya *a* la carpeta con todos los archivos CSV.
* Seleccione todos los archivos CSV que necesite.
* Haga clic con el botón derecho en uno de los archivos CSV y seleccione `Send to`.
* Seleccione `Compressed (zipped) folder` en el menú desplegable.
* Cambie el nombre del archivo ZIP según sea necesario.

#### <a name="python-code-zipping--uploading-data-to-azure-blob-storage"></a>Compresión de código de Python y carga de datos en Azure Blob Storage

Puede consultar [este documento](../../../storage/blobs/storage-quickstart-blobs-portal.md#upload-a-block-blob) para obtener información sobre cómo cargar un archivo en Azure Blob.

O bien, puede hacer referencia al código de ejemplo siguiente que puede realizar la compresión y la carga automáticamente. Puede copiar y guardar el código de Python en esta sección como un archivo .py (por ejemplo, `zipAndUpload.py`) y ejecutarlo mediante líneas de comandos como estas:

* `python zipAndUpload.py -s "foo\bar" -z test123.zip -c {azure blob connection string} -n container_xxx`

    Este comando comprimirá todos los archivos CSV de `foo\bar` en un único archivo ZIP denominado `test123.zip`. Cargará `test123.zip` en el contenedor `container_xxx` del blob.
* `python zipAndUpload.py -s "foo\bar" -z test123.zip -c {azure blob connection string} -n container_xxx -r` 

    Este comando hará lo mismo que lo anterior, pero eliminará el archivo ZIP `test123.zip` después de cargarlo correctamente. 

Argumentos:

* `--source-folder`, `-s`, ruta de acceso a la carpeta de origen que contiene archivos CSV
* `--zipfile-name`, `-z`, nombre del archivo ZIP
* `--connection-string`, `-c`, cadena de conexión al blob
* `--container-name`, `-n`, nombre del contenedor
* `--remove-zipfile`, `-r`, si está activado, quite el archivo ZIP.

```python
import os
import argparse
import shutil
import sys

from azure.storage.blob import BlobClient
import zipfile


class ZipError(Exception):
    pass


class UploadError(Exception):
    pass


def zip_file(root, name):
    try:
        z = zipfile.ZipFile(name, "w", zipfile.ZIP_DEFLATED)
        for f in os.listdir(root):
            if f.endswith("csv"):
                z.write(os.path.join(root, f), f)
        z.close()
        print("Compress files success!")
    except Exception as ex:
        raise ZipError(repr(ex))


def upload_to_blob(file, conn_str, cont_name, blob_name):
    try:
        blob_client = BlobClient.from_connection_string(conn_str, container_name=cont_name, blob_name=blob_name)
        with open(file, "rb") as f:
            blob_client.upload_blob(f, overwrite=True)
        print("Upload Success!")
    except Exception as ex:
        raise UploadError(repr(ex))


if __name__ == "__main__":
    parser = argparse.ArgumentParser()
    parser.add_argument("--source-folder", "-s", type=str, required=True, help="path to source folder")
    parser.add_argument("--zipfile-name", "-z", type=str, required=True, help="name of the zip file")
    parser.add_argument("--connection-string", "-c", type=str, help="connection string")
    parser.add_argument("--container-name", "-n", type=str, help="container name")
    parser.add_argument("--remove-zipfile", "-r", action="store_true", help="whether delete the zip file after uploading")
    args = parser.parse_args()

    try:
        zip_file(args.source_folder, args.zipfile_name)
        upload_to_blob(args.zipfile_name, args.connection_string, args.container_name, args.zipfile_name)
    except ZipError as ex:
        print(f"Failed to compress files. {repr(ex)}")
        sys.exit(-1)
    except UploadError as ex:
        print(f"Failed to upload files. {repr(ex)}")
        sys.exit(-1)
    except Exception as ex:
        print(f"Exception encountered. {repr(ex)}")

    try:
        if args.remove_zipfile:
            os.remove(args.zipfile_name)
    except Exception as ex:
        print(f"Failed to delete the zip file. {repr(ex)}")
```

## <a name="3-train-an-mvad-model"></a>3. Entreno de un modelo MVAD

Este es un cuerpo de solicitud de ejemplo y el código de ejemplo en Python para entrenar un modelo MVAD.

```json
// Sample Request Body
{
    "slidingWindow": 200,
    "alignPolicy": {
        "alignMode": "Outer",
        "fillNAMethod": "Linear", 
        "paddingValue": 0
    },
    // This could be your own ZIP file of training data stored on Azure Blob and a SAS url could be used here
    "source": "https://aka.ms/AnomalyDetector/MVADSampleData", 
    "startTime": "2021-01-01T00:00:00Z", 
    "endTime": "2021-01-02T12:00:00Z", 
    "displayName": "Contoso model"
}
```

```python
# Sample Code in Python
########### Python 3.x #############
import http.client, urllib.request, urllib.parse, urllib.error, base64

headers = {
    # Request headers
    'Content-Type': 'application/json',
    'Ocp-Apim-Subscription-Key': '{API key}',
}

params = urllib.parse.urlencode({})

try:
    conn = http.client.HTTPSConnection('{endpoint}')
    conn.request("POST", "/anomalydetector/v1.1-preview/multivariate/models?%s" % params, "{request body}", headers)
    response = conn.getresponse()
    data = response.read()
    print(data)
    conn.close()
except Exception as e:
    print("[Errno {0}] {1}".format(e.errno, e.strerror))

####################################
```

Un código de respuesta de `201` indica una solicitud correcta.

[!INCLUDE [mvad-input-params](../includes/mvad-input-params.md)]

## <a name="4-get-model-status"></a>4. Obtención del estado del modelo

Como la API de entrenamiento es asincrónica, no se obtiene el modelo inmediatamente después de llamar a la API de entrenamiento. Sin embargo, puede consultar el estado de los modelos por clave de API, que enumerará todos los modelos, o por identificador de modelo, que mostrará información sobre el modelo específico.

### <a name="list-all-the-models"></a>Enumeración de todos los modelos

Puede consultar [esta página](https://westus2.dev.cognitive.microsoft.com/docs/services/AnomalyDetector-v1-1-preview/operations/ListMultivariateModel) para obtener información sobre la dirección URL de solicitud y los encabezados de solicitud. Tenga en cuenta que solo se devuelven 10 modelos ordenados por tiempo de actualización, pero puede visitar otros modelos estableciendo los parámetros `$skip` y `$top` en la dirección URL de la solicitud. Por ejemplo, si la dirección URL de la solicitud es `https://{endpoint}/anomalydetector/v1.1-preview/multivariate/models?$skip=10&$top=20`, omitiremos los 10 modelos más recientes y devolveremos los 20 modelos siguientes.

Una respuesta de ejemplo es: 

```json
{
    "models": [
         {
             "createdTime":"2020-12-01T09:43:45Z",
             "displayName":"DevOps-Test",
             "lastUpdatedTime":"2020-12-01T09:46:13Z",
             "modelId":"b4c1616c-33b9-11eb-824e-0242ac110002",
             "status":"READY",
             "variablesCount":18
         },
         {
             "createdTime":"2020-12-01T09:43:30Z",
             "displayName":"DevOps-Test",
             "lastUpdatedTime":"2020-12-01T09:45:10Z",
             "modelId":"ab9d3e30-33b9-11eb-a3f4-0242ac110002",
             "status":"READY",
             "variablesCount":18
         }
    ],
    "currentCount": 1,
    "maxCount": 50, 
    "nextLink": "<link to more models>"
}
```

La respuesta contiene 4 campos: `models`, `currentCount`, `maxCount` y `nextLink`.

* `models` contiene la hora de creación, la hora de la última actualización, el identificador del modelo, el nombre para mostrar, los recuentos de variables y el estado de cada modelo.
* `currentCount` contiene el número de modelos multivariante entrenados.
* `maxCount` es el número máximo de modelos admitidos por este recurso de Anomaly Detector.
* `nextLink` podría usarse para capturar más modelos.

### <a name="get-models-by-model-id"></a>Obtención de modelos por id. de modelo

[En esta página](https://westus2.dev.cognitive.microsoft.com/docs/services/AnomalyDetector-v1-1-preview/operations/GetMultivariateModel) se describe la dirección URL de solicitud para consultar la información del modelo por identificador de modelo. Un cuerpo de respuesta de muestra tiene el siguiente aspecto:

```json
{
        "modelId": "45aad126-aafd-11ea-b8fb-d89ef3400c5f",
        "createdTime": "2020-06-30T00:00:00Z",
        "lastUpdatedTime": "2020-06-30T00:00:00Z",
        "modelInfo": {
          "slidingWindow": 300,
          "alignPolicy": {
            "alignMode": "Outer",
            "fillNAMethod": "Linear",
            "paddingValue": 0
          },
          "source": "<TRAINING_ZIP_FILE_LOCATED_IN_AZURE_BLOB_STORAGE_WITH_SAS>",
          "startTime": "2019-04-01T00:00:00Z",
          "endTime": "2019-04-02T00:00:00Z",
          "displayName": "Devops-MultiAD",
          "status": "READY",
          "errors": [],
          "diagnosticsInfo": {
            "modelState": {
              "epochIds": [10, 20, 30, 40, 50, 60, 70, 80, 90, 100],
              "trainLosses": [0.6291328072547913, 0.1671326905488968, 0.12354248017072678, 0.1025966405868533, 
                              0.0958492755889896, 0.09069952368736267,0.08686016499996185, 0.0860302299260931,
                              0.0828735455870684, 0.08235538005828857],
              "validationLosses": [1.9232804775238037, 1.0645641088485718, 0.6031560301780701, 0.5302737951278687, 
                                   0.4698025286197664, 0.4395163357257843, 0.4182931482799006, 0.4057914316654053, 
                                   0.4056498706340729, 0.3849248886108984],
              "latenciesInSeconds": [0.3398594856262207, 0.3659665584564209, 0.37360644340515137, 
                                     0.3513407707214355, 0.3370304107666056, 0.31876277923583984, 
                                     0.3283309936523475, 0.3503587245941162, 0.30800247192382812,
                                     0.3327946662902832]
            },
            "variableStates": [
              {
                "variable": "ad_input",
                "filledNARatio": 0,
                "effectiveCount": 1441,
                "startTime": "2019-04-01T00:00:00Z",
                "endTime": "2019-04-02T00:00:00Z",
                "errors": []
              },
              {
                "variable": "ad_ontimer_output",
                "filledNARatio": 0,
                "effectiveCount": 1441,
                "startTime": "2019-04-01T00:00:00Z",
                "endTime": "2019-04-02T00:00:00Z",
                "errors": []
              },
              // More variables
            ]
          }
        }
      }
```

Recibirá información más detallada sobre el modelo consultado. La respuesta contiene metadatos sobre el modelo, sus parámetros de entrenamiento e información de diagnóstico. La información de diagnóstico es útil para depurar y realizar un seguimiento del progreso del entrenamiento.

* `epochIds` indica cuántas épocas se ha entrenado el modelo en un total de 100 épocas. Por ejemplo, si el modelo sigue en estado de entrenamiento, `epochId` podría ser `[10, 20, 30, 40, 50]`, lo que significa que ha completado su 50.ª época de entrenamiento y está a mitad de camino.
* `trainLosses` y `validationLosses` se usan para comprobar si el progreso de optimización converge en cuyo caso las dos pérdidas deben disminuir gradualmente.
* `latenciesInSeconds` contiene el costo de tiempo de cada época y se registra cada 10 épocas. En este ejemplo, la 10.ª época tarda aproximadamente 0,34 segundos. Esto sería útil para calcular el tiempo de finalización del entrenamiento.
* `variableStates` resume información sobre cada variable. Es una lista clasificada por `filledNARatio` en orden descendente. Indica cuántos puntos de datos se usan para cada variable y `filledNARatio` indica cuántos puntos faltan. Normalmente es necesario reducir `filledNARatio` tanto como sea posible.
Si faltan demasiados puntos de datos, se degradará la precisión del modelo.
* Los errores durante el procesamiento de datos se incluirán en el campo `errors`.

## <a name="5-inference-with-mvad"></a>5. Inferencia con MVAD

Para realizar la inferencia, basta con proporcionar el origen del blob al archivo ZIP que contiene los datos de inferencia, la hora de inicio y la hora de finalización.

La inferencia también es asincrónica, por lo que los resultados no se devuelven inmediatamente. Tenga en cuenta que debe guardar en una variable el vínculo de los resultados en el **encabezado de respuesta** que contiene `resultId`, para que pueda saber dónde obtener los resultados más adelante.

Los errores suelen deberse a problemas de modelo o problemas de datos. No se puede realizar la inferencia si el modelo no está listo o el vínculo de datos no es válido. Asegúrese de que los datos de entrenamiento y los datos de inferencia sean coherentes, lo que significa que deben ser **exactamente** las mismas variables, pero con marcas de tiempo diferentes. Más variables, menos variables o inferencia con un conjunto diferente de variables no pasarán la fase de comprobación de datos y se producirán errores. La comprobación de datos se aplaza para que reciba un mensaje de error solo cuando consulte los resultados.

## <a name="6-get-inference-results"></a>6. Obtención de resultados de inferencia

Necesita para `resultId` obtener resultados. `resultId` se obtiene del encabezado de respuesta cuando se envía la solicitud de inferencia. [Esta página](https://westus2.dev.cognitive.microsoft.com/docs/services/AnomalyDetector-v1-1-preview/operations/GetDetectionResult) contiene instrucciones para consultar los resultados de la inferencia. 

Un cuerpo de respuesta de muestra tiene el siguiente aspecto:

```json
 {
        "resultId": "663884e6-b117-11ea-b3de-0242ac130004",
        "summary": {
          "status": "READY",
          "errors": [],
          "variableStates": [
            {
              "variable": "ad_input",
              "filledNARatio": 0,
              "effectiveCount": 26,
              "startTime": "2019-04-01T00:00:00Z",
              "endTime": "2019-04-01T00:25:00Z",
              "errors": []
            },
            {
              "variable": "ad_ontimer_output",
              "filledNARatio": 0,
              "effectiveCount": 26,
              "startTime": "2019-04-01T00:00:00Z",
              "endTime": "2019-04-01T00:25:00Z",
              "errors": []
            },
            // more variables
          ],
          "setupInfo": {
            "source": "https://aka.ms/AnomalyDetector/MVADSampleData",
            "startTime": "2019-04-01T00:15:00Z",
            "endTime": "2019-04-01T00:40:00Z"
          }
        },
        "results": [
          {
            "timestamp": "2019-04-01T00:15:00Z",
            "errors": [
              {
                "code": "InsufficientHistoricalData",
                "message": "historical data is not enough."
              }
            ]
          },
          // more results
          {
            "timestamp": "2019-04-01T00:20:00Z",
            "value": {
              "contributors": [],
              "isAnomaly": false,
              "severity": 0,
              "score": 0.17805261260751692
            }
          },
          // more results
          {
            "timestamp": "2019-04-01T00:27:00Z",
            "value": {
              "contributors": [
                {
                  "contributionScore": 0.0007775013367514271,
                  "variable": "ad_ontimer_output"
                },
                {
                  "contributionScore": 0.0007989604079048129,
                  "variable": "ad_series_init"
                },
                {
                  "contributionScore": 0.0008900927229851369,
                  "variable": "ingestion"
                },
                {
                  "contributionScore": 0.008068144477478554,
                  "variable": "cpu"
                },
                {
                  "contributionScore": 0.008222036467507165,
                  "variable": "data_in_speed"
                },
                {
                  "contributionScore": 0.008674941549594993,
                  "variable": "ad_input"
                },
                {
                  "contributionScore": 0.02232242629793674,
                  "variable": "ad_output"
                },
                {
                  "contributionScore": 0.1583773213660846,
                  "variable": "flink_last_ckpt_duration"
                },
                {
                  "contributionScore": 0.9816531517495176,
                  "variable": "data_out_speed"
                }
              ],
              "isAnomaly": true,
              "severity": 0.42135109874230336,
              "score": 1.213510987423033
            }
          },
          // more results
        ]
      }
```

La respuesta contiene el estado del resultado, la información de variables, los parámetros de inferencia y los resultados de la inferencia.

* `variableStates` muestra la información de cada variable en la solicitud de inferencia.
* `setupInfo` es el cuerpo de la solicitud enviado para esta inferencia.
* `results` contiene los resultados de la detección. Hay tres tipos típicos de resultados de detección.
    1. Código de error `InsufficientHistoricalData`. Esto suele ocurrir solo con las primeras marcas de tiempo porque el modelo inferencia los datos de una manera basada en ventanas y necesita datos históricos para tomar una decisión. Para las primeras marcas de tiempo, no hay suficientes datos históricos, por lo que no se puede realizar la inferencia en ellas. En este caso, se puede omitir el mensaje de error.
    1. `"isAnomaly": false` indica que la marca de tiempo actual no es una anomalía.
        * `severity ` indica la gravedad relativa de la anomalía y para los datos normales siempre es 0.
        * `score` es la salida sin procesar del modelo en el que el modelo toma una decisión que podría ser distinto de cero incluso para los puntos de datos normales.
    1. `"isAnomaly": true` indica una anomalía en la marca de tiempo actual.
        * `severity ` indica la gravedad relativa de la anomalía y para los datos normales siempre es mayor que 0.
        * `score` es la salida sin procesar del modelo en el que el modelo toma una decisión. `severity` es un valor derivado de `score`. Cada punto de datos tiene un `score`.
        * `contributors` es una lista que contiene la puntuación de contribución de cada variable. Las puntuaciones de contribución más altas indican una mayor posibilidad de la causa principal. Esta lista se usa a menudo para interpretar anomalías, así como para diagnosticar las causas principales.

> [!NOTE]
> Un problema común es tomar todos los puntos de datos con `isAnomaly`=`true` como anomalías. Esto puede acabar con demasiados falsos positivos.
> Debe usar `isAnomaly` y `severity` (o `score`) para averiguar las anomalías que no son graves y (opcionalmente) usar la agrupación para comprobar la duración de las anomalías para suprimir el ruido aleatorio. Consulte las [preguntas más frecuentes](../concepts/best-practices-multivariate.md#faq) del documento de procedimientos recomendados para ver la diferencia entre `severity` y `score`.

## <a name="next-steps"></a>Pasos siguientes

* [Procedimientos recomendados: Procedimientos recomendados que se deben seguir al usar las API de Anomaly Detector multivariante](../concepts/best-practices-multivariate.md)
* [Inicios rápidos: Uso de la biblioteca cliente multivariante de Anomaly Detector](../quickstarts/client-libraries-multivariate.md)