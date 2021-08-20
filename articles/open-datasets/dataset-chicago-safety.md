---
title: Datos de seguridad de Chicago
titleSuffix: Azure Open Datasets
description: Aprenda a usar el conjunto de datos Chicago Safety Data de Azure Open Datasets.
ms.service: open-datasets
ms.topic: sample
author: peterclu
ms.author: peterlu
ms.date: 04/16/2021
ms.openlocfilehash: d39c89308f0c33f98f1c5d074746a2008317472a
ms.sourcegitcommit: c05e595b9f2dbe78e657fed2eb75c8fe511610e7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/11/2021
ms.locfileid: "112039117"
---
# <a name="chicago-safety-data"></a>Datos de seguridad de Chicago

Solicitudes de servicio en el número 311 en la ciudad de Chicago. Se incluyen datos históricos de las reclamaciones relacionadas con la normativa de saneamiento, los baches denunciados y problemas con el alumbrado público.

Todas las reclamaciones abiertas relacionadas con la normativa de saneamiento realizadas en el número 311 y todas las solicitudes realizadas desde el 1 de enero de 2011. El Departamento de Calles y Saneamiento investiga y soluciona las infracciones de la normativa de saneamiento de Chicago que se denuncian. Los residentes pueden solicitar asistencia para infracciones como contenedores desbordados y basura en los callejones. En el 311 se reciben a veces reclamaciones relacionadas con la normativa de saneamiento duplicadas. Las solicitudes que se han etiquetado como duplicadas están en la misma área geográfica que una solicitud anterior y se han introducido en el sistema de solicitud de servicio de los usuarios (CSR, por sus siglas en inglés) del número 311 a la misma hora aproximadamente. Las quejas duplicadas se etiquetan como tal en el campo de estado con los valores "Open - Dup" (abierta duplicada) o "Completed - Dup" (finalizada duplicada).

[!INCLUDE [Open Dataset usage notice](../../includes/open-datasets-usage-note.md)]

El Departamento de Transporte de Chicago (CDOT) supervisa la reparación de los baches en más de 6400 kilómetros de calles arteriales y residenciales de Chicago. CDOT recibe notificaciones sobre baches desde el centro de llamadas del 311. CDOT usa un sistema de mapas y seguimiento para identificar las ubicaciones de los baches y programar los equipos.

Una llamada al 311 puede dar lugar a varias reparaciones de baches. Cuando llega un equipo para reparar un bache denunciado en el 311, rellena todos los demás baches de la manzana. Las reparaciones de baches se realizan en un plazo de siete días desde la primera denuncia al 311. Las condiciones meteorológicas, las temperaturas muy bajas y las precipitaciones influyen en el tiempo que se tarda en llevar a cabo una reparación. Los días en los que el tiempo acompaña y no hay precipitaciones, los equipos pueden rellenar varios miles de baches. 

Si ya hay abierta una solicitud anterior para un grupo de cuatro direcciones contiguas, a la nueva solicitud se le asigna el estado "Duplicate (Open)" [Duplicada (abierta)]. Por ejemplo, si ya hay una solicitud en el CSR para la dirección 6535 N Western y se recibe una nueva solicitud para la dirección 6531 N Western (que está dentro de las cuatro direcciones de la solicitud original en el CSR), a la nueva se le asigna el estado "Duplicate (Open)". Una vez que se ha reparado la calle, en el CSR se indicará el estado "Completed" (Finalizada) para la solicitud original y "Duplicate (Closed)" [Duplicada (cerrada)] para las solicitudes duplicadas. Una solicitud de servicio recibe también el estado “Completed” cuando se inspecciona la dirección notificada y no se encuentran baches o ya se han reparado. Si se encuentra otro problema en la calle, como un derrumbe o un corte de algún servicio público sin reparar, se remite al departamento o al contratista correspondiente.

Todas las incidencias abiertas de tipo “Street Lights - All Out” (Farolas - Todo apagado), una avería de tres o más farolas, realizadas al 311 y todas las solicitudes completadas desde el 1 de enero de 2011. Chicago Department of Transportation (CDOT) supervisa aproximadamente 250 000 farolas de avenidas y calles residenciales de la ciudad de Chicago. CDOT lleva a cabo reparaciones y cambios de bombillas en respuesta a las denuncias de los residentes sobre farolas que no funcionan. Cuando CDOT recibe una denuncia de tipo "All Out" (Todo apagado), el electricista asignado para la reparación comprueba todas las farolas de ese circuito (cada circuito tiene entre 8 y 16) para asegurarse de que todas funcionan correctamente. Si se recibe una segunda solicitud de farolas apagadas en el mismo circuito en un período de cuatro días naturales desde la solicitud original, la nueva solicitud recibe automáticamente el estado “Duplicate (Open)”. Como el electricista de CDOT revisará todas las farolas de un circuito para comprobar que funcionan, se observará y reparará automáticamente cualquier dirección con el estado "Duplicate (Open)". Una vez reparadas las farolas, en el CSR se indica el estado “Completed” (Finalizada) para la solicitud original y “Duplicate (Closed)” [Duplicada (cerrada)] para las solicitudes duplicadas. Una solicitud de servicio recibe también el estado “Completed” cuando se inspeccionan las farolas de la solicitud y ya están reparadas y funcionando; cuando la solicitud de servicio es para una dirección que no existe; o cuando un contratista se ocupa del mantenimiento de esas farolas. Los datos se actualizan diariamente.

## <a name="volume-and-retention"></a>Volumen y retención

Este conjunto de datos se almacena en formato Parquet. Se actualiza a diario y contiene alrededor de 1 millón de filas (80 MB) en total desde 2018.

Este conjunto de datos contiene registros históricos acumulados desde 2011 hasta 2018. Puede usar la configuración de parámetros de nuestro SDK para recuperar los datos de un intervalo de tiempo específico.

## <a name="storage-location"></a>Ubicación de almacenamiento

Este conjunto de datos se almacena en la región Este de EE. UU. de Azure. Se recomienda asignar recursos de proceso de la misma región por afinidad.

## <a name="related-datasets"></a>Conjuntos de datos relacionados

- [Saneamiento de Chicago](https://data.cityofchicago.org/Service-Requests/311-Service-Requests-Sanitation-Code-Complaints-Hi/me59-5fac)
- [Baches en Chicago](https://data.cityofchicago.org/Service-Requests/311-Service-Requests-Pot-Holes-Reported-Historical/7as2-ds3y)
- [Farolas de Chicago](https://data.cityofchicago.org/Service-Requests/311-Service-Requests-Street-Lights-All-Out-Histori/zuxi-7xem)

## <a name="additional-information"></a>Información adicional

Este conjunto de datos se alimenta con los datos de la administración pública de la ciudad de Chicago.

Consulte los términos de uso de este conjunto de datos aquí. Envíe un correo electrónico a la dirección dataportal@cityofchicago.org si tiene alguna duda sobre el origen de los datos.

## <a name="columns"></a>Columnas

| Nombre | Tipo de datos | Único | Valores (ejemplo) | Descripción |
|-|-|-|-|-|
| address | string | 140,612 | \" \" 1 City Hall Plz Boston MA 02108 | Ubicación. |
| category | string | 54 | Street Cleaning Sanitation | Motivo de la solicitud de servicio. |
| dataSubtype | string | 1 | 311_All | “311_All” |
| dataType | string | 1 | Seguridad | “Safety” |
| dateTime | timestamp | 1,529,075 | 2015-07-23 10:51:00 2015-07-23 10:47:00 | Fecha y hora de apertura de la solicitud de servicio. |
| latitude | double | 1622 | 42.3594 42.3603 | Este es el valor de la latitud. Las líneas de la latitud son paralelas al ecuador. |
| longitude | double | 1,806 | -71.0587 -71.0583 | Este es el valor de la longitud. Las líneas de la longitud son perpendiculares a las líneas de la latitud y todas pasan por los dos polos. |
| source | string | 7 | Constituent Call Citizens Connect App | Fuente original del caso. |
| status | string | 2 | Closed Open | Estado del caso. |
| subcategory | string | 209 | Parking Enforcement Requests for Street Cleaning | Tipo de solicitud de servicio. |

## <a name="preview"></a>Vista previa

| dataType | dataSubtype | dateTime | category | subcategory | status | address | latitude | longitude | source | extendedProperties |
|-|-|-|-|-|-|-|-|-|-|-|
| Seguridad | 311_All | 4/25/2021 11:55:04 PM | Street Light Out Complaint | null | Abrir | 4800 W WASHINGTON BLVD | 41.882148426 | -87.74556256 | null |  |
| Seguridad | 311_All | 4/25/2021 11:54:31 PM | 311 INFORMATION ONLY CALL | null | Completado | 2111 W Lexington ST |  |  | null |  |
| Seguridad | 311_All | 4/25/2021 11:52:11 PM | 311 INFORMATION ONLY CALL | null | Completado | 2111 W Lexington ST |  |  | null |  |
| Seguridad | 311_All | 4/25/2021 11:49:56 PM | 311 INFORMATION ONLY CALL | null | Completado | 2111 W Lexington ST |  |  | null |  |
| Seguridad | 311_All | 4/25/2021 11:48:53 PM | Garbage Cart Maintenance | null | Abrir | 3409 E 106TH ST | 41.702545562 | -87.540917602 | null |  |
| Seguridad | 311_All | 4/25/2021 11:46:01 PM | 311 INFORMATION ONLY CALL | null | Completado | 2111 W Lexington ST |  |  | null |  |
| Seguridad | 311_All | 4/25/2021 11:45:46 PM | Aircraft Noise Complaint | null | Completado | 10510 W ZEMKE RD |  |  | null |  |
| Seguridad | 311_All | 4/25/2021 11:45:02 PM | 311 INFORMATION ONLY CALL | null | Completado | 2111 W Lexington ST |  |  | null |  |
| Seguridad | 311_All | 4/25/2021 11:44:24 PM | Sewer Cave-In Inspection Request | null | Abrir | 7246 W THORNDALE AVE | 41.987984339 | -87.808702917 | null |  |

## <a name="data-access"></a>Acceso a datos

### <a name="azure-notebooks"></a>Azure Notebooks

# <a name="azureml-opendatasets"></a>[azureml-opendatasets](#tab/azureml-opendatasets)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azureml-opendatasets&registryId=city_safety_chicago -->

> [!TIP]
> **[Descargue en su lugar el cuaderno](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azureml-opendatasets&registryId=city_safety_chicago)** .

```
# This is a package in preview.
from azureml.opendatasets import ChicagoSafety

from datetime import datetime
from dateutil import parser


end_date = parser.parse('2016-01-01')
start_date = parser.parse('2015-05-01')
safety = ChicagoSafety(start_date=start_date, end_date=end_date)
safety = safety.to_pandas_dataframe()
```

```
safety.info()
```

<!-- nbend -->

# <a name="azure-storage"></a>[azure-storage](#tab/azure-storage)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azure-storage&registryId=city_safety_chicago -->

> [!TIP]
> **[Descargue en su lugar el cuaderno](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azure-storage&registryId=city_safety_chicago)** .

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
folder_name = "Safety/Release/city=Chicago"
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

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureDatabricks&package=azureml-opendatasets&registryId=city_safety_chicago -->

> [!TIP]
> **[Descargue en su lugar el cuaderno](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureDatabricks&package=azureml-opendatasets&registryId=city_safety_chicago)** .

```
# This is a package in preview.
# You need to pip install azureml-opendatasets in Databricks cluster. https://docs.microsoft.com/en-us/azure/data-explorer/connect-from-databricks#install-the-python-library-on-your-azure-databricks-cluster
from azureml.opendatasets import ChicagoSafety

from datetime import datetime
from dateutil import parser


end_date = parser.parse('2016-01-01')
start_date = parser.parse('2015-05-01')
safety = ChicagoSafety(start_date=start_date, end_date=end_date)
safety = safety.to_spark_dataframe()
```

```
display(safety.limit(5))
```

<!-- nbend -->

# <a name="azure-storage"></a>[azure-storage](#tab/azure-storage)

Ejemplo no disponible para esta combinación de plataforma y paquete.

# <a name="pyspark"></a>[pyspark](#tab/pyspark)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureDatabricks&package=pyspark&registryId=city_safety_chicago -->

> [!TIP]
> **[Descargue en su lugar el cuaderno](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureDatabricks&package=pyspark&registryId=city_safety_chicago)** .

```python
# Azure storage access info
blob_account_name = "azureopendatastorage"
blob_container_name = "citydatacontainer"
blob_relative_path = "Safety/Release/city=Chicago"
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

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureSynapse&package=azureml-opendatasets&registryId=city_safety_chicago -->

> [!TIP]
> **[Descargue en su lugar el cuaderno](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureSynapse&package=azureml-opendatasets&registryId=city_safety_chicago)** .

```python
# This is a package in preview.
from azureml.opendatasets import ChicagoSafety

from datetime import datetime
from dateutil import parser

end_date = parser.parse('2016-01-01')
start_date = parser.parse('2015-05-01')
safety = ChicagoSafety(start_date=start_date, end_date=end_date)
safety = safety.to_spark_dataframe()
```

```python
# Display top 5 rows
display(safety.limit(5))
```

```python
# Display data statistic information
display(safety, summary = True)
```

<!-- nbend -->

# <a name="azure-storage"></a>[azure-storage](#tab/azure-storage)

Ejemplo no disponible para esta combinación de plataforma y paquete.

# <a name="pyspark"></a>[pyspark](#tab/pyspark)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureSynapse&package=pyspark&registryId=city_safety_chicago -->

> [!TIP]
> **[Descargue en su lugar el cuaderno](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureSynapse&package=pyspark&registryId=city_safety_chicago)** .

```python
# Azure storage access info
blob_account_name = "azureopendatastorage"
blob_container_name = "citydatacontainer"
blob_relative_path = "Safety/Release/city=Chicago"
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