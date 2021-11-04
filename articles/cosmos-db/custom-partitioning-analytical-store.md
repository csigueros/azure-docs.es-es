---
title: Creación de particiones personalizadas en Azure Synapse Link para Azure Cosmos DB (versión preliminar)
description: La creación de particiones personalizadas permite crear particiones de los datos del almacén analítico en campos que se usan normalmente como filtros en consultas analíticas, lo que mejora el rendimiento de las consultas.
author: Rodrigossz
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: rosouz
ms.custom: ignite-fall-2021
ms.openlocfilehash: 72bd4c8a2001cc8e6f314f12862d7ed563ac770b
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131091764"
---
# <a name="custom-partitioning-in-azure-synapse-link-for-azure-cosmos-db-preview"></a>Creación de particiones personalizadas en Azure Synapse Link para Azure Cosmos DB (versión preliminar)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

La creación de particiones personalizadas permite crear particiones de los datos del almacén analítico en campos que se usan normalmente como filtros en consultas analíticas, lo que mejora el rendimiento de las consultas.

En este artículo, aprenderá a crear particiones de los datos en el almacén analítico de Azure Cosmos DB mediante claves críticas para las cargas de trabajo analíticas. También se explica cómo aprovechar el rendimiento mejorado de las consultas con la limpieza de particiones. También aprenderá cómo el almacén con particiones ayuda a mejorar el rendimiento de las consultas cuando las cargas de trabajo tienen un número significativo de actualizaciones o eliminaciones.

> [!IMPORTANT]
> La característica de creación de particiones personalizadas está actualmente en versión preliminar pública. Esta versión preliminar se ofrece sin Acuerdo de Nivel de Servicio y no se recomienda para cargas de trabajo de producción. Es posible que algunas características no sean compatibles o que tengan sus funcionalidades limitadas. Para más información, consulte [Términos de uso complementarios de las Versiones Preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

> [!NOTE]
> Las cuentas de Azure Cosmos DB deben tener Azure Synapse Link habilitado para aprovechar las ventajas de la creación de particiones personalizadas. Actualmente, la creación de particiones personalizadas solo se admite para Azure Synapse Spark 2.0.

## <a name="how-does-it-work"></a>¿Cómo funciona?

Con la creación de particiones personalizadas, puede elegir un único campo o una combinación de campos del conjunto de datos como clave de partición del almacén analítico.

La creación de particiones del almacén analítico es independiente de la del almacén transaccional. De manera predeterminada, el almacén analítico no tiene particiones. Si quiere consultar el almacén analítico con frecuencia en función de campos como Fecha, Hora, Categoría, etc., se recomienda crear un almacén con particiones basado en estas claves.

Para desencadenar la creación de particiones, puede ejecutar periódicamente un trabajo de creación de particiones desde un cuaderno de Spark de Azure Synapse mediante Azure Synapse Link. Puede programarlo para que se ejecute como un trabajo en segundo plano cuando prefiera.

> [!NOTE]
> El almacén con particiones apunta a la cuenta de almacenamiento principal de ADLS Gen2 que está vinculada con el área de trabajo de Azure Synapse.

:::image type="content" source="./media/custom-partitioning-analytical-store/partitioned-store-architecture.png" alt-text="Arquitectura del almacén con particiones en Azure Synapse Link para Azure Cosmos DB" lightbox="./media/custom-partitioning-analytical-store/partitioned-store-architecture.png" border="false":::

El almacén con particiones contiene datos analíticos de Azure Cosmos DB hasta la última marca de tiempo en la que se ha ejecutado el trabajo de creación de particiones. Al consultar los datos analíticos mediante los filtros de clave de partición de Spark de Synapse, Synapse Link combinará automáticamente los datos más recientes del almacén analítico con los del almacén con particiones. De este modo, proporciona los resultados más recientes. Aunque combina los datos antes de realizar consultas, la diferencia no se vuelve a escribir en el almacén con particiones. A medida que se amplía la diferencia entre los datos del almacén analítico y del almacén con particiones, los tiempos de consulta de los datos con particiones pueden variar. El desencadenamiento del trabajo de creación de particiones con más frecuencia reducirá esta diferencia. Cada vez que ejecute el trabajo de creación de particiones, solo se procesarán los cambios incrementales en el almacén analítico, no en todo el conjunto de datos.

## <a name="when-to-use"></a>Cuándo se deben usar

El uso del almacén con particiones es opcional al consultar datos analíticos en Azure Cosmos DB. Puede consultar directamente los mismos datos mediante Synapse Link con el almacén analítico existente. Es posible que quiera activar el almacén con particiones si tiene los requisitos siguientes:

* Quiere consultar con frecuencia los datos analíticos filtrados por algunos campos.

* Tiene un gran volumen de operaciones de eliminación o actualizaciones, o bien los datos se ingieren lentamente. En estos casos el almacén con particiones proporciona un mejor rendimiento de las consultas, independientemente de si la consulta se realiza con claves de partición o no.

Excepto en el caso de las cargas de trabajo anteriores, si va a consultar datos activos mediante filtros de consulta que son diferentes de las claves de partición, se recomienda consultarlos directamente desde el almacén analítico, en especial si los trabajos de creación de particiones no se ejecutan con frecuencia.

## <a name="benefits"></a>Ventajas

### <a name="reduced-data-scanning-from-partition-pruning"></a>Se reduce el examen de datos de la limpieza de particiones

Como los datos correspondientes a cada clave de partición única se colocan en el almacén con particiones, cuando se usa la clave de partición como filtro de consulta, las ejecuciones de consultas pueden eliminar los datos subyacentes y examinar solo los datos necesarios. Al examinar datos limitados, la limpieza de particiones mejora el rendimiento de las consultas analíticas.

### <a name="flexibility-to-partition-your-analytical-data"></a>Flexibilidad para crear particiones de los datos analíticos

Puede tener varias estrategias de creación de particiones para un contenedor de almacén analítico determinado en el que se pueden crear particiones de los datos mediante claves de partición independientes. Por ejemplo, se pueden crear particiones del contenedor "store_sales" mediante "sold_date" como clave y también mediante "item". En este caso, debe tener dos trabajos de creación de particiones independientes, que básicamente dividirán los datos en dos almacenes con particiones independientes. Esta estrategia de creación de particiones es beneficiosa si en algunas de las consultas se usa "sold_date" como filtro de consulta y en otras se usa "item".

Los datos de diferentes claves de partición formarán parte del mismo almacén con particiones y puede realizar la consulta en función de la clave de partición para seleccionar los datos correspondientes.

### <a name="query-performance-improvements"></a>Mejoras en el rendimiento de las consultas

Además de las mejoras de consulta de la limpieza de particiones, la creación de particiones personalizadas también da como resultado un rendimiento mejorado de las consultas para las cargas de trabajo siguientes:

* **Cargas de trabajo con numerosas operaciones de actualización o eliminación**: en lugar de realizar el seguimiento de varias versiones de los registros en el almacén analítico y cargarlas durante cada ejecución de la consulta, el almacén con particiones solo contiene la versión más reciente de los datos. Esto mejora significativamente el rendimiento de las consultas cuando las cargas de trabajo realizan numerosas operaciones de actualización o eliminación.

* **Cargas de trabajo de ingesta de datos lenta**: la creación de particiones compacta los datos analíticos y, por tanto, si la carga de trabajo tiene una ingesta de datos lenta, esta compactación podría dar lugar a un mejor rendimiento de las consultas.

### <a name="transactional-guarantee"></a>Garantía transaccional

Es importante tener en cuenta que la creación de particiones personalizadas garantiza una garantía transaccional completa. La ruta de la consulta no está bloqueada mientras la ejecución de particiones está en curso. Cada ejecución de consulta lee los datos particionados de la última creación de particiones correcta. Lee los datos más recientes del almacén analítico, lo que garantiza que las consultas siempre devuelven los datos más recientes disponibles al usar el almacén con particiones.

## <a name="security"></a>Seguridad

Si ha configurado [puntos de conexión privados administrados](analytical-store-private-endpoints.md) para el almacén analítico, a fin de garantizar el aislamiento de red para el almacén con particiones, se recomienda agregar también puntos de conexión privados administrados para el almacén con particiones. El almacén con particiones es la cuenta de almacenamiento principal asociada al área de trabajo de Synapse.

Del mismo modo, si ha configurado [claves administradas por el cliente en el almacén analítico](how-to-setup-cmk.md#is-it-possible-to-use-customer-managed-keys-in-conjunction-with-the-azure-cosmos-db-analytical-store), tendrá que habilitarlo directamente en la cuenta de almacenamiento principal del área de trabajo de Synapse, que también es el almacén con particiones.

## <a name="limitations"></a>Limitaciones

* La creación de particiones personalizadas solo está disponible para Spark de Azure Synapse. Actualmente no se admite la creación de particiones personalizadas para grupos de SQL sin servidor.

* Actualmente, el almacén con particiones solo puede apuntar a la cuenta de almacenamiento principal asociada al área de trabajo de Synapse. En este momento no se admite la selección de cuentas de almacenamiento personalizadas.

* Aunque la API de MongoDB admite el almacén analítico y Synapse Link, actualmente no admite la creación de particiones personalizadas.

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

Sí, como la definición de clave de partición forma parte de la ruta del almacén con particiones, las distintas claves de partición tendrán distintas rutas que se bifurcarán desde la misma ruta base.

El formato de ruta base se puede especificar como: /mnt/nombre_del_almacén_con_particiones/\<Cosmos_DB_account_name\>/\<Cosmos_DB_database_rid\>/\<Cosmos_DB_container_rid\>/partición=clave_de_partición/

Por ejemplo: /mnt/CosmosDBPartitionedStore/store_sales/…/partition=sold_date/... /mnt/CosmosDBPartitionedStore/store_sales/…/partition=Date/...

## <a name="next-steps"></a>Pasos siguientes

Para obtener más información, consulte la siguiente documentación:

* [Configure la creación de particiones personalizadas](configure-custom-partitioning.md) para crear particiones de los datos del almacén analítico.
* [Azure Synapse Link para Azure Cosmos DB](synapse-link.md)
* [Introducción al almacén analítico de Azure Cosmos DB](analytical-store-introduction.md)
* [Introducción a Azure Synapse Link para Azure Cosmos DB](configure-synapse-link.md)
* [Preguntas frecuentes sobre Azure Synapse Link para Azure Cosmos DB](synapse-link-frequently-asked-questions.yml)
* [Casos de uso de Azure Synapse Link para Azure Cosmos DB](synapse-link-use-cases.md)
