---
title: Casos de COVID-19 del Centro europeo para la prevención y el control de enfermedades (ECDC)
titleSuffix: Azure Open Datasets
description: Aprenda a usar el conjunto de datos de casos de COVID-19 del Centro europeo para la prevención y el control de enfermedades (ECDC) en Azure Open Datasets.
ms.service: open-datasets
ms.topic: sample
author: peterclu
ms.author: peterlu
ms.date: 04/16/2021
ms.openlocfilehash: 31868ee27e531e70df4c89944f0baf888527a190
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/22/2021
ms.locfileid: "114447607"
---
# <a name="european-centre-for-disease-prevention-and-control-ecdc-covid-19-cases"></a>Casos de COVID-19 del Centro europeo para la prevención y el control de enfermedades (ECDC)

[Datos públicos más recientes disponibles](https://www.ecdc.europa.eu/en/publications-data/download-todays-data-geographic-distribution-covid-19-cases-worldwide) sobre la distribución geográfica de los casos de COVID-19 en todo el mundo proporcionados por el Centro europeo para la prevención y el control de enfermedades (ECDC). Cada fila o entrada contiene el número de casos nuevos notificados cada día por países o regiones.

[!INCLUDE [Open Dataset usage notice](../../includes/open-datasets-usage-note.md)]

## <a name="datasets"></a>Conjuntos de datos

Las versiones modificadas del conjunto de datos están disponibles con los formatos CSV, JSON, JSON-Lines y Parquet, y se actualizan a diario.
- https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/ecdc_cases/latest/ecdc_cases.csv
- https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/ecdc_cases/latest/ecdc_cases.json
- https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/ecdc_cases/latest/ecdc_cases.jsonl
- https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/ecdc_cases/latest/ecdc_cases.parquet

Todas las versiones modificadas tienen códigos iso_country_region y los tiempos de carga agregados, y utilizan minúsculas para los nombres de las columnas con guiones bajos como separadores.

Datos sin procesar: https://pandemicdatalake.blob.core.windows.net/public/raw/covid-19/ecdc_cases/latest/ECDCCases.csv

Versiones anteriores de datos modificados y sin procesar: https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/ecdc_cases/ https://pandemicdatalake.blob.core.windows.net/public/raw/covid-19/ecdc_cases/

## <a name="data-volume"></a>Volumen de datos
El 28 de mayo de 2020 contenían 19 876 filas (CSV: 1,5 MB, JSON: 4,9 MB, JSONL: 4,9 MB, Parquet: 54,1 MB).

## <a name="data-source"></a>Origen de datos

Los datos sin procesar se ingieren diariamente desde el [archivo CSV del ECDC](https://opendata.ecdc.europa.eu/covid19/casedistribution/csv). Para más información sobre este conjunto de datos, incluidos sus orígenes, consulte la [página de recopilación de datos del ECDC](https://www.ecdc.europa.eu/en/covid-19/data-collection).

## <a name="data-quality"></a>Calidad de los datos
El ECDC no garantiza la precisión de los datos ni que estén actualizados. [Lea la declinación de responsabilidades](https://www.ecdc.europa.eu/en/covid-19/data-collection).

## <a name="license-and-use-rights-attribution"></a>Atribución de licencias y derechos de uso

Estos datos se ofrecen y se pueden usar conforme a la directiva de propiedad intelectual del ECDC, disponible aquí. En el caso de los documentos cuya propiedad intelectual sea de un tercero, debe obtenerse el permiso del titular de tal propiedad intelectual para poder reproducirlos.

Siempre debe reconocerse al ECDC como origen de estos datos. Dicho reconocimiento se debe incluir en todas las copias del material.

## <a name="contact"></a>Contacto

Si tiene alguna duda o desea aportar algún comentario sobre este u otros conjuntos de datos del lago de datos COVID-19, póngase en contacto con askcovid19dl@microsoft.com.

## <a name="columns"></a>Columnas

| Nombre                      | Tipo de datos | Único | Valores (ejemplo)            | Descripción                            |
|---------------------------|-----------|--------|----------------------------|----------------------------------------|
| cases                     | SMALLINT  | 5515  | 1 2                        | Número de casos notificados.               |
| continent_exp             | string    | 6      | Europa África              | Nombre del continente.                         |
| countries_and_territories | string    | 214    | Canadá Bélgica             | Nombre del país o territorio.              |
| country_territory_code    | string    | 213    | KOR ISL                    | Código de tres letras del país o el territorio. |
| date_rep                  | date      | 350    | 2020-12-11 2020-11-22      | Fecha del informe.                     |
| day                       | SMALLINT  | 31     | 14 13                      | Día del mes.                           |
| deaths                    | SMALLINT  | 1049  | 1 2                        | Número de fallecimientos notificados.              |
| geo_id                    | string    | 214    | CA SE                      | Identificador geográfico.                         |
| iso_country               | string    | 214    | SE US                      | Código de país o región conforme a la norma ISO 3166.        |
| load_date                 | timestamp | 1      | 2021-04-26 00:06:22.123000 | Fecha en la que se cargaron los datos en Azure.      |
| month                     | SMALLINT  | 12     | 10 8                       | Número de mes.                           |
| year                      | SMALLINT  | 2      | 2020 2019                  | Year                                   |

## <a name="preview"></a>Vista previa

| date_rep   | day | month | year | cases | deaths | countries_and_territories | geo_id | country_territory_code | continent_exp | load_date             | iso_country |
|------------|-----|-------|------|-------|--------|---------------------------|--------|------------------------|---------------|-----------------------|-------------|
| 2020-12-14 | 14  | 12    | 2020 | 746   | 6      | Afganistán               | AF     | AFG                    | Asia          | 4/26/2021 12:06:22 AM | AF          |
| 2020-12-13 | 13  | 12    | 2020 | 298   | 9      | Afganistán               | AF     | AFG                    | Asia          | 4/26/2021 12:06:22 AM | AF          |
| 2020-12-12 | 12  | 12    | 2020 | 113   | 11     | Afganistán               | AF     | AFG                    | Asia          | 4/26/2021 12:06:22 AM | AF          |
| 2020-12-11 | 11  | 12    | 2020 | 63    | 10     | Afganistán               | AF     | AFG                    | Asia          | 4/26/2021 12:06:22 AM | AF          |
| 2020-12-10 | 10  | 12    | 2020 | 202   | 16     | Afganistán               | AF     | AFG                    | Asia          | 4/26/2021 12:06:22 AM | AF          |
| 2020-12-09 | 9   | 12    | 2020 | 135   | 13     | Afganistán               | AF     | AFG                    | Asia          | 4/26/2021 12:06:22 AM | AF          |
| 2020-12-08 | 8   | 12    | 2020 | 200   | 6      | Afganistán               | AF     | AFG                    | Asia          | 4/26/2021 12:06:22 AM | AF          |
| 2020-12-07 | 7   | 12    | 2020 | 210   | 26     | Afganistán               | AF     | AFG                    | Asia          | 4/26/2021 12:06:22 AM | AF          |
| 2020-12-06 | 6   | 12    | 2020 | 234   | 10     | Afganistán               | AF     | AFG                    | Asia          | 4/26/2021 12:06:22 AM | AF          |
| 2020-12-05 | 5   | 12    | 2020 | 235   | 18     | Afganistán               | AF     | AFG                    | Asia          | 4/26/2021 12:06:22 AM | AF          |

## <a name="data-access"></a>Acceso a datos

### <a name="azure-notebooks"></a>Azure Notebooks

# <a name="azure-storage"></a>[azure-storage](#tab/azure-storage)

Este cuaderno documenta las direcciones URL y el código de ejemplo para acceder al Centro europeo para la prevención y el control de enfermedades (ECDC). Direcciones URL de diferentes formatos de archivo del conjunto de datos de casos de Covid-19 hospedados en Azure Blob Storage:¶ CSV: https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/ecdc_cases/latest/ecdc_cases.csv

JSON: https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/ecdc_cases/latest/ecdc_cases.json

JSONL: https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/ecdc_cases/latest/ecdc_cases.jsonl

Parquet: https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/ecdc_cases/latest/ecdc_cases.parquet

Descargue el archivo del conjunto de datos mediante la funcionalidad integrada de descarga desde una dirección URL HTTP de Pandas. Pandas tiene lectores para varios formatos de archivo:

https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.read_parquet.html

https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.read_csv.html


```python
import pandas as pd
import numpy as np
%matplotlib inline
import matplotlib.pyplot as plt

df = pd.read_parquet("https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/ecdc_cases/latest/ecdc_cases.parquet")
df.head(10)

df.dtypes

df.groupby('countries_and_territories').first().filter(['continent_exp','cases', 'deaths','date_rep'])

df.groupby('continent_exp').agg({'countries_and_territories': 'count','cases': 'count','deaths': 'count'})

import plotly.graph_objects as go
import plotly.express as px
import matplotlib.pyplot as plt

df.loc[: , ['countries_and_territories', 'cases', 'deaths']].groupby(['countries_and_territories'
         ]).max().sort_values(by='cases',ascending=False).reset_index()[:15].style.background_gradient(cmap='rainbow')

df_Worldwide=df[df['countries_and_territories']=='United_States_of_America']

df.plot(kind='line',x='date_rep',y="cases",grid=True)
df.plot(kind='line',x='date_rep',y="deaths",grid=True)
#df_Worldwide.plot(kind='line',x='date_rep',y="confirmed_change",grid=True)
#df_Worldwide.plot(kind='line',x='date_rep',y="deaths_change",grid=True)

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
blob_relative_path = "curated/covid-19/ecdc_cases/latest/ecdc_cases.parquet"
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
blob_relative_path = "curated/covid-19/ecdc_cases/latest/ecdc_cases.parquet"
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

## <a name="examples"></a>Ejemplos

Consulte ejemplos de cómo se puede usar este conjunto de datos:

- [Análisis de datos de COVID con un punto de conexión de Synapse SQL sin servidor](https://techcommunity.microsoft.com/t5/azure-synapse-analytics/how-azure-synapse-analytics-helps-you-analyze-covid-data-with/ba-p/1457836)
- [Análisis de regresión lineal sobre datos de COVID mediante un punto de conexión de SQL en Azure Synapse Analytics](https://techcommunity.microsoft.com/t5/azure-synapse-analytics/linear-regression-analysis-on-covid-data-using-sql-endpoint-in/ba-p/1468697)

## <a name="next-steps"></a>Pasos siguientes

Consulte el resto de los conjuntos de datos en el [catálogo de Open Datasets](dataset-catalog.md).
