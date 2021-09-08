---
title: Límites de recursos de la unidad de procesamiento de base de datos en grupos elásticos
description: En esta página se describen algunos límites de recursos de DTU comunes para grupos elásticos en Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: elastic-pools
ms.custom: seo-lt-2019 sqldbrb=1 references_regions
ms.devlang: ''
ms.topic: reference
author: dimitri-furman
ms.author: dfurman
ms.reviewer: mathoma
ms.date: 04/09/2021
ms.openlocfilehash: 473aa81bf28dd867bf30acef7a5b407b0e4b67a2
ms.sourcegitcommit: cd7d099f4a8eedb8d8d2a8cae081b3abd968b827
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/25/2021
ms.locfileid: "112964759"
---
# <a name="resources-limits-for-elastic-pools-using-the-dtu-purchasing-model"></a>Límites de recursos para grupos elásticos que utilizan el modelo de compra de DTU
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

En este artículo se proporcionan los límites de recursos detallados para bases de datos en Azure SQL Database que están dentro de un grupo elástico mediante el modelo de compra de DTU.

* Para conocer los límites del modelo de compra en DTU para las bases de datos únicas en un servidor, consulte [Información general de los límites de recursos para un servidor](resource-limits-logical-server.md).
* Para información sobre los límites de recursos del modelo de compra de DTU para Azure SQL Database, vea [Límites de recursos de la unidad de procesamiento de base de datos en bases de datos únicas](resource-limits-dtu-single-databases.md) y [Límites de recursos de la unidad de procesamiento de base de datos en grupos elásticos](resource-limits-dtu-elastic-pools.md).
* Para conocer los límites de recursos de núcleo virtual, consulte [Límites de recursos del núcleo virtual: Azure SQL Database](resource-limits-vcore-single-databases.md) y [Límites de recursos del núcleo virtual: grupos elásticos](resource-limits-vcore-elastic-pools.md).
* Para obtener más información sobre los diferentes modelos de compra, consulte los [niveles de servicio y modelos de compra](purchasing-models.md).

Cada réplica de solo lectura tiene sus propios recursos, como DTU, trabajos y sesiones. Cada réplica de solo lectura está sujeta a los límites de recursos que se detallan más adelante en este artículo.

## <a name="elastic-pool-storage-sizes-and-compute-sizes"></a>Grupo elástico: tamaños de almacenamiento y tamaños de proceso

Para los grupos elásticos de Azure SQL Database, las siguientes tablas muestran los recursos disponibles en cada nivel de servicio y tamaño de proceso. Puede establecer el nivel de servicio, el tamaño de proceso y la cantidad de almacenamiento mediante:

* [Transact-SQL](elastic-pool-scale.md) mediante [ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql#overview-sql-database)
* [Azure Portal](elastic-pool-manage.md#azure-portal)
* [PowerShell](elastic-pool-manage.md#powershell)
* [CLI de Azure](elastic-pool-manage.md#azure-cli)
* [REST API](elastic-pool-manage.md#rest-api)


> [!IMPORTANT]
> Para obtener información sobre la guía y otras consideraciones del escalado, consulte [Escalar un grupo elástico](elastic-pool-scale.md).

Los límites de recursos de las bases de datos individuales de los grupos elásticos suelen ser los mismos que para las bases de datos únicas fuera de los grupos basados en DTU y el nivel de servicio. Por ejemplo, el número máximo de trabajadores simultáneos de una base de datos S2 es de 120 trabajadores. Por lo tanto, el número máximo de trabajadores simultáneos de una base de datos de un grupo estándar también es de 120 trabajadores si el número máximo de DTU por base de datos del grupo es de 50 DTU (que es equivalente a S2).
 
Para el mismo número de DTU, los recursos proporcionados a un grupo elástico pueden superar los recursos proporcionados a una única base de datos fuera de un grupo elástico. Esto significa que es posible que el uso de eDTU de un grupo elástico sea inferior a la suma del uso de DTU entre las bases de datos del grupo, en función de los patrones de carga de trabajo. Por ejemplo, en un caso extremo en el que solo hay una base de datos en un grupo elástico donde el uso de DTU de la base de datos es del 100 %, es posible que el uso de eDTU del grupo sea del 50 % para determinados patrones de carga de trabajo. Esto puede ocurrir incluso si la DTU máxima por base de datos permanece en el valor máximo admitido para el tamaño de grupo especificado.

> [!NOTE]
> El límite de recursos de almacenamiento por grupo de cada una de las tablas siguientes no incluye tempdb ni el almacenamiento de registros.

### <a name="basic-elastic-pool-limits"></a>Límites de grupo elástico básico

| eDTU por grupo | **50** | **100** | **200** | **300** | **400** | **800** | **1200** | **1600** |
|:---|---:|---:|---:| ---: | ---: | ---: | ---: | ---: |
| Almacenamiento incluido por grupo (GB) | 5 | 10 | 20 | 29 | 39 | 78 | 117 | 156 |
| Almacenamiento máximo por grupo (GB) | 5 | 10 | 20 | 29 | 39 | 78 | 117 | 156 |
| Almacenamiento máximo de OLTP en memoria por grupo (GB) | N/D | N/D | N/D | N/D | N/D | N/D | N/D | N/D |
| Máximo número de bases de datos por grupo <sup>1</sup> | 100 | 200 | 500 | 500 | 500 | 500 | 500 | 500 |
| Cantidad máxima de trabajos (solicitudes) simultáneos por grupo <sup>2</sup> | 100 | 200 | 400 | 600 | 800 | 1600 | 2400 | 3200 |
| Cantidad máxima de sesiones simultáneas por grupo <sup>2</sup> | 30000 | 30000 | 30000 | 30000 |30000 | 30000 | 30000 | 30000 |
| Opciones de DTU mínima por base de datos | 0, 5 | 0, 5 | 0, 5 | 0, 5 | 0, 5 | 0, 5 | 0, 5 | 0, 5 |
| Opciones de DTU máxima por base de datos | 5 | 5 | 5 | 5 | 5 | 5 | 5 | 5 |
| Almacenamiento máximo por base de datos (GB) | 2 | 2 | 2 | 2 | 2 | 2 | 2 | 2 |
||||||||

<sup>1</sup> Consulte [Administración de recursos en grupos elásticos densos](elastic-pool-resource-management.md) para conocer las consideraciones adicionales.

<sup>2</sup> Para obtener el máximo de trabajos simultáneos (solicitudes) para cualquier base de datos individual, consulte [Límites de recursos de base de datos única](resource-limits-vcore-single-databases.md). Por ejemplo, si el grupo elástico usa Gen5 y el valor máximo de núcleo virtual por base de datos se establece en 2, el valor de máximo de trabajos simultáneos es 200.  Si el número máximo de núcleo virtual por base de datos se establece en 0,5, el valor de máximo de trabajos simultáneos es 50, ya que en Gen5 hay un máximo de 100 trabajos simultáneos por núcleo virtual. Para otras configuraciones de memoria con núcleo virtual máximo por base de datos que sean un núcleo virtual o menos, la cantidad máxima de trabajos simultáneos se escala de forma similar.

### <a name="standard-elastic-pool-limits"></a>Límites de grupo elástico estándar

| eDTU por grupo | **50** | **100** | **200** | **300** | **400** | **800**|
|:---|---:|---:|---:| ---: | ---: | ---: |
| Almacenamiento incluido por grupo (GB) <sup>1</sup> | 50 | 100 | 200 | 300 | 400 | 800 |
| Almacenamiento máximo por grupo (GB) | 500 | 750 | 1024 | 1280 | 1536 | 2048 |
| Almacenamiento máximo de OLTP en memoria por grupo (GB) | N/D | N/D | N/D | N/D | N/D | N/D |
| Número máximo de bases de datos por grupo <sup>2</sup> | 100 | 200 | 500 | 500 | 500 | 500 |
| Cantidad máxima de trabajos (solicitudes) simultáneos por grupo <sup>3</sup> | 100 | 200 | 400 | 600 | 800 | 1600 |
| Cantidad máxima de sesiones simultáneas por grupo <sup>3</sup> | 30000 | 30000 | 30000 | 30000 | 30000 | 30000 |
| Opciones de DTU mínima por base de datos | 0, 10, 20, 50 | 0, 10, 20, 50, 100 | 0, 10, 20, 50, 100, 200 | 0, 10, 20, 50, 100, 200, 300 | 0, 10, 20, 50, 100, 200, 300, 400 | 0, 10, 20, 50, 100, 200, 300, 400, 800 |
| Opciones de DTU máxima por base de datos | 10, 20, 50 | 10, 20, 50, 100 | 10, 20, 50, 100, 200 | 10, 20, 50, 100, 200, 300 | 10, 20, 50, 100, 200, 300, 400 | 10, 20, 50, 100, 200, 300, 400, 800 |
| Almacenamiento máximo por base de datos (GB) | 1024 | 1024 | 1024 | 1024 | 1024 | 1024 |
||||||||

<sup>1</sup> Consulte [Precios de Azure SQL Database](https://azure.microsoft.com/pricing/details/sql-database/elastic/) para más información sobre los costos extra que se generan al aprovisionar almacenamiento adicional.

<sup>2</sup> Consulte [Administración de recursos en grupos elásticos densos](elastic-pool-resource-management.md) para ver otras consideraciones.

<sup>3</sup> Para obtener el máximo de trabajos simultáneos (solicitudes) para cualquier base de datos individual, consulte [Límites de recursos de base de datos única](resource-limits-vcore-single-databases.md). Por ejemplo, si el grupo elástico usa Gen5 y el valor máximo de núcleo virtual por base de datos se establece en 2, el valor de máximo de trabajos simultáneos es 200.  Si el número máximo de núcleo virtual por base de datos se establece en 0,5, el valor de máximo de trabajos simultáneos es 50, ya que en Gen5 hay un máximo de 100 trabajos simultáneos por núcleo virtual. Para otras configuraciones de memoria con núcleo virtual máximo por base de datos que sean un núcleo virtual o menos, la cantidad máxima de trabajos simultáneos se escala de forma similar.

### <a name="standard-elastic-pool-limits-continued"></a>Límites de grupo elástico estándar (continuación)

| eDTU por grupo | **1200** | **1600** | **2000** | **2500** | **3000** |
|:---|---:|---:|---:| ---: | ---: |
| Almacenamiento incluido por grupo (GB) <sup>1</sup> | 1200 | 1600 | 2000 | 2.500 | 3000 |
| Almacenamiento máximo por grupo (GB) | 2560 | 3072 | 3584 | 4096 | 4096 |
| Almacenamiento máximo de OLTP en memoria por grupo (GB) | N/D | N/D | N/D | N/D | N/D |
| Número máximo de bases de datos por grupo <sup>2</sup> | 500 | 500 | 500 | 500 | 500 |
| Cantidad máxima de trabajos (solicitudes) simultáneos por grupo <sup>3</sup> | 2400 | 3200 | 4000 | 5000 | 6000 |
| Cantidad máxima de sesiones simultáneas por grupo <sup>3</sup> | 30000 | 30000 | 30000 | 30000 | 30000 |
| Opciones de DTU mínima por base de datos | 0, 10, 20, 50, 100, 200, 300, 400, 800, 1200 | 0, 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600 | 0, 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000 | 0, 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000, 2500 | 0, 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000, 2500, 3000 |
| Opciones de DTU máxima por base de datos | 10, 20, 50, 100, 200, 300, 400, 800, 1200 | 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600 | 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000 | 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000, 2500 | 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000, 2500, 3000 |
| Almacenamiento máximo por base de datos (GB) | 1024 | 1536 | 1792 | 2304 | 2816 |
|||||||

<sup>1</sup> Consulte [Precios de Azure SQL Database](https://azure.microsoft.com/pricing/details/sql-database/elastic/) para más información sobre los costos extra que se generan al aprovisionar almacenamiento adicional.

<sup>2</sup> Consulte [Administración de recursos en grupos elásticos densos](elastic-pool-resource-management.md) para ver otras consideraciones.

<sup>3</sup> Para obtener el máximo de trabajos simultáneos (solicitudes) para cualquier base de datos individual, consulte [Límites de recursos de base de datos única](resource-limits-vcore-single-databases.md). Por ejemplo, si el grupo elástico usa Gen5 y el valor máximo de núcleo virtual por base de datos se establece en 2, el valor de máximo de trabajos simultáneos es 200.  Si el número máximo de núcleo virtual por base de datos se establece en 0,5, el valor de máximo de trabajos simultáneos es 50, ya que en Gen5 hay un máximo de 100 trabajos simultáneos por núcleo virtual. Para otras configuraciones de memoria con núcleo virtual máximo por base de datos que sean un núcleo virtual o menos, la cantidad máxima de trabajos simultáneos se escala de forma similar.

### <a name="premium-elastic-pool-limits"></a>Límites de grupo elástico premium

| eDTU por grupo | **125** | **250** | **500** | **1000** | **1500**|
|:---|---:|---:|---:| ---: | ---: |
| Almacenamiento incluido por grupo (GB) <sup>1</sup> | 250 | 500 | 750 | 1024 | 1536 |
| Almacenamiento máximo por grupo (GB) | 1024 | 1024 | 1024 | 1024 | 1536 |
| Almacenamiento máximo de OLTP en memoria por grupo (GB) | 1 | 2 | 4 | 10 | 12 |
| Número máximo de bases de datos por grupo <sup>2</sup> | 50 | 100 | 100 | 100 | 100 |
| Número máximo de trabajos simultáneos por grupo (solicitudes) <sup>3</sup> | 200 | 400 | 800 | 1600 | 2400 |
| Cantidad máxima de sesiones simultáneas por grupo <sup>3</sup> | 30000 | 30000 | 30000 | 30000 | 30000 |
| Cantidad mínima de eDTU por base de datos | 0, 25, 50, 75, 125 | 0, 25, 50, 75, 125, 250 | 0, 25, 50, 75, 125, 250, 500 | 0, 25, 50, 75, 125, 250, 500, 1000 | 0, 25, 50, 75, 125, 250, 500, 1000|
| Cantidad máxima de eDTU por base de datos | 25, 50, 75, 125 | 25, 50, 75, 125, 250 | 25, 50, 75, 125, 250, 500 | 25, 50, 75, 125, 250, 500, 1000 | 25, 50, 75, 125, 250, 500, 1000|
| Almacenamiento máximo por base de datos (GB) | 1024 | 1024 | 1024 | 1024 | 1536 |
|||||||

<sup>1</sup> Consulte [Precios de Azure SQL Database](https://azure.microsoft.com/pricing/details/sql-database/elastic/) para más información sobre los costos extra que se generan al aprovisionar almacenamiento adicional.

<sup>2</sup> Consulte [Administración de recursos en grupos elásticos densos](elastic-pool-resource-management.md) para ver otras consideraciones.

<sup>3</sup> Para obtener el máximo de trabajos simultáneos (solicitudes) para cualquier base de datos individual, consulte [Límites de recursos de base de datos única](resource-limits-vcore-single-databases.md). Por ejemplo, si el grupo elástico usa Gen5 y el valor máximo de núcleo virtual por base de datos se establece en 2, el valor de máximo de trabajos simultáneos es 200.  Si el número máximo de núcleo virtual por base de datos se establece en 0,5, el valor de máximo de trabajos simultáneos es 50, ya que en Gen5 hay un máximo de 100 trabajos simultáneos por núcleo virtual. Para otras configuraciones de memoria con núcleo virtual máximo por base de datos que sean un núcleo virtual o menos, la cantidad máxima de trabajos simultáneos se escala de forma similar.

### <a name="premium-elastic-pool-limits-continued"></a>Límites de grupo elástico premium (continuación)

| eDTU por grupo | **2000** | **2500** | **3000** | **3500** | **4000**|
|:---|---:|---:|---:| ---: | ---: |
| Almacenamiento incluido por grupo (GB) <sup>1</sup> | 2048 | 2560 | 3072 | 3548 | 4096 |
| Almacenamiento máximo por grupo (GB) | 2048 | 2560 | 3072 | 3548 | 4096|
| Almacenamiento máximo de OLTP en memoria por grupo (GB) | 16 | 20 | 24 | 28 | 32 |
| Número máximo de bases de datos por grupo <sup>2</sup> | 100 | 100 | 100 | 100 | 100 |
| Cantidad máxima de trabajos (solicitudes) simultáneos por grupo <sup>3</sup> | 3200 | 4000 | 4800 | 5600 | 6400 |
| Cantidad máxima de sesiones simultáneas por grupo <sup>3</sup> | 30000 | 30000 | 30000 | 30000 | 30000 |
| Opciones de DTU mínima por base de datos | 0, 25, 50, 75, 125, 250, 500, 1000, 1750 | 0, 25, 50, 75, 125, 250, 500, 1000, 1750 | 0, 25, 50, 75, 125, 250, 500, 1000, 1750 | 0, 25, 50, 75, 125, 250, 500, 1000, 1750 | 0, 25, 50, 75, 125, 250, 500, 1000, 1750, 4000 |
| Opciones de DTU máxima por base de datos | 25, 50, 75, 125, 250, 500, 1000, 1750 | 25, 50, 75, 125, 250, 500, 1000, 1750 | 25, 50, 75, 125, 250, 500, 1000, 1750 | 25, 50, 75, 125, 250, 500, 1000, 1750 | 25, 50, 75, 125, 250, 500, 1000, 1750, 4000 |
| Almacenamiento máximo por base de datos (GB) | 2048 | 2560 | 3072 | 3584 | 4096 |
|||||||

<sup>1</sup> Consulte [Precios de Azure SQL Database](https://azure.microsoft.com/pricing/details/sql-database/elastic/) para más información sobre los costos extra que se generan al aprovisionar almacenamiento adicional.

<sup>2</sup> Consulte [Administración de recursos en grupos elásticos densos](elastic-pool-resource-management.md) para ver otras consideraciones.

<sup>3</sup> Para obtener el máximo de trabajos simultáneos (solicitudes) para cualquier base de datos individual, consulte [Límites de recursos de base de datos única](resource-limits-vcore-single-databases.md). Por ejemplo, si el grupo elástico usa Gen5 y el valor máximo de núcleo virtual por base de datos se establece en 2, el valor de máximo de trabajos simultáneos es 200.  Si el número máximo de núcleo virtual por base de datos se establece en 0,5, el valor de máximo de trabajos simultáneos es 50, ya que en Gen5 hay un máximo de 100 trabajos simultáneos por núcleo virtual. Para otras configuraciones de memoria con núcleo virtual máximo por base de datos que sean un núcleo virtual o menos, la cantidad máxima de trabajos simultáneos se escala de forma similar.

> [!IMPORTANT]
> Existe más de 1 TB de almacenamiento en el nivel Premium actualmente disponible en todas las regiones excepto: Este de China, Norte de China, Centro de Alemania y Noreste de Alemania. En estas regiones, el almacenamiento máximo en el nivel Prémium está limitado a 1 TB.  Para más información, consulte las [limitaciones actuales de P11 y P15](single-database-scale.md#p11-and-p15-constraints-when-max-size-greater-than-1-tb).

Si se usan todas las unidades DTU de un grupo elástico, cada una de las bases de datos del grupo recibe una misma cantidad de recursos para procesar consultas. El servicio SQL Database proporciona ecuanimidad de uso compartido de recursos entre bases de datos garantizando los mismos segmentos de tiempo de proceso. La ecuanimidad de uso compartido de recursos del grupo elástico es adicional a cualquier cantidad de recursos garantizados de otro modo a cada base de datos cuando el número mínimo de DTU por base de datos se establece en un valor distinto de cero.

> [!NOTE]
> Para obtener información sobre los límites de `tempdb`, consulte los [límites de tempdb](/sql/relational-databases/databases/tempdb-database#tempdb-database-in-sql-database).
>
> Para información adicional sobre los límites de almacenamiento en el nivel de servicio prémium, consulte [Gobernanza del espacio de almacenamiento](resource-limits-logical-server.md#storage-space-governance).

### <a name="database-properties-for-pooled-databases"></a>Propiedades de base de datos para bases de datos agrupadas

Para cada grupo elástico, opcionalmente puede especificar DTU mínimas y máximas por base de datos para modificar los patrones de consumo de recursos dentro del grupo. Los valores mínimos y máximos especificados se aplican a todas las bases de datos del grupo. No se admite la personalización de DTU mínimas y máximas para bases de datos individuales del grupo. 

También puede establecer el almacenamiento máximo por base de datos, por ejemplo, para impedir que una base de datos consuma todo el almacenamiento del grupo. Esta opción se puede configurar de forma independiente para cada base de datos.

En la tabla siguiente se describen las propiedades por base de datos de las bases de datos agrupadas. 

| Propiedad | Descripción |
|:--- |:--- |
| DTU máximas por base de datos |Cantidad máxima de DTU que puede usar cualquier base de datos del grupo, si está disponible según el uso que hacen otras bases de datos del grupo. La cantidad máxima de DTU por base de datos no garantiza la disponibilidad de recursos. Si la carga de trabajo de cada base de datos no necesita que todos los recursos del grupo disponibles funcionen correctamente, considere la posibilidad de establecer una cantidad máxima de DTU por base de datos para impedir que una sola base de datos monopolice los recursos del grupo. Se admite cierto grado de exceso de asignación de recursos, ya que el grupo suele basarse en patrones de uso en frío y caliente de las bases de datos, cuando en realidad los picos de uso no tienen lugar en todas las bases de datos a la vez. |
| DTU mínimas por base de datos |Cantidad mínima de DTU reservadas para cualquier base de datos del grupo. Considere la posibilidad de establecer una cantidad mínima de DTU por base de datos cuando quiera garantizar la disponibilidad de los recursos para cada base de datos, independientemente del consumo de recursos por parte de otras bases de datos del grupo. La cantidad mínima de DTU por base de datos se puede establecer en 0, y también se trata del valor predeterminado. Esta propiedad se establece en cualquier valor entre 0 y el uso medio de DTU por base de datos.|
| Almacenamiento máximo por base de datos |El tamaño máximo de base de datos establecido por el usuario para una base de datos de un grupo. Las bases de datos agrupadas comparten el almacenamiento del grupo asignado, de modo que el tamaño que puede alcanzar una base de datos se limita a la menor cantidad de almacenamiento restante del grupo y el tamaño máximo de la base de datos. El tamaño máximo de la base de datos hace referencia al tamaño máximo de los archivos de datos, y no incluye el espacio utilizado por el archivo de registro. |
|||

> [!IMPORTANT]
> Dado que los recursos de un grupo elástico son finitos, el hecho de establecer las DTU mínimas por base de datos en un valor mayor que 0 limita implícitamente el uso de recursos por cada base de datos. Si, en un momento dado, la mayoría de las bases de datos de un grupo están inactivas, los recursos reservados para satisfacer la garantía de DTU mínimas no estarán disponibles para las bases de datos activas en ese momento.
>
> Además, al establecer DTU mínimas por base de datos en un valor mayor que 0, se limita implícitamente el número de bases de datos que se pueden agregar al grupo. Por ejemplo, si establece en 100 las DTU mínimas de un grupo de 400 DTU, significa que no podrá agregar más de 4 bases de datos al grupo, ya que se reservan 100 DTU para cada base de datos.
> 

Aunque las propiedades por base de datos se expresan en DTU, también rigen el consumo de otros tipos de recursos, como la E/S de datos, la E/S de registros y los subprocesos de trabajo. A medida que se ajusten los valores de DTU mínimas y máximas por base de datos, las reservas y los límites de todos los tipos de recursos se adaptarán proporcionalmente.

## <a name="next-steps"></a>Pasos siguientes

* Para conocer los límites de recursos de los núcleos virtuales de una base de datos única, consulte los [límites de recursos para bases de datos únicas con el modelo de compra del núcleo virtual](resource-limits-vcore-single-databases.md).
* Para conocer los límites de recursos de DTU para una base de datos única, consulte los [límites de recursos para bases de datos únicas que usan el modelo de compra de DTU](resource-limits-dtu-single-databases.md).
* Para conocer los límites de recursos de núcleos virtuales para grupos elásticos, consulte los [límites de recursos para grupos elásticos con el modelo de compra del núcleo virtual](resource-limits-vcore-elastic-pools.md).
* Para conocer los límites de recursos para instancias administradas en Instancia administrada de Azure SQL, consulte los [límites de recursos para Instancia administrada de SQL](../managed-instance/resource-limits.md).
* Para más información sobre los límites generales de Azure, consulte [Límites, cuotas y restricciones de suscripción y servicios de Microsoft Azure](../../azure-resource-manager/management/azure-subscription-service-limits.md).
* Para más información sobre los límites de recursos en un servidor SQL lógico, vea la [información general sobre los límites de recursos en un servidor SQL lógico](resource-limits-logical-server.md), donde encontrará datos sobre los límites en los niveles de servidor y suscripción.