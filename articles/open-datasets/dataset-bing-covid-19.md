---
title: Bing COVID-19
titleSuffix: Azure Open Datasets
description: Aprenda a usar el conjunto de datos Bing COVID-19 en Azure Open Datasets.
ms.service: open-datasets
ms.topic: sample
author: peterclu
ms.author: peterlu
ms.date: 04/16/2021
ms.openlocfilehash: 7d797e169a0f1fbeeceaf377e731a051112c68e3
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/22/2021
ms.locfileid: "114452306"
---
# <a name="bing-covid-19"></a>Bing COVID-19

Los datos de COVID-19 de Bing incluyen los casos confirmados, los fallecimientos y los casos recuperados de todas las regiones. Se actualizan a diario.
Estos datos se reflejan en [Seguimiento de COVID-19 en Bing](https://bing.com/covid).

Bing recopila datos de múltiples fuentes confiables, como la [Organización Mundial de la Salud (OMS)](https://www.who.int/emergencies/diseases/novel-coronavirus-2019), [Centros para el Control y la Prevención de Enfermedades​ (CDC)](https://www.cdc.gov/coronavirus/2019-ncov/index.html), los ministerios de sanidad o salud pública, [BNO News](https://bnonews.com/index.php/2020/04/the-latest-coronavirus-cases/), [24/7 Wall St.](https://247wallst.com/) y [Wikipedia](https://en.wikipedia.org/wiki/2019%E2%80%9320_coronavirus_pandemic).

[!INCLUDE [Open Dataset usage notice](../../includes/open-datasets-usage-note.md)]

## <a name="datasets"></a>Conjuntos de datos
Los conjuntos de datos modificados están disponibles en los formatos CSV, JSON, JSON-Lines y Parquet.

- https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/bing_covid-19_data/latest/bing_covid-19_data.csv
- https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/bing_covid-19_data/latest/bing_covid-19_data.json
- https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/bing_covid-19_data/latest/bing_covid-19_data.jsonl
- https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/bing_covid-19_data/latest/bing_covid-19_data.parquet

Todos los conjuntos de datos modificados tienen códigos de subdivisión conforme a la norma ISO 3166 y tiempos de carga agregados, y utilizan minúsculas para los nombres de las columnas con guiones bajos como separadores.

Datos sin procesar: https://pandemicdatalake.blob.core.windows.net/public/raw/covid-19/bing_covid-19_data/latest/Bing-COVID19-Data.csv

Versiones anteriores de datos modificados y sin procesar: https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/bing_covid-19_data/

## <a name="data-volume"></a>Volumen de datos
Todos los conjuntos de datos se actualizan cada día. A 11 de mayo de 2020 contenían 125 576 filas (CSV 16,1 MB, JSON 40,0 MB, JSONL 39,6 MB, Parquet 1,1 MB).

## <a name="license-and-use-rights-attribution"></a>Atribución de licencias y derechos de uso
Estos datos están disponibles estrictamente con fines educativos y académicos, como la investigación médica, las agencias gubernamentales y las instituciones académicas, en [términos y condiciones](https://github.com/microsoft/Bing-COVID-19-Data/blob/master/LICENSE.txt).

Los datos que se usen o se citen en las publicaciones deben incluir una atribución a "Seguimiento de COVID-19 en Bing" con un vínculo a www.bing.com/covid.

## <a name="contact"></a>Contacto
Si tiene alguna duda o desea aportar algún comentario sobre este u otros conjuntos de datos del lago de datos COVID-19, póngase en contacto con askcovid19dl@microsoft.com.

## <a name="columns"></a>Columnas

| Nombre             | Tipo de datos | Único    | Valores (ejemplo)                    | Descripción                                                          |
|------------------|-----------|-----------|------------------------------------|----------------------------------------------------------------------|
| admin_region_1   | string    | 864       | Texas Georgia                      | Región dentro de country_region                                         |
| admin_region_2   | string    | 3143     | Washington County Jefferson County | Región dentro de admin_region_1                                         |
| confirmed        | int       | 120 692   | 1 2                                | Número de casos confirmados en la región                                  |
| confirmed_change | int       | 12 120    | 1 2                                | Cambio del número de casos confirmados con respecto al día anterior                 |
| country_region   | string    | 237       | Estados Unidos India                | País/región                                                       |
| deaths           | int       | 20 616    | 1 2                                | Número de muertes en la región                                      |
| deaths_change    | SMALLINT  | 1981     | 1 2                                | Cambio del número de muertes con respecto al día anterior                          |
| id               | int       | 1 783 534 | 742546 69019298                    | Identificador único                                                    |
| iso_subdivision  | string    | 484       | US-TX US-GA                        | Código de subdivisión según la norma ISO, segunda parte                                        |
| iso2             | string    | 226       | US IN                              | Identificador de código de país de 2 letras                                     |
| iso3             | string    | 226       | USA IND                            | Identificador de código de país de 3 letras                                     |
| latitude         | double    | 5675     | 42.28708 19.59852                  | Latitud del centroide de la región                               |
| load_time        | timestamp | 1         | 26-04-2021 00:06:34.719000         | La fecha y hora de carga del archivo desde el origen de Bing en GitHub |
| longitude        | double    | 5693     | -2.5396 -155.5186                  | Longitud del centroide de la región                              |
| recovered        | int       | 73 287    | 1 2                                | Número de recuperados en la región                                       |
| recovered_change | int       | 10 441    | 1 2                                | Cambio del número de casos recuperados con respecto al día anterior                 |
| actualizado          | date      | 457       | 23-04-2021 22-04-2021              | Registro a fecha de hoy                                        |

## <a name="preview"></a>Vista previa

| id     | actualizado    | confirmed | deaths | iso2 | iso3 | country_region | admin_region_1 | iso_subdivision | admin_region_2 | load_time             | confirmed_change | deaths_change |
|--------|------------|-----------|--------|------|------|----------------|----------------|-----------------|----------------|-----------------------|------------------|---------------|
| 338995 | 2020-01-21 | 262       | 0      | null | null | Todo el mundo      | null           | null            | null           | 26/04/2021 12:06:34 AM |                  |               |
| 338996 | 22-01-2020 | 313       | 0      | null | null | Todo el mundo      | null           | null            | null           | 26/04/2021 12:06:34 AM | 51               | 0             |
| 338997 | 23-1-2020 | 578       | 0      | null | null | Todo el mundo      | null           | null            | null           | 26/04/2021 12:06:34 AM | 265              | 0             |
| 338998 | 24-01-2020 | 841       | 0      | null | null | Todo el mundo      | null           | null            | null           | 26/04/2021 12:06:34 AM | 263              | 0             |
| 338999 | 25-01-2020 | 1320      | 0      | null | null | Todo el mundo      | null           | null            | null           | 26/04/2021 12:06:34 AM | 479              | 0             |
| 339000 | 26-01-2020 | 2014      | 0      | null | null | Todo el mundo      | null           | null            | null           | 26/04/2021 12:06:34 AM | 694              | 0             |
| 339001 | 27-01-2020 | 2798      | 0      | null | null | Todo el mundo      | null           | null            | null           | 26/04/2021 12:06:34 AM | 784              | 0             |
| 339002 | 28-01-2020 | 4593      | 0      | null | null | Todo el mundo      | null           | null            | null           | 26/04/2021 12:06:34 AM | 1795             | 0             |
| 339003 | 29-01-2020 | 6065      | 0      | null | null | Todo el mundo      | null           | null            | null           | 26/04/2021 12:06:34 AM | 1472             | 0             |
| 339004 | 30-1-2020 | 7818      | 0      | null | null | Todo el mundo      | null           | null            | null           | 26/04/2021 12:06:34 AM | 1753             | 0             |

## <a name="data-access"></a>Acceso a datos

### <a name="azure-notebooks"></a>Azure Notebooks

# <a name="azure-storage"></a>[azure-storage](#tab/azure-storage)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azure-storage&registryId=bing-covid-19-data -->

> [!TIP]
> **[En su lugar, descargue el cuaderno.](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azure-storage&registryId=bing-covid-19-data)**

#### <a name="this-notebook-documents-the-urls-and-sample-code-to-access-the-bing-covid-19-dataset"></a>En este cuaderno se documentan las direcciones URL y el código de ejemplo para acceder al [conjunto de datos Bing COVID-19](https://github.com/microsoft/Bing-COVID-19-Data)

Use las siguientes direcciones URL para obtener formatos de archivo específicos hospedados en Azure Blob Storage:

CSV: https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/bing_covid-19_data/latest/bing_covid-19_data.csv

JSON: https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/bing_covid-19_data/latest/bing_covid-19_data.json

JSONL: https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/bing_covid-19_data/latest/bing_covid-19_data.jsonl

Parquet: https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/bing_covid-19_data/latest/bing_covid-19_data.parquet

Descargue el archivo del conjunto de datos mediante la funcionalidad integrada de descarga desde una dirección URL HTTP de Pandas. Pandas tiene lectores para varios formatos de archivo:

https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.read_parquet.html

https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.read_csv.html


```python
import pandas as pd
import numpy as np
%matplotlib inline
import matplotlib.pyplot as plt

df = pd.read_parquet("https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/bing_covid-19_data/latest/bing_covid-19_data.parquet")
df.head(10)
```

Se comprobarán los tipos de datos de los distintos campos y que la columna actualizada tiene el formato datetime

```python
df.dtypes
```

Ahora se buscarán datos a nivel mundial y se trazarán algunos gráficos sencillos para visualizar los datos.

```python
df_Worldwide=df[df['country_region']=='Worldwide']
```

```python
df_Worldwide_pivot=df_Worldwide.pivot_table(df_Worldwide, index=['country_region','updated'])

df_Worldwide_pivot
```

```python
df_Worldwide.plot(kind='line',x='updated',y="confirmed",grid=True)
df_Worldwide.plot(kind='line',x='updated',y="deaths",grid=True)
df_Worldwide.plot(kind='line',x='updated',y="confirmed_change",grid=True)
df_Worldwide.plot(kind='line',x='updated',y="deaths_change",grid=True)
```

<!-- nbend -->

# <a name="pyspark"></a>[pyspark](#tab/pyspark)

Ejemplo no disponible para esta combinación de plataforma y paquete.

---

### <a name="azure-databricks"></a>Azure Databricks

# <a name="azure-storage"></a>[azure-storage](#tab/azure-storage)

Ejemplo no disponible para esta combinación de plataforma y paquete.

# <a name="pyspark"></a>[pyspark](#tab/pyspark)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureDatabricks&package=pyspark&registryId=bing-covid-19-data -->

> [!TIP]
> **[Descargue en su lugar el cuaderno](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureDatabricks&package=pyspark&registryId=bing-covid-19-data)** .

```python
# Azure storage access info
blob_account_name = "pandemicdatalake"
blob_container_name = "public"
blob_relative_path = "curated/covid-19/bing_covid-19_data/latest/bing_covid-19_data.parquet"
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

# <a name="azure-storage"></a>[azure-storage](#tab/azure-storage)

Ejemplo no disponible para esta combinación de plataforma y paquete.

# <a name="pyspark"></a>[pyspark](#tab/pyspark)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureSynapse&package=pyspark&registryId=bing-covid-19-data -->

> [!TIP]
> **[Descargue en su lugar el cuaderno](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureSynapse&package=pyspark&registryId=bing-covid-19-data)** .

```python
# Azure storage access info
blob_account_name = "pandemicdatalake"
blob_container_name = "public"
blob_relative_path = "curated/covid-19/bing_covid-19_data/latest/bing_covid-19_data.parquet"
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
