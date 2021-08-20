---
title: 'Índice de precios al productor de Estados Unidos: productos'
titleSuffix: Azure Open Datasets
description: 'Obtenga información sobre cómo usar el conjunto de datos Índice de precios al productor de Estados Unidos: productos en Azure Open Datasets.'
ms.service: open-datasets
ms.topic: sample
author: peterclu
ms.author: peterlu
ms.date: 04/16/2021
ms.openlocfilehash: 224a5d0278237d5a7d87ee8e3c9adaedb71f193a
ms.sourcegitcommit: c05e595b9f2dbe78e657fed2eb75c8fe511610e7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/11/2021
ms.locfileid: "112039159"
---
# <a name="us-producer-price-index---commodities"></a>Índice de precios al productor de Estados Unidos: productos

El índice de precios al productor (IPP) es una medida de la variación media a lo largo del tiempo de los precios de venta que reciben los productores nacionales por su producción. Los precios incluidos en el IPP corresponden a la primera transacción comercial de los productos y servicios cubiertos.

[!INCLUDE [Open Dataset usage notice](../../includes/open-datasets-usage-note.md)]

Cada mes se publican alrededor de 10 000 IPP para productos individuales y grupos de productos. Existen IPP para los productos de casi todas las industrias de los sectores de producción de bienes de la economía estadounidense: minería, fabricación, agricultura, pesca y silvicultura, así como gas natural, electricidad, construcción y productos que compiten con los fabricados en los sectores de producción, como los residuos y desechos. El programa IPP abarca aproximadamente el 72 % de la producción del sector servicios, según los ingresos registrados en el censo económico de 2007. Los datos incluyen industrias de los siguientes sectores: comercio mayorista y minorista; transporte y almacenamiento; información; finanzas y seguros; agentes inmobiliarios, alquiler, alquiler con derecho a compra; servicios profesionales, científicos y técnicos; servicios administrativos, de asistencia y de tratamiento de residuos; asistencia sanitaria y social; y alojamiento.

En la [ubicación original del conjunto de datos](https://download.bls.gov/pub/time.series/wp/) hay disponible un archivo [LÉAME](https://download.bls.gov/pub/time.series/wp/wp.txt) que contiene información detallada sobre el conjunto de datos. También hay más información disponible en la sección de [preguntas más frecuentes](https://www.bls.gov/ppi/ppifaq.htm).

Este conjunto de datos se alimenta de los [datos de índices de precios al productor](https://www.bls.gov/ppi/) que publica la [Oficina de Estadísticas Laborales (BLS, por sus siglas en inglés) de Estados Unidos](https://www.bls.gov/). Revise la [información sobre enlaces y copyright](https://www.bls.gov/bls/linksite.htm) y los [avisos importantes del sitio web](https://www.bls.gov/bls/website-policies.htm) para conocer los términos y condiciones relacionados con el uso de este conjunto de datos.

## <a name="storage-location"></a>Ubicación de almacenamiento

Este conjunto de datos se almacena en la región Este de EE. UU. de Azure. Se recomienda asignar recursos de proceso de la misma región por afinidad.

## <a name="related-datasets"></a>Conjuntos de datos relacionados

- [Índice de precios al consumo de Estados Unidos](dataset-us-consumer-price-index.md)
- [Índice de precios al productor de Estados Unidos: industria](dataset-us-producer-price-index-industry.md)

## <a name="columns"></a>Columnas

| Nombre | Tipo de datos | Único | Valores (ejemplo) | Descripción |
|-|-|-|-|-|
| footnote_codes | string | 3 | nan P | Identifica las notas a pie de página de la serie de datos. La mayoría de los valores son nulos. Vea https://download.bls.gov/pub/time.series/wp/wp.footnote. |
| group_code | string | 56 | 02 01 | Código que identifica el grupo de productos principal que cubre el índice. Consulte https://download.bls.gov/pub/time.series/wp/wp.group para obtener los códigos y los nombres de los grupos. |
| group_name | string | 56 | Alimentos procesados y productos de granja | Nombre del grupo de productos principal que cubre el índice. Consulte https://download.bls.gov/pub/time.series/wp/wp.group para obtener los códigos y los nombres de los grupos. |
| item_code | string | 2,949 | 1 11 | Identifica el producto al que pertenece la observación de los datos. Consulte https://download.bls.gov/pub/time.series/wp/wp.item para obtener los códigos y los nombres de los productos. |
| item_name | string | 3,410 | Almacenamiento y servicios relacionados Alquiler de automóviles de pasajeros | Nombres completos de los productos. Consulte https://download.bls.gov/pub/time.series/wp/wp.item para obtener los códigos y los nombres de los productos. |
| period | string | 13 | M06 M07 | Identifica el período para el que se observan los datos. Consulte https://download.bls.gov/pub/time.series/wp/wp.period para obtener una lista de períodos. |
| seasonal (estacional) | string | 2 | EE. UU. | Código que indica si los datos se han desestacionalizado. S = Desestacionalizado; U = No desestacionalizado. |
| series_id | string | 5,458 | WPU101 WPU091 | Código que identifica la serie específica. Una serie temporal hace referencia a un conjunto de datos observados a lo largo de un período prolongado de tiempo a intervalos constantes. Consulte https://download.bls.gov/pub/time.series/wp/wp.series para obtener información detallada de la serie, como el código, el nombre, los años de inicio y finalización, etc. |
| series_title | string | 4,379 | Datos de mercancías de IPP para servicios de minería de datos, sin ajuste estacional; datos de mercancías de IPP para servicios de tratamiento de metal, sin ajuste estacional | Título de la serie específica. Una serie temporal hace referencia a un conjunto de datos observados a lo largo de un período prolongado de tiempo a intervalos constantes. Vea https://download.bls.gov/pub/time.series/wp/wp.series para obtener información detallada de la serie, como el id., el nombre, los años de inicio y finalización, etc. |
| value | FLOAT | 6,788 | 100.0 99.0999984741211 | Índice de precios del artículo. |
| year | int | 26 | 2018 2017 | Identifica el año de observación. |

## <a name="preview"></a>Vista previa

| item_code | group_code | series_id | year | period | value | footnote_codes | seasonal (estacional) | series_title | group_name | item_name |
|-|-|-|-|-|-|-|-|-|-|-|
| 120922 | 05 | WPU05120922 | 2008 | M06 | 100 | nan | U | Datos de mercancías de IPP para combustibles y productos relacionados, y energía; hulla preparada de extracción subterránea, machacada mecánicamente, seleccionada, calibrada, sin ajuste estacional | Combustibles y productos relacionados, y energía | Hulla preparada de extracción subterránea, machacada mecánicamente, seleccionada, calibrada |
| 120922 | 05 | WPU05120922 | 2008 | M07 | 104,6 | nan | U | Datos de mercancías de IPP para combustibles y productos relacionados, y energía; hulla preparada de extracción subterránea, machacada mecánicamente, seleccionada, calibrada, sin ajuste estacional | Combustibles y productos relacionados, y energía | Hulla preparada de extracción subterránea, machacada mecánicamente, seleccionada, calibrada |
| 120922 | 05 | WPU05120922 | 2008 | M08 | 104.4 | nan | U | Datos de mercancías de IPP para combustibles y productos relacionados, y energía; hulla preparada de extracción subterránea, machacada mecánicamente, seleccionada, calibrada, sin ajuste estacional | Combustibles y productos relacionados, y energía | Hulla preparada de extracción subterránea, machacada mecánicamente, seleccionada, calibrada |
| 120922 | 05 | WPU05120922 | 2008 | M09 | 98,3 | nan | U | Datos de mercancías de IPP para combustibles y productos relacionados, y energía; hulla preparada de extracción subterránea, machacada mecánicamente, seleccionada, calibrada, sin ajuste estacional | Combustibles y productos relacionados, y energía | Hulla preparada de extracción subterránea, machacada mecánicamente, seleccionada, calibrada |
| 120922 | 05 | WPU05120922 | 2008 | M10 | 101,5 | nan | U | Datos de mercancías de IPP para combustibles y productos relacionados, y energía; hulla preparada de extracción subterránea, machacada mecánicamente, seleccionada, calibrada, sin ajuste estacional | Combustibles y productos relacionados, y energía | Hulla preparada de extracción subterránea, machacada mecánicamente, seleccionada, calibrada |
| 120922 | 05 | WPU05120922 | 2008 | M11 | 95.2 | nan | U | Datos de mercancías de IPP para combustibles y productos relacionados, y energía; hulla preparada de extracción subterránea, machacada mecánicamente, seleccionada, calibrada, sin ajuste estacional | Combustibles y productos relacionados, y energía | Hulla preparada de extracción subterránea, machacada mecánicamente, seleccionada, calibrada |

## <a name="data-access"></a>Acceso a datos

### <a name="azure-notebooks"></a>Azure Notebooks

# <a name="azureml-opendatasets"></a>[azureml-opendatasets](#tab/azureml-opendatasets)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azureml-opendatasets&registryId=us-producer-price-index-commodities -->

> [!TIP]
> **[Descargue en su lugar el cuaderno](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azureml-opendatasets&registryId=us-producer-price-index-commodities)** .

```python
# This is a package in preview.
from azureml.opendatasets import UsLaborPPICommodity

labor = UsLaborPPICommodity()
labor_df = labor.to_pandas_dataframe()
```

```python
labor_df.info()
```

<!-- nbend -->


# <a name="azure-storage"></a>[azure-storage](#tab/azure-storage)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azure-storage&registryId=us-producer-price-index-commodities -->

> [!TIP]
> **[Descargue en su lugar el cuaderno](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azure-storage&registryId=us-producer-price-index-commodities)** .

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
folder_name = "ppi_commodity/"
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

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureDatabricks&package=azureml-opendatasets&registryId=us-producer-price-index-commodities -->

> [!TIP]
> **[Descargue en su lugar el cuaderno](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureDatabricks&package=azureml-opendatasets&registryId=us-producer-price-index-commodities)** .

```python
# This is a package in preview.
from azureml.opendatasets import UsLaborPPICommodity

labor = UsLaborPPICommodity()
labor_df = labor.to_spark_dataframe()
```

```python
display(labor_df.limit(5))
```

<!-- nbend -->
 
# <a name="azure-storage"></a>[azure-storage](#tab/azure-storage)

Ejemplo no disponible para esta combinación de plataforma y paquete.

# <a name="pyspark"></a>[pyspark](#tab/pyspark)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureDatabricks&package=pyspark&registryId=us-producer-price-index-commodities -->

> [!TIP]
> **[Descargue en su lugar el cuaderno](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureDatabricks&package=pyspark&registryId=us-producer-price-index-commodities)** .

```python
# Azure storage access info
blob_account_name = "azureopendatastorage"
blob_container_name = "laborstatisticscontainer"
blob_relative_path = "ppi_commodity/"
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

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureSynapse&package=pyspark&registryId=us-producer-price-index-commodities -->

> [!TIP]
> **[Descargue en su lugar el cuaderno](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureSynapse&package=pyspark&registryId=us-producer-price-index-commodities)** .

```python
# Azure storage access info
blob_account_name = "azureopendatastorage"
blob_container_name = "laborstatisticscontainer"
blob_relative_path = "ppi_commodity/"
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