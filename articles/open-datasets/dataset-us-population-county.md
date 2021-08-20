---
title: Población de Estados Unidos por condado
titleSuffix: Azure Open Datasets
description: Aprenda a usar el conjunto de datos de Azure Open Datasets relativo a la población de Estados Unidos por condados.
ms.service: open-datasets
ms.topic: sample
author: peterclu
ms.author: peterlu
ms.date: 04/16/2021
ms.openlocfilehash: fe343d5d2519517653e966c3cb28b5e5f180969f
ms.sourcegitcommit: c05e595b9f2dbe78e657fed2eb75c8fe511610e7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/11/2021
ms.locfileid: "112039164"
---
# <a name="us-population-by-county"></a>Población de Estados Unidos por condado

Población de Estados Unidos por sexo y raza en cada condado del país según los censos decenales de 2000 y 2010.

Los datos de este conjunto de datos proceden de las [API de conjuntos de datos de censos decenales](https://www.census.gov/data/developers/data-sets/decennial-census.html) de la Oficina del Censo de los Estados Unidos. Consulte los [términos de servicio](https://www.census.gov/data/developers/about/terms-of-service.html) y los [avisos y directivas](https://www.census.gov/about/policies.html) para conocer los términos y condiciones de uso de este conjunto de datos.

[!INCLUDE [Open Dataset usage notice](../../includes/open-datasets-usage-note.md)]

## <a name="volume-and-retention"></a>Volumen y retención

Este conjunto de datos se almacena en formato Parquet y tiene datos de los años 2000 y 2010.

## <a name="storage-location"></a>Ubicación de almacenamiento
Este conjunto de datos se almacena en la región Este de EE. UU. de Azure. Se recomienda asignar recursos de proceso de la misma región por afinidad.

## <a name="related-datasets"></a>Conjuntos de datos relacionados

- [Población de Estados Unidos por código postal](dataset-us-population-zip.md)

## <a name="columns"></a>Columnas

| Nombre | Tipo de datos | Único | Valores (ejemplo) | Descripción |
|-|-|-|-|-|
| countyName | string | 1,960 | Washington County Jefferson County | Nombre del condado. |
| decennialTime | string | 2 | 2010 2000 | Fecha en la que se obtuvo el censo decenal; por ejemplo, 2010, 2000. |
| maxAge | int | 23 | 9 66 | Valor máximo del intervalo de edad. Si es nulo, se refiere a todas las edades, o bien el intervalo de edad no tiene límite superior; por ejemplo, edad superior a 85 años. |
| minAge | int | 23 | 35 67 | Valor mínimo del intervalo de edad. Si el valor es nulo, se refiere a todas las edades. |
| rellenado | int | 47,229 | 1 2 | Población de este segmento. |
| race | string | 8 | SOLO ASIÁTICO DOS O MÁS ETNIAS | Categoría de raza en los datos del censo. Si el valor es nulo, se refiere a todas las razas. |
| sex | string | 3 | Hombre Mujer | Masculino o femenino. Si el valor es nulo, se refiere a ambos sexos. |
| stateName | string | 52 | Texas Georgia | Nombre del estado de Estados Unidos. |
| year | int | 2 | 2010 2000 | Año (número entero) de la fecha decenal. |

## <a name="preview"></a>Vista previa

| decennialTime | stateName | countyName | rellenado | race | sex | minAge | maxAge | year |
|-|-|-|-|-|-|-|-|-|
| 2010 | Texas | Crockett County | 123 | WHITE ALONE | Male | 5 | 9 | 2010 |
| 2010 | Texas | Crockett County | 1 | ASIAN ALONE | Female | 67 | 69 | 2010 |
| 2010 | Texas | Crockett County | 111 | WHITE ALONE | Female | 55 | 59 | 2010 |
| 2010 | Texas | Crockett County | 64 | DOS O MÁS ETNIAS | null |  |  | 2010 |
| 2010 | Texas | Crockett County | 18 | null | Male | 85 |  | 2010 |
| 2010 | Texas | Crockett County | 16 | SOLO INDIOS AMERICANOS Y NATIVOS DE ALASKA | Female |  |  | 2010 |
| 2010 | Texas | Crockett County | 7 | WHITE ALONE | Male | 21 | 21 | 2010 |
| 2010 | Texas | Crockett County | 45 | null | Female | 85 |  | 2010 |
| 2010 | Texas | Crockett County | 0 | SOLO NATIVOS DE HAWÁI Y OTROS ISLEÑOS DEL PACÍFICO | Female | 67 | 69 | 2010 |

## <a name="data-access"></a>Acceso a datos

### <a name="azure-notebooks"></a>Azure Notebooks

# <a name="azureml-opendatasets"></a>[azureml-opendatasets](#tab/azureml-opendatasets)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azureml-opendatasets&registryId=us-decennial-census-county -->

> [!TIP]
> **[Descargue en su lugar el cuaderno](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azureml-opendatasets&registryId=us-decennial-census-county)** .

```python
# This is a package in preview.
from azureml.opendatasets import UsPopulationCounty

population = UsPopulationCounty()
population_df = population.to_pandas_dataframe()
```

```python
population_df.info()
```

<!-- nbend -->

# <a name="azure-storage"></a>[azure-storage](#tab/azure-storage)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azure-storage&registryId=us-decennial-census-county -->

> [!TIP]
> **[Descargue en su lugar el cuaderno](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azure-storage&registryId=us-decennial-census-county)** .

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
container_name = "censusdatacontainer"
folder_name = "release/us_population_county/"
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

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureDatabricks&package=azureml-opendatasets&registryId=us-decennial-census-county -->

> [!TIP]
> **[Descargue en su lugar el cuaderno](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureDatabricks&package=azureml-opendatasets&registryId=us-decennial-census-county)** .

```python
# This is a package in preview.
from azureml.opendatasets import UsPopulationCounty

population = UsPopulationCounty()
population_df = population.to_spark_dataframe()
```

```python
display(population_df.limit(5))
```

<!-- nbend -->

# <a name="azure-storage"></a>[azure-storage](#tab/azure-storage)

Ejemplo no disponible para esta combinación de plataforma y paquete.

# <a name="pyspark"></a>[pyspark](#tab/pyspark)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureDatabricks&package=pyspark&registryId=us-decennial-census-county -->

> [!TIP]
> **[Descargue en su lugar el cuaderno](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureDatabricks&package=pyspark&registryId=us-decennial-census-county)** .

```python
# Azure storage access info
blob_account_name = "azureopendatastorage"
blob_container_name = "censusdatacontainer"
blob_relative_path = "release/us_population_county/"
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

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureSynapse&package=azureml-opendatasets&registryId=us-decennial-census-county -->

> [!TIP]
> **[Descargue en su lugar el cuaderno](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureSynapse&package=azureml-opendatasets&registryId=us-decennial-census-county)** .

```python
# This is a package in preview.
from azureml.opendatasets import UsPopulationCounty

population = UsPopulationCounty()
population_df = population.to_spark_dataframe()
```

```python
# Display top 5 rows
display(population_df.limit(5))
```

<!-- nbend -->

# <a name="azure-storage"></a>[azure-storage](#tab/azure-storage)

Ejemplo no disponible para esta combinación de plataforma y paquete.

# <a name="pyspark"></a>[pyspark](#tab/pyspark)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureSynapse&package=pyspark&registryId=us-decennial-census-county -->

> [!TIP]
> **[Descargue en su lugar el cuaderno](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureSynapse&package=pyspark&registryId=us-decennial-census-county)** .

```python
# Azure storage access info
blob_account_name = "azureopendatastorage"
blob_container_name = "censusdatacontainer"
blob_relative_path = "release/us_population_county/"
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