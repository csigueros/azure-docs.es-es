---
title: Datos de seguridad de Boston
titleSuffix: Azure Open Datasets
description: Aprenda a usar el conjunto de datos Boston Safety Data en Azure Open Datasets.
ms.service: open-datasets
ms.topic: sample
author: peterclu
ms.author: peterlu
ms.date: 04/16/2021
ms.openlocfilehash: 1c9a4917f1b5d2b719a247d111b62897f8063bc9
ms.sourcegitcommit: c05e595b9f2dbe78e657fed2eb75c8fe511610e7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/11/2021
ms.locfileid: "112039128"
---
# <a name="boston-safety-data"></a>Datos de seguridad de Boston

Llamadas al número 311 en la ciudad de Boston.

Consulte este vínculo para obtener más información sobre [BOS:311](https://www.cityofboston.gov/311/).

[!INCLUDE [Open Dataset usage notice](../../includes/open-datasets-usage-note.md)]

## <a name="volume-and-retention"></a>Volumen y retención

Este conjunto de datos se almacena en formato Parquet. Se actualiza a diario y contiene alrededor de cien mil filas (10 MB) en total a fecha de 2019.

Este conjunto de datos contiene registros históricos acumulados desde 2011 hasta la actualidad. Puede usar la configuración de parámetros de nuestro SDK para recuperar los datos de un intervalo de tiempo específico.

## <a name="storage-location"></a>Ubicación de almacenamiento

Este conjunto de datos se almacena en la región Este de EE. UU. de Azure. Se recomienda asignar recursos de proceso de la misma región por afinidad.

## <a name="additional-information"></a>Información adicional

Este conjunto de datos se alimenta con los datos de la administración pública de la ciudad de Boston. Para más información, consulte el [sitio del conjunto de datos de Boston](https://data.boston.gov/dataset/311-service-requests). Para las licencias de conjuntos de datos, consulte [Open Data Commons Public Domain Licensing and License (ODC PDDL)](http://opendefinition.org/licenses/odc-pddl/).

## <a name="columns"></a>Columnas

| Nombre | Tipo de datos | Único | Valores (ejemplo) | Descripción |
|-|-|-|-|-|
| address | string | 140,612 | \" \" 1 City Hall Plz Boston MA 02108 | Ubicación. |
| category | string | 54 | Recogida de basuras y limpieza de las calles | Motivo de la solicitud de servicio. |
| dataSubtype | string | 1 | 311_All | “311_All” |
| dataType | string | 1 | Seguridad | “Safety” |
| dateTime | timestamp | 1,529,075 | 2015-07-23 10:51:00 2015-07-23 10:47:00 | Fecha y hora de apertura de la solicitud de servicio. |
| latitude | double | 1622 | 42.3594 42.3603 | Este es el valor de la latitud. Las líneas de la latitud son paralelas al ecuador. |
| longitude | double | 1,806 | -71.0587 -71.0583 | Este es el valor de la longitud. Las líneas de la longitud son perpendiculares a las líneas de la latitud y todas pasan por los dos polos. |
| source | string | 7 | Constituent Call Citizens Connect App | Fuente original del caso. |
| status | string | 2 | Cerrado Abierto | Estado del caso. |
| subcategory | string | 209 | Solicitudes de cumplimiento del aparcamiento para la limpieza de las calles | Tipo de solicitud de servicio. |

## <a name="preview"></a>Vista previa

| ataType | dataSubtype | dateTime | category | subcategory | status | address | latitude | longitude | source | extendedProperties |
|-|-|-|-|-|-|-|-|-|-|-|
| Seguridad | 311_All | 4/27/2021 11:45:49 PM | Aplicación y abandono de vehículos | Cumplimiento del aparcamiento | Abrir | 51 Gardner St Allston MA 02134 | 42.3535 | -71.1285 | Citizens Connect App |  |
| Seguridad | 311_All | 4/27/2021 11:43:43 PM | Recogida de basuras | Falta papelera, reciclaje, poda, elemento masivo | Abrir | 4 Putnam Pl Roxbury MA 02119 | 42.3298 | -71.0883 | Autoservicio |  |
| Seguridad | 311_All | 4/27/2021 11:37:19 PM | Aplicación y abandono de vehículos | Cumplimiento del aparcamiento | Abrir | 36 Raven St Dorchester MA 02125 | 42.3177 | -71.0546 | Citizens Connect App |  |
| Seguridad | 311_All | 4/27/2021 11:30:00 PM | Recogida de basuras | Falta papelera, reciclaje, poda, elemento masivo | Abrir | 58 Bicknell St Dorchester MA 02121 | 42.2984 | -71.0834 | Llamada constituyente |  |
| Seguridad | 311_All | 4/27/2021 11:10:20 PM | Aplicación y abandono de vehículos | Cumplimiento del aparcamiento | Abrir | 2000 Commonwealth Ave Brighton MA 02135 | 42.3394 | -71.1585 | Citizens Connect App |  |
| Seguridad | 311_All | 4/27/2021 11:06:00 PM | Perturbación por ruido | Fiestas ruidosas, música, gente | Abrir | INTERSECTION of Lewis St & North St Boston MA | 42.3594 | -71.0587 | Llamada constituyente |  |
| Seguridad | 311_All | 4/27/2021 11:05:00 PM | Aplicación y abandono de vehículos | Cumplimiento del aparcamiento | Abrir | 1 Nassau St Boston MA 02111 | 42.3486 | -71.0629 | Llamada constituyente |  |
| Seguridad | 311_All | 4/27/2021 11:00:55 PM | Cumplimiento del código | Estado degradado de la propiedad | Abrir | 17 Mercer St South Boston MA 02127 | 42.3332 | -71.0492 | Citizens Connect App |  |

## <a name="data-access"></a>Acceso a datos

### <a name="azure-notebooks"></a>Azure Notebooks

# <a name="azureml-opendatasets"></a>[azureml-opendatasets](#tab/azureml-opendatasets)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azureml-opendatasets&registryId=city_safety_boston -->

> [!TIP]
> **[Descargue en su lugar el cuaderno](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azureml-opendatasets&registryId=city_safety_boston)** .

```
# This is a package in preview.
from azureml.opendatasets import BostonSafety

from datetime import datetime
from dateutil import parser

end_date = parser.parse('2016-01-01')
start_date = parser.parse('2015-05-01')
safety = BostonSafety(start_date=start_date, end_date=end_date)
safety = safety.to_pandas_dataframe()
```

```
safety.info()
```

<!-- nbend -->


# <a name="azure-storage"></a>[azure-storage](#tab/azure-storage)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azure-storage&registryId=city_safety_boston -->

> [!TIP]
> **[Descargue en su lugar el cuaderno](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azure-storage&registryId=city_safety_boston)** .

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
folder_name = "Safety/Release/city=Boston"
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

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureDatabricks&package=azureml-opendatasets&registryId=city_safety_boston -->

> [!TIP]
> **[Descargue en su lugar el cuaderno](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureDatabricks&package=azureml-opendatasets&registryId=city_safety_boston)** .

```
# This is a package in preview.
# You need to pip install azureml-opendatasets in Databricks cluster. https://docs.microsoft.com/en-us/azure/data-explorer/connect-from-databricks#install-the-python-library-on-your-azure-databricks-cluster
from azureml.opendatasets import BostonSafety

from datetime import datetime
from dateutil import parser


end_date = parser.parse('2016-01-01')
start_date = parser.parse('2015-05-01')
safety = BostonSafety(start_date=start_date, end_date=end_date)
safety = safety.to_spark_dataframe()
```

```
display(safety)
```

<!-- nbend -->

# <a name="azure-storage"></a>[azure-storage](#tab/azure-storage)

Ejemplo no disponible para esta combinación de plataforma y paquete.

# <a name="pyspark"></a>[pyspark](#tab/pyspark)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureDatabricks&package=pyspark&registryId=city_safety_boston -->

> [!TIP]
> **[Descargue en su lugar el cuaderno](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureDatabricks&package=pyspark&registryId=city_safety_boston)** .

```python
# Azure storage access info
blob_account_name = "azureopendatastorage"
blob_container_name = "citydatacontainer"
blob_relative_path = "Safety/Release/city=Boston"
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

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureSynapse&package=azureml-opendatasets&registryId=city_safety_boston -->

> [!TIP]
> **[Descargue en su lugar el cuaderno](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureSynapse&package=azureml-opendatasets&registryId=city_safety_boston)** .

```python
from azureml.opendatasets import BostonSafety

from datetime import datetime
from dateutil import parser


end_date = parser.parse('2016-01-01')
start_date = parser.parse('2015-05-01')
safety = BostonSafety(start_date=start_date, end_date=end_date)
safety = safety.to_spark_dataframe()
```

```python
display(safety)
```

<!-- nbend -->

# <a name="azure-storage"></a>[azure-storage](#tab/azure-storage)

Ejemplo no disponible para esta combinación de plataforma y paquete.

# <a name="pyspark"></a>[pyspark](#tab/pyspark)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureSynapse&package=pyspark&registryId=city_safety_boston -->

> [!TIP]
> **[Descargue en su lugar el cuaderno](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureSynapse&package=pyspark&registryId=city_safety_boston)** .

```python
# Azure storage access info
blob_account_name = "azureopendatastorage"
blob_container_name = "citydatacontainer"
blob_relative_path = "Safety/Release/city=Boston"
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