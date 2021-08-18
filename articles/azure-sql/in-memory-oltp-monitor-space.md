---
title: Supervisión del almacenamiento OLTP en memoria
description: Estimar y supervisar el uso y la capacidad del almacenamiento en memoria de XTP; resolver el error de capacidad 41823
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: how-to
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.reviewer: mathoma
ms.date: 01/25/2019
ms.openlocfilehash: c36b07e68081690b4fb326cd9520a418989b8411
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121752206"
---
# <a name="monitor-in-memory-oltp-storage-in-azure-sql-database-and-azure-sql-managed-instance"></a>Supervisión del almacenamiento OLTP en memoria en Azure SQL Database e Instancia administrada de Azure SQL
[!INCLUDE[appliesto-sqldb-sqlmi](includes/appliesto-sqldb-sqlmi.md)]

Cuando se usa [OLTP en memoria](in-memory-oltp-overview.md), los datos de tablas optimizadas para memoria y las variables de tabla residen en el almacenamiento OLTP en memoria.

## <a name="determine-whether-data-fits-within-the-in-memory-oltp-storage-cap"></a>Determinación de si los datos se ajustan al límite de almacenamiento de OLTP en memoria

Determine los extremos de almacenamiento de los diferentes niveles de servicio. Cada nivel de servicio Premium y Crítico para la empresa tiene un tamaño máximo de almacenamiento OLTP en memoria.

- [Límites de recursos basados en DTU para bases de datos únicas](database/resource-limits-dtu-single-databases.md)
- [Límites de recursos basados en DTU para grupos elásticos](database/resource-limits-dtu-elastic-pools.md)
- [Límites de recursos basados en núcleos virtuales para bases de datos únicas](database/resource-limits-vcore-single-databases.md)
- [Límites de recursos basados en núcleos virtuales para grupos elásticos](database/resource-limits-vcore-elastic-pools.md)
- [Límites de recursos basados en núcleos virtuales para instancia administrada](managed-instance/resource-limits.md)

La estimación de los requisitos de memoria para una tabla optimizada para memoria funciona de la misma manera para SQL Server tal y como se hace en Azure SQL Database e Instancia administrada de Azure SQL. Dedique unos minutos a revisar [Estimar los requisitos de memoria](/sql/relational-databases/in-memory-oltp/estimate-memory-requirements-for-memory-optimized-tables).

La tabla y las filas de variables de tabla, así como los índices, se tienen en cuenta para el tamaño máximo de los datos de usuario. Además, ALTER TABLE necesita suficiente espacio para crear una nueva versión de toda la tabla y sus índices.

Una vez que se supere este límite, se pueden producir errores en las operaciones de inserción y actualización con el error 41823 en las bases de datos únicas de Azure SQL Database y bases de datos de Instancia administrada de Azure SQL, y con el error 41840 para los grupos elásticos en Azure SQL Database. En ese momento, es necesario eliminar los datos para reclamar memoria o actualizar el nivel de servicio o el tamaño de proceso de la base de datos.

## <a name="monitoring-and-alerting"></a>Supervisión y alertas

Puede supervisar el uso del almacenamiento en memoria como un porcentaje del límite de almacenamiento para su tamaño de proceso en [Azure Portal](https://portal.azure.com/):

1. En la hoja Base de datos, busque el cuadro de uso de recursos y haga clic en Editar.
2. Seleccione la métrica `In-Memory OLTP Storage percentage`.
3. Para agregar una alerta, haga clic en el cuadro Uso de recursos para abrir la hoja de métricas y después haga clic en Agregar alerta.

O bien, use la siguiente consulta para mostrar el uso del almacenamiento en memoria:

```sql
    SELECT xtp_storage_percent FROM sys.dm_db_resource_stats
```

## <a name="correct-out-of-in-memory-oltp-storage-situations---errors-41823-and-41840"></a>Corrección de las situaciones de almacenamiento de OLTP en memoria agotado: errores 41823 y 41840

Al alcanzar el límite de almacenamiento de OLTP en memoria de la base de datos, se producen errores en las operaciones de inserción, actualización, modificación y creación, cuyos mensajes de error son 41823 (para bases de datos únicas) y 41840 (para grupos elásticos). Ambos errores anulan la transacción activa.

Los mensajes de error 41823 y 41840 indican que las tablas optimizadas en memoria y las variables de tabla de la base de datos o el grupo han alcanzado el tamaño máximo de almacenamiento de OLTP en memoria.

Para resolver este error, haga uno de los siguientes:

- Elimine datos de las tablas optimizadas para memoria, lo que potencialmente descarga los datos a tablas tradicionales, basadas en disco; o bien,
- Actualice el nivel de servicio a uno con suficiente espacio de almacenamiento en memoria para los datos que necesita mantener en tablas optimizadas para memoria.

> [!NOTE]
> En raras ocasiones, los errores 41823 y 41840 pueden ser transitorios, lo que significa que hay suficiente espacio de OLTP disponible en memoria y la operación se realiza de nuevo correctamente. Por lo tanto, se recomienda supervisar el almacenamiento de OLTP en memoria global disponible y volver a intentar cuando primero cuando se produce el error 41823 o 41840. Para más información acerca de la lógica de reintento, consulte [Detección de conflictos y lógica de reintento de OLTP en memoria](/sql/relational-databases/In-memory-oltp/transactions-with-memory-optimized-tables#conflict-detection-and-retry-logic).

## <a name="next-steps"></a>Pasos siguientes

Para obtener instrucciones sobre la supervisión, consulte [Supervisión con vistas de administración dinámica](database/monitoring-with-dmvs.md).