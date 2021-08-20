---
title: Datos de seguridad de San Francisco
titleSuffix: Azure Open Datasets
description: Aprenda a usar el conjunto de datos Datos de seguridad de San Francisco en Azure Open Datasets.
ms.service: open-datasets
ms.topic: sample
author: peterclu
ms.author: peterlu
ms.date: 04/16/2021
ms.openlocfilehash: 4502d51054f43b7b1d876443c12f67418ba84060
ms.sourcegitcommit: 7c44970b9caf9d26ab8174c75480f5b09ae7c3d7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/27/2021
ms.locfileid: "112982463"
---
# <a name="san-francisco-safety-data"></a>Datos de seguridad de San Francisco

Llamadas de servicio a los bomberos y casos del número 311 en San Francisco.

[!INCLUDE [Open Dataset usage notice](../../includes/open-datasets-usage-note.md)]

Las llamadas de servicio a los bomberos incluyen todas las respuestas de una unidad de bomberos a las llamadas. Cada registro incluye el número de llamada, el número de incidente, la dirección, el identificador de la unidad, el tipo de llamada y la disposición. Todos los intervalos de tiempo relacionados están incluidos también. Puesto que este conjunto de datos se basa en respuestas y la mayoría de las llamadas implicaron a varias unidades, hay varios registros por cada número de llamada. Las direcciones están asociadas con un número de manzana, un cruce o un teléfono público, no una dirección específica.

En los casos del número 311 se incluyen los que están asociados con un lugar o una cosa (por ejemplo, parques, calles o edificios) y que se han creado después del 1 de julio de 2008. Se excluyen los casos registrados por un usuario sobre sus propias necesidades. Por ejemplo, preguntas fiscales sobre propiedades o negocios, solicitudes de permiso de aparcamiento, etc. Para obtener más información, vea [vínculo del programa](https://support.datasf.org/help/311-case-data-faq).
 
## <a name="volume-and-retention"></a>Volumen y retención

Este conjunto de datos se almacena en formato Parquet. Se actualiza a diario y tiene alrededor de 6 millones de filas (400 MB) desde 2019.

Este conjunto de datos contiene registros históricos acumulados desde 2015 hasta la actualidad. Puede usar la configuración de parámetros de nuestro SDK para recuperar los datos de un intervalo de tiempo específico.

## <a name="storage-location"></a>Ubicación de almacenamiento

Este conjunto de datos se almacena en la región Este de EE. UU. de Azure. Se recomienda asignar recursos de proceso de la misma región por afinidad.

## <a name="related-datasets"></a>Conjuntos de datos relacionados

- [Llamadas a los bomberos](https://data.sfgov.org/Public-Safety/Fire-Department-Calls-for-Service/nuek-vuh3)
- [Casos del 311](https://data.sfgov.org/City-Infrastructure/311-Cases/vw6y-z8j6)

## <a name="additional-information"></a>Información adicional

Este conjunto de datos se alimenta con los datos de la administración pública de la ciudad de San Francisco. Vea los [términos de uso de este conjunto de datos](https://datasf.org/opendata/terms-of-use/).

## <a name="columns"></a>Columnas

| Nombre | Tipo de datos | Único | Valores (ejemplo) | Descripción |
|-|-|-|-|-|
| address | string | 280,652 | Not associated with a specific address 0 Block of 6TH ST | Dirección del incidente. (Nota: La dirección y la ubicación se han generalizado a la manzana de la calle, un cruce o el lugar del teléfono público más próximo con el fin de proteger la privacidad de la persona que ha llamado). |
| category | string | 108 | Street and Sidewalk Cleaning Potentially Life-Threatening | Nombre legible del tipo de solicitud de servicio en el número 311 o del grupo de tipos de llamada para los avisos de incendio en el número 911. |
| dataSubtype | string | 2 | 911_Fire 311_All | "911_Fire" o "311_All". |
| dataType | string | 1 | Seguridad | “Safety” |
| dateTime | timestamp | 6,496,563 | 2020-10-19 12:28:08 2020-07-28 06:40:26 | Fecha y hora de la solicitud de servicio o del aviso de incendio. |
| latitude | double | 1,615,369 | 37.777624238929 37.786117211838 | Latitud de la ubicación, usando la proyección WGS84. |
| longitude | double | 1,554,612 | -122.39998111124 -122.419854245692 | Longitud de la ubicación, usando la proyección WGS84. |
| source | string | 9 | Phone Mobile/Open311 | Mecanismo o vía por la que se ha recibido la solicitud de servicio. Normalmente, "Teléfono", "Texto/SMS", "Sitio web", "Aplicación móvil", "Twitter", etc., pero los términos pueden variar de unos sistemas a otros. |
| status | string | 3 | Closed Open | Indicador de una sola palabra del estado actual de la solicitud de servicio. (Observación: GeoReport V2 solo admite "open" (abierta) y "closed" (cerrada)) |
| subcategory | string | 1270 | Medical Incident Bulky Items | Nombre legible del subtipo de solicitud de servicio para los casos del número 311 o del tipo de llamada para los avisos de incendio en el número 911. |

## <a name="preview"></a>Vista previa

| dataType | dataSubtype | dateTime | category | subcategory | status | address | latitude | longitude | source | extendedProperties |
|-|-|-|-|-|-|-|-|-|-|-|
| Seguridad | 911_Fire | 4/26/2021 2:56:13 AM | No potencialmente letal | Incidente médico | null | 700 Block of GEARY ST | 37.7863607914647 | -122.415616900246 | null |  |
| Seguridad | 911_Fire | 4/26/2021 2:56:13 AM | No potencialmente letal | Incidente médico | null | 700 Block of GEARY ST | 37.7863607914647 | -122.415616900246 | null |  |
| Seguridad | 911_Fire | 4/26/2021 2:54:03 AM | No potencialmente letal | Incidente médico | null | 0 Block of ESSEX ST | 37.7860048266229 | -122.395077258809 | null |  |
| Seguridad | 911_Fire | 4/26/2021 2:54:03 AM | No potencialmente letal | Incidente médico | null | 0 Block of ESSEX ST | 37.7860048266229 | -122.395077258809 | null |  |
| Seguridad | 911_Fire | 4/26/2021 2:52:17 AM | No potencialmente letal | Incidente médico | null | 700 Block of 29TH AVE | 37.7751770865322 | -122.488604397217 | null |  |
| Seguridad | 911_Fire | 4/26/2021 2:50:28 AM | Potencialmente letal | Incidente médico | null | 1000 Block of GEARY ST | 37.7857350982044 | -122.420555240691 | null |  |
| Seguridad | 911_Fire | 4/26/2021 2:50:28 AM | Potencialmente letal | Incidente médico | null | 1000 Block of GEARY ST | 37.7857350982044 | -122.420555240691 | null |  |
| Seguridad | 911_Fire | 4/26/2021 2:33:52 AM | No potencialmente letal | Incidente médico | null | 100 Block of BELVEDERE ST | 37.767791696654 | -122.449332294394 | null |  |
| Seguridad | 911_Fire | 4/26/2021 2:33:52 AM | No potencialmente letal | Incidente médico | null | 100 Block of BELVEDERE ST | 37.767791696654 | -122.449332294394 | null |  |
| Seguridad | 911_Fire | 4/26/2021 2:33:51 AM | Potencialmente letal | Incidente médico | null | 100 Block of 6TH ST | 37.7807920802756 | -122.408385745499 | null |  |

## <a name="data-access"></a>Acceso a datos

### <a name="azure-notebooks"></a>Azure Notebooks

# <a name="azureml-opendatasets"></a>[azureml-opendatasets](#tab/azureml-opendatasets)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azureml-opendatasets&registryId=city_safety_newyork -->

> [!TIP]
> **[Descargue en su lugar el cuaderno](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azureml-opendatasets&registryId=city_safety_newyork)** .

```
# This is a package in preview.
from azureml.opendatasets import NycSafety

from datetime import datetime
from dateutil import parser


end_date = parser.parse('2016-01-01')
start_date = parser.parse('2015-05-01')
safety = NycSafety(start_date=start_date, end_date=end_date)
safety = safety.to_pandas_dataframe()
```

```
safety.info()
```

<!-- nbend -->

# <a name="azure-storage"></a>[azure-storage](#tab/azure-storage)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azure-storage&registryId=city_safety_newyork -->

> [!TIP]
> **[Descargue en su lugar el cuaderno](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azure-storage&registryId=city_safety_newyork)** .

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
folder_name = "Safety/Release/city=NewYorkCity"
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

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureDatabricks&package=azureml-opendatasets&registryId=city_safety_newyork -->

> [!TIP]
> **[Descargue en su lugar el cuaderno](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureDatabricks&package=azureml-opendatasets&registryId=city_safety_newyork)** .

```
# This is a package in preview.
# You need to pip install azureml-opendatasets in Databricks cluster. https://docs.microsoft.com/en-us/azure/data-explorer/connect-from-databricks#install-the-python-library-on-your-azure-databricks-cluster
from azureml.opendatasets import NycSafety

from datetime import datetime
from dateutil import parser


end_date = parser.parse('2016-01-01')
start_date = parser.parse('2015-05-01')
safety = NycSafety(start_date=start_date, end_date=end_date)
safety = safety.to_spark_dataframe()
```

```
display(safety.limit(5))
```

<!-- nbend -->

# <a name="azure-storage"></a>[azure-storage](#tab/azure-storage)

Ejemplo no disponible para esta combinación de plataforma y paquete.

# <a name="pyspark"></a>[pyspark](#tab/pyspark)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureDatabricks&package=pyspark&registryId=city_safety_newyork -->

> [!TIP]
> **[Descargue en su lugar el cuaderno](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureDatabricks&package=pyspark&registryId=city_safety_newyork)** .

```python
# Azure storage access info
blob_account_name = "azureopendatastorage"
blob_container_name = "citydatacontainer"
blob_relative_path = "Safety/Release/city=NewYorkCity"
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

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureSynapse&package=azureml-opendatasets&registryId=city_safety_newyork -->

> [!TIP]
> **[Descargue en su lugar el cuaderno](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureSynapse&package=azureml-opendatasets&registryId=city_safety_newyork)** .

```python
# This is a package in preview.
from azureml.opendatasets import NycSafety

from datetime import datetime
from dateutil import parser

end_date = parser.parse('2016-01-01')
start_date = parser.parse('2015-05-01')
safety = NycSafety(start_date=start_date, end_date=end_date)
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

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureSynapse&package=pyspark&registryId=city_safety_newyork -->

> [!TIP]
> **[Descargue en su lugar el cuaderno](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureSynapse&package=pyspark&registryId=city_safety_newyork)** .

```python
# Azure storage access info
blob_account_name = "azureopendatastorage"
blob_container_name = "citydatacontainer"
blob_relative_path = "Safety/Release/city=NewYorkCity"
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
