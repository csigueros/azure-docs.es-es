---
title: 'Taxis y limusinas de Nueva York: conjunto de datos de vehículos de alquiler'
titleSuffix: Azure Open Datasets
description: Aprenda a usar el conjunto de datos de Azure Open Datasets relativo a vehículos de alquiler para taxis y limusinas de Nueva York.
ms.service: open-datasets
ms.topic: sample
author: peterclu
ms.author: peterlu
ms.date: 04/16/2021
ms.openlocfilehash: 2875ddfa1bf94121bea9038b576035042accbcfe
ms.sourcegitcommit: c05e595b9f2dbe78e657fed2eb75c8fe511610e7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/11/2021
ms.locfileid: "112038969"
---
# <a name="nyc-taxi--limousine-commission---for-hire-vehicle-fhv-trip-records"></a>Comisión de taxis y limusinas de Nueva York: registros de carreras de vehículos de alquiler (FHV)

Los registros de carreras de vehículos de alquiler incluyen campos donde se recogen el número de licencia de la central y la fecha, la hora y el identificador de la ubicación de la parada de taxi donde se recoge a los pasajeros (archivo de forma a continuación). Estos registros se generan a partir de los envíos de carreras de vehículos de alquiler que realizan las centrales.

[!INCLUDE [Open Dataset usage notice](../../includes/open-datasets-usage-note.md)]

## <a name="volume-and-retention"></a>Volumen y retención

Este conjunto de datos se almacena en formato Parquet. Hay alrededor de 500 millones de filas (5 GB) a partir de 2018.

Este conjunto de datos contiene registros históricos acumulados desde 2009 hasta 2018. Puede usar la configuración de parámetros de nuestro SDK para recuperar los datos de un intervalo de tiempo específico.

## <a name="storage-location"></a>Ubicación de almacenamiento

Este conjunto de datos se almacena en la región Este de EE. UU. de Azure. Se recomienda asignar recursos de proceso de la misma región por afinidad.

## <a name="additional-information"></a>Información adicional

NYC Taxi and Limousine Commission (TLC):

Proveedores de tecnología autorizados por los programas TPEP (Taxicab Passenger Enhancement Program) y LPEP (Livery Passenger Enhancement Program) recopilaron y proporcionaron los datos a NYC Taxi and Limousine Commission (TLC). TLC no creó los datos de las carreras y no hace ninguna declaración respecto a la exactitud de estos datos.

Vea la [ubicación original del conjunto de datos](https://www1.nyc.gov/site/tlc/about/tlc-trip-record-data.page) y los [términos originales de uso](https://www1.nyc.gov/home/terms-of-use.page).

## <a name="columns"></a>Columnas

| Nombre            | Tipo de datos | Único      | Valores (ejemplo)                         | Descripción                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
|-----------------|-----------|-------------|-----------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| dispatchBaseNum | string    | 1,144       | B02510 B02764                           | Número de licencia de la central de TLC que gestionó la carrera.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                             |
| doLocationId    | string    | 267         | 265 132                                 | Parada de taxi de TLC donde termina la carrera.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       |
| dropOffDateTime | timestamp | 57,110,352  | 2017-07-31 23:59:00 2017-10-15 00:44:34 | Fecha y hora a la que se deja a los pasajeros de la carrera.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       |
| pickupDateTime  | timestamp | 111,270,396 | 2016-08-16 00:00:00 2016-08-17 00:00:00 | Fecha y hora de la recogida de los pasajeros para iniciar la carrera.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       |
| puLocationId    | string    | 266         | 79 161                                  | Parada de taxi de TLC donde comienza la carrera.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       |
| puMonth         | int       | 12          | 1 12                                    |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              |
| puYear          | int       | 5           | 2018 2017                               |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              |
| srFlag          | string    | 44          | 1 2                                     | Indica si la carrera formaba parte de una cadena de carreras ofrecidas por una compañía de vehículos de alquiler de gran volumen (como Uber Pool, Lyft Line). Para carreras compartidas, el valor es 1. Para carreras no compartidas, este campo es nulo. NOTA: Para la mayoría de las compañías de vehículos de alquiler de gran volumen, solo se marcan las carreras compartidas que se han solicitado Y se corresponden con otra solicitud de carrera compartida durante el transcurso del viaje. Sin embargo, Lyft (números de licencia base B02510 + B02844) marca también las carreras para las que se ha solicitado una carrera compartida pero no se encontró otro pasajero para compartir la carrera; por tanto, los registros de carreras con SR_Flag=1 de esas dos bases podrían iniciar una primera carrera de una cadena de carreras compartida O una carrera para la que se solicitó una carrera compartida pero no se encontró una correspondencia. Los usuarios deberían prever un recuento excesivo de carreras compartidas llevadas a cabo por Lyft. |

## <a name="preview"></a>Vista previa

| dispatchBaseNum | pickupDateTime        | dropOffDateTime      | puLocationId | doLocationId | srFlag | puYear | puMonth |
|-----------------|-----------------------|----------------------|--------------|--------------|--------|--------|---------|
| B03157          | 6/30/2019 11:59:57 PM | 7/1/2019 12:07:21 AM | 264          | null         | null   | 2019   | 6       |
| B01667          | 6/30/2019 11:59:56 PM | 7/1/2019 12:28:06 AM | 264          | null         | null   | 2019   | 6       |
| B02849          | 6/30/2019 11:59:55 PM | 7/1/2019 12:14:10 AM | 264          | null         | null   | 2019   | 6       |
| B02249          | 6/30/2019 11:59:53 PM | 7/1/2019 12:15:53 AM | 264          | null         | null   | 2019   | 6       |
| B00887          | 6/30/2019 11:59:48 PM | 7/1/2019 12:29:29 AM | 264          | null         | null   | 2019   | 6       |
| B01626          | 6/30/2019 11:59:45 PM | 7/1/2019 12:18:20 AM | 264          | null         | null   | 2019   | 6       |
| B01259          | 6/30/2019 11:59:44 PM | 7/1/2019 12:03:15 AM | 264          | null         | null   | 2019   | 6       |
| B01145          | 6/30/2019 11:59:43 PM | 7/1/2019 12:11:15 AM | 264          | null         | null   | 2019   | 6       |
| B00887          | 6/30/2019 11:59:42 PM | 7/1/2019 12:34:21 AM | 264          | null         | null   | 2019   | 6       |
| B00821          | 6/30/2019 11:59:40 PM | 7/1/2019 12:02:57 AM | 264          | null         | null   | 2019   | 6       |

## <a name="data-access"></a>Acceso a datos

### <a name="azure-notebooks"></a>Azure Notebooks

# <a name="azureml-opendatasets"></a>[azureml-opendatasets](#tab/azureml-opendatasets)

```python
# This is a package in preview.
from azureml.opendatasets import NycTlcFhv

from datetime import datetime
from dateutil import parser


end_date = parser.parse('2018-06-06')
start_date = parser.parse('2018-05-01')
nyc_tlc = NycTlcFhv(start_date=start_date, end_date=end_date)
nyc_tlc_df = nyc_tlc.to_pandas_dataframe()

nyc_tlc_df.info()
```

# <a name="azure-storage"></a>[azure-storage](#tab/azure-storage)

```python
# Pip install packages
import os, sys

!{sys.executable} -m pip install azure-storage-blob
!{sys.executable} -m pip install pyarrow
!{sys.executable} -m pip install pandas

# Azure storage access info
azure_storage_account_name = "azureopendatastorage"
azure_storage_sas_token = r""
container_name = "nyctlc"
folder_name = "fhv"

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

# Read the parquet file into Pandas data frame
import pandas as pd

print('Reading the parquet file into Pandas data frame')
df = pd.read_parquet(filename)

# you can add your filter at below
print('Loaded as a Pandas data frame: ')
df
```

# <a name="pyspark"></a>[pyspark](#tab/pyspark)

Ejemplo no disponible para esta combinación de plataforma y paquete.

---

### <a name="azure-databricks"></a>Azure Databricks

# <a name="azureml-opendatasets"></a>[azureml-opendatasets](#tab/azureml-opendatasets)

```python
# This is a package in preview.
# You need to pip install azureml-opendatasets in Databricks cluster. https://docs.microsoft.com/en-us/azure/data-explorer/connect-from-databricks#install-the-python-library-on-your-azure-databricks-cluster
from azureml.opendatasets import NycTlcFhv

from datetime import datetime
from dateutil import parser


end_date = parser.parse('2018-06-06')
start_date = parser.parse('2018-05-01')
nyc_tlc = NycTlcFhv(start_date=start_date, end_date=end_date)
nyc_tlc_df = nyc_tlc.to_spark_dataframe()

display(nyc_tlc_df.limit(5))
```

# <a name="azure-storage"></a>[azure-storage](#tab/azure-storage)

Ejemplo no disponible para esta combinación de plataforma y paquete.

# <a name="pyspark"></a>[pyspark](#tab/pyspark)

```python
# Azure storage access info
blob_account_name = "azureopendatastorage"
blob_container_name = "nyctlc"
blob_relative_path = "fhv"
blob_sas_token = r""

# Allow SPARK to read from Blob remotely
wasbs_path = 'wasbs://%s@%s.blob.core.windows.net/%s' % (blob_container_name, blob_account_name, blob_relative_path)
spark.conf.set(
  'fs.azure.sas.%s.%s.blob.core.windows.net' % (blob_container_name, blob_account_name),
  blob_sas_token)
print('Remote blob path: ' + wasbs_path)

# SPARK read parquet, note that it won't load any data yet by now
df = spark.read.parquet(wasbs_path)
print('Register the DataFrame as a SQL temporary view: source')
df.createOrReplaceTempView('source')

# Display top 10 rows
print('Displaying top 10 rows: ')
display(spark.sql('SELECT * FROM source LIMIT 10'))
```

---

### <a name="azure-synapse"></a>Azure Synapse

# <a name="azureml-opendatasets"></a>[azureml-opendatasets](#tab/azureml-opendatasets)

```python
# This is a package in preview.
from azureml.opendatasets import NycTlcFhv

from datetime import datetime
from dateutil import parser


end_date = parser.parse('2018-06-06')
start_date = parser.parse('2018-05-01')
nyc_tlc = NycTlcFhv(start_date=start_date, end_date=end_date)
nyc_tlc_df = nyc_tlc.to_spark_dataframe()

# Display top 5 rows
display(nyc_tlc_df.limit(5))
```

# <a name="azure-storage"></a>[azure-storage](#tab/azure-storage)

Ejemplo no disponible para esta combinación de plataforma y paquete.

# <a name="pyspark"></a>[pyspark](#tab/pyspark)

```python
# Azure storage access info
blob_account_name = "azureopendatastorage"
blob_container_name = "nyctlc"
blob_relative_path = "fhv"
blob_sas_token = r""

# Allow SPARK to read from Blob remotely
wasbs_path = 'wasbs://%s@%s.blob.core.windows.net/%s' % (blob_container_name, blob_account_name, blob_relative_path)
spark.conf.set(
  'fs.azure.sas.%s.%s.blob.core.windows.net' % (blob_container_name, blob_account_name),
  blob_sas_token)
print('Remote blob path: ' + wasbs_path)

# SPARK read parquet, note that it won't load any data yet by now
df = spark.read.parquet(wasbs_path)
print('Register the DataFrame as a SQL temporary view: source')
df.createOrReplaceTempView('source')

# Display top 10 rows
print('Displaying top 10 rows: ')
display(spark.sql('SELECT * FROM source LIMIT 10'))
```

---

## <a name="next-steps"></a>Pasos siguientes

Consulte el resto de los conjuntos de datos en el [catálogo de Open Datasets](dataset-catalog.md).