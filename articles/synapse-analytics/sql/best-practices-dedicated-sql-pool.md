---
title: Procedimientos recomendados para grupos de SQL dedicados
description: Recomendaciones y procedimientos recomendados que debe saber para trabajar con grupos de SQL dedicados.
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 03/17/2021
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: 4d37cabb8b74fea3a72ddafdf3322d20379f8d29
ms.sourcegitcommit: 6c6b8ba688a7cc699b68615c92adb550fbd0610f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121860983"
---
# <a name="best-practices-for-dedicated-sql-pools-in-azure-synapse-analytics"></a>Procedimientos recomendados para grupos de SQL dedicados en Azure Synapse Analytics

En este artículo se proporciona una colección de procedimientos recomendados que le ayudarán a conseguir un rendimiento óptimo con los grupos de SQL dedicados en Azure Synapse Analytics. A continuación, encontrará instrucciones básicas y áreas importantes en las que centrar su atención durante la creación de la solución. En cada sección se presenta un concepto y se le indican artículos que lo desarrollan más en detalle.

## <a name="dedicated-sql-pools-loading"></a>Carga de grupos de SQL dedicados

Puede encontrar una guía sobre la carga de grupos de SQL dedicados en [Guía para cargar datos](data-loading-best-practices.md).

## <a name="reduce-cost-with-pause-and-scale"></a>Menos costos gracias a las características de pausa y escalado

Para obtener más información acerca de cómo reducir los costos mediante la pausa y el escalado, consulte [Administración de procesos](../sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md?context=/azure/synapse-analytics/context/context).

## <a name="maintain-statistics"></a>Mantenimiento de estadísticas

El grupo de SQL dedicado se puede configurar para detectar y crear estadísticas automáticamente con información de las columnas.  Los planes de consulta que crea el optimizador son igual de buenos que las estadísticas disponibles.  

Se recomienda habilitar AUTO_CREATE_STATISTICS para las bases de datos y mantener actualizadas las estadísticas a diario o después de cada carga. Así, se asegurará de que las estadísticas de las columnas utilizadas en las consultas siempre estén actualizadas.

Para acortar el tiempo de mantenimiento de las estadísticas, sea selectivo sobre qué columnas tienen estadísticas o cuáles necesitan actualizaciones más frecuentes. Por ejemplo, debería actualizar las columnas de fecha en las que se añaden valores todos los días. Céntrese en tener estadísticas sobre las columnas que intervienen en combinaciones, las columnas que se usan en la cláusula WHERE y las columnas que se encuentran en GROUP BY.

Puede encontrar información adicional sobre las estadísticas en los artículos [Administración de estadísticas de tabla](develop-tables-statistics.md), [CREATE STATISTICS](/sql/t-sql/statements/create-statistics-transact-sql?view=azure-sqldw-latest&preserve-view=true) y [UPDATE STATISTICS](/sql/t-sql/statements/update-statistics-transact-sql?view=azure-sqldw-latest&preserve-view=true).

## <a name="tune-query-performance-with-new-product-enhancements"></a>Optimización del rendimiento de las consultas con nuevas mejoras en el producto

- [Optimización del rendimiento con vistas materializadas](../sql-data-warehouse/performance-tuning-materialized-views.md)
- [Optimización del rendimiento con el índice de almacén de columnas agrupado ordenado](../sql-data-warehouse/performance-tuning-ordered-cci.md)
- [Ajuste del rendimiento con la copia en caché del conjunto de resultados](../sql-data-warehouse/performance-tuning-result-set-caching.md)


## <a name="group-insert-statements-into-batches"></a>Agrupación de instrucciones INSERT en lotes

Una carga única en una tabla pequeña con una instrucción INSERT como `INSERT INTO MyLookup VALUES (1, 'Type 1')` puede ser el mejor enfoque en función de sus necesidades. Sin embargo, si necesita cargar miles o millones de filas a lo largo del día, es posible que las instrucciones INSERT sencillas no sean óptimas.

Una manera de solucionar este problema consiste en desarrollar un proceso que escriba en un archivo y, a continuación, otro proceso para cargar periódicamente este archivo. Consulte el artículo sobre [INSERT](/sql/t-sql/statements/insert-transact-sql?view=azure-sqldw-latest&preserve-view=true) para más información.

## <a name="use-polybase-to-load-and-export-data-quickly"></a>Uso de PolyBase para cargar y exportar los datos rápidamente

El grupo de SQL dedicado admite la carga y exportación de datos con varias herramientas, como Azure Data Factory, PolyBase y BCP.  Para pequeñas cantidades de datos donde el rendimiento no es clave, cualquier herramienta le sirve.  

> [!NOTE]
> PolyBase es la mejor opción para cargar o exportar grandes volúmenes de datos o si se necesita un rendimiento rápido.

Lo que haya cargado con PolyBase se ejecuta con la consulta CTAS o de selección. CTAS reducirá el registro de transacciones y es la manera más rápida de cargar datos. Azure Data Factory también admite cargas de PolyBase y puede lograr un rendimiento parecido a CTAS. PolyBase admite varios formatos de archivo, como Gzip.

Con el fin de conseguir un mayor rendimiento al usar archivos de texto Gzip, divídalos en 60 o más archivos para aumentar el paralelismo de la carga. Para conseguir un rendimiento total más rápido, cargue los datos simultáneamente. En los siguientes artículos se incluye información adicional sobre los temas pertinentes para esta sección:

- [Carga de datos](../sql-data-warehouse/design-elt-data-loading.md?context=/azure/synapse-analytics/context/context)
- [Guía para el uso de PolyBase](data-loading-best-practices.md)
- [Modelos y estrategias de carga en el grupo de SQL dedicado](/archive/blogs/sqlcat/azure-sql-data-warehouse-loading-patterns-and-strategies)
- [Carga de datos con Azure Data Factory](../../data-factory/load-azure-sql-data-warehouse.md?context=/azure/synapse-analytics/context/context)
- [Movimiento de datos con Azure Data Factory](../../data-factory/transform-data-using-machine-learning.md?context=/azure/synapse-analytics/context/context)
- [CREATE EXTERNAL FILE FORMAT](/sql/t-sql/statements/create-external-file-format-transact-sql?view=azure-sqldw-latest&preserve-view=true)
- [Create Table As Select (CTAS) en Almacenamiento de datos SQL](../sql-data-warehouse/sql-data-warehouse-develop-ctas.md?context=/azure/synapse-analytics/context/context)

## <a name="load-then-query-external-tables"></a>Carga y consulta de tablas externas

PolyBase no es óptimo para las consultas. Las tablas de PolyBase para grupos de SQL dedicados solo admiten actualmente archivos de blobs de Azure y almacenamiento de Azure Data Lake. Estos archivos no tienen ningún recurso de proceso que los respalde. Como resultado, los grupos de SQL dedicados no pueden descargar este trabajo y, para leer los datos del archivo entero, tienen que cargarlo en tempdb.

Si tiene varias consultas que usan estos datos, es mejor cargarlos una vez que las consultas usen la tabla local. Se incluyen más instrucciones sobre PolyBase en el artículo [Guía de uso de PolyBase](data-loading-best-practices.md).

## <a name="hash-distribute-large-tables"></a>Distribución Hash para tablas grandes

De forma predeterminada, las tablas se distribuyen según el patrón Round Robin.   Esta opción predeterminada facilita a los usuarios la tarea de comenzar a crear tablas sin tener que decidir sobre la distribución. El rendimiento de las tablas round robin puede ser suficiente para algunas cargas de trabajo. Sin embargo, en la mayoría de los casos, una columna de distribución ofrece un mejor rendimiento.  

El ejemplo más común de una tabla distribuida por una columna que supera a una round robin es al combinar dos tablas grandes de hechos.  

Por ejemplo, si tiene una tabla de pedidos que se distribuye por order_id, y una tabla de transacciones que también se distribuye por order_id, cuando se una la tabla de pedidos a la de transacciones en order_id, esta consulta se convertirá en una consulta de paso a través. A continuación, se eliminan las operaciones de movimiento de datos. Menos pasos suponen consultas más rápidas. Menos movimiento de datos también se traduce en consultas más rápidas.

> [!TIP]
> Al cargar una tabla distribuida, los datos entrantes no se deben ordenar con la clave de distribución. Si se ordena así, las cargas se ralentizarán.

Los vínculos a los artículos que se proporcionan a continuación le proporcionarán más detalles sobre cómo mejorar el rendimiento al seleccionar una columna de distribución. Además, encontrará información sobre cómo definir una tabla distribuida en la cláusula WITH de la instrucción CREATE TABLE:

- [Información general sobre las tablas](develop-tables-overview.md)
- [Distribución de tablas](../sql-data-warehouse/sql-data-warehouse-tables-distribute.md?context=/azure/synapse-analytics/context/context)
- [Selección de la distribución de tablas](/archive/blogs/sqlcat/choosing-hash-distributed-table-vs-round-robin-distributed-table-in-azure-sql-dw-service)
- [CREATE TABLE](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?view=azure-sqldw-latest&preserve-view=true)
- [CREATE TABLE AS SELECT](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?view=azure-sqldw-latest&preserve-view=true)

## <a name="do-not-over-partition"></a>Sin particiones excesivas

Crear particiones de datos puede resultar eficaz para el mantenimiento de los datos mediante la modificación de particiones o exámenes de optimización, pero el exceso de particiones puede ralentizar las consultas.  A menudo, una estrategia de creación de particiones con granularidad alta que puede funcionar bien en SQL Server no funciona correctamente en el grupo de SQL dedicado.  

El exceso de particiones puede reducir la eficacia de los índices de almacén de columnas agrupadas si cada partición tiene menos de 1 millón de filas. Los grupos de SQL dedicados particionan automáticamente los datos en 60 bases de datos. Por lo tanto, si crea una tabla con 100 particiones, el resultado serán 6000 particiones. Cada carga de trabajo es diferente, así que lo mejor es probar las particiones para ver qué funciona mejor para su carga.  

Una opción que debe tener en cuenta consiste en usar una granularidad inferior a la que estaba implementada al usar SQL Server. Por ejemplo, puede usar particiones semanales o mensuales, en lugar de particiones diarias.

Encontrará más información sobre las particiones en el artículo [Creación de particiones de tablas](../sql-data-warehouse/sql-data-warehouse-tables-partition.md?context=/azure/synapse-analytics/context/context).

## <a name="minimize-transaction-sizes"></a>Reducción del tamaño de las transacciones

Las instrucciones SELECT, UPDATE y DELETE se ejecutan en una transacción. Si devuelven un error, se deben revertir. Para que la reversión no se tarde tanto, minimice el tamaño de las transacciones siempre que pueda.  Puede realizar esta operación si divide las instrucciones INSERT, UPDATE y DELETE en partes. Por ejemplo, si tiene una instrucción INSERT que se suele tardar 1 hora, puede dividirla en cuatro partes. Cada ejecución durará 15 minutos.  

> [!TIP]
> Aproveche los casos de registro mínimo, como CTAS, TRUNCATE, DROP TABLE o INSERT para vaciar las tablas y así reducir el riesgo de reversión.  

Otra manera de eliminar reversiones es usar funciones de solo metadatos, como la modificación de particiones para la administración de datos.  Por ejemplo, en lugar de ejecutar una instrucción DELETE para eliminar todas las filas de una tabla en la que order_date fue en octubre de 2001, podría particionar los datos mensualmente. A continuación, puede cambiar la partición con datos por una partición vacía de otra tabla (consulte ejemplos de ALTER TABLE).  

Para tablas sin particiones, puede usar CTAS en lugar de DELETE para escribir los datos que quiera mantener en una tabla.  Si CTAS tarda lo mismo, es mucho más seguro, ya que su registro de transacciones es mínimo y se puede cancelar rápidamente si es necesario.

En los artículos siguientes se incluye más información sobre el contenido relacionado con esta sección:

- [Create Table As Select (CTAS) en Almacenamiento de datos SQL](../sql-data-warehouse/sql-data-warehouse-develop-ctas.md?context=/azure/synapse-analytics/context/context)
- [Descripción de las transacciones](develop-transactions.md)
- [Optimización de transacciones para Almacenamiento de datos SQL](../sql-data-warehouse/sql-data-warehouse-develop-best-practices-transactions.md?context=/azure/synapse-analytics/context/context)
- [Partición de tabla](../sql-data-warehouse/sql-data-warehouse-tables-partition.md?context=/azure/synapse-analytics/context/context)
- [TRUNCATE TABLE](/sql/t-sql/statements/truncate-table-transact-sql?view=azure-sqldw-latest&preserve-view=true)
- [ALTER TABLE](/sql/t-sql/statements/alter-table-transact-sql?view=azure-sqldw-latest&preserve-view=true)

## <a name="reduce-query-result-sizes"></a>Reducción del tamaño de los resultados de consultas

Esta operación le ayuda a evitar problemas en el lado del cliente a causa de resultados de consulta de gran tamaño.  Puede editar la consulta para reducir el número de filas devueltas. Algunas herramientas de generación de consultas le permiten agregar sintaxis de "N principal" a cada consulta.  También puede aplicar una instrucción CETAS al resultado de la consulta en una tabla temporal y, a continuación, usar la exportación de PolyBase para el procesamiento de nivel inferior.

## <a name="use-the-smallest-possible-column-size"></a>Uso del tamaño de columna mínimo

Al definir el DDL, use el tipo de datos mínimo compatible con los datos, ya que esto mejorará el rendimiento de la consulta.  Esta recomendación tiene especial importancia para las columnas CHAR y VARCHAR.  Si el valor mayor máximo de una columna es 25 caracteres, defina la columna como VARCHAR(25).  Evite definir todas las columnas de caracteres con una longitud predeterminada de gran tamaño.  Además, defina las columnas como VARCHAR en lugar de NVARCHAR cuando no se necesite nada más.

Consulte los artículos [Información general sobre tablas](develop-tables-overview.md), [Tipos de datos de tabla](develop-tables-data-types.md) y [CREATE TABLE](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?view=azure-sqldw-latest&preserve-view=true) para obtener un repaso más detallado de los conceptos esenciales relacionados con la información anterior.

## <a name="use-temporary-heap-tables-for-transient-data"></a>Uso de tablas de apilamiento temporal para datos transitorios

Cuando deje temporalmente los datos en grupos de SQL dedicados, las tablas de montón normalmente harán que el proceso general sea más rápido.  Si va a cargar datos solo para almacenarlos temporalmente antes de ejecutar más transformaciones, será más rápido cargar la tabla en una tabla de montón que cargar los datos en una tabla agrupada de almacén de columnas.  

Los datos de una tabla temporal también se cargarán mucho más rápido que las tablas de almacenamiento permanente.  Las tablas temporales empiezan por "#" y solo se puede acceder a ellas desde la sesión en la que se crean. Por lo tanto, pueden no funcionar en algunas situaciones. Las tablas de apilamiento se definen en la cláusula WITH de CREATE TABLE.  Si usa una tabla temporal, no olvide crear estadísticas en ella también.

Para obtener instrucciones adicionales, consulte los artículos [Tablas temporales](/sql/t-sql/statements/alter-table-transact-sql?view=azure-sqldw-latest&preserve-view=true), [CREATE TABLE](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?view=azure-sqldw-latest&preserve-view=true) y [CREATE TABLE AS SELECT](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?view=azure-sqldw-latest&preserve-view=true).

## <a name="optimize-clustered-columnstore-tables"></a>Optimización de tablas de almacén de columnas agrupadas

Los índices de almacén de columnas en clúster son una de las maneras más eficaces de almacenar los datos en el grupo de SQL dedicado.  De forma predeterminada, las tablas del grupo de SQL dedicado se crean como almacén de columnas en clúster.  Para conseguir el máximo rendimiento de las consultas en las tablas de almacén de columnas, es importante la calidad de los segmentos.  Escriben filas en las tablas de almacén de columnas bajo presión de memoria afecta a la calidad de segmento.  

La calidad de los segmentos se puede medir por el número de filas en un grupo de filas comprimido. Para obtener instrucciones detalladas acerca de la detección y mejora de la calidad de los segmentos en las tablas de almacén de columnas agrupadas, consulte la sección [Causas de una calidad deficiente del índice de almacén de columnas](../sql-data-warehouse/sql-data-warehouse-tables-index.md?context=/azure/synapse-analytics/context/context#causes-of-poor-columnstore-index-quality) del artículo sobre [Indexación de tablas](../sql-data-warehouse/sql-data-warehouse-tables-index.md?context=/azure/synapse-analytics/context/context).  

Como es importante que los segmentos de almacén de columnas sean de una buena calidad, es conveniente usar identificadores de usuario que se encuentren en la clase de recursos grande o mediana para cargar los datos. El uso de [unidades de almacenamiento de datos](resource-consumption-models.md) inferiores significa que desea asignar una clase de recurso mayor para el usuario que realiza la carga.

Normalmente, las tablas de almacén de columnas no envían datos en un segmento de almacén de columnas comprimido hasta que haya más de 1 millón de filas por tabla. Cada tabla del grupo de SQL dedicado se divide en 60 tablas. Por lo tanto, las tablas de almacén de columnas no beneficiarán a una consulta, a menos que la tabla tenga más de 60 millones de filas.  

> [!TIP]
> En el caso de las tablas con menos de 60 millones de filas, un índice de almacén de columnas puede no ser la solución óptima.  

Si divide los datos, cada partición deberá tener 1 millón de filas para beneficiarse de un índice de almacén de columnas agrupadas.  En el caso de una tabla con 100 particiones, serán necesarias al menos 6 mil millones de filas para beneficiarse de un almacén de columnas agrupadas (60 distribuciones *100 particiones* 1 millón de filas).  

Si la tabla no tiene 6 mil millones filas, tiene dos opciones principales. Reduzca el número de particiones o considere la posibilidad de usar una tabla de montón en su lugar.  También puede experimentar para ver si consigue un mejor rendimiento mediante una tabla de montón con índices secundarios, en lugar de con una tabla de almacén de columnas.

Al consultar una tabla de almacén de columnas, las consultas se ejecutarán más rápido si selecciona solo las que necesita.  Más información sobre los índices de tabla y de almacén de columnas en los siguientes artículos:
- [Índices de tablas](../sql-data-warehouse/sql-data-warehouse-tables-index.md?context=/azure/synapse-analytics/context/context)
- [Guía de índices de almacén de columnas](/sql/relational-databases/indexes/columnstore-indexes-overview?view=azure-sqldw-latest&preserve-view=true)
- [Regeneración de índices de almacén de columnas](../sql-data-warehouse/sql-data-warehouse-tables-index.md?view=azure-sqldw-latest&preserve-view=true#rebuilding-indexes-to-improve-segment-quality) 
- [Optimización del rendimiento con el índice de almacén de columnas agrupado ordenado](../sql-data-warehouse/performance-tuning-ordered-cci.md)

## <a name="use-larger-resource-class-to-improve-query-performance"></a>Uso de clases de recursos más grandes para mejorar el rendimiento de las consultas

Los grupos de SQL usan grupos de recursos para asignar memoria a las consultas. Inicialmente, todos los usuarios se asignan a los recursos de la clase pequeña, que concede 100 MB de memoria por distribución.  Siempre hay 60 distribuciones. Cada distribución recibe un mínimo de 100 MB. La asignación de memoria total en todo el sistema es de 6000 MB o poco menos de 6 GB.  

Algunas consultas, como las combinaciones de gran tamaño o las cargas a las tablas de almacén de columnas agrupadas, se beneficiarán de las mayores asignaciones de memoria.  Algunas consultas, como los exámenes puros, no sufrirán cambios. El uso de clases de recursos más grandes afecta a la simultaneidad. Por lo tanto, deberá tener estos datos en cuenta antes de mover todos los usuarios a una clase de recursos grande.

Para obtener información adicional sobre las clases de recursos, consulte el artículo [Clases de recursos para la administración de cargas de trabajo](../sql-data-warehouse/resource-classes-for-workload-management.md?context=/azure/synapse-analytics/context/context).

## <a name="use-smaller-resource-class-to-increase-concurrency"></a>Uso de clases de recursos más pequeñas para aumentar la simultaneidad

Si observa retrasos prolongados en las consultas de usuario, es posible que los usuarios estén ejecutándose en clases de recursos más grandes. Esta situación promueve el consumo de espacios de simultaneidad, que puede ocasionar la puesta en cola de otras consultas.  Para determinar si hay consultas de usuarios en cola, ejecute `SELECT * FROM sys.dm_pdw_waits` para ver si se devuelve alguna fila.

Los artículos [Clases de recursos para la administración de cargas de trabajo](../sql-data-warehouse/resource-classes-for-workload-management.md) y [sys.dm_pdw_waits](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-waits-transact-sql?view=azure-sqldw-latest&preserve-view=true) le proporcionarán más información.

## <a name="use-dmvs-to-monitor-and-optimize-your-queries"></a>Uso de vistas de administración dinámica (DMV) para supervisar y optimizar las consultas

Los grupos de SQL dedicados tienen varias vistas de administración dinámica que sirven para supervisar la ejecución de las consultas.  El siguiente artículo de supervisión le guía con instrucciones paso a paso acerca de cómo revisar los detalles de una consulta en curso.  Usar la opción LABEL con las consultas puede ayudar a encontrar rápidamente las consultas en estas DMV. Para obtener información detallada adicional, consulte los artículos incluidos en la siguiente lista:

- [Supervisión de la carga de trabajo mediante DMV](../sql-data-warehouse/sql-data-warehouse-manage-monitor.md?context=/azure/synapse-analytics/context/context)

- [LABEL](develop-label.md)
- [OPTION](/sql/t-sql/queries/option-clause-transact-sql?view=azure-sqldw-latest&preserve-view=true)
- [sys.dm_exec_sessions](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-sessions-transact-sql?view=azure-sqldw-latest&preserve-view=true)
- [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?view=azure-sqldw-latest&preserve-view=true)
- [sys.dm_pdw_request_steps](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql?view=azure-sqldw-latest&preserve-view=true)
- [sys.dm_pdw_sql_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-sql-requests-transact-sql?view=azure-sqldw-latest&preserve-view=true)
- [sys.dm_pdw_dms_workers](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-dms-workers-transact-sql?view=azure-sqldw-latest&preserve-view=true)
- [DBCC PDW_SHOWEXECUTIONPLAN](/sql/t-sql/database-console-commands/dbcc-pdw-showexecutionplan-transact-sql?view=azure-sqldw-latest&preserve-view=true)
- [sys.dm_pdw_waits](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-waits-transact-sql?view=azure-sqldw-latest&preserve-view=true)

## <a name="next-steps"></a>Pasos siguientes

Consulte también el artículo sobre [Solución de problemas](../sql-data-warehouse/sql-data-warehouse-troubleshoot.md?context=/azure/synapse-analytics/context/context) para conocer los problemas comunes y sus soluciones.

Si necesita información que no se proporciona en este artículo, busque en la [página de preguntas y respuestas de Microsoft para Azure Synapse](/answers/topics/azure-synapse-analytics.html), donde puede plantear preguntas a otros usuarios y al grupo de productos de Azure Synapse Analytics.  

Supervisamos continuamente este foro para garantizar que sus preguntas las responde otro usuario o alguno de nosotros.  Si prefiere formular sus preguntas en Stack Overflow, también tenemos un [foro de Stack Overflow acerca de Azure Synapse Analytics](https://stackoverflow.com/questions/tagged/azure-synapse).

Para las solicitudes de características, use la página de [comentarios de Azure Synapse Analytics](https://feedback.azure.com/forums/307516-sql-data-warehouse).  Al agregar solicitudes o votar por otras nos ayuda a centrarnos en las características más demandadas.