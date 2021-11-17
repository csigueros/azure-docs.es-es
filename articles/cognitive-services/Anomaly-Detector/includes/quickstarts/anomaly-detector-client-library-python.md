---
title: Inicio rápido de la biblioteca cliente de Python de Anomaly Detector
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 11/25/2020
ms.author: mbullwin
ms.openlocfilehash: 84510afb4a6bac20f7b0496bf99a3e2a5d2571f9
ms.sourcegitcommit: 4cd97e7c960f34cb3f248a0f384956174cdaf19f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/08/2021
ms.locfileid: "132040387"
---
Comience a usar la biblioteca cliente de Anomaly Detector para Python. Siga estos pasos para instalar el inicio del paquete con los algoritmos que proporciona el servicio. El servicio de Anomaly Detector le permite detectar anomalías en los datos de serie temporal mediante el uso automático de los mejores modelos, independientemente del sector, el escenario o el volumen de datos.

Use la biblioteca cliente de Anomaly Detector para Python para las siguientes acciones:

* Detectar anomalías en el conjunto de datos de serie temporal como una solicitud por lotes
* Detectar el estado de anomalía del punto de datos más reciente en la serie temporal
* Detectar puntos de cambio de tendencia en el conjunto de datos.

[Documentación de referencia de la biblioteca](https://go.microsoft.com/fwlink/?linkid=2090370) | [Código fuente de la biblioteca](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-anomalydetector) | [Paquete (PyPi)](https://pypi.org/project/azure-ai-anomalydetector/) | [Buscar el ejemplo de código en GitHub](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/anomalydetector/azure-ai-anomalydetector/samples)

## <a name="prerequisites"></a>Prerrequisitos

* [Python 3.x](https://www.python.org/)
* La [biblioteca de análisis de datos de Pandas](https://pandas.pydata.org/)
* Una suscripción a Azure: [cree una cuenta gratuita](https://azure.microsoft.com/free/cognitive-services)
* Cuando tenga la suscripción de Azure, <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector"  title="Creación de un recurso de Anomaly Detector"  target="_blank">cree un recurso de Anomaly Detector </a> en Azure Portal para obtener la clave y el punto de conexión. Espere a que se implemente y haga clic en el botón **Ir al recurso**.
    * Necesitará la clave y el punto de conexión del recurso que cree para conectar la aplicación a Anomaly Detector API. En una sección posterior de este mismo inicio rápido pegará la clave y el punto de conexión en el código siguiente.
    Puede usar el plan de tarifa gratis (`F0`) para probar el servicio y actualizarlo más adelante a un plan de pago para producción.


## <a name="setting-up"></a>Instalación

[!INCLUDE [anomaly-detector-environment-variables](../environment-variables.md)]

### <a name="create-a-new-python-application"></a>Creación de una nueva aplicación de Python

 Cree un archivo de Python e importe las bibliotecas siguientes.

```python
import os
from azure.ai.anomalydetector import AnomalyDetectorClient
from azure.ai.anomalydetector.models import DetectRequest, TimeSeriesPoint, TimeGranularity, \
    AnomalyDetectorError
from azure.core.credentials import AzureKeyCredential
import pandas as pd
```

Cree variables para la clave como una variable de entorno, la ruta de acceso a un archivo de datos de serie temporal y la ubicación de Azure de su suscripción. Por ejemplo, `westus2`.

```python
SUBSCRIPTION_KEY = os.environ["ANOMALY_DETECTOR_KEY"]
ANOMALY_DETECTOR_ENDPOINT = os.environ["ANOMALY_DETECTOR_ENDPOINT"]
TIME_SERIES_DATA_PATH = os.path.join("./sample_data", "request-data.csv")
```

### <a name="install-the-client-library"></a>Instalación de la biblioteca cliente

Después de instalar Python, puede instalar la biblioteca cliente con:

```console
pip install --upgrade azure-ai-anomalydetector
```

## <a name="object-model"></a>Modelo de objetos

El cliente de Anomaly Detector es un objeto [AnomalyDetectorClient](https://github.com/Azure/azure-sdk-for-python/blob/0b8622dc249969c2f01c5d7146bd0bb36bb106dd/sdk/cognitiveservices/azure-cognitiveservices-anomalydetector/azure/cognitiveservices/anomalydetector/_anomaly_detector_client.py) que se autentica en Azure mediante la clave. El cliente puede realizar detección de anomalías en un conjunto de datos completo mediante [detect_entire_series](https://github.com/Azure/azure-sdk-for-python/blob/bf9d44f2a50aea46a59c4cb83ccfccaff5e2b218/sdk/anomalydetector/azure-ai-anomalydetector/azure/ai/anomalydetector/operations/_anomaly_detector_client_operations.py#L26), o en el punto de datos más reciente mediante [detect_last_point](https://github.com/Azure/azure-sdk-for-python/blob/bf9d44f2a50aea46a59c4cb83ccfccaff5e2b218/sdk/anomalydetector/azure-ai-anomalydetector/azure/ai/anomalydetector/operations/_anomaly_detector_client_operations.py#L87). La función [detect_change_point](https://github.com/Azure/azure-sdk-for-python/blob/bf9d44f2a50aea46a59c4cb83ccfccaff5e2b218/sdk/anomalydetector/azure-ai-anomalydetector/azure/ai/anomalydetector/aio/operations_async/_anomaly_detector_client_operations_async.py#L142) detecta los puntos que marcan los cambios en una tendencia.

Los datos de series temporales se envían como una serie de objetos [TimeSeriesPoints](https://github.com/Azure/azure-sdk-for-python/blob/bf9d44f2a50aea46a59c4cb83ccfccaff5e2b218/sdk/anomalydetector/azure-ai-anomalydetector/azure/ai/anomalydetector/models/_models_py3.py#L370). Por ejemplo, el objeto `DetectRequest` contiene propiedades para describir los datos de `TimeGranularity`, así como los parámetros para la detección de anomalías.

La respuesta de Anomaly Detector es un objeto [LastDetectResponse](/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.lastdetectresponse), [EntireDetectResponse](/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.entiredetectresponse) o [ChangePointDetectResponse](https://github.com/Azure/azure-sdk-for-python/blob/bf9d44f2a50aea46a59c4cb83ccfccaff5e2b218/sdk/anomalydetector/azure-ai-anomalydetector/azure/ai/anomalydetector/models/_models_py3.py#L107), según el método usado.

## <a name="code-examples"></a>Ejemplos de código

Estos fragmentos de código muestran cómo realizar las siguientes acciones con la biblioteca cliente de Anomaly Detector para Python:

* [Autenticar el cliente](#authenticate-the-client)
* [Cargar un conjunto de datos de serie temporal desde un archivo](#load-time-series-data-from-a-file)
* [Detectar anomalías en todo el conjunto de datos](#detect-anomalies-in-the-entire-data-set)
* [Detectar el estado de anomalía del punto de datos más reciente](#detect-the-anomaly-status-of-the-latest-data-point)
* [Detección de los puntos de cambio en el conjunto de datos](#detect-change-points-in-the-data-set)

## <a name="authenticate-the-client"></a>Autenticar el cliente

Agregue la variable de ubicación de Azure al punto de conexión y autentique el cliente con la clave.

```python
client = AnomalyDetectorClient(AzureKeyCredential(SUBSCRIPTION_KEY), ANOMALY_DETECTOR_ENDPOINT)
```

## <a name="load-time-series-data-from-a-file"></a>Cargar datos de serie temporal desde un archivo

Descargue los datos de ejemplo de este inicio rápido desde [GitHub](https://github.com/Azure-Samples/AnomalyDetector/blob/master/example-data/request-data.csv):
1. En el explorador, haga clic con el botón derecho en **Raw** (Sin formato).
2. Haga clic en **Save link as** (Guardar vínculo como).
3. Guarde el archivo como .csv en el directorio de aplicaciones.

Estos datos de serie temporal tienen el formato de un archivo .csv y se enviarán a Anomaly Detector API.

Cargue el archivo de datos con el método `read_csv()` de la biblioteca de Pandas y cree una variable de lista vacía para almacenar la serie de datos. Itere el archivo y anexe los datos como un objeto `TimeSeriesPoint`. Este objeto contendrá la marca de tiempo y el valor numérico de las filas del archivo de datos .csv.

```python
series = []
data_file = pd.read_csv(TIME_SERIES_DATA_PATH, header=None, encoding='utf-8', parse_dates=[0])
for index, row in data_file.iterrows():
    series.append(TimeSeriesPoint(timestamp=row[0], value=row[1]))
```

Cree un objeto `DetectRequest` con la serie temporal y la `TimeGranularity` (o periodicidad) de sus puntos de datos. Por ejemplo, `TimeGranularity.daily`.

```python
request = DetectRequest(series=series, granularity=TimeGranularity.daily)
```

Descripciones de argumentos de entrada: "series": necesario en la solicitud. debe ser tipo matriz o lista y tener más de 12 puntos y no más de 8640 puntos. debe ordenarse por marca de tiempo en orden ascendente y no puede tener una marca de tiempo duplicada. "granularidad": se requiere en la solicitud. solo puede ser uno de los siguientes: ['daily', 'minutely', 'hourly', 'weekly', 'monthly', 'yearly', 'secondly'].
"customInterval": debe ser un entero >0.
"period": debe ser un entero >0.
"maxAnomalyRatio": debe ser inferior al 50 % de los puntos de la serie (0 < maxAnomalyRatio < 0,5).
"sensitivity": debe ser un entero entre 0 y 99.

## <a name="detect-anomalies-in-the-entire-data-set"></a>Detectar anomalías en todo el conjunto de datos

Llame a la API para detectar anomalías en el conjunto de datos de serie temporal mediante el método `detect_entire_series` del cliente. Almacene el objeto [EntireDetectResponse](/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.entiredetectresponse) devuelto. Itere la lista `is_anomaly` de la respuesta e imprima el índice de los valores `true`. Estos valores se corresponden con el índice de los puntos de datos anómalos, si se detecta alguno.

```python
print('Detecting anomalies in the entire time series.')

try:
    response = client.detect_entire_series(request)
except AnomalyDetectorError as e:
    print('Error code: {}'.format(e.error.code), 'Error message: {}'.format(e.error.message))
except Exception as e:
    print(e)

if any(response.is_anomaly):
    print('An anomaly was detected at index:')
    for i, value in enumerate(response.is_anomaly):
        if value:
            print(i)
else:
    print('No anomalies were detected in the time series.')
```

## <a name="detect-the-anomaly-status-of-the-latest-data-point"></a>Detección del estado de anomalía del punto de datos más reciente

Llame a la API Anomaly Detector para determinar si el punto de datos más reciente es una anomalía mediante el método `detect_last_point` del cliente y almacene el objeto `LastDetectResponse` devuelto. El valor `is_anomaly` de la respuesta es un valor booleano que especifica el estado de anomalía de ese punto.  

```python
print('Detecting the anomaly status of the latest data point.')

try:
    response = client.detect_last_point(request)
except AnomalyDetectorError as e:
    print('Error code: {}'.format(e.error.code), 'Error message: {}'.format(e.error.message))
except Exception as e:
    print(e)

if response.is_anomaly:
    print('The latest point is detected as anomaly.')
else:
    print('The latest point is not detected as anomaly.')
```

## <a name="detect-change-points-in-the-data-set"></a>Detección de los puntos de cambio en el conjunto de datos

Llame a la API para detectar los puntos de cambio de los datos de la serie temporal con el método`detect_change_point` del cliente. Almacene el objeto `ChangePointDetectResponse` devuelto. Itere la lista `is_change_point` de la respuesta e imprima el índice de los valores `true`. Estos valores corresponden a los índices de los puntos de cambio de tendencia, si se encuentra alguno.

```python
print('Detecting change points in the entire time series.')

try:
    response = client.detect_change_point(request)
except AnomalyDetectorError as e:
    print('Error code: {}'.format(e.error.code), 'Error message: {}'.format(e.error.message))
except Exception as e:
    print(e)

if any(response.is_change_point):
    print('An change point was detected at index:')
    for i, value in enumerate(response.is_change_point):
        if value:
            print(i)
else:
    print('No change point were detected in the time series.')
```

## <a name="run-the-application"></a>Ejecución de la aplicación

Ejecute la aplicación con el comando `python` en el nombre de archivo.

[!INCLUDE [anomaly-detector-next-steps](../quickstart-cleanup-next-steps.md)]
