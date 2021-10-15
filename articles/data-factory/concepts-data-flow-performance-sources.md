---
title: Optimización del rendimiento de origen en el flujo de datos de asignación
titleSuffix: Azure Data Factory & Azure Synapse
description: Aprenda sobre la optimización del rendimiento de origen en los flujos de datos de asignación en las canalizaciones de Azure Data Factory y Azure Synapse Analytics.
author: kromerm
ms.topic: conceptual
ms.author: makromer
ms.service: data-factory
ms.subservice: data-flows
ms.custom: synapse
ms.date: 09/29/2021
ms.openlocfilehash: 7fef72d1ca52ae04106797c8ce7088b259aad033
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2021
ms.locfileid: "129293527"
---
# <a name="optimizing-sources"></a>Optimización de orígenes

En todos los orígenes, excepto Azure SQL Database, se recomienda mantener **Use current partitioning**  (Usar particiones actuales) como valor seleccionado. Cuando leen desde el resto de los sistemas de origen, los flujos de datos crean automáticamente particiones de los datos en función de su tamaño. Se crea una nueva partición aproximadamente por cada 128 MB de datos. A medida que aumenta el tamaño de los datos, aumenta el número de particiones.

Las particiones personalizadas tienen lugar *después* de que Spark lee los datos y afectan negativamente al rendimiento del flujo de datos. Puesto que los datos se dividen en particiones uniformes durante la lectura, esta opción no se recomienda. 

> [!NOTE]
> Las velocidades de lectura pueden verse limitadas por la capacidad de proceso del sistema de origen.

## <a name="azure-sql-database-sources"></a>Orígenes de Azure SQL Database

Azure SQL Database tiene una opción única de creación de particiones denominada creación de particiones "de origen". Si se habilita esta opción, pueden mejorar los tiempos de lectura de Azure SQL DB, ya que se habilitan conexiones paralelas en el sistema de origen. Se especifica el número de particiones y cómo se crean particiones de los datos. Se usa una columna de partición con alta cardinalidad. También se puede escribir una consulta que coincida con el esquema de partición de la tabla de origen.

> [!TIP]
> En la creación de particiones de origen, la E/S de SQL Server es el cuello de botella. Si se agregan demasiadas particiones, se puede saturar la base de datos de origen. Normalmente, cuando se usa esta opción el número ideal de particiones es de cuatro o cinco.

:::image type="content" source="media/data-flow/sourcepart3.png" alt-text="Creación de particiones de origen":::

### <a name="isolation-level"></a>Nivel de aislamiento

El nivel de aislamiento de la lectura en un sistema de origen de Azure SQL afecta al rendimiento. La opción "Read uncommitted" (Lectura no confirmada) proporcionará el rendimiento más rápido y evitará bloqueos de base de datos. Para obtener más información acerca de los niveles de aislamiento de SQL, consulte [Descripción de los niveles de aislamiento](/sql/connect/jdbc/understanding-isolation-levels).

### <a name="read-using-query"></a>Lectura mediante consulta

Puede leer de Azure SQL Database mediante una tabla o una consulta SQL. Si está ejecutando una consulta SQL, esta debe finalizar antes de que se pueda iniciar la transformación. Las consultas SQL pueden resultar útiles para insertar operaciones que pueden ejecutarse más rápido y reducir la cantidad de datos que se leen de una instancia de SQL Server como las instrucciones SELECT, WHERE y JOIN. Cuando se insertan las operaciones, se pierde la capacidad de realizar un seguimiento del linaje y el rendimiento de las transformaciones antes de que los datos lleguen al flujo de datos.

## <a name="azure-synapse-analytics-sources"></a>Orígenes de Azure Synapse Analytics

Cuando se usa Azure Synapse Analytics, las opciones de origen incluyen un valor denominado **Enable staging** (Habilitar almacenamiento provisional). Este permite que el servicio lea desde Synapse mediante ```Staging```, lo que mejora considerablemente el rendimiento de lectura con el comando[COPY de Synapse](/sql/t-sql/statements/copy-into-transact-sql) para obtener la capacidad de carga masiva con mayor rendimiento. La habilitación de ```Staging``` requiere que especifique una ubicación de almacenamiento provisional de Azure Blob Storage o Azure Data Lake Storage Gen2 en la configuración de la actividad del flujo de datos.

:::image type="content" source="media/data-flow/enable-staging.png" alt-text="Enable staging"::: (Habilitar almacenamiento provisional)

## <a name="file-based-sources"></a>Orígenes basados en archivos

Aunque los flujos de datos admiten una serie de tipos de archivo, se recomienda usar el formato Parquet nativo de Spark para conseguir tiempos óptimos de lectura y escritura.

Si está ejecutando el mismo flujo de datos en un conjunto de archivos, se recomienda leer de una carpeta, usar rutas de acceso con caracteres comodín o leer de una lista de archivos. Una sola ejecución de actividad de flujo de datos puede procesar todos los archivos por lotes. Puede encontrar más información sobre cómo establecer esta configuración en la sección **Transformación de origen** de la documentación sobre el [conector de Azure Blob Storage](connector-azure-blob-storage.md#source-transformation).

Si es posible, evite usar la actividad For-Eeach para ejecutar flujos de datos en un conjunto de archivos. Esto haría que cada iteración de For-Each activase su propio clúster de Spark, lo que a menudo no suele ser necesario y puede resultar caro. 

## <a name="next-steps"></a>Pasos siguientes

- [Introducción al rendimiento del flujo de datos](concepts-data-flow-performance.md)
- [Optimización de receptores](concepts-data-flow-performance-sinks.md)
- [Optimización de transformaciones](concepts-data-flow-performance-transformations.md)
- [Uso de flujos de datos en canalizaciones](concepts-data-flow-performance-pipelines.md)

Vea el resto de artículos sobre Data Flow relacionados con el rendimiento:

- [Actividad Data Flow](control-flow-execute-data-flow-activity.md)
- [Supervisión del rendimiento de flujo de datos](concepts-data-flow-monitoring.md)
- [Rendimiento de Microsoft Integration Runtime](concepts-integration-runtime-performance.md)
