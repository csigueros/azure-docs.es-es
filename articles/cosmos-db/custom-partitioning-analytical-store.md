---
title: Creación de particiones personalizadas en Azure Synapse Link para Azure Cosmos DB (versión preliminar)
description: La creación de particiones personalizadas permite crear particiones de los datos del almacén analítico en campos que se usan normalmente como filtros en consultas analíticas, lo que mejora el rendimiento de las consultas.
author: Rodrigossz
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: rosouz
ms.custom: ignite-fall-2021
ms.openlocfilehash: 7534149aacc76bebdcf591471d08b120b885e625
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2021
ms.locfileid: "131426133"
---
# <a name="custom-partitioning-in-azure-synapse-link-for-azure-cosmos-db-preview"></a>Creación de particiones personalizadas en Azure Synapse Link para Azure Cosmos DB (versión preliminar)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

La creación de particiones personalizadas permite crear particiones de los datos del almacén analítico en campos que se usan normalmente como filtros en consultas analíticas, lo que mejora el rendimiento de las consultas.

En este artículo, aprenderá a crear particiones de los datos en el almacén analítico de Azure Cosmos DB mediante claves críticas para las cargas de trabajo analíticas. También se explica cómo aprovechar el rendimiento mejorado de las consultas con la limpieza de particiones. También aprenderá cómo el almacén con particiones ayuda a mejorar el rendimiento de las consultas cuando las cargas de trabajo tienen un número significativo de actualizaciones o eliminaciones.

> [!IMPORTANT]
> La característica de creación de particiones personalizadas está actualmente en versión preliminar pública. Esta versión preliminar se ofrece sin Acuerdo de Nivel de Servicio y no se recomienda para cargas de trabajo de producción. Es posible que algunas características no sean compatibles o que tengan sus funcionalidades limitadas. Para más información, consulte [Términos de uso complementarios de las Versiones Preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

> [!NOTE]
> Las cuentas de Azure Cosmos DB deben tener [Azure Synapse Link](synapse-link.md) habilitado para aprovechar las ventajas de la creación de particiones personalizadas. Actualmente, la creación de particiones personalizadas solo se admite para Azure Synapse Spark 2.0.

## <a name="how-does-it-work"></a>¿Cómo funciona?

La creación de particiones del almacén analítico es independiente de la del almacén transaccional. De manera predeterminada, el almacén analítico no tiene particiones. Si quiere consultar el almacén analítico con frecuencia en función de campos como Fecha, Hora, Categoría, etc., debe sacar provecho de la creación de particiones personalizadas para crear un almacén con particiones independiente basado en estas claves. Puede elegir un único campo o una combinación de campos del conjunto de datos como clave de partición del almacén analítico.

Puede desencadenar la creación de particiones desde un cuaderno de Spark de Azure Synapse mediante Azure Synapse Link. Puede programarlo para que se ejecute como un trabajo en segundo plano, una o dos veces al día, pero se puede ejecutar con más frecuencia, si es necesario. 

> [!NOTE]
> El almacén con particiones apunta a la cuenta de almacenamiento principal de ADLS Gen2 que está vinculada con el área de trabajo de Azure Synapse.

:::image type="content" source="./media/custom-partitioning-analytical-store/partitioned-store-architecture.png" alt-text="Arquitectura del almacén con particiones en Azure Synapse Link para Azure Cosmos DB" lightbox="./media/custom-partitioning-analytical-store/partitioned-store-architecture.png" border="false":::

El almacén con particiones contiene datos analíticos de Azure Cosmos DB hasta la última marca de tiempo en la que se ha ejecutado el trabajo de creación de particiones. Al consultar los datos analíticos mediante los filtros de clave de partición de Spark de Synapse, Synapse Link combinará automáticamente los datos del almacén con particiones con los datos más recientes del almacén analítico. De este modo, proporciona los resultados más recientes para las consultas. Aunque combina los datos antes de realizar consultas, la diferencia no se vuelve a escribir en el almacén con particiones. A medida que se amplía la diferencia entre los datos del almacén analítico y del almacén con particiones, los tiempos de consulta de los datos con particiones pueden variar. El desencadenamiento del trabajo de creación de particiones con más frecuencia reducirá esta diferencia. Cada vez que ejecute el trabajo de creación de particiones, solo se procesarán los cambios incrementales en el almacén analítico, no en todo el conjunto de datos.

## <a name="when-to-use"></a>Cuándo se deben usar

El uso del almacén con particiones es opcional al consultar datos analíticos en Azure Cosmos DB. Puede consultar directamente los mismos datos mediante Synapse Link con el almacén analítico existente. Es posible que quiera activar el almacén con particiones si tiene los requisitos siguientes:
* Filtros de consulta analíticos comunes que podrían usarse como columnas de partición
* Columnas de partición de cardinalidad baja
* La columna de partición distribuye los datos equitativamente entre las particiones
* Volumen elevado de operaciones de actualización o eliminación
* Ingesta de datos lenta 

A excepción de las cargas de trabajo que cumplen los requisitos anteriores, si va a consultar datos activos mediante filtros de consulta que son diferentes de las claves de partición, se recomienda realizar consultas directamente desde el almacén analítico. Esto es especialmente cierto si los trabajos de creación de particiones no están programados para ejecutarse con frecuencia.

## <a name="benefits"></a>Ventajas

### <a name="reduced-data-scanning-from-partition-pruning"></a>Se reduce el examen de datos de la limpieza de particiones

Como los datos correspondientes a cada clave de partición única se colocan en el almacén con particiones, cuando se usa la clave de partición como filtro de consulta, las ejecuciones de consultas pueden eliminar los datos subyacentes y examinar solo los datos necesarios. Al examinar datos limitados, la limpieza de particiones mejora el rendimiento de las consultas analíticas.

### <a name="flexibility-to-partition-your-analytical-data"></a>Flexibilidad para crear particiones de los datos analíticos

Puede tener varias estrategias de creación de particiones para un contenedor de almacén analítico determinado. Puede usar claves de partición compuestas o independientes en función de los requisitos de consulta. Vea las estrategias de creación de particiones para obtener instrucciones al respecto. 

### <a name="query-performance-improvements"></a>Mejoras en el rendimiento de las consultas

Además de las mejoras de consulta de la limpieza de particiones, la creación de particiones personalizadas también da como resultado un rendimiento mejorado de las consultas para las cargas de trabajo siguientes:

* **Cargas de trabajo con numerosas operaciones de actualización o eliminación**: en lugar de realizar el seguimiento de varias versiones de los registros en el almacén analítico y cargarlas durante cada ejecución de la consulta, el almacén con particiones solo contiene la versión más reciente de los datos. Esto mejora significativamente el rendimiento de las consultas cuando las cargas de trabajo realizan numerosas operaciones de actualización o eliminación.

* **Cargas de trabajo de ingesta de datos lenta**: la creación de particiones compacta los datos analíticos y, por tanto, si la carga de trabajo tiene una ingesta de datos lenta, esta compactación podría dar lugar a un mejor rendimiento de las consultas.

### <a name="transactional-guarantee"></a>Garantía transaccional

Es importante tener en cuenta que la creación de particiones personalizadas garantiza una garantía transaccional completa. La ruta de la consulta no está bloqueada mientras la ejecución de particiones está en curso. Cada ejecución de consulta lee los datos particionados de la última creación de particiones correcta. Lee los datos más recientes del almacén analítico, lo que garantiza que las consultas siempre devuelven los datos más recientes disponibles al usar el almacén con particiones.

## <a name="security"></a>Seguridad

Si ha configurado [puntos de conexión privados administrados](analytical-store-private-endpoints.md) para el almacén analítico, a fin de garantizar el aislamiento de red para el almacén con particiones, se recomienda agregar también puntos de conexión privados administrados para el almacén con particiones. El almacén con particiones es la cuenta de almacenamiento principal asociada al área de trabajo de Synapse.

Del mismo modo, si ha configurado [claves administradas por el cliente en el almacén analítico](how-to-setup-cmk.md#is-it-possible-to-use-customer-managed-keys-in-conjunction-with-the-azure-cosmos-db-analytical-store), tendrá que habilitarlo directamente en la cuenta de almacenamiento principal del área de trabajo de Synapse, que también es el almacén con particiones.

## <a name="partitioning-strategies"></a>Estrategias de creación de particiones
Puede usar una o varias claves de partición para los datos analíticos. Si usa varias claves de partición, a continuación se muestran algunas recomendaciones sobre cómo crear particiones de los datos: 
   - **Uso de claves compuestas:**

     Imagine que quiere realizar consultas con frecuencia basadas en Key1 y Key2. 
      
     Por ejemplo, "Consultar todos los registros donde ReadDate = "2021-10-08" y Location = "Sydney"". 
       
     En este caso, el uso de claves compuestas será más eficaz, para buscar todos los registros que coincidan con ReadDate y los que coinciden con Location dentro de ese valor ReadDate. 
       
     Opciones de configuración de ejemplo:      
     ```python
     .option("spark.cosmos.asns.partition.keys", "ReadDate String, Location String") \
     .option("spark.cosmos.asns.basePath", "/mnt/CosmosDBPartitionedStore/") \
     ```
      
     Ahora, en el almacén con particiones anterior, si solo quiere realizar consultas basadas en el filtro "Location":      
     * Es posible que quiera consultar directamente el almacén analítico. El almacén con particiones examinará primero todos los registros por ReadDate y, después, por Location. 
     Por tanto, en función de la carga de trabajo y la cardinalidad de los datos analíticos, podría obtener mejores resultados si consulta directamente el almacén analítico. 
     * También podría ejecutar otro trabajo de partición para crear particiones basadas en "Location" en el mismo almacén con particiones.
                           
  *  **Uso de varias claves por separado:**
     
     Imagine que quiere realizar consultas frecuentes basadas en "ReadDate" y otras veces en "Location". 
     
     Por ejemplo, 
     - Consulta de todos los registros donde ReadDate = "2021-10-08"
     - Consulta de todos los registros donde Location = "Sydney"
     
     Ejecute dos trabajos de partición con claves de partición, como se define a continuación para este escenario:      
     
     Trabajo 1:
     ```python
     .option("spark.cosmos.asns.partition.keys", "ReadDate String") \
     .option("spark.cosmos.asns.basePath", "/mnt/CosmosDBPartitionedStore/") \
     ```                  
     Trabajo 2: 
     ```python
     .option("spark.cosmos.asns.partition.keys", "Location String") \
     .option("spark.cosmos.asns.basePath", "/mnt/CosmosDBPartitionedStore/") \
     ```        
     Tenga en cuenta que ahora no es eficaz realizar consultas con frecuencia basadas en los filtros "ReadDate" y "Location" juntos, en las particiones anteriores. En ese caso, las claves compuestas ofrecerán un mejor rendimiento de las consultas. 
      
## <a name="limitations"></a>Limitaciones

* La creación de particiones personalizadas solo está disponible para Spark de Azure Synapse. Actualmente no se admite la creación de particiones personalizadas para grupos de SQL sin servidor.

* Actualmente, el almacén con particiones solo puede apuntar a la cuenta de almacenamiento principal asociada al área de trabajo de Synapse. En este momento no se admite la selección de cuentas de almacenamiento personalizadas.

* La creación de particiones personalizadas solo está disponible para SQL API en Cosmos DB. La API para Mongo DB, Gremlin y Cassandra no se admite en este momento. 

## <a name="pricing"></a>Precios

Además de los [precios de Azure Synapse Link](synapse-link.md#pricing), incurrirá en los siguientes cargos al usar la creación de particiones personalizadas:

* Se le [facturará](https://azure.microsoft.com/pricing/details/synapse-analytics/#pricing) por el uso de grupos de Apache Spark de Synapse al ejecutar trabajos de creación de particiones en el almacén analítico.

* Los datos con particiones se almacenan en la cuenta principal de Azure Data Lake Storage Gen2 asociada al área de Azure Synapse Analytics principal. Incurrirá en los costos asociados con el uso del almacenamiento de ADLS Gen2 y las transacciones. Estos costos se determinan por el almacenamiento necesario para los datos analíticos con particiones y los datos procesados para las consultas analíticas en Synapse, respectivamente. Para más información sobre los precios, visite la [página de precios de Azure Data Lake Storage](https://azure.microsoft.com/pricing/details/storage/data-lake/).

## <a name="frequently-asked-questions"></a>Preguntas más frecuentes

### <a name="how-often-should-i-run-the-custom-partitioning-job"></a>¿Con qué frecuencia se debe ejecutar el trabajo de creación de particiones personalizadas?

Hay varios factores, como el volumen de datos incremental, los requisitos de latencia de consulta, etc., que determinan la frecuencia con la que se puede ejecutar el trabajo de creación de particiones personalizadas. Se podría ejecutar una vez al día o una vez cada pocas horas. Es posible que quiera programar el trabajo de creación de particiones con más frecuencia si el volumen de datos entrantes es alto y la latencia de consulta que se espera es baja. También debe acumular primero datos incrementales en el almacén analítico para que la limpieza de particiones sea eficaz.

### <a name="do-the-query-results-include-latest-data-while-the-partitioning-job-execution-is-in-progress"></a>¿Los resultados de la consulta incluyen los datos más recientes mientras la ejecución del trabajo de creación de particiones está en curso?

Sí, la creación de particiones personalizadas proporciona una garantía transaccional completa. Por tanto, los resultados de la consulta en cualquier momento combinan los datos con particiones existentes con los datos finales que devuelven el conjunto de datos del almacén analítico más reciente.

### <a name="can-the-custom-partitioning-make-use-of-linked-service-authentication-on-azure-synapse-analytics"></a>¿Puede la creación de particiones personalizadas usar la autenticación de servicio vinculado en Azure Synapse Analytics?

Sí, la autenticación de servicio vinculado se puede usar para la creación de particiones del almacén analítico.

### <a name="can-i-change-the-partition-key-for-a-given-container-at-a-later-point-in-time"></a>¿Se puede cambiar la clave de partición de un contenedor determinado en un momento posterior?

Sí, se puede cambiar la clave de partición del contenedor dado y la nueva definición de clave de partición creará un almacén con particiones.

> [!NOTE]
> La definición de clave de partición forma parte de la ruta del almacén con particiones.

### <a name="can-different-partition-keys-point-to-the-same-basepath"></a>¿Pueden claves de partición diferentes apuntar a la misma ruta base?

Sí, puede especificar varias claves de partición en el mismo almacén con particiones como se indica a continuación: 

```python
.option("spark.cosmos.asns.partition.keys", "ReadDate String, Location String") \
.option("spark.cosmos.asns.basePath", "/mnt/CosmosDBPartitionedStore/") \
```

## <a name="next-steps"></a>Pasos siguientes

Para obtener más información, consulte la siguiente documentación:

* [Configure la creación de particiones personalizadas](configure-custom-partitioning.md) para crear particiones de los datos del almacén analítico.
* [Azure Synapse Link para Azure Cosmos DB](synapse-link.md)
* [Introducción al almacén analítico de Azure Cosmos DB](analytical-store-introduction.md)
* [Introducción a Azure Synapse Link para Azure Cosmos DB](configure-synapse-link.md)
* [Preguntas frecuentes sobre Azure Synapse Link para Azure Cosmos DB](synapse-link-frequently-asked-questions.yml)
* [Casos de uso de Azure Synapse Link para Azure Cosmos DB](synapse-link-use-cases.md)
