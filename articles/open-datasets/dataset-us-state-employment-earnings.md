---
title: Horario laboral y salarios de ámbito estatal de Estados Unidos
titleSuffix: Azure Open Datasets
description: Aprenda a usar el conjunto de datos US State Employment Hours and Earnings de Azure Open Datasets.
ms.service: open-datasets
ms.topic: sample
author: peterclu
ms.author: peterlu
ms.date: 04/16/2021
ms.openlocfilehash: a1cb79e704eeae13e8794cad7409e0b945889d22
ms.sourcegitcommit: c05e595b9f2dbe78e657fed2eb75c8fe511610e7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/11/2021
ms.locfileid: "112039158"
---
# <a name="us-state-employment-hours-and-earnings"></a>Horario laboral y salarios de ámbito estatal de Estados Unidos

El programa Current Employment Statistics (CES) realiza cálculos detallados sobre el empleo, el horario y los ingresos de los trabajadores en plantilla fuera del sector agrario en Estados Unidos.

[!INCLUDE [Open Dataset usage notice](../../includes/open-datasets-usage-note.md)]

Este conjunto de datos se obtiene de los [datos de empleo, horario e ingresos](https://www.bls.gov/sae/) [estatales y metropolitanos](https://www.bls.gov/sae/) que publica la [Oficina de Estadísticas de Empleo](https://www.bls.gov/) (BLS, por sus siglas en inglés) de Estados Unidos. Revise la [información sobre enlaces y copyright](https://www.bls.gov/bls/linksite.htm) y los [avisos importantes del sitio web](https://www.bls.gov/bls/website-policies.htm) para conocer los términos y condiciones relacionados con el uso de este conjunto de datos.

## <a name="storage-location"></a>Ubicación de almacenamiento

Este conjunto de datos se almacena en la región Este de EE. UU. de Azure. Se recomienda asignar recursos de proceso de la misma región por afinidad.

## <a name="related-datasets"></a>Conjuntos de datos relacionados

- [Horario laboral y salarios de ámbito nacional de Estados Unidos](dataset-us-national-employment-earnings.md)
- [Estadísticas de desempleo por áreas de Estados Unidos](dataset-us-local-unemployment.md)
- [Estadísticas de población activa de Estados Unidos](dataset-us-labor-force.md)

## <a name="columns"></a>Columnas

| Nombre | Tipo de datos | Único | Valores (ejemplo) |
|-|-|-|-|
| area_code | string | 446 | 0 31084 |
| area_name | string | 446 | Todo el estado Los Angeles-Long Beach-Glendale, CA División metropolitana |
| data_type_code | string | 9 | 1 3 |
| data_type_text | string | 9 | Todos los empleados (en miles), promedio de horas semanales de todos los empleados |
| footnote_codes | string | 3 | nan P |
| industry_code | string | 343 | 0 5000000 |
| industry_name | string | 343 | Total Nonfarm Total Private |
| period | string | 13 | M04 M05 |
| seasonal (estacional) | string | 2 | EE. UU. |
| series_id | string | 23,853 | SMU12000000000000001 SMU36000000000000001 |
| state_code | string | 53 | 6 48 |
| state_name | string | 53 | California Texas |
| supersector_code | string | 22 | 90 60 |
| supersector_name | string | 22 | Government Professional and Business Services |
| value | FLOAT | 132,565 | 0.30000001192092896 0.10000000149011612 |
| year | int | 81 | 2014 2018 |

## <a name="preview"></a>Vista previa

| area_code | state_code | data_type_code | industry_code | supersector_code | series_id | year | period | value | footnote_codes | seasonal (estacional) | supersector_name | industry_name | data_type_text | state_name | area_name |
|-|-|-|-|-|-|-|-|-|-|-|-|-|-|-|-|
| 13460 | 41 | 26 | 0 | 0 | SMS41134600000000026 | 1990 | M04 | 0,2 | nan | S | Total Nonfarm | Total Nonfarm | All Employees, 3-month average change, In Thousands, seasonally adjusted | Oregón | Bend-Redmond, OR |
| 13460 | 41 | 26 | 0 | 0 | SMS41134600000000026 | 1990 | M05 | 0,2 | nan | S | Total Nonfarm | Total Nonfarm | All Employees, 3-month average change, In Thousands, seasonally adjusted | Oregón | Bend-Redmond, OR |
| 13460 | 41 | 26 | 0 | 0 | SMS41134600000000026 | 1990 | M06 | 0,1 | nan | S | Total Nonfarm | Total Nonfarm | All Employees, 3-month average change, In Thousands, seasonally adjusted | Oregón | Bend-Redmond, OR |
| 13460 | 41 | 26 | 0 | 0 | SMS41134600000000026 | 1990 | M07 | 0,1 | nan | S | Total Nonfarm | Total Nonfarm | All Employees, 3-month average change, In Thousands, seasonally adjusted | Oregón | Bend-Redmond, OR |
| 13460 | 41 | 26 | 0 | 0 | SMS41134600000000026 | 1990 | M08 | 0,2 | nan | S | Total Nonfarm | Total Nonfarm | All Employees, 3-month average change, In Thousands, seasonally adjusted | Oregón | Bend-Redmond, OR |
| 13460 | 41 | 26 | 0 | 0 | SMS41134600000000026 | 1990 | M09 | 0,2 | nan | S | Total Nonfarm | Total Nonfarm | All Employees, 3-month average change, In Thousands, seasonally adjusted | Oregón | Bend-Redmond, OR |
| 13460 | 41 | 26 | 0 | 0 | SMS41134600000000026 | 1990 | M10 | 0,1 | nan | S | Total Nonfarm | Total Nonfarm | All Employees, 3-month average change, In Thousands, seasonally adjusted | Oregón | Bend-Redmond, OR |

## <a name="data-access"></a>Acceso a datos

### <a name="azure-notebooks"></a>Azure Notebooks

# <a name="azureml-opendatasets"></a>[azureml-opendatasets](#tab/azureml-opendatasets)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azureml-opendatasets&registryId=us-employment-hours-earnings-state -->

> [!TIP]
> **[Descargue en su lugar el cuaderno](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azureml-opendatasets&registryId=us-employment-hours-earnings-state)** .

```python
# This is a package in preview.
from azureml.opendatasets import UsLaborEHEState

usLaborEHEState = UsLaborEHEState()
usLaborEHEState_df = usLaborEHEState.to_pandas_dataframe()
```

```python
usLaborEHEState_df.info()
```

<!-- nbend -->

# <a name="azure-storage"></a>[azure-storage](#tab/azure-storage)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azure-storage&registryId=us-employment-hours-earnings-state -->

> [!TIP]
> **[Descargue en su lugar el cuaderno](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azure-storage&registryId=us-employment-hours-earnings-state)** .

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
container_name = "laborstatisticscontainer"
folder_name = "ehe_state/"
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

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureDatabricks&package=azureml-opendatasets&registryId=us-employment-hours-earnings-state -->

> [!TIP]
> **[Descargue en su lugar el cuaderno](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureDatabricks&package=azureml-opendatasets&registryId=us-employment-hours-earnings-state)** .

```python
# This is a package in preview.
from azureml.opendatasets import UsLaborEHEState

usLaborEHEState = UsLaborEHEState()
usLaborEHEState_df = usLaborEHEState.to_spark_dataframe()
```

```python
display(usLaborEHEState_df.limit(5))
```

<!-- nbend -->
 
# <a name="azure-storage"></a>[azure-storage](#tab/azure-storage)

Ejemplo no disponible para esta combinación de plataforma y paquete.

# <a name="pyspark"></a>[pyspark](#tab/pyspark)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureDatabricks&package=pyspark&registryId=us-employment-hours-earnings-state -->

> [!TIP]
> **[Descargue en su lugar el cuaderno](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureDatabricks&package=pyspark&registryId=us-employment-hours-earnings-state)** .

```python
# Azure storage access info
blob_account_name = "azureopendatastorage"
blob_container_name = "laborstatisticscontainer"
blob_relative_path = "ehe_state/"
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

Ejemplo no disponible para esta combinación de plataforma y paquete.

# <a name="azure-storage"></a>[azure-storage](#tab/azure-storage)

Ejemplo no disponible para esta combinación de plataforma y paquete.

# <a name="pyspark"></a>[pyspark](#tab/pyspark)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureSynapse&package=pyspark&registryId=us-employment-hours-earnings-state -->

> [!TIP]
> **[Descargue en su lugar el cuaderno](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureSynapse&package=pyspark&registryId=us-employment-hours-earnings-state)** .

```python
# Azure storage access info
blob_account_name = "azureopendatastorage"
blob_container_name = "laborstatisticscontainer"
blob_relative_path = "ehe_state/"
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

## <a name="next-steps"></a>Pasos siguientes

Consulte el resto de los conjuntos de datos en el [catálogo de Open Datasets](dataset-catalog.md).