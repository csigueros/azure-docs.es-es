---
title: Migración de DTU a núcleo virtual
description: Migre una base de datos de Azure SQL Database del modelo de DTU al modelo de núcleo virtual. La migración a un núcleo virtual es similar al proceso de actualización o degradación entre los niveles estándar y premium.
services: sql-database
ms.service: sql-database
ms.subservice: service-overview
ms.topic: conceptual
ms.custom: sqldbrb=1
author: dimitri-furman
ms.author: dfurman
ms.reviewer: mathoma, moslake
ms.date: 07/26/2021
ms.openlocfilehash: aad6da736bde6a50d3bf0b0164830333823f444b
ms.sourcegitcommit: e6de87b42dc320a3a2939bf1249020e5508cba94
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/27/2021
ms.locfileid: "114709750"
---
# <a name="migrate-azure-sql-database-from-the-dtu-based-model-to-the-vcore-based-model"></a>Migración de Azure SQL Database del modelo basado en DTU al modelo basado en núcleo virtual.
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

En este artículo se describe cómo migrar la base de datos de Azure SQL Database del [modelo de compra basado en DTU](service-tiers-dtu.md) al [modelo de compra basado en núcleo virtual](service-tiers-vcore.md). 

## <a name="migrate-a-database"></a>Migración de bases de datos

La migración de una base de datos del modelo de compra basado en DTU al modelo de compra basado en núcleo virtual es similar al escalado entre los objetivos de servicio de los niveles de servicio básico, estándar y prémium, con una [duración](single-database-scale.md#latency) similar y un [tiempo de inactividad mínimo](scale-resources.md) al final del proceso de migración. Una base de datos migrada al modelo de compra basado en núcleo virtual se puede volver a migrar al modelo de compra basado en DTU en cualquier momento de la misma manera, con la excepción de las bases de datos que se han migrado al nivel de servicio [Hiperescala](service-tier-hyperscale.md). 

## <a name="choose-the-vcore-service-tier-and-service-objective"></a>Elecció del nivel de servicio y el objetivo de servicio de núcleo virtual

Para la mayoría de los escenarios de migración de DTU a núcleo virtual, las bases de datos y los grupos elásticos de los niveles de servicio básico y estándar se asignarán al nivel de servicio [De uso general](service-tier-general-purpose.md). Las bases de datos y los grupos elásticos del nivel de servicio prémium se asignarán al nivel de servicio [Crítico para la empresa](service-tier-business-critical.md). Según los requisitos y el escenario de la aplicación, a menudo se puede usar el nivel de servicio [Hiperescala](service-tier-hyperscale.md) como destino de migración para bases de datos únicas en todos los niveles de servicio de DTU.

Para elegir el objetivo del servicio, o el tamaño de proceso, para la base de datos migrada del modelo de núcleo virtual, puede usar una regla general sencilla pero aproximada: cada 100 DTU en los niveles básico o estándar requieren *al menos* 1 núcleo virtual y cada 125 DTU del nivel prémium requieren *al menos* 1 núcleo virtual. 

> [!TIP]
> Esta regla es aproximada porque no tiene en cuenta la generación de hardware utilizada para la base de datos de DTU o el grupo elástico. 

En el modelo de DTU, el sistema podría seleccionar cualquier [generación de hardware](purchasing-models.md#hardware-generations-in-the-dtu-based-purchasing-model) disponible para la base de datos o el grupo elástico. Además, en el modelo de DTU, solo tiene control indirecto sobre el número de núcleos virtuales (CPU lógicas) si elige valores más altos o más bajos de DTU o eDTU. 

En el modelo de núcleo virtual, los clientes deben tomar una decisión explícita de la generación de hardware y el número de núcleos virtuales (CPU lógicas). Mientras que el modelo de DTU no ofrece estas opciones, la generación de hardware y el número de CPU lógicas que se usan para cada base de datos y cada grupo elástico se exponen a través de vistas de administración dinámicas. Esto permite determinar el objetivo de servicio de núcleo virtual coincidente con más precisión. 

El siguiente enfoque usa esta información para determinar un objetivo de servicio de núcleo virtual con una asignación similar de recursos, para obtener un nivel de rendimiento similar después de la migración al modelo de núcleo virtual.

### <a name="dtu-to-vcore-mapping"></a>Asignación de DTU a núcleo virtual

La consulta de T-SQL siguiente, si se ejecuta en el contexto de una base de datos de DTU que se va a migrar, devuelve un número coincidente (posiblemente fraccionario) de núcleos virtuales en cada generación de hardware del modelo de núcleo virtual. Al redondear este número al número más cercano de núcleos virtuales disponibles para las [bases de datos](resource-limits-vcore-single-databases.md) y los [grupos elásticos](resource-limits-vcore-elastic-pools.md) en cada generación de hardware del modelo de núcleo virtual, los clientes pueden elegir el objetivo de servicio de núcleo virtual que sea la coincidencia más exacta para su grupo elástico o base de datos de DTU. 

Los escenarios de migración de ejemplo que usan este enfoque se describen en la sección [Ejemplos](#dtu-to-vcore-migration-examples).

Ejecute esta consulta en el contexto de la base de datos que se va a migrar, en lugar de en la base de datos `master`. Al migrar un grupo elástico, ejecute la consulta en el contexto de cualquier base de datos del grupo.

```SQL
WITH dtu_vcore_map AS
(
SELECT rg.slo_name,
       DATABASEPROPERTYEX(DB_NAME(), 'Edition') AS dtu_service_tier,
       CASE WHEN rg.slo_name LIKE '%SQLG4%' THEN 'Gen4'
            WHEN rg.slo_name LIKE '%SQLGZ%' THEN 'Gen4'
            WHEN rg.slo_name LIKE '%SQLG5%' THEN 'Gen5'
            WHEN rg.slo_name LIKE '%SQLG6%' THEN 'Gen5'
            WHEN rg.slo_name LIKE '%SQLG7%' THEN 'Gen5'
       END AS dtu_hardware_gen,
       s.scheduler_count * CAST(rg.instance_cap_cpu/100. AS decimal(3,2)) AS dtu_logical_cpus,
       CAST((jo.process_memory_limit_mb / s.scheduler_count) / 1024. AS decimal(4,2)) AS dtu_memory_per_core_gb
FROM sys.dm_user_db_resource_governance AS rg
CROSS JOIN (SELECT COUNT(1) AS scheduler_count FROM sys.dm_os_schedulers WHERE status = 'VISIBLE ONLINE') AS s
CROSS JOIN sys.dm_os_job_object AS jo
WHERE rg.dtu_limit > 0
      AND
      DB_NAME() <> 'master'
      AND
      rg.database_id = DB_ID()
)
SELECT dtu_logical_cpus,
       dtu_hardware_gen,
       dtu_memory_per_core_gb,
       dtu_service_tier,
       CASE WHEN dtu_service_tier = 'Basic' THEN 'General Purpose'
            WHEN dtu_service_tier = 'Standard' THEN 'General Purpose or Hyperscale'
            WHEN dtu_service_tier = 'Premium' THEN 'Business Critical or Hyperscale'
       END AS vcore_service_tier,
       CASE WHEN dtu_hardware_gen = 'Gen4' THEN dtu_logical_cpus
            WHEN dtu_hardware_gen = 'Gen5' THEN dtu_logical_cpus * 0.7
       END AS Gen4_vcores,
       7 AS Gen4_memory_per_core_gb,
       CASE WHEN dtu_hardware_gen = 'Gen4' THEN dtu_logical_cpus * 1.7
            WHEN dtu_hardware_gen = 'Gen5' THEN dtu_logical_cpus
       END AS Gen5_vcores,
       5.05 AS Gen5_memory_per_core_gb,
       CASE WHEN dtu_hardware_gen = 'Gen4' THEN dtu_logical_cpus
            WHEN dtu_hardware_gen = 'Gen5' THEN dtu_logical_cpus * 0.8
       END AS Fsv2_vcores,
       1.89 AS Fsv2_memory_per_core_gb,
       CASE WHEN dtu_hardware_gen = 'Gen4' THEN dtu_logical_cpus * 1.4
            WHEN dtu_hardware_gen = 'Gen5' THEN dtu_logical_cpus * 0.9
       END AS M_vcores,
       29.4 AS M_memory_per_core_gb
FROM dtu_vcore_map;
```

### <a name="additional-factors"></a>Factores adicionales

Además del número de núcleos virtuales (CPU lógicas) y la generación de hardware, varios factores diferentes pueden influir en la elección del objetivo de servicio de núcleo virtual:

- La consulta de T-SQL de asignación coincide con los objetivos de servicio de DTU y núcleo virtual en cuanto a su capacidad de CPU, por lo que los resultados serán más precisos para las cargas de trabajo enlazadas a la CPU.
- Para la misma generación de hardware y el mismo número de núcleos virtuales, los límites de recursos de rendimiento del registro de transacciones e IOPS para las bases de datos de núcleo virtual suelen ser mayores que para las bases de datos de DTU. En el caso de las cargas de trabajo enlazadas a E/S, es posible reducir el número de núcleos virtuales en el modelo de núcleo virtual para lograr el mismo nivel de rendimiento. Los límites de recursos reales para las bases de datos de DTU y núcleo virtual se exponen en la vista [sys.dm_user_db_resource_governance](/sql/relational-databases/system-dynamic-management-views/sys-dm-user-db-resource-governor-azure-sql-database). La comparación de estos valores entre la base de datos de DTU o el grupo que se van a migrar y una base de datos de núcleo virtual o un grupo con un objetivo de servicio de coincidencia aproximada le ayudará a seleccionar el objetivo de servicio de núcleo virtual con mayor precisión.
- La consulta de asignación también devuelve la cantidad de memoria por núcleo para el grupo elástico o la base de datos de DTU que se va a migrar, y para cada generación de hardware del modelo de núcleo virtual. Garantizar una memoria total similar o superior después de la migración a núcleo virtual es importante para las cargas de trabajo que requieren una memoria caché de datos de gran tamaño para lograr un rendimiento suficiente o para cargas de trabajo que requieren concesiones de memoria grandes para el procesamiento de consultas. En el caso de estas cargas de trabajo, en función del rendimiento real, puede ser necesario aumentar el número de núcleos virtuales para obtener suficiente memoria total.
- La [utilización de recursos históricos](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database) de la base de datos de DTU se debe tener en cuenta al elegir el objetivo de servicio de núcleo virtual. Es posible que las bases de datos de DTU con recursos de CPU infrautilizados de forma constante necesiten menos núcleos virtuales que el número devuelto por la consulta de asignación. Por el contrario, las bases de datos de DTU en las que el uso de CPU elevado constante provoca un rendimiento inadecuado de la carga de trabajo pueden requerir más núcleos virtuales de los que devuelve la consulta.
- Si migra bases de datos con patrones de uso intermitentes o imprevisibles, tenga en cuenta el uso del nivel de proceso [Sin servidor](serverless-tier-overview.md). Tenga en cuenta que el número máximo de trabajos simultáneos (solicitudes) en la opción sin servidor es el 75 % del límite en el proceso aprovisionado para el mismo número de núcleos virtuales máximo configurado. Además, la memoria máxima disponible en la opción sin servidor expresada en GB es 3 veces el número máximo de núcleos virtuales configurados, lo que es menor que la memoria por núcleo para el proceso aprovisionado. Por ejemplo, en Gen5, la memoria máxima será de 120 GB cuando se configura un máximo de 40 núcleos virtuales como máximo en la opción sin servidor, frente a 204 GB para un proceso aprovisionado de 40 núcleos virtuales.
- En el modelo de núcleo virtual, el tamaño máximo admitido de la base de datos puede variar en función de la generación de hardware. En el caso de las bases de datos de gran tamaño, compruebe los tamaños máximos admitidos en el modelo de núcleo virtual para [bases de datos únicas](resource-limits-vcore-single-databases.md) y [grupos elásticos](resource-limits-vcore-elastic-pools.md).
- En el caso de los grupos elásticos, los modelos de [DTU](resource-limits-dtu-elastic-pools.md) y [núcleo virtual](resource-limits-vcore-elastic-pools.md) presentan diferencias en el número máximo de bases de datos admitidas por grupo. Esto se debe tener en cuenta al migrar grupos elásticos con muchas bases de datos.
- Es posible que algunas generaciones de hardware no estén disponibles en todas las regiones. Compruebe la disponibilidad en [Generaciones de hardware para SQL Database](./service-tiers-sql-database-vcore.md#hardware-generations).

> [!IMPORTANT]
> Las directrices de ajuste de tamaño de DTU a núcleo virtual se proporcionan para ayudarle en la estimación inicial del objetivo de servicio de base de datos de destino.
>
> La configuración óptima de la base de datos de destino depende de la carga de trabajo. Por lo tanto, para lograr una relación óptima entre el precio y el rendimiento después de la migración, aproveche la flexibilidad del modelo de núcleo virtual para ajustar el número de núcleos virtuales, la generación de hardware y los niveles de servicio y proceso. También puede que tenga que ajustar los parámetros de configuración de la base de datos, como el [grado máximo de paralelismo](configure-max-degree-of-parallelism.md), o cambiar el [nivel de compatibilidad](/sql/t-sql/statements/alter-database-transact-sql-compatibility-level) de la base de datos para habilitar mejoras recientes en el motor de base de datos.
> 

### <a name="dtu-to-vcore-migration-examples"></a>Ejemplos de migración de DTU a núcleo virtual

> [!NOTE]
> Los valores de los ejemplos siguientes son para fines ilustrativos solamente. Los valores reales devueltos en los escenarios descritos pueden ser diferentes.
> 

**Migración de una base de datos S9 estándar**

La consulta de asignación devuelve el resultado siguiente (algunas columnas no se muestran por motivos de brevedad):

|dtu_logical_cpus|dtu_hardware_gen|dtu_memory_per_core_gb|Gen4_vcores|Gen4_memory_per_core_gb|Gen5_vcores|Gen5_memory_per_core_gb|
|----------------|----------------|----------------------|-----------|-----------------------|-----------|-----------------------|
|24,00|Gen5|5,40|16,800|7|24,000|5,05|

Vemos que la base de datos de DTU tiene 24 CPU lógicas (núcleos virtuales), con 5,4 GB de memoria por núcleo virtual y que usa hardware Gen5. La coincidencia directa correspondiente es una base de datos de uso general y 24 núcleos virtuales en hardware Gen5, es decir, el objetivo de servicio de núcleo virtual **GP_Gen5_24**.

**Migración de una base de datos S0 estándar**

La consulta de asignación devuelve el resultado siguiente (algunas columnas no se muestran por motivos de brevedad):

|dtu_logical_cpus|dtu_hardware_gen|dtu_memory_per_core_gb|Gen4_vcores|Gen4_memory_per_core_gb|Gen5_vcores|Gen5_memory_per_core_gb|
|----------------|----------------|----------------------|-----------|-----------------------|-----------|-----------------------|
|0,25|Gen4|0,42|0,250|7|0,425|5,05|

Vemos que la base de datos de DTU tiene el equivalente de 0,25 CPU lógicas (núcleos virtuales), con 0,42 GB de memoria por núcleo virtual y que usa hardware Gen4. Los objetivos de servicio de núcleo virtual inferiores de las generaciones de hardware Gen4 y Gen5, **GP_Gen4_1** y **GP_Gen5_2**, proporcionan más recursos de proceso que la base de datos S0 estándar, por lo que no es posible una coincidencia directa. Dado que el hardware Gen4 se está [retirando](https://azure.microsoft.com/updates/gen-4-hardware-on-azure-sql-database-approaching-end-of-life-in-2020/), se prefiere la opción **GP_Gen5_2**. Además, si la carga de trabajo es adecuada para el nivel de proceso [Sin servidor](serverless-tier-overview.md), **GP_S_Gen5_1** sería una coincidencia más aproximada.

**Migración de una base de datos P15 prémium**

La consulta de asignación devuelve el resultado siguiente (algunas columnas no se muestran por motivos de brevedad):

|dtu_logical_cpus|dtu_hardware_gen|dtu_memory_per_core_gb|Gen4_vcores|Gen4_memory_per_core_gb|Gen5_vcores|Gen5_memory_per_core_gb|
|----------------|----------------|----------------------|-----------|-----------------------|-----------|-----------------------|
|42,00|Gen5|4,86|29,400|7|42,000|5,05|

Vemos que la base de datos de DTU tiene 42 CPU lógicas (núcleos virtuales), con 4,86 GB de memoria por núcleo virtual y que usando hardware Gen5. Aunque no hay ningún objetivo de servicio de núcleo virtual con 42 núcleos, el objetivo de servicio **BC_Gen5_40** está muy cerca de la capacidad de CPU y de memoria, y es una buena coincidencia.

**Migración de un grupo elástico básico de 200 eDTU**

La consulta de asignación devuelve el resultado siguiente (algunas columnas no se muestran por motivos de brevedad):

|dtu_logical_cpus|dtu_hardware_gen|dtu_memory_per_core_gb|Gen4_vcores|Gen4_memory_per_core_gb|Gen5_vcores|Gen5_memory_per_core_gb|
|----------------|----------------|----------------------|-----------|-----------------------|-----------|-----------------------|
|4.00|Gen5|5,40|2,800|7|4,000|5,05|

Vemos que el grupo elástico de DTU tiene 4 CPU lógicas (núcleos virtuales), con 5,4 GB de memoria por núcleo virtual y que usa hardware Gen5. La coincidencia directa del modelo de núcleo virtual es un grupo elástico **GP_Gen5_4**. Sin embargo, este objetivo de servicio admite un máximo de 200 bases de datos por grupo, mientras que el grupo elástico básico de 200 eDTU admite hasta 500 bases de datos. Si el grupo elástico que se va a migrar tiene más de 200 bases de datos, el objetivo de servicio de núcleo virtual coincidente tendría que ser **GP_Gen5_6**, que admite hasta 500 bases de datos.

## <a name="migrate-geo-replicated-databases"></a>Migración de bases de datos con replicación geográfica

La migración desde el modelo basado en DTU al modelo basado en núcleos virtuales es similar a la actualización o degradación de las relaciones de replicación geográfica entre las bases de datos en los niveles de servicio Estándar y Premium. Durante la migración, no tendrá que detener la replicación geográfica, pero tiene que seguir estas reglas de secuenciación:

- Al actualizar, debe actualizar primero la base de datos secundaria y luego la principal.
- Al degradar, invierta el orden; es decir, debe degradar primero la base de datos principal y luego la secundaria.

Cuando se usa la replicación geográfica entre dos grupos elásticos, se recomienda designar un grupo como principal y otro como secundario. En ese caso, cuando migre grupos elásticos debe usar la misma guía de secuenciación. Sin embargo, si tiene grupos elásticos que contienen bases de datos principales y secundarias, trate al grupo con la utilización más alta como principal y siga las reglas de secuenciación como corresponda.  

En la tabla siguiente se proporciona una guía para escenarios de migración específicos:

|Nivel de servicio actual|Nivel de servicio de destino|Tipo de migración|Acciones del usuario|
|---|---|---|---|
|Estándar|Uso general|Lateral|Puede realizar la migración en cualquier orden, pero debe asegurarse de tener un tamaño adecuado de núcleo virtual como hemos descrito anteriormente.|
|Premium|Crítico para la empresa|Lateral|Puede realizar la migración en cualquier orden, pero debe asegurarse de tener un tamaño adecuado de núcleo virtual como hemos descrito anteriormente.|
|Estándar|Crítico para la empresa|Actualizar|Debe migrar primero la secundaria|
|Crítico para la empresa|Estándar|Degradar|Debe migrar primero la principal|
|Premium|Uso general|Degradar|Debe migrar primero la principal|
|Uso general|Premium|Actualizar|Debe migrar primero la secundaria|
|Crítico para la empresa|Uso general|Degradar|Debe migrar primero la principal|
|Uso general|Crítico para la empresa|Actualizar|Debe migrar primero la secundaria|
||||

## <a name="migrate-failover-groups"></a>Migración de grupos de conmutación por error

La migración de grupos de conmutación por error con varias bases de datos requiere la migración individual de las bases de datos principales y secundarias. Durante ese proceso, se aplican las mismas consideraciones y reglas de secuenciación. Después de que las bases de datos se convierten al modelo de compra basado en núcleo virtual, el grupo de conmutación por error permanecerá en vigor con la misma configuración de directiva.

### <a name="create-a-geo-replication-secondary-database"></a>Creación de una base de datos secundaria de replicación geográfica

Puede crear una base de datos secundaria de replicación geográfica (geo-secundaria) solo con el mismo nivel de servicio que utilizó para la base de datos principal. Para las bases de datos con una tasa alta de generación de registro, se recomienda crear la replicación geográfica secundaria con el mismo tamaño de proceso que la principal.

Si va a crear una base de datos secundaria de replicación geográfica en el grupo elástico para una única base de datos principal, asegúrese de que el valor `maxVCore` del grupo coincida con el tamaño de proceso de la base de datos principal. Si va a crear una base de datos secundaria de replicación geográfica para una principal en otro grupo elástico, se recomienda que los grupos tengan la misma configuración de `maxVCore`.

## <a name="use-database-copy-to-migrate-from-dtu-to-vcore"></a>Uso de la copia de base de datos para la migración de DTU a núcleo virtual

Puede copiar cualquier base de datos con un tamaño de proceso basado en DTU en una base de datos que tenga un tamaño de proceso basado en núcleos virtuales sin restricciones o secuencias especiales, siempre y cuando el tamaño de proceso de destino admita el tamaño máximo de base de datos de la base de datos de origen. La copia de la base de datos crea una instantánea transaccionalmente coherente de los datos a partir de un momento dado después de que se inicie la operación de copia. No sincroniza los datos entre el origen y el destino después de ese momento dado.

## <a name="next-steps"></a>Pasos siguientes

- Para más información sobre las opciones de tamaños de proceso y de almacenamiento específicas que hay disponibles para las bases de datos únicas, consulte [Límites de recursos basados en núcleos virtuales de SQL Database para bases de datos únicas](resource-limits-vcore-single-databases.md).
- Para más información sobre las opciones de tamaño de proceso y de almacenamiento específicas que hay disponibles para los grupos elásticos, consulte [Límites de recursos basados en núcleos virtuales de Azure SQL Database para grupos elásticos](resource-limits-vcore-elastic-pools.md).