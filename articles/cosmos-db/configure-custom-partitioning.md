---
title: Configuración de particiones personalizadas para crear particiones de datos del almacén analítico (versión preliminar)
description: Aprenda a desencadenar la creación de particiones personalizadas del cuaderno de Spark de Azure Synapse mediante Azure Synapse Link para Azure Cosmos DB. Explica las opciones de configuración.
author: Rodrigossz
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: rosouz
ms.custom: ignite-fall-2021
ms.openlocfilehash: f7848a9b578c6054ef1d3deb29bf4c2bf92b9f68
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131091016"
---
# <a name="configure-custom-partitioning-to-partition-analytical-store-data-preview"></a>Configuración de particiones personalizadas para crear particiones de datos del almacén analítico (versión preliminar)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Para usar la creación de particiones personalizadas, debe habilitar Azure Synapse Link en la cuenta de Azure Cosmos DB. Para más información, consulte [cómo configurar Azure Synapse Link](configure-synapse-link.md). La ejecución de particiones personalizadas se puede desencadenar desde el cuaderno de Spark de Azure Synapse mediante Azure Synapse Link para Azure Cosmos DB.

> [!IMPORTANT]
> La característica de creación de particiones personalizadas está actualmente en versión preliminar pública. Esta versión preliminar se ofrece sin Acuerdo de Nivel de Servicio y no se recomienda para cargas de trabajo de producción. Es posible que algunas características no sean compatibles o que tengan sus funcionalidades limitadas. Para más información, consulte [Términos de uso complementarios de las Versiones Preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

> [!NOTE]
> Las cuentas de Azure Cosmos DB deben tener Azure Synapse Link habilitado para aprovechar las ventajas de la creación de particiones personalizadas. Actualmente, la creación de particiones personalizadas solo se admite para Azure Synapse Spark 2.0.

Las siguientes son opciones de configuración obligatorias necesarias para desencadenar la ejecución de creación de particiones personalizadas:

* `spark.cosmos.asns.execute.partitioning`: valor booleano que desencadena la ejecución de particiones personalizadas. El valor predeterminado es false.

* `spark.cosmos.asns.partition.keys`: clave o claves de partición mediante la cadena con formato DDL. Por ejemplo, *cadena ReadDate*.

* `spark.cosmos.asns.basePath`: la ruta de acceso base del almacén con particiones en la cuenta de almacenamiento principal de Synapse.

> [!NOTE]
> Si elige varias claves de partición, puede acceder a estos registros desde el mismo almacén con particiones con basePath que indica la clave.

Las siguientes son opciones de configuración opcionales que puede usar al desencadenar la ejecución de particiones personalizadas:

* `spark.cosmos.asns.merge.partitioned.files`: valor booleano que permite crear un único archivo por valor de partición por ejecución. El valor predeterminado es False.

* `spark.cosmos.asns.partitioning.maxRecordsPerFile`: número máximo de registros en un archivo con una sola partición en el almacén con particiones. Si se especifican esta configuración y `spark.cosmos.asns.merge.partitioned.files`, se crean nuevos archivos una vez que el número de registros supera el valor maxRecordsPerFile. Normalmente, esta configuración solo es necesaria para la creación de particiones inicial para colecciones más grandes. El valor predeterminado es 1 000 000.

  Cuando se establece maxRecordsPerFile pero no se configura `spark.cosmos.asns.merge.partitioned.files`, los registros se pueden dividir entre archivos antes de llegar a maxRecordsPerFile. La división de archivos también depende del paralelismo disponible en el grupo.

* `spark.cosmos.asns.partitioning.shuffle.partitions`: controla el paralelismo durante las escrituras con particiones en el almacén con particiones. Esta configuración solo es necesaria para la creación de particiones inicial para colecciones más grandes. Se establece en el número de núcleos disponibles en el grupo de Spark. El valor predeterminado es 200. Un valor inferior podría desperdiciar recursos si el grupo no se usa para otras cargas de trabajo. Normalmente, un valor mayor no provoca problemas, ya que algunas tareas se completan pronto y pueden iniciar más tareas mientras se ejecutan las lentas. Si desea que el trabajo de creación de particiones se complete más rápido, es una buena práctica aumentar el tamaño del grupo.

# <a name="python"></a>[Python](#tab/python)

```python
spark.read\
    .format("cosmos.olap") \
    .option("spark.synapse.linkedService", "<enter linked service name>") \
    .option("spark.cosmos.container", "<enter container name>") \
    .option("spark.cosmos.asns.execute.partitioning", "true") \
    .option("spark.cosmos.asns.partition.keys", "readDate String") \
    .option("spark.cosmos.asns.basePath", "/mnt/CosmosDBPartitionedStore/") \
    .option("spark.cosmos.asns.merge.partitioned.files", "true") \
    .option("spark.cosmos.asns.partitioning.maxRecordsPerFile", "2000000") \
    .option("spark.cosmos.asns.partitioning.shuffle.partitions", "400") \
    .load()
```

# <a name="scala"></a>[Scala](#tab/scala)

```scala
spark.read.
    format("cosmos.olap").
    option("spark.synapse.linkedService", "<enter linked service name>").
    option("spark.cosmos.container", "<enter container name>").
    option("spark.cosmos.asns.execute.partitioning", "true").
    option("spark.cosmos.asns.partition.keys", "readDate String").
    option("spark.cosmos.asns.basePath", "/mnt/CosmosDBPartitionedStore/").
    option("spark.cosmos.asns.merge.partitioned.files", "true").
    option("spark.cosmos.asns.partitioning.maxRecordsPerFile", "2000000").
    option("spark.cosmos.asns.partitioning.shuffle.partitions", "400").
    load()
```
---

## <a name="query-execution-with-partitioned-store"></a>Ejecución de consultas con almacén con particiones

Las dos configuraciones siguientes son necesarias para ejecutar consultas con compatibilidad con el almacén con particiones:

* `spark.cosmos.asns.partition.keys`
* `spark.cosmos.asns.basePath`

En el ejemplo siguiente se muestra cómo usar estas configuraciones para consultar el almacén con particiones anterior y cómo el filtrado mediante la clave de partición puede hacer uso de la limpieza de particiones. Este almacén con particiones se particiona mediante el campo "ReadDate".

# <a name="python"></a>[Python](#tab/python)

```python
df = spark.read\
    .format("cosmos.olap") \
    .option("spark.synapse.linkedService", "<enter linked service name>") \
    .option("spark.cosmos.container", "<enter container name>") \
    .option("spark.cosmos.asns.partition.keys", "readDate String") \
    .option("spark.cosmos.asns.basePath", "/mnt/CosmosDBPartitionedStore/") \
    .load()

df_filtered = df.filter("readDate='2020-11-27 00:00:00.000'")
display(df_filtered.limit(10))
```

# <a name="scala"></a>[Scala](#tab/scala)

```scala
val df = spark.read.
            format("cosmos.olap").
            option("spark.synapse.linkedService", "<enter linked service name>").
            option("spark.cosmos.container", "<enter container name>").
            option("spark.cosmos.asns.partition.keys", "readDate String").
            option("spark.cosmos.asns.basePath", "/mnt/CosmosDBPartitionedStore/").
            load()
val df_filtered = df.filter("readDate='2020-11-27 00:00:00.000'")
display(df_filtered.limit(10))
```
---

El filtro *ReadDate = "2021-11-01"* anterior eliminará los datos correspondientes a valores ReadDate distintos de *2021-11-01* del examen, durante la ejecución.

> [!NOTE]
> Las mejoras de consulta que usan el almacén con particiones son aplicables cuando las consultas se ejecutan en lo siguiente:
>
> * Los dataframes de Spark creados a partir del contenedor del almacén analítico de Azure Cosmos DB y
> * Las tablas de Spark que apuntan al contenedor del almacén analítico de Azure Cosmos DB.

## <a name="next-steps"></a>Pasos siguientes

Para obtener más información, consulte la siguiente documentación:

* [Qué es la creación de particiones personalizadas](custom-partitioning-analytical-store.md) en Azure Synapse Link para Azure Cosmos DB?
* [Azure Synapse Link para Azure Cosmos DB](synapse-link.md)
* [Introducción al almacén analítico de Azure Cosmos DB](analytical-store-introduction.md)
* [Introducción a Azure Synapse Link para Azure Cosmos DB](configure-synapse-link.md)
* [Preguntas frecuentes sobre Azure Synapse Link para Azure Cosmos DB](synapse-link-frequently-asked-questions.yml)
