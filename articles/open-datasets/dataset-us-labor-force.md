---
title: Estadísticas de población activa de Estados Unidos
titleSuffix: Azure Open Datasets
description: Aprenda a usar el conjunto de datos US Labor Force Statistics en Azure Open Datasets.
ms.service: open-datasets
ms.topic: sample
author: peterclu
ms.author: peterlu
ms.date: 04/16/2021
ms.openlocfilehash: d5e244ee760dbf274a4fc8efcf5320d6ed6ac303
ms.sourcegitcommit: c05e595b9f2dbe78e657fed2eb75c8fe511610e7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/11/2021
ms.locfileid: "112038966"
---
# <a name="us-labor-force-statistics"></a>Estadísticas de población activa de Estados Unidos

Estadísticas de población activa, tasas de participación en la población activa y población civil no institucional por edad, sexo, raza y grupo étnico en Estados Unidos.

Este conjunto de datos se alimenta con los [datos del programa Current Employment Statistics - CES (nacional)](https://www.bls.gov/ces/) que publica la [Oficina de Estadísticas Laborales (BLS, por sus siglas en inglés) de Estados Unidos](https://www.bls.gov/). Revise la [información sobre enlaces y copyright](https://www.bls.gov/bls/linksite.htm) y los [avisos importantes del sitio web](https://www.bls.gov/bls/website-policies.htm) para conocer los términos y condiciones relacionados con el uso de este conjunto de datos.

[!INCLUDE [Open Dataset usage notice](../../includes/open-datasets-usage-note.md)]

## <a name="storage-location"></a>Ubicación de almacenamiento

Este conjunto de datos se almacena en la región Este de EE. UU. de Azure. Se recomienda asignar recursos de proceso de la misma región por afinidad.

## <a name="related-datasets"></a>Conjuntos de datos relacionados

- [Horario laboral y salarios de ámbito nacional de Estados Unidos](dataset-us-national-employment-earnings.md)
- [Horario laboral y salarios de ámbito estatal de Estados Unidos](dataset-us-state-employment-earnings.md)
- [Estadísticas de desempleo por áreas de Estados Unidos](dataset-us-local-unemployment.md)

## <a name="columns"></a>Columnas

| Nombre | Tipo de datos | Único | Valores (ejemplo) |
|-|-|-|-|
| absn_code | int | 4 | 3 4 |
| activity_code | int | 7 | 8 3 |
| ages_code | int | 35 | 10 17 |
| born_code | int | 3 | 1 2 |
| cert_code | int | 5 | 4 3 |
| chld_code | int | 6 | 2 5 |
| class_code | int | 14 | 2 1 |
| disa_code | int | 3 | 2 1 |
| duration_code | int | 11 | 18 6 |
| education_code | int | 9 | 40 19 |
| entr_code | int | 3 | 1 2 |
| expr_code | int | 3 | 1 2 |
| footnote_codes | string | 7 | nan 4.0 |
| hheader_code | int | 2 | 1 |
| hour_code | int | 13 | 1 16 |
| indy_code | int | 323 | 368 169 |
| jdes_code | int | 3 | 1 2 |
| lfst_code | int | 33 | 20 30 |
| look_code | int | 7 | 1 6 |
| mari_code | int | 5 | 2 1 |
| mjhs_code | int | 6 | 1 5 |
| occupation_code | int | 566 | 8999 4999 |
| orig_code | int | 14 | 1 2 |
| pcts_code | int | 23 | 5 8 |
| period | string | 18 | M07 M06 |
| periodicity_code | string | 3 | M Q |
| race_code | int | 14 | 1 3 |
| rjnw_code | int | 9 | 1 3 |
| rnlf_code | int | 11 | 63 64 |
| rwns_code | int | 17 | 10 1 |
| seasonal (estacional) | string | 2 | EE. UU. |
| seek_code | int | 2 | 1 |
| series_id | string | 45,478 | LNU01300000 LNU02034560 |
| series_title | string | 34,264 | (Unadj) Employment Level - Agriculture and Related Industries (Unadj) Civilian Labor Force Level |
| sexs_code | int | 3 | 1 2 |
| tdat_code | int | 6 | 1 4 |
| value | FLOAT | 121,742 | 3.0 4.0 |
| vets_code | int | 8 | 25 1 |
| wkst_code | int | 7 | 1 4 |
| year | int | 80 | 2018 2017 |

## <a name="preview"></a>Vista previa

| series_id | year | period | value | footnote_codes | lfst_code | periodicity_code | series_title | absn_code | activity_code | ages_code | cert_code | class_code | duration_code | education_code | entr_code | expr_code | hheader_code | hour_code | indy_code | jdes_code | look_code | mari_code | mjhs_code | occupation_code | orig_code | pcts_code | race_code | rjnw_code | rnlf_code | rwns_code | seek_code | sexs_code | tdat_code | vets_code | wkst_code | born_code | chld_code | disa_code | seasonal (estacional) |
|-|-|-|-|-|-|-|-|-|-|-|-|-|-|-|-|-|-|-|-|-|-|-|-|-|-|-|-|-|-|-|-|-|-|-|-|-|-|-|-|
| LNS11000031Q | 1972 | Q01 | 4300 | nan | 10 | Q | (Seas) Civilian Labor Force Level - 20 yrs. & over, Black or African American Men | 0 | 0 | 17 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 3 | 0 | 0 | 0 | 0 | 1 | 0 | 0 | 0 | 0 | 0 | 0 | S |
| LNS11000031Q | 1972 | Q02 | 4370 | nan | 10 | Q | (Seas) Civilian Labor Force Level - 20 yrs. & over, Black or African American Men | 0 | 0 | 17 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 3 | 0 | 0 | 0 | 0 | 1 | 0 | 0 | 0 | 0 | 0 | 0 | S |
| LNS11000031Q | 1972 | Q03 | 4397 | nan | 10 | Q | (Seas) Civilian Labor Force Level - 20 yrs. & over, Black or African American Men | 0 | 0 | 17 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 3 | 0 | 0 | 0 | 0 | 1 | 0 | 0 | 0 | 0 | 0 | 0 | S |
| LNS11000031Q | 1972 | Q04 | 4381 | nan | 10 | Q | (Seas) Civilian Labor Force Level - 20 yrs. & over, Black or African American Men | 0 | 0 | 17 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 3 | 0 | 0 | 0 | 0 | 1 | 0 | 0 | 0 | 0 | 0 | 0 | S |
| LNS11000031Q | 1973 | Q01 | 4408 | nan | 10 | Q | (Seas) Civilian Labor Force Level - 20 yrs. & over, Black or African American Men | 0 | 0 | 17 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 3 | 0 | 0 | 0 | 0 | 1 | 0 | 0 | 0 | 0 | 0 | 0 | S |
| LNS11000031Q | 1973 | Q02 | 4445 | nan | 10 | Q | (Seas) Civilian Labor Force Level - 20 yrs. & over, Black or African American Men | 0 | 0 | 17 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 3 | 0 | 0 | 0 | 0 | 1 | 0 | 0 | 0 | 0 | 0 | 0 | S |
| LNS11000031Q | 1973 | Q03 | 4477 | nan | 10 | Q | (Seas) Civilian Labor Force Level - 20 yrs. & over, Black or African American Men | 0 | 0 | 17 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 3 | 0 | 0 | 0 | 0 | 1 | 0 | 0 | 0 | 0 | 0 | 0 | S |
| LNS11000031Q | 1973 | Q04 | 4523 | nan | 10 | Q | (Seas) Civilian Labor Force Level - 20 yrs. & over, Black or African American Men | 0 | 0 | 17 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 3 | 0 | 0 | 0 | 0 | 1 | 0 | 0 | 0 | 0 | 0 | 0 | S |
| LNS11000031Q | 1974 | Q01 | 4574 | nan | 10 | Q | (Seas) Civilian Labor Force Level - 20 yrs. & over, Black or African American Men | 0 | 0 | 17 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 3 | 0 | 0 | 0 | 0 | 1 | 0 | 0 | 0 | 0 | 0 | 0 | S |
| LNS11000031Q | 1974 | Q02 | 4538 | nan | 10 | Q | (Seas) Civilian Labor Force Level - 20 yrs. & over, Black or African American Men | 0 | 0 | 17 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 3 | 0 | 0 | 0 | 0 | 1 | 0 | 0 | 0 | 0 | 0 | 0 | S |

## <a name="data-access"></a>Acceso a datos

### <a name="azure-notebooks"></a>Azure Notebooks

# <a name="azureml-opendatasets"></a>[azureml-opendatasets](#tab/azureml-opendatasets)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azureml-opendatasets&registryId=us-labor-force-statistics -->

> [!TIP]
> **[Descargue en su lugar el cuaderno](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azureml-opendatasets&registryId=us-labor-force-statistics)** .

```python
# This is a package in preview.
from azureml.opendatasets import UsLaborLFS

labor = UsLaborLFS()
labor_df = labor.to_pandas_dataframe()
```

```python
labor_df.info()
```

<!-- nbend -->

# <a name="azure-storage"></a>[azure-storage](#tab/azure-storage)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azure-storage&registryId=us-labor-force-statistics -->

> [!TIP]
> **[Descargue en su lugar el cuaderno](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azure-storage&registryId=us-labor-force-statistics)** .

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
folder_name = "lfs/"
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

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureDatabricks&package=azureml-opendatasets&registryId=us-labor-force-statistics -->

> [!TIP]
> **[Descargue en su lugar el cuaderno](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureDatabricks&package=azureml-opendatasets&registryId=us-labor-force-statistics)** .

```python
# This is a package in preview.
from azureml.opendatasets import UsLaborLFS

labor = UsLaborLFS()
labor_df = labor.to_spark_dataframe()
```

```python
display(labor_df.limit(5))
```

<!-- nbend -->

# <a name="azure-storage"></a>[azure-storage](#tab/azure-storage)

Ejemplo no disponible para esta combinación de plataforma y paquete.

# <a name="pyspark"></a>[pyspark](#tab/pyspark)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureDatabricks&package=pyspark&registryId=us-labor-force-statistics -->

> [!TIP]
> **[Descargue en su lugar el cuaderno](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureDatabricks&package=pyspark&registryId=us-labor-force-statistics)** .

```python
# Azure storage access info
blob_account_name = "azureopendatastorage"
blob_container_name = "laborstatisticscontainer"
blob_relative_path = "lfs/"
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

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureSynapse&package=pyspark&registryId=us-labor-force-statistics -->

> [!TIP]
> **[Descargue en su lugar el cuaderno](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureSynapse&package=pyspark&registryId=us-labor-force-statistics)** .

```python
# Azure storage access info
blob_account_name = "azureopendatastorage"
blob_container_name = "laborstatisticscontainer"
blob_relative_path = "lfs/"
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