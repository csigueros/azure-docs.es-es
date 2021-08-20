---
title: Conjunto de datos de taxis y limusinas amarillos de Nueva York
titleSuffix: Azure Open Datasets
description: Aprenda a usar el conjunto de datos de Azure Open Datasets relativo a los taxis y limusinas amarillos de Nueva York.
ms.service: open-datasets
ms.topic: sample
author: peterclu
ms.author: peterlu
ms.date: 04/16/2021
ms.openlocfilehash: 5bf2a3b363c7d8a1cd0b10b1c958c4cfbb567deb
ms.sourcegitcommit: c05e595b9f2dbe78e657fed2eb75c8fe511610e7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/11/2021
ms.locfileid: "112039170"
---
# <a name="nyc-taxi--limousine-commission---yellow-taxi-trip-records"></a>NYC Taxi & Limousine Commission - yellow taxi trip records

Los registros de carreras de taxis amarillos incluyen campos que recopilan la fecha, la hora y el lugar donde se recoge y se deja a los pasajeros, las distancias de las carreras, las tarifas desglosadas, los tipos de tarifa, los tipos de pago y los recuentos de pasajeros notificados por el conductor.

[!INCLUDE [Open Dataset usage notice](../../includes/open-datasets-usage-note.md)]

## <a name="volume-and-retention"></a>Volumen y retención

Este conjunto de datos se almacena en formato Parquet. Hay alrededor de 1500 millones de filas (50 GB) en total desde 2018.

Este conjunto de datos contiene registros históricos acumulados desde 2009 hasta 2018. Puede usar la configuración de parámetros de nuestro SDK para recuperar los datos de un intervalo de tiempo específico.

## <a name="storage-location"></a>Ubicación de almacenamiento

Este conjunto de datos se almacena en la región Este de EE. UU. de Azure. Se recomienda asignar recursos de proceso de la misma región por afinidad.

## <a name="additional-information"></a>Información adicional

NYC Taxi and Limousine Commission (TLC):

Proveedores de tecnología autorizados por los programas TPEP (Taxicab Passenger Enhancement Program) y LPEP (Livery Passenger Enhancement Program) recopilaron y proporcionaron los datos a NYC Taxi and Limousine Commission (TLC). TLC no creó los datos de las carreras y no hace ninguna declaración respecto a la exactitud de estos datos.

Vea la [ubicación original del conjunto de datos](https://www1.nyc.gov/site/tlc/about/tlc-trip-record-data.page) y los [términos originales de uso](https://www1.nyc.gov/home/terms-of-use.page).

## <a name="columns"></a>Columnas
| Nombre                 | Tipo de datos | Único      | Valores (ejemplo)                         | Descripción                                                                                                                                                                                                                                            |
|----------------------|-----------|-------------|-----------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| doLocationId         | string    | 265         | 161 236                                 | Parada de taxi de TLC donde se desconectó el taxímetro.                                                                                                                                                                                                   |
| endLat               | double    | 961 994     | 41.366138 40.75                         |                                                                                                                                                                                                                                                        |
| endLon               | double    | 1 144 935   | -73.137393 -73.9824                     |                                                                                                                                                                                                                                                        |
| extra                | double    | 877         | 0.5 1.0                                 | Diversos extras y suplementos. Actualmente, solo se incluyen los cargos de 0,50 USD y 1 USD por hora punta y tarifa nocturna.                                                                                                                                   |
| fareAmount           | double    | 18 935      | 6.5 4.5                                 | Tarifa que calcula el taxímetro en función del tiempo y la distancia.                                                                                                                                                                                                    |
| improvementSurcharge | string    | 60          | 0.3 0                                   | Suplemento de 0,30 USD por mejoras del servicio que se aplica a la carrera al bajar la bandera. El suplemento por mejoras se comenzó a cobrar en 2015.                                                                                                                                     |
| mtaTax               | double    | 360         | 0.5 -0.5                                | Impuesto MTA de 0,50 USD que se aplica automáticamente en función de la tarifa utilizada.                                                                                                                                                                        |
| passengerCount       | int       | 64          | 1 2                                     | Número de pasajeros en el vehículo. Este valor lo proporciona el conductor.                                                                                                                                                                               |
| paymentType          | string    | 6282       | CSH CRD                                 | Código numérico que indica cómo pagó el pasajero la carrera. 1= Tarjeta de crédito 2= Efectivo 3= Gratis 4= Conflicto 5= Desconocido 6= Carrera anulada                                                                                                          |
| puLocationId         | string    | 266         | 237 161                                 | Parada de taxi de TLC donde se conectó el taxímetro.                                                                                                                                                                                                      |
| puMonth              | int       | 12          | 3 5                                     |                                                                                                                                                                                                                                                        |
| puYear               | int       | 29          | 2012 2011                               |                                                                                                                                                                                                                                                        |
| rateCodeID           | int       | 56          | 1 2                                     | Código de la tarifa final en vigor cuando termina la carrera. 1= Tarifa estándar 2= JFK 3= Newark 4= Nassau o Westchester 5= tarifa negociada 6= Carrera en grupo                                                                                                |
| startLat             | double    | 833 016     | 41.366138 40.7741                       |                                                                                                                                                                                                                                                        |
| startLon             | double    | 957 428     | -73.137393 -73.9821                     |                                                                                                                                                                                                                                                        |
| storeAndFwdFlag      | string    | 8           | N 0                                     | Esta marca indica si el registro de la carrera se mantuvo en la memoria del vehículo antes de enviarlo al proveedor, lo que se conoce también como “almacenar y reenviar”, porque el vehículo no tenía conexión con el servidor. Y= almacenar y reenviar carrera N= no almacenar ni reenviar carrera |
| tipAmount            | double    | 12 121      | 1.0 2.0                                 | Este campo se rellena automáticamente para las propinas mediante tarjeta de crédito. No se incluyen las propinas en efectivo.                                                                                                                                                                |
| tollsAmount          | double    | 6 634       | 5.33 4.8                                | Importe total de todos los peajes pagados durante la carrera.                                                                                                                                                                                                                |
| totalAmount          | double    | 39 707      | 7.0 7.8                                 | Importe total que se cobra a los pasajeros. No incluye las propinas en efectivo.                                                                                                                                                                                    |
| tpepDropoffDateTime  | timestamp | 290 185 010 | 2010-11-07 01:29:00 2013-11-03 01:22:00 | Fecha y hora de la desconexión del taxímetro.                                                                                                                                                                                                       |
| tpepPickupDateTime   | timestamp | 289 948 585 | 2010-11-07 01:00:00 2009-11-01 01:05:00 | Fecha y hora de la conexión del taxímetro.                                                                                                                                                                                                          |
| tripDistance         | double    | 14 003      | 1.0 0.9                                 | Distancia recorrida de la carrera (en millas) que indica el taxímetro.                                                                                                                                                                                          |
| vendorID             | string    | 7           | VTS CMT                                 | Código que indica el proveedor del programa TPEP que proporcionó el registro. 1= Creative Mobile Technologies, LLC; 2= VeriFone Inc.                                                                                                                                   |
| vendorID             | int       | 2           | 2 1                                     | Código que indica el proveedor del programa LPEP que proporcionó el registro. 1= Creative Mobile Technologies, LLC; 2= VeriFone Inc.                                                                                                                                   |

## <a name="preview"></a>Vista previa

| vendorID | tpepPickupDateTime    | tpepDropoffDateTime   | passengerCount | tripDistance | puLocationId | doLocationId | rateCodeID | storeAndFwdFlag | paymentType | fareAmount | extra | mtaTax | improvementSurcharge | tipAmount | tollsAmount | totalAmount | puYear | puMonth |
|----------|-----------------------|-----------------------|----------------|--------------|--------------|--------------|------------|-----------------|-------------|------------|-------|--------|----------------------|-----------|-------------|-------------|--------|---------|
| 2        | 1/24/2088 12:25:39 AM | 1/24/2088 7:28:25 AM  | 1              | 4.05         | 24           | 162          | 1          | No               | 2           | 14.5       | 0     | 0,5    | 0,3                  | 0         | 0           | 15,3        | 2088   | 1       |
| 2        | 1/24/2088 12:15:42 AM | 1/24/2088 12:19:46 AM | 1              | 0.63         | 41           | 166          | 1          | No               | 2           | 4.5.        | 0     | 0,5    | 0,3                  | 0         | 0           | 5.3         | 2088   | 1       |
| 2        | 11/4/2084 12:32:24 PM | 11/4/2084 12:47:41 PM | 1              | 1,34         | 238          | 236          | 1          | No               | 2           | 10         | 0     | 0,5    | 0,3                  | 0         | 0           | 10,8        | 2084   | 11      |
| 2        | 11/4/2084 12:25:53 PM | 11/4/2084 12:29:00 PM | 1              | 0,32         | 238          | 238          | 1          | No               | 2           | 4          | 0     | 0,5    | 0,3                  | 0         | 0           | 4.8         | 2084   | 11      |
| 2        | 11/4/2084 12:08:33 PM | 11/4/2084 12:22:24 PM | 1              | 1,85         | 236          | 238          | 1          | No               | 2           | 10         | 0     | 0,5    | 0,3                  | 0         | 0           | 10,8        | 2084   | 11      |
| 2        | 11/4/2084 11:41:35 AM | 11/4/2084 11:59:41 AM | 1              | 1.65         | 68           | 237          | 1          | No               | 2           | 12.5       | 0     | 0,5    | 0,3                  | 0         | 0           | 13.3        | 2084   | 11      |
| 2        | 11/4/2084 11:27:28 AM | 11/4/2084 11:39:52 AM | 1              | 1.07         | 170          | 68           | 1          | No               | 2           | 9          | 0     | 0,5    | 0,3                  | 0         | 0           | 9.8         | 2084   | 11      |
| 2        | 11/4/2084 11:19:06 AM | 11/4/2084 11:26:44 AM | 1              | 1.3          | 107          | 170          | 1          | No               | 2           | 7.5        | 0     | 0,5    | 0,3                  | 0         | 0           | 8.3         | 2084   | 11      |
| 2        | 11/4/2084 11:02:59 AM | 11/4/2084 11:15:51 AM | 1              | 1,85         | 113          | 137          | 1          | No               | 2           | 10         | 0     | 0,5    | 0,3                  | 0         | 0           | 10,8        | 2084   | 11      |
| 2        | 11/4/2084 10:46:05 AM | 11/4/2084 10:50:09 AM | 1              | 0.62         | 231          | 231          | 1          | No               | 2           | 4.5.        | 0     | 0,5    | 0,3                  | 0         | 0           | 5.3         | 2084   | 11      |

## <a name="data-access"></a>Acceso a datos

### <a name="azure-notebooks"></a>Azure Notebooks

# <a name="azureml-opendatasets"></a>[azureml-opendatasets](#tab/azureml-opendatasets)

```python
# This is a package in preview.
from azureml.opendatasets import NycTlcYellow

from datetime import datetime
from dateutil import parser


end_date = parser.parse('2018-06-06')
start_date = parser.parse('2018-05-01')
nyc_tlc = NycTlcYellow(start_date=start_date, end_date=end_date)
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
folder_name = "yellow"

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
from azureml.opendatasets import NycTlcYellow

from datetime import datetime
from dateutil import parser


end_date = parser.parse('2018-06-06')
start_date = parser.parse('2018-05-01')
nyc_tlc = NycTlcYellow(start_date=start_date, end_date=end_date)
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
blob_relative_path = "yellow"
blob_sas_token = r"

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
from azureml.opendatasets import NycTlcYellow

from datetime import datetime
from dateutil import parser

end_date = parser.parse('2018-06-06')
start_date = parser.parse('2018-05-01')
nyc_tlc = NycTlcYellow(start_date=start_date, end_date=end_date)
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
blob_relative_path = "yellow"
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