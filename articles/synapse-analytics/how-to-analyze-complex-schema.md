---
title: Análisis del esquema con matrices y estructuras anidadas
description: Procedimiento para analizar matrices y estructuras anidadas con Apache Spark y SQL
services: synapse-analytics
author: Rodrigossz
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: spark
ms.date: 06/15/2020
ms.author: rosouz
ms.reviewer: jrasnick
ms.openlocfilehash: 0130379b5c9225329bde1d49c119eaec446d17de
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/27/2021
ms.locfileid: "123112629"
---
# <a name="analyze-complex-data-types-in-azure-synapse-analytics"></a>Análisis de tipos de datos complejos en Azure Synapse Analytics

Este artículo es adecuado para archivos y contenedores de Parquet en [Azure Synapse Link para Azure Cosmos DB](.\synapse-link\how-to-connect-synapse-link-cosmos-db.md). Puede usar Spark o SQL para leer o transformar datos con esquemas complejos como matrices o estructuras anidadas. El ejemplo siguiente se realiza con un solo documento, pero se puede escalar con facilidad a miles de millones de documentos con Spark o SQL. En el código incluido en este artículo se usa PySpark (Python).

## <a name="use-case"></a>Caso de uso

Los tipos de datos complejos son cada vez más comunes y representan un desafío para los ingenieros de datos. El análisis de matrices y esquemas anidados puede implicar consultas SQL complejas y lentas. Además, puede ser difícil cambiar el nombre o convertir el tipo de datos de las columnas anidadas. Por otra parte, si está trabajando con objetos profundamente anidados, puede encontrar problemas de rendimiento.

Los ingenieros de datos deben entender cómo procesar de forma eficaz los tipos de datos complejos y hacer que sean fácilmente accesibles para todos. En el ejemplo siguiente se usa Synapse Spark en Azure Synapse Analytics para leer y transformar objetos en una estructura plana por medio de tramas de datos. Se usa el modelo sin servidor de SQL en Azure Synapse Analytics para consultar estos objetos directamente y devolver los resultados como una tabla convencional.

## <a name="what-are-arrays-and-nested-structures"></a>¿Qué son las matrices y estructuras anidadas?

El objeto siguiente procede de [Application Insights](../azure-monitor/app/app-insights-overview.md). En este objeto hay estructuras anidadas y matrices que contienen estructuras anidadas.

```json
{
    "id": "66532691-ab20-11ea-8b1d-936b3ec64e54",
    "context": {
        "data": {
            "eventTime": "2020-06-10T13:43:34.553Z",
            "samplingRate": "100.0",
            "isSynthetic": "false"
        },
        "session": {
            "isFirst": "false",
            "id": "38619c14-7a23-4687-8268-95862c5326b1"
        },
        "custom": {
            "dimensions": [
                {
                    "customerInfo": {
                        "ProfileType": "ExpertUser",
                        "RoomName": "",
                        "CustomerName": "diamond",
                        "UserName": "XXXX@yahoo.com"
                    }
                },
                {
                    "customerInfo": {
                        "ProfileType": "Novice",
                        "RoomName": "",
                        "CustomerName": "topaz",
                        "UserName": "XXXX@outlook.com"
                    }
                }
            ]
        }
    }
}
```

### <a name="schema-example-of-arrays-and-nested-structures"></a>Ejemplo de esquema de matrices y estructuras anidadas
Al imprimir el esquema de la trama de datos del objeto (denominado **df**) con el comando `df.printschema`, se ve la siguiente representación:

* El color amarillo representa las estructuras anidadas.
* El color verde representa una matriz con dos elementos.

[![Código con resaltado amarillo y verde que muestra el origen del esquema](./media/how-to-complex-schema/schema-origin.png)](./media/how-to-complex-schema/schema-origin.png#lightbox)

`_rid`, `_ts` y `_etag` se han agregado al sistema a medida que el documento se ingería en el almacén transaccional de Azure Cosmos DB.

En la trama de datos anterior solo hay 5 columnas y 1 fila. Después de la transformación, la trama de datos seleccionada tendrá 13 columnas y 2 filas en un formato tabular.

## <a name="flatten-nested-structures-and-explode-arrays"></a>Acoplamiento de estructuras anidadas y expansión de matrices

Con Spark en Azure Synapse Analytics, resulta sencillo transformar estructuras anidadas en columnas y elementos de matriz en varias filas. Use los pasos siguientes para la implementación.

[![Diagrama de flujo que muestra los pasos para las transformaciones de Spark](./media/how-to-complex-schema/spark-transform-steps.png)](./media/how-to-complex-schema/spark-transform-steps.png#lightbox)

### <a name="define-a-function-to-flatten-the-nested-schema"></a>Definición de una función para acoplar el esquema anidado

Puede usar esta función sin cambiar. Cree una celda en un [cuaderno de PySpark](quickstart-apache-spark-notebook.md) con la siguiente función:

```python
from pyspark.sql.functions import col

def flatten_df(nested_df):
    stack = [((), nested_df)]
    columns = []

    while len(stack) > 0:
        parents, df = stack.pop()

        flat_cols = [
            col(".".join(parents + (c[0],))).alias("_".join(parents + (c[0],)))
            for c in df.dtypes
            if c[1][:6] != "struct"
        ]

        nested_cols = [
            c[0]
            for c in df.dtypes
            if c[1][:6] == "struct"
        ]

        columns.extend(flat_cols)

        for nested_col in nested_cols:
            projected_df = df.select(nested_col + ".*")
            stack.append((parents + (nested_col,), projected_df))

    return nested_df.select(columns)
```

### <a name="use-the-function-to-flatten-the-nested-schema"></a>Uso de la función para acoplar el esquema anidado

En este paso, se acopla el esquema anidado de la trama de datos (**df**) en una nueva trama de datos (`df_flat`):

```python
from pyspark.sql.types import StringType, StructField, StructType
df_flat = flatten_df(df)
display(df_flat.limit(10))
```

La función display debe devolver 10 columnas y 1 fila. La matriz y sus elementos anidados todavía están allí.

### <a name="transform-the-array"></a>Transformación de la matriz

Aquí se transforma la matriz, `context_custom_dimensions`, de la trama de datos `df_flat`, en una nueva trama de datos `df_flat_explode`. En el código siguiente también se define la columna que se va a seleccionar:

```python
from pyspark.sql.functions import explode
from pyspark.sql.functions import flatten
from pyspark.sql.functions import arrays_zip
df_flat_explode = df_flat.select("_rid","_ts","id","_etag",explode(df_flat.context_custom_dimensions),"context_session_isFirst","context_session_id","context_data_eventTime","context_data_samplingRate","context_data_isSynthetic")\
.select("_rid","_ts","id","_etag","col.*","context_session_isFirst","context_session_id","context_data_eventTime","context_data_samplingRate","context_data_isSynthetic")
display(df_flat_explode.limit(10))

```

La función display debe devolver 10 columnas y 2 filas. El siguiente paso consiste en acoplar los esquemas anidados con la función definida en el paso 1.

### <a name="use-the-function-to-flatten-the-nested-schema"></a>Uso de la función para acoplar el esquema anidado

Por último, se usa la función para acoplar el esquema anidado de la trama de datos `df_flat_explode` en una nueva trama de datos `df_flat_explode_flat`:
```python
df_flat_explode_flat = flatten_df(df_flat_explode)
display(df_flat_explode_flat.limit(10))
```

La función display debe mostrar 13 columnas y 2 filas.

La función `printSchema` de la trama de datos `df_flat_explode_flat` devuelve el siguiente resultado:

[![Código que muestra el esquema final](./media/how-to-complex-schema/schema-final.png)](./media/how-to-complex-schema/schema-final.png#lightbox)

## <a name="read-arrays-and-nested-structures-directly"></a>Lectura de matrices y estructuras anidadas directamente

Con el modelo sin servidor de SQL, puede consultar y crear vistas y tablas a través de dichos objetos.

En primer lugar, en función de cómo se hayan almacenado los datos, los usuarios deben usar la siguiente taxonomía. Todo lo que aparece en mayúsculas es específico del caso de uso:

| En bloque | Formato |
| ------ | ------ |
| 'https://ACCOUNTNAME.dfs.core.windows.net/FILESYSTEM/PATH/FINENAME.parquet' |"Parquet" (ADLSg2)|
| N'endpoint=https://ACCOUNTNAME.documents-staging.windows-ppe.net:443/;account=ACCOUNTNAME;database=DATABASENAME;collection=COLLECTIONNAME;region=REGIONTOQUERY', SECRET='YOURSECRET' |'CosmosDB' (Azure Synapse Link)|


Reemplace cada campo de esta manera:
* "YOUR BULK ABOVE" es la cadena de conexión del origen de datos al que se conecta.
* "YOUR TYPE ABOVE" es el formato que usa para conectarse al origen.

```sql
select *
FROM
openrowset(
    BULK 'YOUR BULK ABOVE',
    FORMAT='YOUR TYPE ABOVE'
)
with (id varchar(50),
        contextdataeventTime varchar(50) '$.context.data.eventTime',
        contextdatasamplingRate varchar(50) '$.context.data.samplingRate',
        contextdataisSynthetic varchar(50) '$.context.data.isSynthetic',
        contextsessionisFirst varchar(50) '$.context.session.isFirst',
        contextsessionid varchar(50) '$.context.session.id',
        contextcustomdimensions varchar(max) '$.context.custom.dimensions'
) as q 
cross apply openjson (contextcustomdimensions) 
with ( ProfileType varchar(50) '$.customerInfo.ProfileType',
            RoomName varchar(50) '$.customerInfo.RoomName',
            CustomerName varchar(50) '$.customerInfo.CustomerName',
            UserName varchar(50) '$.customerInfo.UserName'
    )
```

Hay dos tipos distintos de operaciones:

- El primer tipo de operación se indica en la siguiente línea de código, que define la columna denominada `contextdataeventTime` que hace referencia al elemento anidado, `Context.Data.eventTime`. 
  ```sql
  contextdataeventTime varchar(50) '$.context.data.eventTime'
  ```

  En esta línea se define la columna denominada `contextdataeventTime` que hace referencia al elemento anidado, `Context>Data>eventTime`.

- El segundo tipo de operación utiliza `cross apply` para crear nuevas filas para cada elemento de la matriz. A continuación, define cada objeto anidado. 
  ```sql
  cross apply openjson (contextcustomdimensions) 
  with ( ProfileType varchar(50) '$.customerInfo.ProfileType', 
  ```

  Si la matriz tuviera 5 elementos con 4 estructuras anidadas, el modelo sin servidor de SQL devolvería 5 filas y 4 columnas. El modelo sin servidor de SQL sin servidor puede consultar en contexto, asignar la matriz en 2 filas y mostrar todas las estructuras anidadas en columnas.

## <a name="next-steps"></a>Pasos siguientes

* [Información para consultar Synapse Link para Azure Cosmos DB con Spark 3](./synapse-link/how-to-query-analytical-store-spark-3.md)
* [Información para consultar Synapse Link para Azure Cosmos DB con Spark 2](./synapse-link/how-to-query-analytical-store-spark.md)
* [Consulta de tipos anidados de Parquet](./sql/query-parquet-nested-types.md)
