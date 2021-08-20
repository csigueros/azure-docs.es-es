---
title: Oxford COVID-19 Government Response Tracker
titleSuffix: Azure Open Datasets
description: Obtenga información sobre cómo usar el conjunto de datos Oxford COVID-19 Government Response Tracker en Azure Open Datasets.
ms.service: open-datasets
ms.topic: sample
author: peterclu
ms.author: peterlu
ms.date: 04/16/2021
ms.openlocfilehash: a39c8915027cddeb168f5bb0c63f915492ece398
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/22/2021
ms.locfileid: "114444621"
---
# <a name="oxford-covid-19-government-response-tracker"></a>Oxford COVID-19 Government Response Tracker

El [conjunto de datos Covid-19 Government Response Tracker (OxCGRT)](https://github.com/OxCGRT/covid-policy-tracker/) contiene información sistemática sobre qué gobiernos han tomado medidas y cuándo.

Esta información puede ayudar a los responsables de la toma de decisiones y a los ciudadanos a conocer la respuesta de los gobiernos de un modo coherente, lo que puede facilitar la labor de lucha contra la pandemia. El conjunto de datos OxCGRT recopila de manera sistemática información sobre varias políticas comunes que han llevado a cabo los gobiernos, registra estas políticas en una escala para mostrar la amplitud de las medidas gubernamentales y suma las puntuaciones para producir un conjunto de índices de políticas.


[!INCLUDE [Open Dataset usage notice](../../includes/open-datasets-usage-note.md)]

## <a name="datasets"></a>Conjuntos de datos

Las versiones modificadas del conjunto de datos están disponibles con los formatos CSV, JSON, JSON-Lines y Parquet, y se actualizan a diario.
- https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/covid_policy_tracker/latest/covid_policy_tracker.csv
- https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/covid_policy_tracker/latest/covid_policy_tracker.json
- https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/covid_policy_tracker/latest/covid_policy_tracker.jsonl
- https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/covid_policy_tracker/latest/covid_policy_tracker.parquet

Todas las versiones modificadas tienen códigos iso_country y los tiempos de carga agregados, y utilizan minúsculas para los nombres de las columnas con guiones bajos como separadores.

Datos sin procesar: https://pandemicdatalake.blob.core.windows.net/public/raw/covid-19/covid_policy_tracker/latest/CovidPolicyTracker.csv

Versiones anteriores de datos modificados y sin procesar: https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/covid_policy_tracker/ https://pandemicdatalake.blob.core.windows.net/public/raw/covid-19/covid_policy_tracker/

## <a name="data-volume"></a>Volumen de datos

El 8 de junio de 2020 contenía 27 919 filas (CSV: 4,9 MB, JSON: 20,9 MB, JSONL: 20,8 MB, Parquet: 133 MB).

## <a name="data-source"></a>Origen de datos

El origen de estos datos es Thomas Hale, Sam Webster, Anna Petherick, Toby Phillips y Beatriz Kira. (2020). Oxford COVID-19 Government Response Tracker. [Blavatnik School of Government](https://www.bsg.ox.ac.uk/). Los datos sin procesar se ingieren diariamente desde el [archivo csv de OxCGRT más reciente](https://github.com/OxCGRT/covid-policy-tracker/blob/master/data/OxCGRT_latest.csv). Para obtener más información sobre este conjunto de datos, incluida cómo se recopilan, vea el [sitio de seguimiento de las respuestas de los gobiernos](https://www.bsg.ox.ac.uk/research/research-projects/covid-19-government-response-tracker).

## <a name="data-quality"></a>Calidad de los datos
El conjunto de datos OxCGRT no garantiza la precisión de los datos ni que estén actualizados. Para obtener más información, vea la [declaración de calidad de datos](https://github.com/OxCGRT/covid-policy-tracker#data-quality).

## <a name="license-and-use-rights-attribution"></a>Atribución de licencias y derechos de uso

Estos datos se conceden bajo la [licencia internacional Creative Commons Attribution 4.0](https://github.com/OxCGRT/covid-policy-tracker/blob/master/LICENSE.txt).

Deben citarse del siguiente modo: Thomas Hale, Sam Webster, Anna Petherick, Toby Phillips y Beatriz Kira. (2020). Oxford COVID-19 Government Response Tracker. Blavatnik School of Government.

## <a name="contact"></a>Contacto

Si tiene alguna pregunta o comentario sobre este u otros conjuntos de datos del lago de datos COVID-19, póngase en contacto con askcovid19dl@microsoft.com.

## <a name="columns"></a>Columnas

| Nombre                                  | Tipo de datos | Único | Valores (ejemplo)            | Descripción                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                         |
|---------------------------------------|-----------|--------|----------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| c1_flag                               | boolean   | 3      | True                       | Marca binaria del ámbito geográfico. 0: específico; 1: general; en blanco: sin datos.                                                                                                                                                                                                                                                                                                                                                                                                                                                          |
| c1_school_closing                     | double    | 5      | 3.0 2.0                    | Registra el cierre de centros educativos y universidades. 0: ninguna medida; 1: se recomienda el cierre; 2: cierre obligatorio (solo algunos niveles o categorías; por ejemplo, solo la enseñanza secundaria o solo los centros públicos); 3: cierre obligatorio de todos los niveles; en blanco: sin datos                                                                                                                                                                                                                                                                                                 |
| c2_flag                               | boolean   | 3      | True                       | Marca binaria del ámbito geográfico. 0: específico; 1: general; en blanco: sin datos.                                                                                                                                                                                                                                                                                                                                                                                                                                                        |
| c2_workplace_closing                  | double    | 5      | 2.0 1.0                    | Registra el cierre de los lugares de trabajo. 0: ninguna medida; 1: se recomienda el cierre (o se recomienda trabajar desde casa); 2: cierre (o trabajo desde casa) obligatorio para algunos sectores o categorías de trabajadores; 3: cierre (o trabajo desde casa) obligatorio de todos los lugares de trabajo excepto los esenciales (por ejemplo, supermercados, centros médicos); en blanco: sin datos                                                                                                                                                                                                                          |
| c3_cancel_public_events               | double    | 4      | 2.0 1.0                    | Registra la cancelación de eventos públicos. 0: ninguna medida; 1: se recomienda la cancelación; 2: cancelación obligatoria; en blanco: sin datos                                                                                                                                                                                                                                                                                                                                                                                                                    |
| c3_flag                               | boolean   | 3      | True                       | Marca binaria del ámbito geográfico. 0: específico; 1: general; en blanco: sin datos.                                                                                                                                                                                                                                                                                                                                                                                                                                                          |
| c4_flag                               | boolean   | 3      | True                       | Marca binaria del ámbito geográfico. 0: específico; 1: general; en blanco: sin datos.                                                                                                                                                                                                                                                                                                                                                                                                                                                           |
| c4_restrictions_on_gatherings         | double    | 6      | 4.0 3.0                    | Registra los límites en cuanto a reuniones privadas. 0: sin restricciones; 1: restricciones para reuniones muy numerosas (límite superior a 1000 personas); 2: restricciones para reuniones de 101-1000 personas; 3: restricciones para reuniones de 11-100 personas; 4: restricciones para reuniones de 10 personas o menos; en blanco: sin datos.                                                                                                                                                                                                                 |
| c5_close_public_transport             | double    | 4      | 1.0 2.0                    | Registra el cierre del transporte público. 0: ninguna medida; 1: se recomienda el cierre (o reducir considerablemente el volumen, las rutas o los medios de transporte disponibles); 2: cierre obligatorio (o se prohíbe a la mayoría de los ciudadanos utilizarlo); en blanco: sin datos.                                                                                                                                                                                                                                                                                                          |
| c5_flag                               | boolean   | 3      | True                       | Marca binaria del ámbito geográfico. 0: específico; 1: general; en blanco: sin datos.                                                                                                                                                                                                                                                                                                                                                                                                                                                           |
| c6_flag                               | boolean   | 3      | True                       | Marca binaria del ámbito geográfico. 0: específico; 1: general; en blanco: sin datos.                                                                                                                                                                                                                                                                                                                                                                                                                                                           |
| c6_stay_at_home_requirements          | double    | 5      | 1.0 2.0                    | Registra las órdenes de "aislamiento" y confinamiento en los hogares. 0: ninguna medida; 1: se recomienda no salir de casa; 2: se prohíbe salir de casa excepto para practicar ejercicio diario, hacer la compra y para desplazamientos "esenciales"; 3: se prohíbe salir de casa con algunas excepciones mínimas (por ejemplo, se permite salir una vez a la semana o solo puede salir una persona cada vez, etc.); en blanco: sin datos                                                                                                                                                |
| c7_flag                               | boolean   | 3      | True                       | Marca binaria del ámbito geográfico. 0: específico; 1: general; en blanco: sin datos.                                                                                                                                                                                                                                                                                                                                                                                                                                                           |
| c7_restrictions_on_internal_movement  | double    | 4      | 2.0 1.0                    | Registra las restricciones de la movilidad interna entre ciudades o regiones. 0: ninguna medida; 1: se recomienda no viajar entre regiones o ciudades; 2: con restricciones a la movilidad interna; en blanco: sin datos.                                                                                                                                                                                                                                                                                                                                      |
| c8_international_travel_controls      | double    | 6      | 3.0 4.0                    | Registra las restricciones de los viajes internacionales. Nota: Registra la política relativa a los viajeros extranjeros, no a los ciudadanos nacionales. 0: sin restricciones; 1: controles a la llegada; 2: cuarentena a la llegada para viajeros procedentes de algunas o todas las regiones; 3: entrada prohibida a personas de algunas regiones; 4: entrada prohibida a personas de todas las regiones o cierre total de las fronteras; en blanco: sin datos                                                                                                                                                                                                                           |
| confirmedcases                        | SMALLINT  | 18,238 | 1 2                        |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
| confirmeddeaths                       | SMALLINT  | 14,906 | 1 2                        |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
| countrycode                           | string    | 186    | USA BRA                    |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
| countryname                           | string    | 186    | Estados Unidos Brasil       |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
| date                                  | date      | 478    | 2020-08-25 2021-03-30      |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
| e1_flag                               | boolean   | 3      | True                       | Marca binaria del ámbito sectorial. 0: solo trabajadores del sector formal; 1: se incluyen también los trabajadores del sector informal; en blanco: sin datos                                                                                                                                                                                                                                                                                                                                                                                                          |
| e1_income_support                     | double    | 4      | 1.0 2.0                    | Registra si el gobierno proporciona pagos directos en efectivo a las personas que pierden su puesto de trabajo o no pueden trabajar. Nota: Solo incluye los pagos a las empresas que los han vinculado explícitamente a las nóminas o salarios. 0: sin ayudas económicas; 1: el gobierno cubre menos del 50 % del salario perdido (o, si es una cantidad fija, menos del 50 % de la media salarial); 2: el gobierno cubre el 50 % o más del salario perdido (o, si es una cantidad fija, supera el 50 % de la media salarial); en blanco: sin datos.                                                                        |
| e2_debt/contract_relief               | double    | 4      | 1.0 2.0                    |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
| e3_fiscal_measures                    | double    | 819    | -0.01 3.0                  | Gasto anunciado por medidas de estímulo económico. Nota: Solo registra la cantidad adicional al gasto anunciado previamente. Registra el valor monetario de los estímulos fiscales en USD e incluye cualquier gasto debido a recortes tributarios que NO esté incluido en E4, H4 o H5. 0: sin gasto nuevo ese día; en blanco: sin datos                                                                                                                                                                                                                                                               |
| e4_international_support              | double    | 113    | -0.02 5000000.0            | Ofertas anunciadas de gasto por ayuda a otros países en relación con la Covid-19. Nota: Solo registra la cantidad adicional al gasto anunciado previamente. Registra el valor monetario en USD. 0: sin gasto nuevo ese día; en blanco: sin datos                                                                                                                                                                                                                                                                                                                 |
| h1_flag                               | boolean   | 3      | True                       | Marca binaria del ámbito geográfico. 0: específico; 1: general; en blanco: sin datos.                                                                                                                                                                                                                                                                                                                                                                                                                                                           |
| h1_public_information_campaigns       | double    | 4      | 2.0 1.0                    | Registra la presencia de campañas de información públicas. 0: ninguna campaña de información pública sobre la Covid-19; 1: las autoridades públicas instan a la prudencia en cuanto a la Covid-19; 2: campaña de información pública coordinada (por ejemplo, entre medios tradicionales y sociales); en blanco: sin datos                                                                                                                                                                                                                                                                                         |
| h2_testing_policy                     | double    | 5      | 2.0 1.0                    | Registra la política de los gobiernos en cuanto a quién tiene acceso a las pruebas. Nota: Se registran las políticas relacionadas con las pruebas para saber si existe infección en el momento actual (PCR), no las pruebas de inmunidad (anticuerpos). 0: ninguna política sobre las pruebas; 1: solo para las personas que tienen síntomas Y cumplen unos criterios específicos (por ejemplo, trabajadores esenciales, pacientes hospitalizados, personas que han estado en contacto con un caso conocido, personas que regresan del extranjero); 2: para todas las personas que muestren síntomas de la Covid-19; 3: para el público en general (por ejemplo, pruebas que se realizan a personas asintomáticas en su vehículo propio); en blanco: sin datos |
| h3_contact_tracing                    | double    | 4      | 2.0 1.0                    | Registra la política de los gobiernos en cuanto al seguimiento de los contactos cuando se diagnostica un caso positivo. Nota: Se buscan políticas que identificarían a todas las personas potencialmente expuestas a la Covid-19; no es probable que las aplicaciones por bluetooth voluntarias lo consiguieran. 0: sin seguimiento de los contactos; 1: seguimiento limitado de los contactos, es decir, no para todos los casos; 2: seguimiento completo de los contactos, es decir, para todos los casos identificados                                                                                                                                                          |
| h4_emergency_investment_in_healthcare | double    | 462    | 35.0 562.0                 | Gasto a corto plazo anunciado para el sistema sanitario; por ejemplo, hospitales, mascarillas, etc. Nota: Solo registra la cantidad adicional al gasto que se había anunciado previamente. Registra el valor monetario en USD. 0: sin gasto nuevo ese día; en blanco: sin datos                                                                                                                                                                                                                                                                                                         |
| h5_investment_in_vaccines             | double    | 133    | 1.0 191.0                  | Gasto público anunciado para el desarrollo de una vacuna contra la Covid-19. Nota: Solo registra la cantidad adicional al gasto anunciado previamente. Registra el valor monetario en USD. 0: sin gasto nuevo ese día; en blanco: sin datos.                                                                                                                                                                                                                                                                                                                            |
| iso_country                           | string    | 186    | US BR                      | Código de país o región conforme a la norma ISO 3166.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
| load_date                             | timestamp | 1      | 2021-04-26 00:06:25.157000 | Los datos de fecha y hora se han cargado de un origen externo.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
| stringencyindex                       | double    | 188    | 11.11 60.19                |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
| stringencyindexfordisplay             | double    | 188    | 11.11 60.19                |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |

## <a name="preview"></a>Vista previa

| countryname | countrycode | date       | c1_school_closing | c2_workplace_closing | c3_cancel_public_events | c4_restrictions_on_gatherings | c5_close_public_transport | c6_stay_at_home_requirements | c7_restrictions_on_internal_movement | c8_international_travel_controls | e1_income_support | e2_debt/contract_relief | e3_fiscal_measures | e4_international_support | h1_public_information_campaigns | h2_testing_policy | h3_contact_tracing | h4_emergency_investment_in_healthcare | h5_investment_in_vaccines | m1_wildcard | stringencyindex | stringencyindexfordisplay | iso_country | load_date             |
|-------------|-------------|------------|-------------------|----------------------|-------------------------|-------------------------------|---------------------------|------------------------------|--------------------------------------|----------------------------------|-------------------|-------------------------|--------------------|--------------------------|---------------------------------|-------------------|--------------------|---------------------------------------|---------------------------|-------------|-----------------|---------------------------|-------------|-----------------------|
| Aruba       | ABW         | 01-01-2020 | 0                 | 0                    | 0                       | 0                             | 0                         | 0                            | 0                                    | 0                                | 0                 | 0                       | 0                  | 0                        | 0                               | 0                 | 0                  | 0                                     | 0                         | null        | 0               | 0                         | AW          | 4/26/2021 12:06:25 AM |
| Aruba       | ABW         | 02-01-2020 | 0                 | 0                    | 0                       | 0                             | 0                         | 0                            | 0                                    | 0                                | 0                 | 0                       | 0                  | 0                        | 0                               | 0                 | 0                  | 0                                     | 0                         | null        | 0               | 0                         | AW          | 4/26/2021 12:06:25 AM |
| Aruba       | ABW         | 03-01-2020 | 0                 | 0                    | 0                       | 0                             | 0                         | 0                            | 0                                    | 0                                | 0                 | 0                       | 0                  | 0                        | 0                               | 0                 | 0                  | 0                                     | 0                         | null        | 0               | 0                         | AW          | 4/26/2021 12:06:25 AM |
| Aruba       | ABW         | 04-01-2020 | 0                 | 0                    | 0                       | 0                             | 0                         | 0                            | 0                                    | 0                                | 0                 | 0                       | 0                  | 0                        | 0                               | 0                 | 0                  | 0                                     | 0                         | null        | 0               | 0                         | AW          | 4/26/2021 12:06:25 AM |
| Aruba       | ABW         | 05-01-2020 | 0                 | 0                    | 0                       | 0                             | 0                         | 0                            | 0                                    | 0                                | 0                 | 0                       | 0                  | 0                        | 0                               | 0                 | 0                  | 0                                     | 0                         | null        | 0               | 0                         | AW          | 4/26/2021 12:06:25 AM |
| Aruba       | ABW         | 2020-01-06 | 0                 | 0                    | 0                       | 0                             | 0                         | 0                            | 0                                    | 0                                | 0                 | 0                       | 0                  | 0                        | 0                               | 0                 | 0                  | 0                                     | 0                         | null        | 0               | 0                         | AW          | 4/26/2021 12:06:25 AM |
| Aruba       | ABW         | 07-01-2020 | 0                 | 0                    | 0                       | 0                             | 0                         | 0                            | 0                                    | 0                                | 0                 | 0                       | 0                  | 0                        | 0                               | 0                 | 0                  | 0                                     | 0                         | null        | 0               | 0                         | AW          | 4/26/2021 12:06:25 AM |
| Aruba       | ABW         | 2020-01-08 | 0                 | 0                    | 0                       | 0                             | 0                         | 0                            | 0                                    | 0                                | 0                 | 0                       | 0                  | 0                        | 0                               | 0                 | 0                  | 0                                     | 0                         | null        | 0               | 0                         | AW          | 4/26/2021 12:06:25 AM |
| Aruba       | ABW         | 09-1-2020 | 0                 | 0                    | 0                       | 0                             | 0                         | 0                            | 0                                    | 0                                | 0                 | 0                       | 0                  | 0                        | 0                               | 0                 | 0                  | 0                                     | 0                         | null        | 0               | 0                         | AW          | 4/26/2021 12:06:25 AM |
| Aruba       | ABW         | 2020-01-10 | 0                 | 0                    | 0                       | 0                             | 0                         | 0                            | 0                                    | 0                                | 0                 | 0                       | 0                  | 0                        | 0                               | 0                 | 0                  | 0                                     | 0                         | null        | 0               | 0                         | AW          | 4/26/2021 12:06:25 AM |

## <a name="data-access"></a>Acceso a datos

### <a name="azure-notebooks"></a>Azure Notebooks

# <a name="azure-storage"></a>[azure-storage](#tab/azure-storage)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azure-storage&registryId=oxford-covid-19-government-response-tracker -->

> [!TIP]
> **[Descargue en su lugar el cuaderno](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azure-storage&registryId=oxford-covid-19-government-response-tracker)** .

## <a name="this-notebook-documents-the-urls-and-sample-code-to-access-the-oxford-covid-19-government-response-tracker-oxcgrt-dataset"></a>En este cuaderno se documentan las direcciones URL y el código de ejemplo para acceder al conjunto de datos Oxford Covid-19 Government Response Tracker (OxCGRT)

Direcciones URL de diferentes formatos de archivo hospedadas en Azure Blob Storage:

CSV: https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/covid_policy_tracker/latest/covid_policy_tracker.csv 

JSON: https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/covid_policy_tracker/latest/covid_policy_tracker.json

JSONL: https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/covid_policy_tracker/latest/covid_policy_tracker.jsonl 

Parquet: https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/covid_policy_tracker/latest/covid_policy_tracker.parquet

Descargue el archivo del conjunto de datos mediante la descarga de funcionalidad integrada desde una dirección URL HTTP en Pandas. Pandas tiene lectores para varios formatos de archivo:

https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.read_parquet.html

https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.read_csv.html


Para empezar, cargue el archivo del conjunto de datos en una trama de datos de Pandas y vea algunas filas de ejemplo.

```python
import pandas as pd
import numpy as np
%matplotlib inline
import matplotlib.pyplot as plt

df = pd.read_parquet("https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/covid_policy_tracker/latest/covid_policy_tracker.parquet")
df.head(10)
```

Se comprobarán los tipos de datos de los distintos campos y que la columna actualizada tiene el formato datetime

```python
df.dtypes
```

Este conjunto de datos contiene datos de distintos países. Se comprobará para qué países hay datos.

Empezará por ver los datos más recientes de cada país:

```python
df.groupby('countryname').first().filter(['confirmedcases ', 'confirmeddeaths','h5_investment_in_vaccines',
    'c6_stay_at_home_requirements','h4_emergency_investment_in_healthcare','c4_restrictions_on_gatherings', 'load_date'])
```

A continuación, se realizarán algunas agregaciones para asegurarse de que columnas como `confirmedcases` y `confirmeddeaths` registren los datos más recientes. Debería ver que los números de positivos y fallecidos en la fecha más reciente de la tabla anterior coinciden con la agregación de `confirmedcases` y `confirmeddeaths`.


```python
df.groupby('countryname').agg({'countryname': 'count','confirmedcases': 'sum','confirmeddeaths': 'sum',
                               'h5_investment_in_vaccines': 'count', 'c6_stay_at_home_requirements':'sum'})
```

Ahora se realizarán algunas visualizaciones básicas para algunos países.

```python
import plotly.graph_objects as go
import plotly.express as px
import matplotlib.pyplot as plt

df.loc[: , ['countryname', 'confirmedcases', 
'confirmeddeaths']].groupby(['countryname']).max().sort_values(by='confirmedcases', 
                                           ascending=False).reset_index()[:15].style.background_gradient(cmap='rainbow')
```

```python
df_US = df.groupby(df.date).agg({'confirmedcases': 'sum','confirmeddeaths':'sum'}).reset_index()

df_US.plot(kind='line',x='date',y="confirmedcases",grid=True)
df_US.plot(kind='line',x='date',y="confirmeddeaths",grid=True)

```

<!-- nbend -->

# <a name="pyspark"></a>[pyspark](#tab/pyspark)

Ejemplo no disponible para esta combinación de plataforma y paquete.

---

### <a name="azure-databricks"></a>Azure Databricks

# <a name="azure-storage"></a>[azure-storage](#tab/azure-storage)

Ejemplo no disponible para esta combinación de plataforma y paquete.

# <a name="pyspark"></a>[pyspark](#tab/pyspark)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureDatabricks&package=pyspark&registryId=oxford-covid-19-government-response-tracker -->

> [!TIP]
> **[En su lugar, descargue el cuaderno](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureDatabricks&package=pyspark&registryId=oxford-covid-19-government-response-tracker)** .

```python
# Azure storage access info
blob_account_name = "pandemicdatalake"
blob_container_name = "public"
blob_relative_path = "curated/covid-19/covid_policy_tracker/latest/covid_policy_tracker.parquet"
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

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureSynapse&package=pyspark&registryId=oxford-covid-19-government-response-tracker -->

> [!TIP]
> **[En su lugar, descargue el cuaderno](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureSynapse&package=pyspark&registryId=oxford-covid-19-government-response-tracker)** .

```python
# Azure storage access info
blob_account_name = "pandemicdatalake"
blob_container_name = "public"
blob_relative_path = "curated/covid-19/covid_policy_tracker/latest/covid_policy_tracker.parquet"
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
