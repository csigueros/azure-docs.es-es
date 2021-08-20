---
title: Proyecto de seguimiento de COVID
titleSuffix: Azure Open Datasets
description: Aprenda a usar el conjunto de datos del proyecto de seguimiento de COVID en Azure Open Datasets.
ms.service: open-datasets
ms.topic: sample
author: peterclu
ms.author: peterlu
ms.date: 04/16/2021
ms.openlocfilehash: 55814a6b1b78673c20447d6129f609058d5c794f
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/22/2021
ms.locfileid: "114453709"
---
# <a name="covid-tracking-project"></a>Proyecto de seguimiento de COVID

El conjunto de datos de COVID Tracking Project ofrece las últimas cifras sobre las pruebas, los casos confirmados, las hospitalizaciones y los pronósticos de los pacientes de cada estado y territorio de EE. UU.

Para obtener más información sobre este conjunto de datos, consulte el [repositorio de GitHub del proyecto](https://github.com/COVID19Tracking/covid-tracking-data).

[!INCLUDE [Open Dataset usage notice](../../includes/open-datasets-usage-note.md)]

## <a name="datasets"></a>Conjuntos de datos

Las versiones modificadas del conjunto de datos están disponibles en CSV, JSON, JSON-Lines y Parquet.
- https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/covid_tracking/latest/covid_tracking.csv
- https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/covid_tracking/latest/covid_tracking.json
- https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/covid_tracking/latest/covid_tracking.jsonl
- https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/covid_tracking/latest/covid_tracking.parquet

Todas las versiones modificadas tienen códigos de subdivisión conforme a la norma ISO 3166 y tiempos de carga agregados, y utilizan minúsculas para los nombres de las columnas con guiones bajos como separadores.

Datos sin procesar: "https://pandemicdatalake.blob.core.windows.net/public/raw/covid-19/covid_tracking/latest/daily.json"

Versiones anteriores de datos modificados y sin procesar: https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/covid_tracking/

https://pandemicdatalake.blob.core.windows.net/public/raw/covid-19/covid_tracking/

## <a name="data-volume"></a>Volumen de datos
Todos los conjuntos de datos se actualizan cada día. Desde el 13 de mayo de 2020, contenían 4100 filas (CSV: 574 KB, JSON: 1,8 MB, JSONL: 1,8 MB y Parquet: 334 KB).

## <a name="data-source"></a>Origen de datos

Estos datos se publican inicialmente en el proyecto “COVID Tracking Project at The Atlantic”. Los datos sin procesar se ingieren desde el repositorio de GitHub de seguimiento de COVID mediante el [archivo states_daily_4p_et.csv](https://github.com/COVID19Tracking/covid-tracking-data/blob/master/data/states_daily_4pm_et.csv). Para obtener más información sobre este conjunto de datos, incluidos sus orígenes, de la API del proyecto de seguimiento de COVID, consulte el [repositorio de GitHub del proyecto](https://github.com/COVID19Tracking/covid-tracking-data).

## <a name="data-quality"></a>Calidad de los datos
El proyecto de seguimiento de COVID clasifica la calidad de los datos para cada estado y ofrece más información sobre la evaluación de la calidad de los datos. Para obtener más información, consulte la [página de datos del proyecto de seguimiento de COVID](https://covidtracking.com/data). Los datos del repositorio de GitHub pueden tener una hora de retraso respecto a la API. Es necesario utilizar la API para acceder a los datos más recientes.

## <a name="license-and-use-rights-attribution"></a>Atribución de licencias y derechos de uso

Estos datos se conceden bajo los términos y condiciones de la [licencia de Apache 2.0](https://github.com/COVID19Tracking/covid-tracking-data/blob/master/LICENSE).

Todo uso de los datos debe mantener los avisos de propiedad intelectual, patente, marca comercial y atribución.

## <a name="contact"></a>Contacto

Si tiene alguna pregunta o comentario sobre este u otros conjuntos de datos del lago de datos COVID-19, póngase en contacto con askcovid19dl@microsoft.com.

## <a name="columns"></a>Columnas

| Nombre                        | Tipo de datos | Único | Valores (ejemplo)                                                                   | Descripción                                                                                                                 |
|-----------------------------|-----------|--------|-----------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------|
| date                        | date      | 420    | 2020-11-10 2021-01-30                                                             | Fecha para la que se han recopilado los totales diarios.                                                                             |
| date_checked                | string    | 9,487  | 2020-12-01T00:00:00Z 2020-09-01T00:00:00Z                                         | Obsoleto                                                                                                                  |
| death                       | SMALLINT  | 7,327  | 2 5                                                                               | Número total de personas fallecidas por COVID-19 hasta ahora.                                                        |
| death_increase              | SMALLINT  | 429    | 1 2                                                                               | Obsoleto                                                                                                                  |
| fips                        | SMALLINT  | 56     | 26 55                                                                             | Código del censo conforme a la norma FIPS.                                                                                             |
| fips_code                   | string    | 60     | 53 25                                                                             | Código del censo conforme a la norma FIPS.                                                                                             |
| hash                        | string    | 20,780 | 63df8cccd23a5476bab2d8111b138e4c9becd35e c606cd6990f16086b5382e12d84f6206172d493d | Hash para este registro.                                                                                                      |
| hospitalized                | int       | 7,641  | 89995 4                                                                           | Obsoleto                                                                                                                  |
| hospitalized_cumulative     | int       | 7,641  | 89995 4                                                                           | Número total de personas que han estado en el hospital por la COVID-19 hasta ahora, incluidos los que ya se han recuperado o han fallecido. |
| hospitalized_currently      | SMALLINT  | 3,886  | 8 13                                                                              | Número de personas hospitalizadas por la COVID-19 ese día.                                                                      |
| hospitalized_increase       | SMALLINT  | 615    | 1 2                                                                               | Obsoleto                                                                                                                  |
| in_icu_cumulative           | SMALLINT  | 2,295  | 990 220                                                                           | Número total de personas que han estado en la UCI por la COVID-19 hasta ahora, incluidos los que ya se han recuperado o han fallecido.      |
| in_icu_currently            | SMALLINT  | 1,643  | 2 8                                                                               | Número de personas en la UCI por la COVID-19 ese día.                                                                 |
| iso_country                 | string    | 1      | US                                                                                | Código de país o región conforme a la norma ISO 3166.                                                                                             |
| iso_subdivision             | string    | 57     | US-UM US-WA                                                                       | Código de subdivisión conforme a la norma ISO 3166.                                                                                                   |
| last_update_et              | timestamp | 9,487  | 2020-12-01 00:00:00 2020-09-01 00:00:00                                           | Hora de la última actualización de los datos de ese día.                                                                                        |
| load_time                   | timestamp | 1      | 2021-04-26 00:06:49.883000                                                        | Los datos de fecha y hora se han cargado en Azure desde el origen.                                                                  |
| negativa                    | int       | 10,864 | 305972 2140                                                                       | Número total de personas que han dado negativo en las pruebas de la COVID-19 hasta ahora.                                                        |
| negative_increase           | int       | 7,328  | 6 17                                                                              | Obsoleto                                                                                                                  |
| on_ventilator_cumulative    | SMALLINT  | 677    | 411 412                                                                           | Número total de personas que han utilizado un respirador por la COVID-19 hasta ahora, incluidos los que ya se han recuperado o han fallecido.    |
| on_ventilator_currently     | SMALLINT  | 837    | 4 10                                                                              | Número de personas que usan un respirador por la COVID-19 ese día.                                                               |
| pending                     | SMALLINT  | 944    | 2 17                                                                              | Número de pruebas cuyos resultados están todavía por determinar.                                                                    |
| pos_neg                     | int       | 18,282 | 2140 2                                                                            | Obsoleto                                                                                                                  |
| positiva                    | int       | 16,837 | 2 1                                                                               | Número total de personas que han dado positivo en las pruebas de la COVID-19 hasta ahora.                                                        |
| positive_increase           | SMALLINT  | 4,754  | 1 2                                                                               | Obsoleto                                                                                                                  |
| recovered                   | int       | 8,286  | 29 19                                                                             | Número total de personas que se han recuperado de la COVID-19 hasta ahora.                                                             |
| state                       | string    | 56     | MI PA                                                                             | Código de dos letras del estado.                                                                                              |
| total                       | int       | 18,283 | 2140 2                                                                            | Obsoleto                                                                                                                  |
| total_test_results          | int       | 18,648 | 2140 3                                                                            | Resultados totales de las pruebas proporcionados por el estado.                                                                                    |
| total_test_results_increase | int       | 13,463 | 1 2                                                                               | Obsoleto                                                                                                                  |

## <a name="preview"></a>Vista previa

| date       | state | positiva | hospitalized_currently | hospitalized_cumulative | on_ventilator_currently | data_quality_grade | last_update_et        | hash                                     | date_checked          | death | hospitalized | total   | total_test_results | pos_neg | fips | death_increase | hospitalized_increase | negative_increase | positive_increase | total_test_results_increase | fips_code | iso_subdivision | load_time             | iso_country | negativa | in_icu_cumulative | on_ventilator_cumulative | recovered | in_icu_currently |
|------------|-------|----------|------------------------|-------------------------|-------------------------|--------------------|-----------------------|------------------------------------------|-----------------------|-------|--------------|---------|--------------------|---------|------|----------------|-----------------------|-------------------|-------------------|-----------------------------|-----------|-----------------|-----------------------|-------------|----------|-------------------|--------------------------|-----------|------------------|
| 2021-03-07 | AK    | 56886    | 33                     | 1293                    | 2                       | null               | 3/5/2021 3:59:00 AM   | dc4bccd4bb885349d7e94d6fed058e285d4be164 | 3/5/2021 3:59:00 AM   | 305   | 1293         | 56886   | 1731628            | 56886   | 2    | 0              | 0                     | 0                 | 0                 | 0                           | 2         | US-AK           | 4/26/2021 12:06:49 AM | US          |          |                   |                          |           |                  |
| 2021-03-07 | AL    | 499819   | 494                    | 45976                   |                         | null               | 3/7/2021 11:00:00 AM  | 997207b430824ea40b8eb8506c19a93e07bc972e | 3/7/2021 11:00:00 AM  | 10148 | 45976        | 2431530 | 2323788            | 2431530 | 1    | -1             | 0                     | 2087              | 408               | 2347                        | 1         | US-AL           | 4/26/2021 12:06:49 AM | US          | 1931711  | 2676              | 1515                     | 295690    |                  |
| 2021-03-07 | AR    | 324818   | 335                    | 14926                   | 65                      | null               | 3/7/2021 12:00:00 AM  | 50921aeefba3e30d31623aa495b47fb2ecc72fae | 3/7/2021 12:00:00 AM  | 5319  | 14926        | 2805534 | 2736442            | 2805534 | 5    | 22             | 11                    | 3267              | 165               | 3380                        | 5         | US-AR           | 4/26/2021 12:06:49 AM | US          | 2480716  |                   | 1533                     | 315517    | 141              |
| 2021-03-07 | AS    | 0        |                        |                         |                         | null               | 12/1/2020 12:00:00 AM | 96d23f888c995b9a7f3b4b864de6414f45c728ff | 12/1/2020 12:00:00 AM | 0     |              | 2140    | 2140               | 2140    | 60   | 0              | 0                     | 0                 | 0                 | 0                           | 60        | US-AS           | 4/26/2021 12:06:49 AM | US          | 2140     |                   |                          |           |                  |
| 2021-03-07 | AZ    | 826454   | 963                    | 57907                   | 143                     | null               | 3/7/2021 12:00:00 AM  | 0437a7a96f4471666f775e63e86923eb5cbd8cdf | 3/7/2021 12:00:00 AM  | 16328 | 57907        | 3899464 | 7908105            | 3899464 | 4    | 5              | 44                    | 13678             | 1335              | 45110                       | 4         | US-AZ           | 4/26/2021 12:06:49 AM | US          | 3073010  |                   |                          |           | 273              |
| 2021-03-07 | CA    | 3501394  | 4291                   |                         |                         | null               | 3/7/2021 2:59:00 AM   | 63c5c0fd2daef2fb65150e9db486de98ed3f7b72 | 3/7/2021 2:59:00 AM   |       |              | 3501394 | 49646014           | 3501394 | 6    | 258            | 0                     | 0                 | 3816              | 133186                      | 6         | US-CA           | 4/26/2021 12:06:49 AM | US          |          |                   |                          |           | 1159             |
| 2021-03-07 | CO    | 436602   | 326                    | 23904                   |                         | null               | 3/7/2021 1:59:00 AM   | 444746cda3a596f183f3fa3269c8cab68704e819 | 3/7/2021 1:59:00 AM   | 5989  | 23904        | 2636060 | 6415123            | 2636060 | 8    | 3              | 18                    | 0                 | 840               | 38163                       | 8         | US-CO           | 4/26/2021 12:06:49 AM | US          | 2199458  |                   |                          |           |                  |
| 2021-03-07 | CT    | 285330   | 428                    | 12257                   |                         | null               | 3/4/2021 11:59:00 PM  | bcc0f7bc8c2bf77eec31b25f8b59d510f679d3e7 | 3/4/2021 11:59:00 PM  | 7704  | 12257        | 285330  | 6520366            | 285330  | 9    | 0              | 0                     | 0                 | 0                 | 0                           | 9         | US-CT           | 4/26/2021 12:06:49 AM | US          |          |                   |                          |           |                  |
| 2021-03-07 | DC    | 41419    | 150                    |                         | 16                      | null               | 3/6/2021 12:00:00 AM  | a3aa0d623d538807fb9577ad64354f48cf728cc8 | 3/6/2021 12:00:00 AM  | 1030  |              | 41419   | 1261363            | 41419   | 11   | 0              | 0                     | 0                 | 146               | 5726                        | 11        | US-DC           | 4/26/2021 12:06:49 AM | US          |          |                   |                          | 29570     | 38               |
| 2021-03-07 | DE    | 88354    | 104                    |                         |                         | null               | 3/6/2021 6:00:00 PM   | 059d870e689d5cc19c35f5eb398214d7d9856373 | 3/6/2021 6:00:00 PM   | 1473  |              | 633424  | 1431942            | 633424  | 10   | 9              | 0                     | 917               | 215               | 5867                        | 10        | US-DE           | 4/26/2021 12:06:49 AM | US          | 545070   |                   |                          |           | 13               |

## <a name="data-access"></a>Acceso a datos

### <a name="azure-notebooks"></a>Azure Notebooks

# <a name="azure-storage"></a>[azure-storage](#tab/azure-storage)

Direcciones URL de diferentes formatos de archivo de conjunto de datos hospedadas en Azure Blob Storage:

CSV: https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/covid_tracking/latest/covid_tracking.csv

JSON: https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/covid_tracking/latest/covid_tracking.json

JSONL: https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/covid_tracking/latest/covid_tracking.jsonl

Parquet: https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/covid_tracking/latest/covid_tracking.parquet

Descargue el archivo del conjunto de datos mediante la descarga de funcionalidad integrada desde una dirección URL HTTP en Pandas. Pandas tiene lectores para varios formatos de archivo:

https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.read_parquet.html

https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.read_csv.html

```python
import pandas as pd
import numpy as np
%matplotlib inline
import matplotlib.pyplot as plt

df = pd.read_parquet("https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/covid_tracking/latest/covid_tracking.parquet ")
df.head(10)

df.dtypes

df.groupby('state').first().filter(['date','positive', 'death'])

df.groupby(df.state).agg({'state': 'count','positive_increase': 'sum','death_increase': 'sum'})

df_NY=df[df['state'] == 'NY']
df_NY.plot(kind='line',x='date',y="positive",grid=True)
df_NY.plot(kind='line',x='date',y="positive_increase",grid=True)
df_NY.plot(kind='line',x='date',y="death",grid=True)
df_NY.plot(kind='line',x='date',y="death_increase",grid=True)

df_US=df.groupby(df.date).agg({'positive': 'sum','positive_increase': 'sum','death':'sum','death_increase': 'sum'}).reset_index()

df_US.plot(kind='line',x='date',y="positive",grid=True)
df_US.plot(kind='line',x='date',y="positive_increase",grid=True)
df_US.plot(kind='line',x='date',y="death",grid=True)
df_US.plot(kind='line',x='date',y="death_increase",grid=True)



```

# <a name="pyspark"></a>[pyspark](#tab/pyspark)

Ejemplo no disponible para esta combinación de plataforma y paquete.

---

### <a name="azure-databricks"></a>Azure Databricks

# <a name="azure-storage"></a>[azure-storage](#tab/azure-storage)

Ejemplo no disponible para esta combinación de plataforma y paquete.

# <a name="pyspark"></a>[pyspark](#tab/pyspark)

```python
# Azure storage access info
blob_account_name = "pandemicdatalake"
blob_container_name = "public"
blob_relative_path = "curated/covid-19/covid_tracking/latest/covid_tracking.parquet"
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

# <a name="azure-storage"></a>[azure-storage](#tab/azure-storage)

Ejemplo no disponible para esta combinación de plataforma y paquete.

# <a name="pyspark"></a>[pyspark](#tab/pyspark)

```python
# Azure storage access info
blob_account_name = "pandemicdatalake"
blob_container_name = "public"
blob_relative_path = "curated/covid-19/covid_tracking/latest/covid_tracking.parquet"
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
