---
title: Niveles de servicio de uso general y crítico para la empresa
titleSuffix: Azure SQL Database & SQL Managed Instance
description: En el artículo se describen los niveles de servicio de uso general y crítico para la empresa en el modelo de compra basado en núcleo virtual, usado por Azure SQL Database e Instancia administrada de Azure SQL.
services: sql-database
ms.service: sql-db-mi
ms.subservice: service-overview
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
author: dimitri-furman
ms.author: dfurman
ms.reviewer: mathoma
ms.date: 7/7/2021
ms.openlocfilehash: 56bd4dcc121b5ebd2ac48f772bd0793dffccd50d
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/16/2021
ms.locfileid: "114289925"
---
# <a name="azure-sql-database-and-azure-sql-managed-instance-service-tiers"></a>Niveles de servicio de Azure SQL Database e Instancia administrada de Azure SQL
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

 Hay dos niveles de servicio de [núcleo virtual](service-tiers-vcore.md) disponibles en Azure SQL Database y en Azure SQL Managed Instance:

- El nivel [De uso general](service-tier-general-purpose.md) es un nivel económico diseñado para la mayoría de las cargas de trabajo con requisitos comunes de rendimiento y disponibilidad.
- El nivel [Crítico para la empresa](service-tier-business-critical.md) está diseñado para las cargas de trabajo sensibles al rendimiento con requisitos estrictos de disponibilidad.

Azure SQL Database también proporciona el nivel de servicio Hiperescala: 

- El nivel [Hiperescala](service-tier-hyperscale.md) está diseñado para la mayoría de las cargas de trabajo empresariales; proporciona almacenamiento altamente escalable, escalado horizontal de lectura, escalado rápido y funcionalidades rápidas de restauración de las bases de datos.

## <a name="service-tier-comparison"></a>Comparación de niveles de servicio

En la tabla siguiente se describen las diferencias principales entre los niveles de servicio.

|-| Tipo de recurso | De uso general | Hiperescala | Crítico para la empresa |
|:---:|:---:|:---:|:---:|:---:|
| **Más adecuado para** | |  Ofrece opciones de proceso y almacenamiento equilibradas adecuadas para un presupuesto limitado. | La mayoría de las cargas de trabajo empresariales. Escalado automático del tamaño de almacenamiento hasta 100 TB, escalado de procesos vertical y horizontal fluido, restauración rápida de bases de datos. | Aplicaciones de OLTP con una alta tasa de transacciones y latencia de E/S baja. Ofrece mayor resistencia a los errores y rapidez en las conmutaciones por error mediante varias réplicas actualizadas sincrónicamente.|
| **Disponible en estos tipos de recurso:** ||SQL Database / Instancia administrada de SQL | Base de datos única de Azure SQL Database | SQL Database / Instancia administrada de SQL |
| **Tamaño de proceso**| SQL Database | 1 a 80 núcleos virtuales | 1 a 80 núcleos virtuales | 1 a 128 núcleos virtuales |
| | Instancia administrada de SQL | 4, 8, 16, 24, 32, 40, 64 y 80 núcleos virtuales | N/D | 4, 8, 16, 24, 32, 40, 64 y 80 núcleos virtuales |
| | Grupos de Instancia administrada de SQL | 2, 4, 8, 16, 24, 32, 40, 64 y 80 núcleos virtuales | N/D | N/D |
| **Tipo de almacenamiento** | All | Almacenamiento remoto | Almacenamiento local de SSD y remoto en niveles | Almacenamiento local de SSD |
| **Tamaño de la base de datos** | SQL Database | 1 GB a 4 TB | 40 GB a 100 TB | 1 GB a 4 TB |
| | Instancia administrada de SQL  | 32 GB–8 TB | N/D | 32 GB – 4 TB |
| **Tamaño de almacenamiento** | SQL Database | 1 GB a 4 TB | 40 GB a 100 TB | 1 GB a 4 TB |
| | Instancia administrada de SQL  | 32 GB–8 TB | N/D | 32 GB – 4 TB |
| **Tamaño de TEMPDB** | SQL Database | [32 GB por núcleo virtual](resource-limits-vcore-single-databases.md) | [32 GB por núcleo virtual](resource-limits-vcore-single-databases.md) | [32 GB por núcleo virtual](resource-limits-vcore-single-databases.md) |
| | Instancia administrada de SQL  | [24 GB por núcleo virtual](../managed-instance/resource-limits.md#service-tier-characteristics) | N/D | Hasta 4 TB: [limitado en función del tamaño de almacenamiento](../managed-instance/resource-limits.md#service-tier-characteristics) |
| **Rendimiento de escritura de registros** | SQL Database | Bases de datos únicas: [4,5 MB/s por núcleo virtual (máximo 50 MB/s)](resource-limits-vcore-single-databases.md) <br> Grupos elásticos: [6 MB/s por núcleo virtual (máximo 62,5 MB/s)](resource-limits-vcore-elastic-pools.md)| 100 MB/s | Bases de datos únicas: [12 MB/s por núcleo virtual (máximo 96 MB/s)](resource-limits-vcore-single-databases.md) <br> Grupos elásticos: [15 MB/s por núcleo virtual (máximo 120 MB/s)](resource-limits-vcore-elastic-pools.md)|
| | Instancia administrada de SQL | [3 MB/s por núcleo virtual (máximo 22 MB/s)](../managed-instance/resource-limits.md#service-tier-characteristics) | N/D | [4 MB/s por núcleo virtual (máximo 48 MB/s)](../managed-instance/resource-limits.md#service-tier-characteristics) |
|**Disponibilidad**|All| 99,99% |  [99,95 % con una réplica secundaria; 99,99 % con más réplicas](service-tier-hyperscale-frequently-asked-questions-faq.yml#what-slas-are-provided-for-a-hyperscale-database-) | 99,99% <br/> [99,995 % con una base de datos única con redundancia de zona](https://azure.microsoft.com/blog/understanding-and-leveraging-azure-sql-database-sla/) |
|**Copias de seguridad**|All|RA-GRS, de 1 a 35 días (7 días de forma predeterminada) | RA-GRS, 7 días, recuperación a un momento dado (PITR) rápida | RA-GRS, de 1 a 35 días (7 días de forma predeterminada) |
|**OLTP en memoria** | | N/D | La compatibilidad es parcial. Se admiten tipos de tablas optimizadas para memoria, variables de tabla y módulos compilados de forma nativa. | Disponible |
|**Réplicas de solo lectura**| | 0 integradas <br> 0 a 4 con [replicación geográfica](active-geo-replication-overview.md) | 0 a 4 integradas | 1 integrada, incluida en el precio <br> 0 a 4 con [replicación geográfica](active-geo-replication-overview.md) |
|**Precios y facturación** | SQL Database | [El núcleo virtual, el almacenamiento reservado y el almacenamiento de copia de seguridad](https://azure.microsoft.com/pricing/details/sql-database/single/) se cobran. <br/>Las IOPS no se cobran. | Se cobran los [núcleos virtuales de cada réplica y el almacenamiento usado](https://azure.microsoft.com/pricing/details/sql-database/single/). <br/>IOPS todavía no se ha cargado. | [El núcleo virtual, el almacenamiento reservado y el almacenamiento de copia de seguridad](https://azure.microsoft.com/pricing/details/sql-database/single/) se cobran. <br/>Las IOPS no se cobran. |
|| Instancia administrada de SQL | [El núcleo virtual, el almacenamiento reservado y el almacenamiento de copia de seguridad](https://azure.microsoft.com/pricing/details/sql-database/managed/) se cobran. <br/>Las IOPS no se cobran.| N/D | [El núcleo virtual, el almacenamiento reservado y el almacenamiento de copia de seguridad](https://azure.microsoft.com/pricing/details/sql-database/managed/) se cobran. <br/>Las IOPS no se cobran.| 
|**Modelos de descuento**| | [Instancias reservadas](reserved-capacity-overview.md)<br/>[Ventaja híbrida de Azure](../azure-hybrid-benefit.md) (no disponible en suscripciones de desarrollo y pruebas)<br/>Suscripciones de Desarrollo/pruebas de [Enterprise](https://azure.microsoft.com/offers/ms-azr-0148p/) y [de pago por uso](https://azure.microsoft.com/offers/ms-azr-0023p/)| [Ventaja híbrida de Azure](../azure-hybrid-benefit.md) (no disponible en suscripciones de desarrollo y pruebas)<br/>Suscripciones de Desarrollo/pruebas de [Enterprise](https://azure.microsoft.com/offers/ms-azr-0148p/) y [de pago por uso](https://azure.microsoft.com/offers/ms-azr-0023p/)| [Instancias reservadas](reserved-capacity-overview.md)<br/>[Ventaja híbrida de Azure](../azure-hybrid-benefit.md) (no disponible en suscripciones de desarrollo y pruebas)<br/>Suscripciones de Desarrollo/pruebas de [Enterprise](https://azure.microsoft.com/offers/ms-azr-0148p/) y [de pago por uso](https://azure.microsoft.com/offers/ms-azr-0023p/)|

Para obtener más información, consulte las diferencias detalladas entre los niveles de servicio en las páginas [Azure SQL Database (núcleo virtual)](resource-limits-vcore-single-databases.md), [bases de datos únicas en Azure SQL Database (DTU)](resource-limits-dtu-single-databases.md), [bases de datos agrupadas en Azure SQL Database (DTU)](resource-limits-dtu-single-databases.md), e [Instancia administrada de Azure SQL](../managed-instance/resource-limits.md).

> [!NOTE]
> Para información sobre el nivel de servicio Hiperescala, consulte [Nivel de servicio Hiperescala](service-tier-hyperscale.md). Para ver una comparación entre el modelo de compra basado en núcleo virtual y el modelo de compra basado en DTU, consulte [Modelos de compra y recursos](purchasing-models.md).

## <a name="data-and-log-storage"></a>Almacenamiento de datos y de registro

Los factores siguientes afectan a la cantidad de almacenamiento utilizado para los datos y los archivos de registro, y se aplican a los niveles De uso general y Crítico para la empresa. Para información detallada sobre el almacenamiento de datos y registros en Hiperescala, consulte [Nivel de servicio Hiperescala](service-tier-hyperscale.md).

- Cada tamaño de proceso admite un tamaño máximo de datos con un valor predeterminado de 32 GB.
- Al configurar el tamaño máximo de datos, se agrega automáticamente un 30 por ciento adicional de almacenamiento para los archivos de registro.
- Puede seleccionar cualquier tamaño máximo de datos entre 1 GB y el tamaño de almacenamiento máximo admitido, en incrementos de 1 GB.
- En el nivel de servicio De uso general, `tempdb` usa el almacenamiento local de SSD y este costo de almacenamiento se incluye en el precio del núcleo virtual.
- En el nivel de servicio Crítico para la empresa, `tempdb` comparte el almacenamiento local de SSD con datos y archivos de registro, y el costo de almacenamiento `tempdb` se incluye en el precio del núcleo virtual.
- El tamaño máximo de almacenamiento para una instancia de SQL Managed Instance se debe especificar en múltiplos de 32 GB.

> [!IMPORTANT]
> En los niveles De uso general y Crítico para la empresa, se le cobra por el tamaño máximo de almacenamiento que tiene configurado para una base de datos, un grupo elástico o una instancia administrada. En el nivel Hiperescala, se le cobrará el almacenamiento de datos asignado.

Si desea supervisar el tamaño actual del almacenamiento de datos asignados y utilizados en SQL Database, use las [métricas](../../azure-monitor/essentials/metrics-supported.md#microsoftsqlserversdatabases) *allocated_data_storage* y *storage* Azure Monitor, respectivamente. Para supervisar el tamaño de almacenamiento de instancias consumido total para SQL Managed Instance, use la [métrica](../../azure-monitor/essentials/metrics-supported.md#microsoftsqlmanagedinstances) *storage_space_used_mb*. Si desea supervisar el tamaño actual del almacenamiento asignado y utilizado de datos y archivos de registro individuales en una base de datos con T-SQL, utilice la vista [sys.database_files](/sql/relational-databases/system-catalog-views/sys-database-files-transact-sql) y la función [FILEPROPERTY(... , 'SpaceUsed')](/sql/t-sql/functions/fileproperty-transact-sql).

> [!TIP]
> En algunas circunstancias, puede que deba reducir una base de datos para reclamar el espacio no utilizado. Para obtener más información, consulte [Administración del espacio de archivo en Azure SQL Database](file-space-manage.md).

## <a name="backups-and-storage"></a>Copias de seguridad y almacenamiento

Para admitir las funcionalidades de [restauración a un momento dado (PITR)](recovery-using-backups.md) y [retención a largo plazo (LTR)](long-term-retention-overview.md) de SQL Database y SQL Managed Instance, se asigna almacenamiento a las copias de seguridad de base de datos. Este almacenamiento es independiente del almacenamiento de datos y archivos de registro, y se factura por separado.

- **PITR**: En los niveles De uso general y Crítico para la empresa, las copias de seguridad de base de datos individuales se copian en el [almacenamiento con redundancia geográfica con acceso de lectura (RA-GRS)](../../storage/common/geo-redundant-design.md) automáticamente. El tamaño de almacenamiento aumenta dinámicamente a medida que se crean nuevas copias de seguridad. El almacenamiento se utiliza para copias de seguridad completas, diferenciales y del registro de transacciones. El consumo de almacenamiento depende de la tasa de cambio de la base de datos y del período de retención configurado para las copias de seguridad. Puede configurar un período de retención independiente para cada base de datos de entre 1 y 35 días para SQL Database y de entre 0 a 35 días para SQL Managed Instance. Se proporciona una cantidad de almacenamiento de copia de seguridad equivalente al tamaño máximo de datos configurado sin costo adicional.
- **LTR**: también tiene la opción de configurar la retención a largo plazo de copias de seguridad completas hasta un máximo de 10 años. Si ha instalado la directiva de LTR, estas copias de seguridad se almacenan en almacenamiento RA-GRS automáticamente, pero puede controlar la frecuencia con que se realizan las copias de seguridad. Para satisfacer los distintos requisitos de cumplimiento, puede seleccionar distintos períodos de retención para copias de seguridad semanales, mensuales o anuales. La configuración que elija determina la cantidad de almacenamiento que se usará para las copias de seguridad de LTR. Para obtener más información, vea [Retención de copias de seguridad a largo plazo](long-term-retention-overview.md).

## <a name="next-steps"></a>Pasos siguientes

Para detalles sobre los tamaños de almacenamiento y proceso específicos disponibles en los niveles de servicio de núcleo virtual, consulte: 

- [Límites de recursos basados en núcleos virtuales de Azure SQL Database](resource-limits-vcore-single-databases.md).
- [Límites de recursos basados en núcleos virtuales para bases de datos agrupadas en Azure SQL Database](resource-limits-vcore-elastic-pools.md).
- [Límites de recursos basados en núcleos virtuales para Instancia administrada de Azure SQL](../managed-instance/resource-limits.md).