---
title: Datos de seguridad de Nueva York
titleSuffix: Azure Open Datasets
description: Aprenda a usar el conjunto de datos New York City Safety de Azure Open Datasets.
ms.service: open-datasets
ms.topic: sample
author: peterclu
ms.author: peterlu
ms.date: 04/16/2021
ms.openlocfilehash: d8efbd7469ed92b4c323ed3d94315ec8f0f4dc5c
ms.sourcegitcommit: c05e595b9f2dbe78e657fed2eb75c8fe511610e7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/11/2021
ms.locfileid: "112038985"
---
# <a name="new-york-city-safety-data"></a>Datos de seguridad de Nueva York

Todas las solicitudes de servicio en el número 311 de la ciudad de Nueva York desde 2010 hasta la actualidad.

[!INCLUDE [Open Dataset usage notice](../../includes/open-datasets-usage-note.md)]


## <a name="volume-and-retention"></a>Volumen y retención

Este conjunto de datos se almacena en formato Parquet. Se actualiza a diario y contiene alrededor de 12 millones de filas (500 MB) en total desde 2019.

Este conjunto de datos contiene registros históricos acumulados desde 2010 hasta la actualidad. Puede usar la configuración de parámetros de nuestro SDK para recuperar los datos de un intervalo de tiempo específico.

## <a name="storage-location"></a>Ubicación de almacenamiento

Este conjunto de datos se almacena en la región Este de EE. UU. de Azure. Se recomienda asignar recursos de proceso de la misma región por afinidad.

## <a name="additional-information"></a>Información adicional

Este conjunto de datos se obtiene del gobierno de la ciudad de Nueva York. Para obtener más información, vea el [sitio web de la ciudad de Nueva York](https://data.cityofnewyork.us/Social-Services/311-Service-Requests-from-2010-to-Present/erm2-nwe9). Vea los [términos de este conjunto de datos](https://www1.nyc.gov/home/terms-of-use.page).

## <a name="columns"></a>Columnas

| Nombre | Tipo de datos | Único | Valores (ejemplo) | Descripción |
|-|-|-|-|-|
| address | string | 1,536,593 | 655 EAST 230 STREET 78-15 PARSONS BOULEVARD | Número de casa de la dirección del incidente proporcionado por el remitente. |
| category | string | 446 | Noise - Residential HEAT/HOT WATER | Este es el primer nivel de una jerarquía que identifica el tema del incidente o la condición (tipo de queja). Puede tener una subcategoría correspondiente (descriptor) o ser independiente. |
| dataSubtype | string | 1 | 311_All | “311_All” |
| dataType | string | 1 | Seguridad | “Safety” |
| dateTime | timestamp | 17,332,609 | 2013-01-24 00:00:00 2015-01-08 00:00:00 | Fecha en la que se creó la solicitud de servicio. |
| latitude | double | 1,513,691 | 40.89187241649303 40.72195913199264 | Latitud geográfica de la ubicación donde ocurrió el incidente. |
| longitude | double | 1,513,713 | -73.86016845296459 -73.80969682426189 | Longitud geográfica de la ubicación donde ocurrió el incidente. |
| status | string | 13 | Closed Pending | Estado de la solicitud de servicio enviada. |
| subcategory | string | 1,716 | Loud Music/Party ENTIRE BUILDING | Está asociada a la categoría (tipo de reclamación) y proporciona más detalles sobre el incidente o la condición. Los valores dependen del tipo de reclamación y no siempre son necesarios en las solicitudes de servicio. |

## <a name="preview"></a>Vista previa

| dataType | dataSubtype | dateTime | category | subcategory | status | address | latitude | longitude | source | extendedProperties |
|-|-|-|-|-|-|-|-|-|-|-|
| Seguridad | 311_All | 4/25/2021 2:05:05 AM | Noise - Street/Sidewalk | Loud Music/Party | En curso | 2766 BATH AVENUE | 40.5906129741766 | -73.9847949011337 | null |  |
| Seguridad | 311_All | 4/25/2021 2:04:33 AM | Noise - Commercial | Loud Music/Party | En curso | 1033 WEBSTER AVENUE | 40.8285784533256 | -73.9117746958432 | null |  |
| Seguridad | 311_All | 4/25/2021 2:04:27 AM | Noise - Residential | Loud Music/Party | En curso | 620 WEST 141 STREET | 40.8241726554395 | -73.9530069547366 | null |  |
| Seguridad | 311_All | 4/25/2021 2:04:04 AM | Noise - Residential | Loud Music/Party | En curso | 1647 64 STREET | 40.6218907202382 | -73.9931125332078 | null |  |
| Seguridad | 311_All | 4/25/2021 2:04:01 AM | Noise - Residential | Loud Music/Party | En curso | 30 LENOX AVENUE | 40.7991622274945 | -73.9517496365803 | null |  |
| Seguridad | 311_All | 4/25/2021 2:03:40 AM | Illegal Parking | Double Parked Blocking Traffic | En curso | 304 WEST 148 STREET | 40.8248229687124 | -73.940696262361 | null |  |
| Seguridad | 311_All | 4/25/2021 2:03:31 AM | Noise - Street/Sidewalk | Loud Music/Party | En curso | ADEE AVENUE | 40.8708386263454 | -73.8382363208686 | null |  |
| Seguridad | 311_All | 4/25/2021 2:03:18 AM | Noise - Residential | Loud Music/Party | En curso | 340 EVERGREEN AVENUE | 40.6947512704197 | -73.9248330229197 | null |  |
| Seguridad | 311_All | 4/25/2021 2:03:13 AM | Noise - Residential | Banging/Pounding | En curso | 25 REMSEN STREET | 40.6948938116483 | -73.9973494607802 | null |  |

## <a name="data-access"></a>Acceso a datos

### <a name="azure-notebooks"></a>Azure Notebooks

# <a name="azureml-opendatasets"></a>[azureml-opendatasets](#tab/azureml-opendatasets)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azureml-opendatasets&registryId=city_safety_sanfrancisco -->

> [!TIP]
> **[Descargue en su lugar el cuaderno](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azureml-opendatasets&registryId=city_safety_sanfrancisco)** .

```
# This is a package in preview.
from azureml.opendatasets import SanFranciscoSafety

from datetime import datetime
from dateutil import parser


end_date = parser.parse('2016-01-01')
start_date = parser.parse('2015-05-01')
safety = SanFranciscoSafety(start_date=start_date, end_date=end_date)
safety = safety.to_pandas_dataframe()
```

```
safety.info()
```

<!-- nbend -->

# <a name="azure-storage"></a>[azure-storage](#tab/azure-storage)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azure-storage&registryId=city_safety_sanfrancisco -->

> [!TIP]
> **[Descargue en su lugar el cuaderno](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azure-storage&registryId=city_safety_sanfrancisco)** .

```python
# Pip install packages
import os, sys

!{sys.executable} -m pip install azure-storage-blob
!{sys.executable} -m pip install pyarrow
!{sys.executable} -m pip install pandas
```

```python
# Azure storage access info
azure_storage_account_name = "azureopendatastorage"
azure_storage_sas_token = r""
container_name = "citydatacontainer"
folder_name = "Safety/Release/city=SanFrancisco"
```

```python
from azure.storage.blob import BlockBlobServicefrom azure.storage.blob import BlobServiceClient, BlobClient, ContainerClient

if azure_storage_account_name is None or azure_storage_sas_token is None:
    raise Exception(
        "Provide your specific name and key for your Azure Storage account--see the Prerequisites section earlier.")

print('Looking for the first parquet under the folder ' +
      folder_name + ' in container "' + container_name + '"...')
container_url = f"https://{azure_storage_account_name}.blob.core.windows.net/"
blob_service_client = BlobServiceClient(
    container_url, azure_storage_sas_token if azure_storage_sas_token else None)

container_client = blob_service_client.get_container_client(container_name)
blobs = container_client.list_blobs(folder_name)
sorted_blobs = sorted(list(blobs), key=lambda e: e.name, reverse=True)
targetBlobName = ''
for blob in sorted_blobs:
    if blob.name.startswith(folder_name) and blob.name.endswith('.parquet'):
        targetBlobName = blob.name
        break

print('Target blob to download: ' + targetBlobName)
_, filename = os.path.split(targetBlobName)
blob_client = container_client.get_blob_client(targetBlobName)
with open(filename, 'wb') as local_file:
    blob_client.download_blob().download_to_stream(local_file)
```

```python
# Read the parquet file into Pandas data frame
import pandas as pd

print('Reading the parquet file into Pandas data frame')
df = pd.read_parquet(filename)
```

```python
# you can add your filter at below
print('Loaded as a Pandas data frame: ')
df
```

<!-- nbend -->

# <a name="pyspark"></a>[pyspark](#tab/pyspark)

Ejemplo no disponible para esta combinación de plataforma y paquete.

---

### <a name="azure-databricks"></a>Azure Databricks

# <a name="azureml-opendatasets"></a>[azureml-opendatasets](#tab/azureml-opendatasets)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureDatabricks&package=azureml-opendatasets&registryId=city_safety_sanfrancisco -->

> [!TIP]
> **[Descargue en su lugar el cuaderno](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureDatabricks&package=azureml-opendatasets&registryId=city_safety_sanfrancisco)** .

```
# This is a package in preview.
# You need to pip install azureml-opendatasets in Databricks cluster. https://docs.microsoft.com/en-us/azure/data-explorer/connect-from-databricks#install-the-python-library-on-your-azure-databricks-cluster
from azureml.opendatasets import SanFranciscoSafety

from datetime import datetime
from dateutil import parser


end_date = parser.parse('2016-01-01')
start_date = parser.parse('2015-05-01')
safety = SanFranciscoSafety(start_date=start_date, end_date=end_date)
safety = safety.to_spark_dataframe()
```

```
display(safety.limit(5))
```

<!-- nbend -->

# <a name="azure-storage"></a>[azure-storage](#tab/azure-storage)

Ejemplo no disponible para esta combinación de plataforma y paquete.

# <a name="pyspark"></a>[pyspark](#tab/pyspark)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureDatabricks&package=pyspark&registryId=city_safety_sanfrancisco -->

> [!TIP]
> **[Descargue en su lugar el cuaderno](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureDatabricks&package=pyspark&registryId=city_safety_sanfrancisco)** .

```python
# Azure storage access info
blob_account_name = "azureopendatastorage"
blob_container_name = "citydatacontainer"
blob_relative_path = "Safety/Release/city=SanFrancisco"
blob_sas_token = r""
```

```python
# Allow SPARK to read from Blob remotely
wasbs_path = 'wasbs://%s@%s.blob.core.windows.net/%s' % (blob_container_name, blob_account_name, blob_relative_path)
spark.conf.set(
  'fs.azure.sas.%s.%s.blob.core.windows.net' % (blob_container_name, blob_account_name),
  blob_sas_token)
print('Remote blob path: ' + wasbs_path)
```

```python
# SPARK read parquet, note that it won't load any data yet by now
df = spark.read.parquet(wasbs_path)
print('Register the DataFrame as a SQL temporary view: source')
df.createOrReplaceTempView('source')
```

```python
# Display top 10 rows
print('Displaying top 10 rows: ')
display(spark.sql('SELECT * FROM source LIMIT 10'))
```

<!-- nbend -->

---

### <a name="azure-synapse"></a>Azure Synapse

# <a name="azureml-opendatasets"></a>[azureml-opendatasets](#tab/azureml-opendatasets)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureSynapse&package=azureml-opendatasets&registryId=city_safety_sanfrancisco -->

> [!TIP]
> **[Descargue en su lugar el cuaderno](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureSynapse&package=azureml-opendatasets&registryId=city_safety_sanfrancisco)** .

```python
# This is a package in preview.
from azureml.opendatasets import SanFranciscoSafety

from datetime import datetime
from dateutil import parser


end_date = parser.parse('2016-01-01')
start_date = parser.parse('2015-05-01')
safety = SanFranciscoSafety(start_date=start_date, end_date=end_date)
safety = safety.to_spark_dataframe()
```

```python
# Display top 5 rows
display(safety.limit(5))
```

<!-- nbend -->

# <a name="azure-storage"></a>[azure-storage](#tab/azure-storage)

Ejemplo no disponible para esta combinación de plataforma y paquete.

# <a name="pyspark"></a>[pyspark](#tab/pyspark)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureSynapse&package=pyspark&registryId=city_safety_sanfrancisco -->

> [!TIP]
> **[Descargue en su lugar el cuaderno](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureSynapse&package=pyspark&registryId=city_safety_sanfrancisco)** .

```python
# Azure storage access info
blob_account_name = "azureopendatastorage"
blob_container_name = "citydatacontainer"
blob_relative_path = "Safety/Release/city=SanFrancisco"
blob_sas_token = r""
```

```python
# Allow SPARK to read from Blob remotely
wasbs_path = 'wasbs://%s@%s.blob.core.windows.net/%s' % (blob_container_name, blob_account_name, blob_relative_path)
spark.conf.set(
  'fs.azure.sas.%s.%s.blob.core.windows.net' % (blob_container_name, blob_account_name),
  blob_sas_token)
print('Remote blob path: ' + wasbs_path)
```

```python
# SPARK read parquet, note that it won't load any data yet by now
df = spark.read.parquet(wasbs_path)
print('Register the DataFrame as a SQL temporary view: source')
df.createOrReplaceTempView('source')
```

```python
# Display top 10 rows
print('Displaying top 10 rows: ')
display(spark.sql('SELECT * FROM source LIMIT 10'))
```

<!-- nbend -->

---

## <a name="examples"></a>Ejemplos

- Consulte el ejemplo [City Safety Analytics](https://github.com/scottcounts/CitySafety) en GitHub.


## <a name="next-steps"></a>Pasos siguientes

Consulte el resto de los conjuntos de datos en el [catálogo de Open Datasets](dataset-catalog.md).