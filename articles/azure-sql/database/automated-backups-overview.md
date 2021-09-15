---
title: Copias de seguridad automáticas con redundancia geográfica
titleSuffix: Azure SQL Database & Azure SQL Managed Instance
description: Azure SQL Database y la Instancia administrada de Azure SQL crean automáticamente una copia de seguridad local de la base de datos cada pocos minutos y usan almacenamiento con redundancia geográfica con acceso de lectura de Azure para proporcionar redundancia geográfica.
services: sql-database
ms.service: sql-db-mi
ms.subservice: backup-restore
ms.custom: references_regions, devx-track-azurepowershell
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: mathoma, wiassaf, danil
ms.date: 08/28/2021
ms.openlocfilehash: 2a6213a0359daf58d0ef34986d1bf3edbd4e1c9a
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/03/2021
ms.locfileid: "123424424"
---
# <a name="automated-backups---azure-sql-database--azure-sql-managed-instance"></a>Copias de seguridad automatizadas: Azure SQL Database y Azure SQL Managed Instance

[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="what-is-a-database-backup"></a>¿Qué es una copia de seguridad de base de datos?

Las copias de seguridad de base de datos son una parte esencial de cualquier estrategia de continuidad empresarial y recuperación ante desastres, ya que protegen los datos de daños o eliminaciones. Estas copias de seguridad habilitan la restauración de la base de datos a un momento dado dentro del período de retención configurado. Si las reglas de protección de datos exigen que las copias de seguridad estén disponibles durante un tiempo prolongado (hasta 10 años), puede configurar la [retención a largo plazo](long-term-retention-overview.md) para bases de datos únicas y agrupadas.

### <a name="backup-frequency"></a>Frecuencia de copia de seguridad

Tanto SQL Database como SQL Managed Instance usan tecnología de SQL Server para crear [copias de seguridad completas](/sql/relational-databases/backup-restore/full-database-backups-sql-server) cada semana, [copias de seguridad diferenciales](/sql/relational-databases/backup-restore/differential-backups-sql-server) cada 12 o 24 horas y [copias de seguridad del registro de transacciones](/sql/relational-databases/backup-restore/transaction-log-backups-sql-server) cada 5 o 10 minutos. La frecuencia de las copias de seguridad del registro de transacciones se basa en el tamaño de proceso y en la cantidad de actividad de la base de datos.

Cuando una base de datos se restaura, el servicio averigua qué copia de seguridad completa, diferencial o del registro de transacciones es necesario restaurar.

### <a name="backup-storage-redundancy"></a>Redundancia del almacenamiento de copia de seguridad

De forma predeterminada, SQL Database y SQL Managed Instance almacenan datos en [blobs de almacenamiento](../../storage/common/storage-redundancy.md) de redundancia geográfica que se replican en una [región emparejada](../../best-practices-availability-paired-regions.md). La redundancia geográfica ayuda a proteger frente a interrupciones que afectan al almacenamiento de copia de seguridad en la región primaria y permiten restaurar el servidor en una región diferente en caso de desastre. 

La opción para configurar la redundancia del almacenamiento de copia de seguridad ofrece la flexibilidad de elegir entre blobs de almacenamiento con redundancia local, con redundancia de zona o con redundancia geográfica. Para asegurarse de que los datos permanecen dentro de la misma región donde está implementada la instancia administrada o la base de datos SQL, puede cambiar la opción predeterminada de almacenamiento de copia de seguridad con redundancia geográfica y configurar blobs de almacenamiento con redundancia local o con redundancia de zona para las copias de seguridad. Los mecanismos de redundancia de Storage almacenan varias copias de los datos, con el fin de protegerlos de eventos planeados y no planeados, como errores transitorios del hardware, interrupciones del suministro eléctrico o cortes de la red, y desastres naturales masivos. La redundancia de copia de seguridad establecida se aplica a la configuración de retención de la copia de seguridad a corto plazo que se usa para la restauración a un momento dado (PITR) y la configuración de retención a largo plazo que se usa para copias de seguridad a largo plazo (LTR). 

En el caso de una instancia de SQL Database, la redundancia del almacenamiento de copia de seguridad se puede configurar en el momento de crear la base de datos, o se puede actualizar para una base de datos existente; los cambios realizados en una base de datos existente solo se aplican a las copias de seguridad futuras. Una vez actualizada la redundancia del almacenamiento de copia de seguridad de una base de datos existente, los cambios pueden tardar hasta 48 horas en aplicarse. La restauración geográfica se deshabilita en cuanto se actualiza una base de datos para usar almacenamiento con redundancia local o de zona. 


> [!IMPORTANT]
> La redundancia de almacenamiento de copia de seguridad para Hiperescala y SQL Managed Instance solo se puede establecer durante la creación de la base de datos. Esta configuración no se puede modificar una vez aprovisionado el recurso. El proceso de [copia de la base de datos](database-copy.md) se puede usar para actualizar la configuración de redundancia del almacenamiento de copia de seguridad de una base de datos de Hiperescala existente. 

> [!IMPORTANT]
> El almacenamiento con redundancia de zona solo está disponible actualmente en [determinadas regiones](../../storage/common/storage-redundancy.md#zone-redundant-storage). 

> [!NOTE]
> La redundancia del almacenamiento de copia de seguridad configurable de Azure SQL Database está disponible de momento en versión preliminar pública en todas las regiones de Azure y con carácter general solo en la región Sudeste de Asia de Azure. 

### <a name="backup-usage"></a>Uso de copia de seguridad

Puede utilizar estas copias de seguridad para realizar lo siguiente:

- **Restauración a un momento dado de una base de datos existente** - [Restaurar una base de datos existente a un momento en el tiempo pasado](recovery-using-backups.md#point-in-time-restore) dentro del período de retención mediante Azure Portal, Azure PowerShell, la CLI de Azure o una API REST. En el caso de SQL Database, esta operación crea una base de datos en el mismo servidor que la original, pero usa otro nombre para evitar sobrescribirla. Una vez finalizada la restauración, puede eliminar la base de datos original. Como alternativa, puede [cambiar el nombre](/sql/relational-databases/databases/rename-a-database) de la base de datos original y, luego, de la base de datos restaurada para que tenga el nombre de la original. De igual forma, en SQL Managed Instance esta operación crea una copia de la base de datos en la misma instancia administrada (o en otra) en la misma suscripción y región.
- **Restauración a un momento dado de una base de datos eliminada** - [Restaurar una base de datos eliminada al momento de su eliminación](recovery-using-backups.md#deleted-database-restore) o a cualquier otro punto dentro del período de retención. La base de datos eliminada solo se puede restaurar en el mismo servidor o instancia administrada donde se ha creado la base de datos original. Al eliminar una base de datos, el servicio toma una copia de seguridad del registro de transacciones final antes de la eliminación, para evitar la pérdida de datos.
- **Restauración geográfica** - [Restaurar una base de datos en otra región geográfica](recovery-using-backups.md#geo-restore). La restauración geográfica le permite recuperarse de un desastre en una región geográfica cuando no puede acceder a la base de datos o a las copias de seguridad en la región primaria. Crea una base de datos en cualquier servidor o instancia administrada existente, en cualquier región de Azure.
   > [!IMPORTANT]
   > La restauración geográfica solo está disponible para bases de datos o instancias administradas SQL configuradas con almacenamiento de copia de seguridad con redundancia geográfica.
- **Restauración desde una copia de seguridad a largo plazo** - [Restaurar una base de datos desde una copia de seguridad a largo plazo específica](long-term-retention-overview.md) de una base de datos única o agrupada, si la base de datos se ha configurado con una directiva de retención a largo plazo (LTR). LTR permite restaurar una versión antigua de la base de datos con [Azure Portal](long-term-backup-retention-configure.md#using-the-azure-portal) o [Azure PowerShell](long-term-backup-retention-configure.md#using-powershell) para respetar una solicitud de cumplimiento o ejecutar una versión antigua de la aplicación. Para más información, consulte [retención a largo plazo](long-term-retention-overview.md).

> [!NOTE]
> En Azure Storage, el término *replicación* hace referencia a la copia de blobs desde una ubicación a otra. En SQL, la *replicación de base de datos* se refiere a las distintas tecnologías que se usan para mantener varias bases de datos secundarias sincronizadas con una base de datos principal.

### <a name="restore-capabilities-and-features-of-azure-sql-database-and-azure-sql-managed-instance"></a><a id="restore-capabilities"></a>Funcionalidades y características de restauración de Azure SQL Database y Azure SQL Managed Instance

En esta tabla se resumen las funcionalidades y características de la [restauración a un momento dado (PITR)](recovery-using-backups.md#point-in-time-restore), la [restauración geográfica](recovery-using-backups.md#geo-restore) y las [copias de seguridad de retención a largo plazo](long-term-retention-overview.md).

| **Propiedades de copia de seguridad** | Recuperación a un momento dado (PITR) | Geo-restore | Restauración de copias de seguridad a largo plazo |           
|----|--|--|--|
| **Tipos de copia de seguridad de SQL** | Completa, diferencial y registro | Copias replicadas de copias de seguridad PITR | Solo copias de seguridad completas | 
| **Objetivo de punto de recuperación (RPO)** |  De 5 a 10 minutos, en función del tamaño del proceso y la cantidad de actividad de la base de datos. | Hasta 1 hora, en función de la replicación geográfica.\*  |  Una semana (o directiva del usuario).|
| **Objetivo de tiempo de recuperación (RTO)** | La restauración suele tardar más de 12 horas, pero podría tardar más en función del tamaño y la actividad. Consulte [Recuperación](recovery-using-backups.md#recovery-time). | La restauración suele tardar más de 12 horas, pero podría tardar más en función del tamaño y la actividad. Consulte [Recuperación](recovery-using-backups.md#recovery-time). | La restauración suele tardar más de 12 horas, pero podría tardar más en función del tamaño y la actividad. Consulte [Recuperación](recovery-using-backups.md#recovery-time). | 
| **Retención** | 7 días de forma predeterminada, hasta 35 días |  Se habilita de forma predeterminada, igual que el origen.\*\* | No se habilita de forma predeterminada, retención hasta 10 años. |     
| **Almacenamiento de Azure**  | Con redundancia de zona de forma predeterminada. Opcionalmente, se puede configurar el almacenamiento con redundancia local o de zona. | Disponible cuando la redundancia del almacenamiento de copia de seguridad PITR está establecida en Con redundancia geográfica. No disponible cuando el almacén de copia de seguridad PITR es almacenamiento de zona o con redundancia local. | Con redundancia de zona de forma predeterminada. Se puede configurar el almacenamiento con redundancia local o de zona. | 
| **Use to create new database in same region** (Usar para crear una base de datos en la misma región) | Compatible | Compatible | Compatible |
| **Use to create new database in another region** (Usar para crear una base de datos en otra región) | No compatible | Compatible con cualquier región de Azure | Compatible con cualquier región de Azure |
| **Use to create new database in another Subscription** (Usar para crear una base de datos en otra suscripción) |  No compatible  |  No compatible\*\*\* | No compatible\*\*\*  | 
| **Restore via Azure portal** (Restaurar mediante Azure Portal)|Sí|Sí|Sí|
| **Restore via PowerShell** (Restaurar mediante PowerShell) |Sí|Sí|Sí| 
| **Restore via Azure CLI** (Restaurar mediante la CLI de Azure) |Sí|Sí|Sí| 
| | | | |

\*En el caso de las aplicaciones críticas para la empresa que necesitan bases de datos grandes y deben garantizar la continuidad empresarial, use [grupos de conmutación por error automática](auto-failover-group-overview.md). 

\*\* Todas las copias de seguridad PITR se almacenan de forma predeterminada en el almacenamiento con redundancia geográfica. Por lo tanto, la restauración geográfica está habilitada de forma predeterminada. 

\*\*\* La solución consiste en realizar la restauración en un nuevo servidor y usar el movimiento de recursos para mover el servidor a otra suscripción.

### <a name="restoring-a-database-from-backups"></a>Restauración de una base de datos de las copias de seguridad 

Para llevar a cabo una restauración, vea [Recuperación de una base de datos mediante copias de seguridad](recovery-using-backups.md). Puede probar las operaciones de configuración de copia de seguridad y restauración con los ejemplos siguientes:

| Operación | Azure portal | Azure PowerShell |
|---|---|---|
| **Cambio del período de retención de copia de seguridad** | [SQL Database](#change-the-short-term-retention-policy-using-the-azure-portal) <br/> [Instancia administrada de SQL](#change-the-short-term-retention-policy-using-the-azure-portal) | [SQL Database](#change-the-short-term-retention-policy-using-powershell) <br/>[Instancia administrada de SQL](#change-the-short-term-retention-policy-using-powershell) |
| **Cambio del período de retención de copia de seguridad a largo plazo** | [SQL Database](long-term-backup-retention-configure.md#configure-long-term-retention-policies)<br/> [Instancia administrada de SQL](../managed-instance/long-term-backup-retention-configure.md#using-the-azure-portal) | [SQL Database](long-term-backup-retention-configure.md)<br/>[Instancia administrada de SQL](../managed-instance/long-term-backup-retention-configure.md#using-powershell)  |
| **Restauración de una base de datos desde un momento dado** | [SQL Database](recovery-using-backups.md#point-in-time-restore)<br>[Instancia administrada de SQL](../managed-instance/point-in-time-restore.md) | [SQL Database](/powershell/module/az.sql/restore-azsqldatabase) <br/> [Instancia administrada de SQL](/powershell/module/az.sql/restore-azsqlinstancedatabase) |
| **Restauración de una base de datos eliminada** | [SQL Database](recovery-using-backups.md)<br>[Instancia administrada de SQL](../managed-instance/point-in-time-restore.md#restore-a-deleted-database) | [SQL Database](/powershell/module/az.sql/get-azsqldeleteddatabasebackup) <br/> [Instancia administrada de SQL](/powershell/module/az.sql/get-azsqldeletedinstancedatabasebackup)|
| **Restauración de una base de datos desde Azure Blob Storage** | SQL Database: N/D <br/>SQL Managed Instance: N/D  | SQL Database: N/D <br/>[Instancia administrada de SQL](../managed-instance/restore-sample-database-quickstart.md) |

## <a name="backup-scheduling"></a>Programación de copias de seguridad

La primera copia de seguridad completa se programa inmediatamente después de la creación o restauración de una base de datos. Normalmente, esta copia de seguridad se completa en 30 minutos, pero puede tardar más si la base de datos es grande. Por ejemplo, la copia de seguridad inicial puede tardar más en una base de datos restaurada o una copia de la base de datos, que normalmente será mayor que una base de datos nueva. Después de la primera copia de seguridad completa, todas las demás se programan y administran de forma automática. El servicio SQL Database o SQL Managed Instance determina el momento exacto en el que se producen todas las copias de seguridad de la base de datos a medida que se equilibra la carga de trabajo global del sistema. No se puede cambiar la programación de trabajos de copia de seguridad ni deshabilitarlos.

> [!IMPORTANT]
> En una base de datos nueva, restaurada o copiada, la funcionalidad de restauración a un momento dado está disponible desde el momento en el que se crea la copia de seguridad del registro de transacciones inicial que sigue a la copia de seguridad completa inicial.

## <a name="backup-storage-consumption"></a>Consumo del almacenamiento de copia de seguridad

Con la tecnología de copia de seguridad y restauración de SQL Server, la restauración de una base de datos a un momento dado requiere una cadena de copia de seguridad ininterrumpida que conste de una copia de seguridad completa, una copia de seguridad diferencial (opcional) y una o varias copias de seguridad del registro de transacciones. La programación de copias de seguridad de SQL Database y SQL Managed Instance incluye una copia de seguridad completa cada semana. Por tanto, para proporcionar PITR en todo el período de retención, el sistema debe almacenar copias de seguridad completas, diferenciales y del registro de transacciones durante una semana más transcurrido el período de retención configurado. 

Es decir, para cualquier punto en el tiempo durante el período de retención, debe haber una copia de seguridad completa que sea anterior a la hora más antigua del período de retención, así como una cadena ininterrumpida de copias de seguridad diferenciales y del registro de transacciones desde esa copia de seguridad completa hasta la siguiente.

> [!NOTE]
> Para proporcionar PITR, las copias de seguridad adicionales se almacenan hasta una semana más que el período de retención configurado. El almacenamiento de copia de seguridad se cobra a la misma tarifa para todas las copias de seguridad. 

Las copias de seguridad que ya no sean necesarias para proporcionar la funcionalidad PITR se eliminan automáticamente. Como las copias de seguridad diferenciales y las del registro necesitan una copia de seguridad completa anterior para que se puedan restaurar, los tres tipos de copia de seguridad se depuran en conjuntos semanales.

Para todas las bases de datos, incluidas las [cifradas con TDE](transparent-data-encryption-tde-overview.md), las copias de seguridad se comprimen para reducir los costos y la compresión del almacenamiento de copia de seguridad. La relación promedio de compresión de copia de seguridad es de entre 3 y 4 veces, pero puede ser significativamente menor o mayor, en función de la naturaleza de los datos y de si se usa la compresión de datos en la base de datos.

SQL Database y SQL Managed Instance calculan el almacenamiento de copia de seguridad total usado como un valor acumulativo. Cada hora, este valor se envía a la canalización de facturación de Azure, que se encarga de agregar este uso por hora para calcular el consumo al final de cada mes. Después de eliminar la base de datos, el consumo disminuye a medida que las copias de seguridad caducan y se eliminan. Una vez que se han eliminado todas las copias de seguridad y PITR ya no es posible, se detiene la facturación.
   
> [!IMPORTANT]
> Las copias de seguridad de una base de datos se conservan para proporcionar PITR, aunque la base de datos se haya eliminado. A pesar de que eliminar y volver a crear una base de datos puede ahorrar costos de almacenamiento y proceso, puede aumentar los costos de almacenamiento de copias de seguridad, ya que el servicio conserva las copias de seguridad de cada base de datos eliminada, cada vez que se elimina. 

### <a name="monitor-consumption"></a>Supervisión del consumo

En el caso de las bases de datos de núcleo virtual, el almacenamiento que consume cada tipo de copia de seguridad (completa, diferencial y de registro) se notifica en la hoja de supervisión de la base de datos como una métrica independiente. En este diagrama se muestra cómo supervisar el consumo de almacenamiento de copia de seguridad para una única base de datos. Esta característica no está disponible actualmente para las instancias administradas.

![Supervisión del consumo de copia de seguridad de base de datos en Azure Portal](./media/automated-backups-overview/backup-metrics.png)

### <a name="fine-tune-backup-storage-consumption"></a>Ajuste del consumo del almacenamiento de copia de seguridad

No se cobra el consumo de almacenamiento de copia de seguridad hasta el tamaño máximo de los datos de una base de datos. El exceso de consumo del almacenamiento de copia de seguridad dependerá de la carga de trabajo y del tamaño máximo de las bases de datos individuales. Considere la posibilidad de poner en marcha algunas de las siguientes técnicas de optimización para reducir el consumo de almacenamiento de copia de seguridad:

- Reduzca el [período de retención de copias de seguridad](#change-the-short-term-retention-policy-using-the-azure-portal) al mínimo posible para sus necesidades.
- Evite realizar operaciones de escritura de gran tamaño (como recompilaciones de índices) con más frecuencia de la debida.
- En el caso de las operaciones de carga de datos de gran tamaño, considere la posibilidad de usar [índices de almacén de columnas agrupados](/sql/relational-databases/indexes/columnstore-indexes-overview), siga los [procedimientos recomendados](/sql/relational-databases/indexes/columnstore-indexes-data-loading-guidance) relacionados, o bien reduzca el número de índices no agrupados.
- En el nivel de servicio De uso general, el almacenamiento de datos aprovisionado es más económico que el precio del almacenamiento de copia de seguridad. Si los costos de exceso de almacenamiento de copia de seguridad siempre son elevados, le conviene valorar aumentar el almacenamiento de datos para ahorrar en almacenamiento de copia de seguridad.
- En la lógica de aplicación, use TempDB en lugar de tablas permanentes para almacenar resultados temporales o datos transitorios.
- Use el almacenamiento de copia de seguridad con redundancia local siempre que sea posible (por ejemplo, en entornos de desarrollo y pruebas).

## <a name="backup-retention"></a>Retención de copias de seguridad

Azure SQL Database y Azure SQL Managed Instance proporciona retención de copias de seguridad a corto y largo plazo. La retención de copias de seguridad a corto plazo permite la restauración a un momento dado (PITR) en el período de retención de la base de datos, mientras que la retención a largo plazo proporciona copias de seguridad para varios requisitos de cumplimiento.  

### <a name="short-term-retention"></a>Retención a corto plazo

En el caso de todas las bases de datos nuevas, restauradas y copiadas, Azure SQL Database y Azure SQL Managed Instance conservan copias de seguridad suficientes para permitir PITR de forma predeterminada en los últimos siete días. Se toman copias de seguridad completas, diferenciales y de registro periódicas para garantizar que las bases de datos se pueden restaurar a cualquier momento dado dentro del período de retención definido para la base de datos o la instancia administrada. Además, para las bases de datos de Azure SQL, las copias de seguridad diferenciales se pueden configurar para una frecuencia de 12 horas (valor predeterminado) o de 24 horas. 

> [!NOTE]
> Una frecuencia de copia de seguridad diferencial de 24 horas puede aumentar el tiempo necesario para restaurar la base de datos. 

A excepción de las bases de datos de nivel básico y de hiperescala, puede [cambiar el período de retención de la copia de seguridad](#change-the-short-term-retention-policy) por cada base de datos activa en el intervalo de 1 a 35 días. Como se describe en [Consumo de almacenamiento de copia de seguridad](#backup-storage-consumption), las copias de seguridad almacenadas para habilitar PITR pueden ser anteriores al período de retención. Solo para Azure SQL Managed Instance, es posible establecer la tasa de retención de copia de seguridad de recuperación a un momento dado una vez que se haya eliminado una base de datos en el intervalo de 0 a 35 días. 

Si elimina una base de datos, el sistema conserva las copias de seguridad de la misma manera que en una base de datos en línea con su período de retención específico. No se puede cambiar el período de retención de copia de seguridad de una base de datos eliminada.

> [!IMPORTANT]
> Si elimina un servidor o una instancia administrada, también se eliminan todas las bases de datos que contenga y no se pueden recuperar. No se puede restaurar un servidor o una instancia administrada que se haya eliminado. Pero si ha configurado la retención a largo plazo (LTR) para una base de datos o una instancia administrada, las copias de seguridad de retención a largo plazo no se eliminan y se pueden usar para restaurar bases de datos en otro servidor o instancia administrada de la misma suscripción, a un momento dado en el que se haya realizado una copia de seguridad de retención a largo plazo.

La retención de copias de seguridad para fines de PITR durante el período de los últimos 1 a 35 días se denomina a veces retención de copias de seguridad a corto plazo. Si tiene que mantener las copias de seguridad durante más tiempo del período de retención a corto plazo máximo de 35 días, puede habilitar la [retención a largo plazo](long-term-retention-overview.md).

### <a name="long-term-retention"></a>Retención a largo plazo

En el caso de SQL Database y SQL Managed Instance, se puede configurar una retención a largo plazo (LTR) de las copias de seguridad completas de hasta 10 años en Azure Blob Storage. Después de configurar la directiva LTR, las copias de seguridad completas se copian de forma automática en otro contenedor de almacenamiento semanalmente. Para satisfacer los distintos requisitos de cumplimiento, puede seleccionar otros períodos de retención para copias de seguridad completas semanales, mensuales o anuales. El consumo de almacenamiento depende de la frecuencia seleccionada y de los pe.ríodos de retención de las copias de seguridad de LTR. Para estimar el costo del almacenamiento de LTR, se puede usar la [calculadora de precios de LTR](https://azure.microsoft.com/pricing/calculator/?service=sql-database).

> [!IMPORTANT]
> La actualización de la redundancia del almacenamiento de copia de seguridad de una instancia de Azure SQL Database existente solo se aplica a las copias de seguridad futuras que se realicen de la base de datos. Las copias de seguridad de LTR de la base de datos existentes seguirán residiendo en el blob de almacenamiento existente; las nuevas copias de seguridad se almacenarán en el tipo de blob de almacenamiento solicitado. 

Para más información sobre LTR, vea [Retención de copias de seguridad a largo plazo](long-term-retention-overview.md).

## <a name="backup-storage-costs"></a>Costos de almacenamiento de copia de seguridad

El precio del almacenamiento de copia de seguridad varía y depende del modelo de compra (DTU o núcleo virtual), la opción de redundancia del almacenamiento de copia de seguridad elegida y también de su región. El almacenamiento de copia de seguridad se cobra por GB consumidos al mes. Para ver los precios, consulte la página de [Precios de Azure SQL Database](https://azure.microsoft.com/pricing/details/sql-database/single/) y la página de [Precios de Azure SQL Managed Instance](https://azure.microsoft.com/pricing/details/azure-sql/sql-managed-instance/single/). 

Para más información sobre los modelos de compra, consulte [Elección entre los modelos de compra de núcleo virtual y DTU](purchasing-models.md).

> [!NOTE]
> La factura de Azure solo mostrará el exceso de almacenamiento de copia de seguridad consumido, no todo el consumo de almacenamiento de copia de seguridad. Por ejemplo, en un escenario hipotético, si ha aprovisionado 4 TB de almacenamiento de datos, obtendrá 4 TB de espacio de almacenamiento de copia de seguridad disponible. En caso de que haya usado el total de 5,8 TB de espacio de almacenamiento de copia de seguridad, la factura de Azure solo mostrará 1,8 TB, ya que solo se cobra el exceso de almacenamiento de copia de seguridad.

### <a name="dtu-model"></a>Modelo de DTU

En el modelo de DTU, no hay ningún cargo adicional por el almacenamiento de copia de seguridad para bases de datos y grupos elásticos. El precio del almacenamiento de copia de seguridad forma parte del de la base de datos o del grupo.

### <a name="vcore-model"></a>Modelo de núcleos virtuales

En el caso de las bases de datos únicas en SQL Database, se ofrece una cantidad de almacenamiento de copia de seguridad igual al 100 % del tamaño de almacenamiento de datos máximo de la base de datos sin costo adicional. Para grupos elásticos e instancias administradas, se proporciona una cantidad de almacenamiento de copia de seguridad igual al 100 % del almacenamiento de datos máximo para el grupo o del tamaño de almacenamiento de instancia máximo, respectivamente, sin costo adicional. 

En el caso de las bases de datos únicas, esta ecuación sirve para calcular el uso de almacenamiento de copia de seguridad facturable total:

`Total billable backup storage size = (size of full backups + size of differential backups + size of log backups) – maximum data storage`

En el caso de las bases de datos agrupadas, el tamaño total del almacenamiento de copia de seguridad facturable se agrega en el nivel de grupo y se calcula de la siguiente manera:

`Total billable backup storage size = (total size of all full backups + total size of all differential backups + total size of all log backups) - maximum pool data storage`

En el caso de las instancias administradas, el tamaño total del almacenamiento de copia de seguridad facturable se agrega en el nivel de instancia y se calcula de la siguiente manera:

`Total billable backup storage size = (total size of full backups + total size of differential backups + total size of log backups) – maximum instance data storage`

El almacenamiento de copia de seguridad facturable total, si existe, se cobrará en GB/mes según la tarifa de la redundancia de almacenamiento de copia de seguridad utilizada. Este consumo del almacenamiento de copia de seguridad dependerá de la carga de trabajo y del tamaño de las bases de datos individuales, los grupos elásticos y las instancias administradas. Las bases de datos que se modifiquen con mucha frecuencia tienen copias de seguridad diferenciales y de registro mayores, ya que su tamaño es proporcional a la cantidad de cambios en los datos. Por tanto, estas bases de datos tendrán mayores cargos de copia de seguridad.

SQL Database y SQL Managed Instance calculan el almacenamiento de copia de seguridad facturable total como un valor acumulativo entre todos los archivos de copia de seguridad. Cada hora, este valor se notifica a la canalización de facturación de Azure, que lo suma para obtener el consumo de almacenamiento de copia de seguridad al final de cada mes. Si se elimina una base de datos, el consumo de almacenamiento de copia de seguridad disminuirá gradualmente a medida que las copias de seguridad antiguas expiren y se eliminen. Como las copias de seguridad diferenciales y las del registro necesitan una copia de seguridad completa anterior para que se puedan restaurar, los tres tipos de copia de seguridad se depuran en conjuntos semanales. Una vez que se hayan eliminado todas las copias de seguridad, se detiene la facturación. 

Como ejemplo simplificado, imagine que una base de datos ha acumulado 744 GB de almacenamiento de copia de seguridad y esta cantidad permanece constante durante todo un mes porque la base de datos está inactiva. Para convertir este consumo de almacenamiento acumulativo en un uso por hora, lo dividimos entre 744,0 (31 días al mes x 24 horas al día). SQL Database notificará a la canalización de facturación de Azure que la base de datos ha consumido 1 GB de copia de seguridad PITR cada hora, a una velocidad constante. La facturación de Azure sumará este consumo y reflejará un uso de 744 GB durante todo el mes. El costo se basará en la tarifa de cantidad/GB/mes de la región.

Veamos ahora un ejemplo más complejo. Imagine que la misma base de datos inactiva ha aumentado su retención de 7 a 14 días a mediados de mes. Este aumento hace que el almacenamiento de copia de seguridad total se duplique hasta llegar a 1488 GB. SQL Database notificaría un uso de 1 GB para las horas 1 a 372 (la primera mitad del mes). Notificaría un uso de 2 GB para las horas 373 a 744 (la segunda mitad del mes). Este uso se sumaría a una factura final de 1116 GB/mes.

Los escenarios reales de facturación de copia de seguridad son más complejos. Como la tasa de cambios en la base de datos depende de la carga de trabajo y varía con el tiempo, el tamaño de cada copia de seguridad diferencial y de registros también variará, lo que provocará que el consumo de almacenamiento de copia de seguridad por hora varíe en consecuencia. Además, cada copia de seguridad diferencial contiene todos los cambios realizados en la base de datos desde la última copia de seguridad completa, por lo que el tamaño total de todas las copias de seguridad diferenciales aumenta gradualmente durante el transcurso de una semana y, después, se reduce de manera abrupta cuando expira un conjunto más antiguo de copias de seguridad completas, diferenciales y del registro. Por ejemplo, si se ha ejecutado un actividad de escritura intensiva como la recompilación de índices justo después de finalizar una copia de seguridad completa, las modificaciones realizadas por la recompilación del índice se incluirán en las copias de seguridad del registro de transacciones realizadas durante la recompilación, en la siguiente copia de seguridad diferencial y en todas las copias de seguridad diferenciales que se realicen hasta la siguiente copia de seguridad completa. Para el segundo escenario en bases de datos de mayor tamaño, una optimización en el servicio crea una copia de seguridad completa en lugar de una diferencial si una copia de seguridad diferencial tuviera un tamaño excesivo. Esto reduce el tamaño de todas las copias de seguridad diferenciales hasta la siguiente copia de seguridad completa.

Puede supervisar el consumo total de almacenamiento de copia de seguridad para cada tipo de copia de seguridad (completa, diferencial, del registro de transacciones) en el tiempo, como se describe en [Supervisión del consumo](#monitor-consumption).

### <a name="backup-storage-redundancy"></a>Redundancia del almacenamiento de copia de seguridad

La redundancia del almacenamiento de copia de seguridad afecta a los costos de copia de seguridad de la siguiente manera:
- precio con redundancia local = x
- precio con redundancia de zona = 1,25x
- precio con redundancia geográfica = 2x

Para más información sobre los precios de almacenamiento de copia de seguridad, visite la página de [Precios de Azure SQL Database](https://azure.microsoft.com/pricing/details/sql-database/single/) y la página de [Precios de Azure SQL Managed Instance](https://azure.microsoft.com/pricing/details/azure-sql/sql-managed-instance/single/).

> [!IMPORTANT]
> La redundancia del almacenamiento de copia de seguridad configurable para SQL Managed Instance está disponible en todas las regiones de Azure; para SQL Database actualmente solo está disponible en la región Sudeste de Asia. En SQL Managed Instance, solo se puede especificar durante el proceso de creación de la instancia administrada. Una vez que se ha aprovisionado el recurso, no se puede cambiar la opción de redundancia del almacenamiento de copia de seguridad.

### <a name="monitor-costs"></a>Supervisión de costos

Para comprender los costos de almacenamiento de copia de seguridad, vaya a **Administración de costos + facturación** en Azure Portal, seleccione **Administración de costos** y, después, seleccione **Análisis de costos**. Seleccione la suscripción deseada como **ámbito** y, a continuación, filtre por el período de tiempo y el servicio que le interese.

Agregue un filtro para el **nombre de servicio** y, después, seleccione **Base de datos SQL** en la lista desplegable. Use el filtro de **subcategoría del medidor** para elegir el contador de facturación para el servicio. En el caso de una sola base de datos o de un grupo de bases de datos elásticas, seleccione **single/elastic pool PITR backup storage** (almacenamiento de copia de seguridad de recuperación a un momento dado de base de datos única/grupo de bases de datos elásticas). En el caso de una instancia administrada, seleccione **mi PITR backup storage** (almacenamiento de copia de seguridad de recuperación a un momento dado de instancia administrada). Las subcategorías **Almacenamiento** y **Proceso** pueden interesarle también, pero no están asociadas con los costos de almacenamiento de copia de seguridad.

![Análisis de costos del almacenamiento de copia de seguridad](./media/automated-backups-overview/check-backup-storage-cost-sql-mi.png)

  >[!NOTE]
  > Los medidores solo son visibles para los contadores que están en uso actualmente. Si un contador no está disponible, es probable que la categoría no se esté usando actualmente. Por ejemplo, los contadores de instancias administradas no estarán presentes para los clientes que no tengan implementada una instancia administrada. Del mismo modo, los contadores de almacenamiento no estarán visibles para los recursos que no consuman almacenamiento. 

Para más información, consulte [Administración de costos de Azure SQL Database](cost-management.md).

## <a name="encrypted-backups"></a>Copias de seguridad cifradas

Si la base de datos se cifra con TDE, las copias de seguridad se cifran de forma automática en reposo, incluidas las copias de seguridad LTR. Todas las bases de datos nuevas de Azure SQL están configuradas con TDE habilitado de forma predeterminada. Para obtener más información sobre TDE, vea [Cifrado de datos transparente con SQL Database y la Instancia administrada de SQL](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql).

## <a name="backup-integrity"></a>Integridad de copia de seguridad

El equipo de ingeniería de Azure SQL prueba permanentemente y de manera automática la restauración de las copias de seguridad de bases de datos automatizadas. (Estas pruebas no están disponibles actualmente en SQL Managed Instance. Debe programar DBCC CHECKDB en las bases de datos de SQL Managed Instance programadas en torno a la carga de trabajo).

Tras la restauración a un momento dado, las bases de datos también reciben comprobaciones de integridad de DBCC CHECKDB.

Los problemas encontrados durante la comprobación de integridad producen una alerta para el equipo de ingeniería. Para más información, consulte [Integridad de datos en SQL Database](https://azure.microsoft.com/blog/data-integrity-in-azure-sql-database/).

Todas las copias de seguridad de base de datos se llevan a cabo con la opción CHECKSUM para proporcionar una mayor integridad de copia de seguridad.

## <a name="compliance"></a>Cumplimiento normativo

Al migrar la base de datos de un nivel de servicio basado en DTU a un nivel de servicio basado en núcleos virtuales, se conserva la retención PITR para garantizar que la directiva de recuperación de datos de la aplicación no se ponga en peligro. Si el período de retención predeterminado no satisface los requisitos de cumplimiento, puede cambiar el período de retención PITR. Para más información, vea [Cambio del período de retención de copia de seguridad PITR](#change-the-short-term-retention-policy).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="change-the-short-term-retention-policy"></a>Cambio de la directiva de retención a corto plazo

Puede cambiar el período de retención predeterminado de copia de seguridad PITR y la frecuencia de copia de seguridad diferencial usando Azure Portal, PowerShell o la API de REST. En los siguientes ejemplos se muestra cómo cambiar la retención PITR a 28 días y las copias de seguridad diferenciales a un intervalo de 24 horas.

> [!WARNING]
> Si reduce el período de retención actual, perderá la capacidad de restaurar a puntos en el tiempo más antiguos que el nuevo período de retención. Se eliminan las copias de seguridad que ya no son necesarias para proporcionar PITR dentro del nuevo período de retención. Si aumenta el período de retención actual, no obtendrá de forma inmediata la capacidad de restaurar a puntos en el tiempo más antiguos dentro del nuevo período de retención. Obtiene esa capacidad a lo largo del tiempo, ya que el sistema empieza a conservar las copias de seguridad durante más tiempo.

> [!NOTE]
> Estas API afectan únicamente al período de retención PITR. Si ha configurado LTR para la base de datos, no se verá afectada. Para más información sobre cómo cambiar los períodos de retención LTR, vea [Retención de copias de seguridad a largo plazo](long-term-retention-overview.md).

### <a name="change-the-short-term-retention-policy-using-the-azure-portal"></a>Cambiar la directiva de retención a corto plazo mediante Azure Portal

Para cambiar el período de retención de copia de seguridad PITR o la frecuencia copia de seguridad diferencial para bases de datos activas mediante Azure Portal, vaya al servidor o a la instancia administrada con las bases de datos cuyo período de retención quiera cambiar. Seleccione **Copias de seguridad** en el panel izquierdo y, luego, elija la pestaña **Directivas de retención**. Seleccione las bases de datos en las que quiere cambiar la retención de copia de seguridad de la recuperación a un momento dado. Luego, seleccione **Configurar retención** en la barra de acciones.

#### <a name="sql-database"></a>[SQL Database](#tab/single-database)

![Cambio de retención PITR, nivel de servidor](./media/automated-backups-overview/configure-backup-retention-sqldb.png)

#### <a name="sql-managed-instance"></a>[Instancia administrada de SQL](#tab/managed-instance)

![Cambio de retención PITR, instancia administrada](./media/automated-backups-overview/configure-backup-retention-sqlmi.png)

---

### <a name="change-the-short-term-retention-policy-using-powershell"></a>Cambie la directiva de retención a corto plazo mediante PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
> [!IMPORTANT]
> El módulo AzureRM de PowerShell todavía es compatible con SQL Database y la Instancia administrada de SQL, pero todo el desarrollo futuro se realizará para el módulo Az.Sql. Para más información, vea [AzureRM.Sql](/powershell/module/AzureRM.Sql/). Los argumentos de los comandos del módulo Az son básicamente idénticos a los de los módulos AzureRm.

#### <a name="sql-database"></a>[SQL Database](#tab/single-database)

A fin de cambiar la retención de copia de seguridad de PITR y la frecuencia de copia de seguridad diferencial para las bases de datos de Azure SQL Database activas, use el siguiente ejemplo de PowerShell.

```powershell
# SET new PITR backup retention period on an active individual database
# Valid backup retention must be between 1 and 35 days
Set-AzSqlDatabaseBackupShortTermRetentionPolicy -ResourceGroupName resourceGroup -ServerName testserver -DatabaseName testDatabase -RetentionDays 28
```

```powershell
# SET new PITR differental backup frequency on an active individual database
# Valid differential backup frequency must be ether 12 or 24. 
Set-AzSqlDatabaseBackupShortTermRetentionPolicy -ResourceGroupName resourceGroup -ServerName testserver -DatabaseName testDatabase -RetentionDays 28 -DiffBackupIntervalInHours 24
```

#### <a name="sql-managed-instance"></a>[Instancia administrada de SQL](#tab/managed-instance)

Para cambiar la retención de copia de seguridad PITR de una base de datos **individual activa** en una instancia de SQL Managed Instance, use el ejemplo siguiente de PowerShell.

```powershell
# SET new PITR backup retention period on an active individual database
# Valid backup retention must be between 1 and 35 days
Set-AzSqlInstanceDatabaseBackupShortTermRetentionPolicy -ResourceGroupName resourceGroup -InstanceName testserver -DatabaseName testDatabase -RetentionDays 1
```

Para cambiar la retención de copia de seguridad PITR de las bases de datos de tipo **todas activas** en una instancia de SQL Managed Instance, use el ejemplo siguiente de PowerShell.

```powershell
# SET new PITR backup retention period for ALL active databases
# Valid backup retention must be between 1 and 35 days
Get-AzSqlInstanceDatabase -ResourceGroupName resourceGroup -InstanceName testserver | Set-AzSqlInstanceDatabaseBackupShortTermRetentionPolicy -RetentionDays 1
```

Para cambiar la retención de copia de seguridad PITR de una base de datos **individual eliminada** en una instancia de SQL Managed Instance, use el ejemplo siguiente de PowerShell.
 
```powershell
# SET new PITR backup retention on an individual deleted database
# Valid backup retention must be between 0 (no retention) and 35 days. Valid retention rate can only be lower than the period of the retention period when database was active, or remaining backup days of a deleted database.
Get-AzSqlDeletedInstanceDatabaseBackup -ResourceGroupName resourceGroup -InstanceName testserver -DatabaseName testDatabase | Set-AzSqlInstanceDatabaseBackupShortTermRetentionPolicy -RetentionDays 0
```

Para cambiar la retención de copia de seguridad PITR de las bases de datos de tipo **todas eliminadas** en una instancia de SQL Managed Instance, use el ejemplo siguiente de PowerShell.

```powershell
# SET new PITR backup retention for ALL deleted databases
# Valid backup retention must be between 0 (no retention) and 35 days. Valid retention rate can only be lower than the period of the retention period when database was active, or remaining backup days of a deleted database
Get-AzSqlDeletedInstanceDatabaseBackup -ResourceGroupName resourceGroup -InstanceName testserver | Set-AzSqlInstanceDatabaseBackupShortTermRetentionPolicy -RetentionDays 0
```

La retención de cero (0) días denotará que la copia de seguridad se elimina inmediatamente y ya no se conserva para una base de datos eliminada.
Una vez que se ha reducido la retención de copia de seguridad de recuperación a un momento dado para una base de datos eliminada, esta ya no se puede aumentar.

---

### <a name="change-the-short-term-retention-policy-using-the-rest-api"></a>Cambio de la directiva de retención a corto plazo mediante la API de REST

La siguiente solicitud actualiza el período de retención a 28 días y también establece la frecuencia de copia de seguridad diferencial en 24 horas.


#### <a name="sql-database"></a>[SQL Database](#tab/single-database)

#### <a name="sample-request"></a>Solicitud de ejemplo

```http
PUT https://management.azure.com/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/resourceGroup/providers/Microsoft.Sql/servers/testserver/databases/testDatabase/backupShortTermRetentionPolicies/default?api-version=2021-02-01-preview
```

#### <a name="request-body"></a>Cuerpo de la solicitud

```json
{ 
    "properties":{
        "retentionDays":28
        "diffBackupIntervalInHours":24
  }
}
```

#### <a name="sample-response"></a>Respuesta de ejemplo: 

```json
{ 
  "id": "/subscriptions/00000000-1111-2222-3333-444444444444/providers/Microsoft.Sql/resourceGroups/resourceGroup/servers/testserver/databases/testDatabase/backupShortTermRetentionPolicies/default",
  "name": "default",
  "type": "Microsoft.Sql/resourceGroups/servers/databases/backupShortTermRetentionPolicies",
  "properties": {
    "retentionDays": 28
    "diffBackupIntervalInHours":24
  }
}
```


Para más información, consulte [API REST de retención de Backup](/rest/api/sql/backupshorttermretentionpolicies).

#### <a name="sql-managed-instance"></a>[Instancia administrada de SQL](#tab/managed-instance)

#### <a name="sample-request"></a>Solicitud de ejemplo

```http
PUT https://management.azure.com/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/resourceGroup/providers/Microsoft.Sql/servers/testserver/databases/testDatabase/backupShortTermRetentionPolicies/default?api-version=2017-10-01-preview
```

#### <a name="request-body"></a>Cuerpo de la solicitud

```json
{
  "properties":{
    "retentionDays":28
  }
}
```

#### <a name="sample-response"></a>Respuesta de muestra

Código de estado: 200

```json
{
  "id": "/subscriptions/00000000-1111-2222-3333-444444444444/providers/Microsoft.Sql/resourceGroups/resourceGroup/servers/testserver/databases/testDatabase/backupShortTermRetentionPolicies/default",
  "name": "default",
  "type": "Microsoft.Sql/resourceGroups/servers/databases/backupShortTermRetentionPolicies",
  "properties": {
    "retentionDays": 28
  }
}
```

Para más información, consulte [API REST de retención de Backup](/rest/api/sql/backupshorttermretentionpolicies).

---

## <a name="configure-backup-storage-redundancy"></a>Configuración de la redundancia del almacenamiento de copia de seguridad

> [!NOTE]
> La redundancia de almacenamiento configurable para copias de seguridad de SQL Managed Instance solo se puede especificar durante el proceso de creación de la instancia administrada. Una vez que se ha aprovisionado el recurso, no se puede cambiar la opción de redundancia del almacenamiento de copia de seguridad. En el caso de SQL Database, la versión preliminar pública de esta característica está disponible de momento en todas las regiones de Azure y con carácter general en la región Sudeste de Asia de Azure. 

La redundancia del almacenamiento de copia de seguridad de una instancia administrada se puede configurar solo durante la creación de la instancia. En el caso de SQL Database se puede establecer al crear la base de datos o se puede actualizar para una base de datos existente. El valor predeterminado es el almacenamiento con redundancia geográfica. Para conocer las diferencias de precio entre el almacenamiento de copia de seguridad con redundancia local, con redundancia de zona y con redundancia geográfica, visite la [página de precios de Azure SQL Managed Instance](https://azure.microsoft.com/pricing/details/azure-sql/sql-managed-instance/single/).

### <a name="configure-backup-storage-redundancy-by-using-the-azure-portal"></a>Configuración de la redundancia del almacenamiento de copia de seguridad mediante Azure Portal

#### <a name="sql-database"></a>[SQL Database](#tab/single-database)

En Azure Portal, puede configurar la redundancia del almacenamiento de copia de seguridad en el panel **Crear base de datos SQL**. La opción está disponible en la sección Redundancia del almacenamiento de copias de seguridad. 
![Apertura de la hoja Crear base de datos SQL](./media/automated-backups-overview/sql-database-backup-storage-redundancy.png)

#### <a name="sql-managed-instance"></a>[Instancia administrada de SQL](#tab/managed-instance)

En Azure Portal, la opción para cambiar la redundancia del almacenamiento de copia de seguridad se encuentra en el panel **Proceso y almacenamiento**, al que se puede acceder desde la opción **Configurar instancia administrada** de la pestaña **Aspectos básicos** al crear la instancia de SQL Managed Instance.
![Apertura del panel de configuración Proceso y almacenamiento](./media/automated-backups-overview/open-configuration-blade-managedinstance.png)

Busque la opción para seleccionar la redundancia del almacenamiento de copia de seguridad en el panel **Proceso y almacenamiento**.
![Configuración de la redundancia del almacenamiento de copia de seguridad](./media/automated-backups-overview/select-backup-storage-redundancy-managedinstance.png)

---

### <a name="configure-backup-storage-redundancy-by-using-powershell"></a>Configuración de la redundancia del almacenamiento de copia de seguridad mediante PowerShell

#### <a name="sql-database"></a>[SQL Database](#tab/single-database)

Para configurar la redundancia del almacenamiento de copia de seguridad al crear una base de datos, puede especificar el parámetro -BackupStorageRedundancy. Los valores posibles son Geo, Zone y Local. De manera predeterminada, todas las bases de datos SQL usan almacenamiento con redundancia geográfica para las copias de seguridad. La restauración geográfica se deshabilita si se crea una base de datos con almacenamiento de copia de seguridad con redundancia local o de zona. 

```powershell
# Create a new database with geo-redundant backup storage.  
New-AzSqlDatabase -ResourceGroupName "ResourceGroup01" -ServerName "Server01" -DatabaseName "Database03" -Edition "GeneralPurpose" -Vcore 2 -ComputeGeneration "Gen5" -BackupStorageRedundancy Geo
```

Para obtener más información, consulte [New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase).

Para actualizar la redundancia del almacenamiento de copia de seguridad de una base de datos existente, puede usar el parámetro -BackupStorageRedundancy. Los valores posibles son Geo, Zone y Local.
Los cambios pueden tardar hasta 48 horas en aplicarse en la base de datos. Al cambiar del almacenamiento de copia de seguridad con redundancia geográfica al almacenamiento con redundancia local o de zona se deshabilita la restauración geográfica. 

```powershell
# Change the backup storage redundancy for Database01 to zone-redundant. 
Set-AzSqlDatabase -ResourceGroupName "ResourceGroup01" -DatabaseName "Database01" -ServerName "Server01" -BackupStorageRedundancy Zone
```

Para obtener más información, consulte [Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase).

> [!NOTE]
> Para usar el parámetro -BackupStorageRedundancy con operaciones de restauración de base de datos, copia de base de datos o creación de un elemento secundario, use Azure PowerShell versión Az.Sql 2.11.0. 


#### <a name="sql-managed-instance"></a>[Instancia administrada de SQL](#tab/managed-instance)

Para configurar la redundancia del almacenamiento de copia de seguridad al crear una instancia administrada, puede especificar el parámetro -BackupStorageRedundancy. Los valores posibles son Geo, Zone y Local.

```powershell
New-AzSqlInstance -Name managedInstance2 -ResourceGroupName ResourceGroup01 -Location westcentralus -AdministratorCredential (Get-Credential) -SubnetId "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/resourcegroup01/providers/Microsoft.Network/virtualNetworks/vnet_name/subnets/subnet_name" -LicenseType LicenseIncluded -StorageSizeInGB 1024 -VCore 16 -Edition "GeneralPurpose" -ComputeGeneration Gen4 -BackupStorageRedundancy Geo
```

Para más información, consulte [New-AzSqlInstance](/powershell/module/az.sql/new-azsqlinstance).

---

## <a name="use-azure-policy-to-enforce-backup-storage-redundancy"></a>Uso de Azure Policy para aplicar la redundancia del almacenamiento de copia de seguridad

Si tiene que cumplir requisitos de residencia de datos que le exigen que mantenga todos los datos en una única región de Azure, es posible que desee aplicar copias de seguridad con redundancia con redundancia local o de zona para su instancia de SQL Database o Managed Instance mediante Azure Policy. Azure Policy es un servicio que puede usar para crear, asignar y administrar directivas que aplican reglas a los recursos de Azure. Azure Policy le permite mantener esos recursos conforme a lo establecido en los estándares corporativos y los contratos de nivel de servicio. Para más información, consulte la [Introducción a Azure Policy](../../governance/policy/overview.md). 

### <a name="built-in-backup-storage-redundancy-policies"></a>Directivas de redundancia del almacenamiento de copia de seguridad integradas 

Se han agregado las siguientes directivas integradas nuevas, que se pueden asignar en el nivel de suscripción o de grupo de recursos para bloquear la creación de nuevas bases de datos o instancias con almacenamiento de copia de seguridad con redundancia geográfica. 

[SQL Database debe evitar el uso de la redundancia de copia de seguridad con almacenamiento con redundancia geográfica](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb219b9cf-f672-4f96-9ab0-f5a3ac5e1c13)


[Las instancias administradas de SQL deben evitar el uso de redundancia de copia de seguridad con almacenamiento con redundancia geográfica](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa9934fd7-29f2-4e6d-ab3d-607ea38e9079)

Puede encontrar una lista completa de las definiciones de directiva integradas para SQL Database y SQL Managed Instance [aquí](./policy-reference.md).

Si quiere aplicar los requisitos de residencia de datos en el nivel de la organización, puede asignar estas directivas a una suscripción. Después de que estas directivas se asignan a una suscripción, los usuarios de esa suscripción no podrán crear una base de datos ni una instancia administrada con almacenamiento de copia de seguridad con redundancia geográfica a través de Azure Portal o Azure PowerShell. 

> [!IMPORTANT]
> Las directivas de Azure no se aplican cuando la base de datos se crea mediante T-SQL. Para aplicar la residencia de datos al crear una base de datos mediante T-SQL, [use "LOCAL" o "ZONE" como entrada del parámetro BACKUP_STORAGE_REDUNDANCY en la instrucción CREATE DATABASE](/sql/t-sql/statements/create-database-transact-sql#create-database-using-zone-redundancy-for-backups).

Obtenga información sobre cómo asignar directivas mediante [Azure Portal](../../governance/policy/assign-policy-portal.md) o [Azure PowerShell](../../governance/policy/assign-policy-powershell.md).

## <a name="next-steps"></a>Pasos siguientes

- Las copias de seguridad de base de datos son una parte esencial de cualquier estrategia de recuperación ante desastres y continuidad del negocio, ya que protegen los datos de daños o eliminaciones accidentales. Para descubrir otras soluciones de continuidad empresarial de SQL Database, consulte el artículo de [información general sobre la continuidad empresarial](business-continuity-high-availability-disaster-recover-hadr-overview.md).
- Para más información sobre cómo configurar, administrar y restaurar desde la retención a largo plazo de copias de seguridad automatizadas en Azure Blob Storage usando Azure Portal, vea [Administración de la retención de copia de seguridad a largo plazo mediante Azure Portal](long-term-backup-retention-configure.md).
- Para más información sobre cómo configurar, administrar y restaurar desde la retención a largo plazo de copias de seguridad automatizadas en Azure Blob Storage usando PowerShell, vea [Administración de la retención de copia de seguridad a largo plazo mediante PowerShell](long-term-backup-retention-configure.md#using-powershell). 
- Obtenga más información sobre cómo [restaurar una base de datos a un momento dado usando Azure Portal](recovery-using-backups.md).
- Obtenga más información sobre cómo [restaurar una base de datos a un momento dado usando PowerShell](scripts/restore-database-powershell.md).
- Para información sobre el consumo del almacenamiento de copia de seguridad en Azure SQL Managed Instance, consulte [Explicación del consumo de almacenamiento de copia de seguridad en Managed Instance](https://aka.ms/mi-backup-explained).
- Para obtener información sobre cómo ajustar la retención y los costos de almacenamiento de copia de seguridad para Azure SQL Managed Instance, vea [Ajuste de la retención y los costos de almacenamiento de copia de seguridad en Managed Instance](https://aka.ms/mi-backup-tuning).
