---
title: Optimización del rendimiento del receptor en el flujo de datos de asignación
titleSuffix: Azure Data Factory & Azure Synapse
description: Aprenda sobre la optimización del rendimiento del receptor en los flujos de datos de asignación en las canalizaciones de Azure Data Factory y Azure Synapse Analytics.
author: kromerm
ms.topic: conceptual
ms.author: makromer
ms.service: data-factory
ms.subservice: data-flows
ms.custom: synapse
ms.date: 09/29/2021
ms.openlocfilehash: 54c03cc8b4c34be02d3dee608ce4a759e75f2200
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2021
ms.locfileid: "129293531"
---
# <a name="optimizing-sinks"></a>Optimización de receptores

Cuando los flujos de datos escriben en los receptores, la creación de particiones personalizada se producirá inmediatamente antes de la escritura. Al igual que en el origen, en la mayoría de los casos se recomienda mantener **Use current partitioning** (Usar particiones actuales) como opción de partición seleccionada. Los datos con particiones se escribirán mucho más rápido que los datos sin particiones, incluso si el destino no tiene particiones. A continuación se muestran las cuestiones específicas que se aplican a varios tipos de receptores. 

## <a name="azure-sql-database-sinks"></a>Receptores de Azure SQL Database

En el caso de Azure SQL Database, la creación de particiones predeterminada debería funcionar en la mayoría de los casos. Existe la posibilidad de que el receptor pueda tener demasiadas particiones para que la base de datos SQL pueda administrarlas. Si ve que este puede ser el caso, reduzca el número de particiones que salen del receptor de SQL Database.

### <a name="impact-of-error-row-handling-to-performance"></a>Efecto del control de filas de error sobre el rendimiento

Cuando se habilita el control de filas de error ("continuar en caso de error") en la transformación del receptor, el servicio realiza un paso adicional antes de escribir las filas compatibles en la tabla de destino. Este paso adicional tendrá una pequeña penalización en el rendimiento que puede estar en torno al 5 %, a lo que se suma una pequeña reducción adicional del rendimiento si establece también la opción con las filas incompatibles en un archivo de registro.

### <a name="disabling-indexes-using-a-sql-script"></a>Deshabilitación de índices mediante un script SQL

Deshabilitar los índices antes de una carga en una base de datos SQL puede mejorar considerablemente el rendimiento de la escritura en la tabla. Ejecute el siguiente comando antes de escribir en el receptor de SQL.

`ALTER INDEX ALL ON dbo.[Table Name] DISABLE`

Una vez finalizada la escritura, vuelva a generar los índices con el siguiente comando:

`ALTER INDEX ALL ON dbo.[Table Name] REBUILD`

En los flujos de datos de asignación, ambos se pueden ejecutar de forma nativa mediante scripts anteriores y posteriores a SQL en una base de datos SQL de Azure o un receptor de Synapse.

:::image type="content" source="media/data-flow/disable-indexes-sql.png" alt-text="Deshabilitación de índices":::

> [!WARNING]
> Cuando se deshabilitan los índices, el flujo de datos toma de hecho el control sobre una base de datos y es poco probable que las consultas se realicen correctamente en este caso. Como resultado, se desencadenan muchos trabajos ETL en medio de la noche para evitar este conflicto. Para obtener más información, vea las [restricciones que conlleva deshabilitar índices SQL](/sql/relational-databases/indexes/disable-indexes-and-constraints).

### <a name="scaling-up-your-database"></a>Escalado vertical de la base de datos

Programe un cambio de tamaño del origen y el receptor de Azure SQL DB y Azure SQL DataWarehouse antes de que la canalización se ejecute para aumentar el rendimiento y minimizar la limitación de Azure una vez que se alcancen los límites de DTU. Una vez completada la ejecución de la canalización, cambie el tamaño de las bases de datos a la velocidad de ejecución normal.

## <a name="azure-synapse-analytics-sinks"></a>Receptores de Azure Synapse Analytics

Al escribir en Azure Synapse Analytics, asegúrese de que la opción **Enable staging**  (Habilitar almacenamiento provisional) esté establecida en true. Esta permite que el servicio escriba mediante el [comando COPY de SQL](/sql/t-sql/statements/copy-into-transact-sql), que carga los datos de forma eficaz y masiva. Tendrá que hacer referencia a una cuenta de Azure Data Lake Storage gen2 o de Azure Blob Storage para el almacenamiento provisional de los datos al usar Staging.

Además de Staging, en Azure Synapse Analytics se aplican los mismos procedimientos recomendados que en Azure SQL Database.

## <a name="file-based-sinks"></a>Receptores basados en archivos 

Aunque los flujos de datos admiten una serie de tipos de archivo, se recomienda usar el formato Parquet nativo de Spark para conseguir tiempos óptimos de lectura y escritura.

Si los datos se distribuyen uniformemente, **Use current partitioning** (Usar particiones actuales) será la opción de creación de particiones más rápida para escribir archivos.

### <a name="file-name-options"></a>Opciones de nombre de archivo

Al escribir archivos, tiene una selección de opciones de nomenclatura que afectan de diferente forma al rendimiento.

:::image type="content" source="media/data-flow/file-sink-settings.png" alt-text="Opciones del receptor":::

La opción **Default** (Predeterminada) ofrece la escritura más rápida. Cada partición equivaldrá a un archivo con el nombre predeterminado de Spark. Esto resulta útil si solo está leyendo de la carpeta de datos.

La opción **Pattern** (Patrón) establece un patrón de nomenclatura que cambiará el nombre de cada archivo de partición a un nombre más descriptivo. Esta operación se produce después de la escritura y es ligeramente más lenta que elegir la opción predeterminada. Per partition (Por partición) permite asignar un nombre a cada partición individual manualmente.

Si una columna corresponde a la forma en la que desea generar los datos, puede seleccionar **As data in column** (Como datos de columna). Esta opción reordena los datos y puede afectar al rendimiento si las columnas no están distribuidas uniformemente.

**Output to single file** (Salida a un solo archivo) combina todos los datos en una sola partición. Esto supone tiempos de escritura largos, especialmente en grandes conjuntos de datos. No es nada recomendable usar esta opción, a menos que haya una razón empresarial explícita para ello.

## <a name="cosmosdb-sinks"></a>Receptores de CosmosDB

Al escribir en CosmosDB, la modificación de la capacidad de proceso y del tamaño del lote durante la ejecución del flujo de datos pueden mejorar el rendimiento. Estos cambios solo surten efecto durante la ejecución de la actividad de flujo de datos y volverán a la configuración original de la colección tras la conclusión. 

**Batch size** (Tamaño de lote): Normalmente alcanza con comenzar con el tamaño de lote predeterminado. Para optimizar aún más este valor, calcule el tamaño de objeto aproximado de los datos y asegúrese de que el tamaño del objeto multiplicado por el tamaño de lote sea menor que 2 MB. Si lo es, puede aumentar el tamaño del lote para obtener un mejor rendimiento

**Throughput** (Capacidad de proceso): establezca aquí un valor de rendimiento mayor aquí para que los documentos escriban más rápidamente en CosmosDB. Tenga en cuenta que los costos de RU son mayores cuando aumenta el valor de capacidad de proceso.

**Write throughput budget** (Presupuesto de capacidad de proceso de escritura): use un valor que sea menor que el total de RU por minuto. Si tiene un flujo de datos con un número elevado de particiones de Spark y establece un presupuesto de capacidad de proceso, habrá más equilibrio entre las particiones.

## <a name="next-steps"></a>Pasos siguientes

- [Introducción al rendimiento del flujo de datos](concepts-data-flow-performance.md)
- [Optimización de orígenes](concepts-data-flow-performance-sources.md)
- [Optimización de transformaciones](concepts-data-flow-performance-transformations.md)
- [Uso de flujos de datos en canalizaciones](concepts-data-flow-performance-pipelines.md)

Vea el resto de artículos sobre Data Flow relacionados con el rendimiento:

- [Actividad Data Flow](control-flow-execute-data-flow-activity.md)
- [Supervisión del rendimiento de flujo de datos](concepts-data-flow-monitoring.md)
- [Rendimiento de Microsoft Integration Runtime](concepts-integration-runtime-performance.md)
