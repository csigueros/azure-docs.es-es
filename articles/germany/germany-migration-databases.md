---
title: Migración de recursos de base de datos de Azure, Azure Alemania a Azure global
description: En este artículo se proporciona información sobre cómo migrar los recursos de base de datos de Azure desde Azure Alemania a Azure global.
ms.topic: article
ms.date: 03/29/2021
author: gitralf
ms.author: ralfwi
ms.service: germany
ms.custom: bfmigrate, devx-track-azurepowershell
ms.openlocfilehash: 6fe6d653712e034c13f3b755e906de491b4dd49a
ms.sourcegitcommit: 20acb9ad4700559ca0d98c7c622770a0499dd7ba
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/29/2021
ms.locfileid: "117029061"
---
# <a name="migrate-database-resources-to-global-azure"></a>Migración de recursos de base de datos a Azure global

[!INCLUDE [closureinfo](../../includes/germany-closure-info.md)]

En este artículo se proporciona información que puede ayudarle a migrar los recursos de base de datos de Azure desde Azure Alemania a Azure global.

## <a name="sql-database"></a>SQL Database

Para migrar las cargas de trabajo más pequeñas de Azure SQL Database, sin mantener la base de datos migrada en línea, use la función de exportación para crear un archivo BACPAC. Un archivo BACPAC es un archivo comprimido (.zip) que contiene los metadatos y los datos de la base de datos de SQL Server. Después de crear el archivo BACPAC, puede copiar el archivo en el entorno de destino (por ejemplo, mediante el uso de AzCopy) y utilizar la función de importación para recompilar la base de datos. Tenga presente las siguientes consideraciones:

- Para que una exportación sea transaccionalmente coherente, asegúrese de que se cumple una de las siguientes condiciones:
  - No se produce ninguna actividad de escritura durante la exportación.
  - Realiza la exportación desde una copia transaccionalmente coherente de la base de datos SQL.
- Para exportar a Azure Blob Storage, el tamaño del archivo BACPAC se limita a 200 GB. Para un archivo BACPAC de mayor tamaño, exporte al almacenamiento local.
- Si la operación de exportación de SQL Database tarda más de 20 horas, es posible que se cancele la operación. Consulte los artículos siguientes para obtener sugerencias sobre cómo aumentar el rendimiento.

> [!NOTE]
> La cadena de conexión cambia después de la operación de exportación porque el nombre DNS del servidor cambia durante la exportación.

Para obtener más información:

- Aprenda a [exportar una base de datos a un archivo BACPAC](../azure-sql/database/database-export.md).
- Aprenda a [importar un archivo BACPAC en una base de datos](../azure-sql/database/database-import.md).
- Revise la [documentación de Azure SQL Database](/azure/sql-database/).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]


## <a name="migrate-sql-database-using-active-geo-replication"></a>Migración de SQL Database mediante la replicación geográfica activa

En el caso de las bases de datos que son demasiado grandes para los archivos BACPAC o para migrarlas de una nube a otra y permanecer en línea con un tiempo de inactividad mínimo, puede configurar la replicación geográfica activa de Azure Alemania a Azure global.

> [!IMPORTANT]
> La configuración de la replicación geográfica activa para migrar bases de datos a Azure global solo se admite con Transact-SQL (T-SQL) y antes de migrar debe solicitar que se habilite la suscripción para admitir la migración a Azure global. Para enviar una solicitud, debe usar [este vínculo de solicitud de soporte técnico](#requesting-access). 

> [!Note]
> Las regiones de nube global de Azure, Centro-oeste de Alemania y Norte de Alemania, son las regiones admitidas para la replicación geográfica activa con la nube de Azure Alemania. Si se quiere usar una región de Azure global alternativa como destino final de las bases de datos, se recomienda que, después de completar la migración a Azure global, se configure un vínculo de replicación geográfica adicional de Centro-oeste de Alemania o Norte de Alemania a la región de nube global de Azure requerida.

Para obtener detalles sobre los costes de la replicación geográfica activa, consulte la sección titulada **Replicación geográfica activa** en [Precios de Azure SQL Database](https://azure.microsoft.com/pricing/details/sql-database/single/).

La migración de bases de datos con replicación geográfica activa requiere un servidor lógico de Azure SQL en Azure global. Para crear el servidor puede usar el portal, Azure PowerShell o la CLI de Azure, entre otros, pero la configuración de la replicación geográfica activa para migrar de Azure Alemania a Azure global solo se admite con Transact-SQL (T-SQL).

> [!IMPORTANT]
> Al migrar entre nubes, los prefijos de nombre de servidor principal (Azure Alemania) y secundario (Azure global) deben ser distintos. Si los nombres de servidor son iguales, la instrucción ALTER DATABASE se ejecutará correctamente, pero se producirá un error al migrar. Por ejemplo, si el prefijo del nombre del servidor principal es `myserver` (`myserver.database.cloudapi.de`), el prefijo del nombre del servidor secundario en Azure global no puede ser `myserver`.


La instrucción `ALTER DATABASE` permite especificar un servidor de destino en Azure global mediante el uso de su nombre completo del servidor DNS en el lado de destino. 


```sql
ALTER DATABASE [sourcedb] add secondary on server [public-server.database.windows.net]
```


- *`sourcedb`* representa el nombre de la base de datos en un servidor de Azure SQL en Azure Alemania. 
- *`public-server.database.windows.net`* representa el nombre del servidor de Azure SQL que existe en Azure global, donde debe migrarse la base de datos. El espacio de nombres "database.windows.net" es necesario; reemplace *public-server* por el nombre del servidor SQL lógico en Azure global. El servidor de Azure global debe tener un nombre distinto al del servidor principal de Azure Alemania.


El comando se ejecuta en la base de datos maestra del servidor de Azure Alemania que hospeda la base de datos local que se va a migrar. 
- La API start-copy de T-SQL autentica al usuario que inició sesión en el servidor de nube pública mediante la búsqueda de un usuario con el mismo nombre de usuario o inicio de sesión de SQL en la base de datos maestra de ese servidor. Este enfoque es independiente de la nube; por lo tanto, la API de T-SQL se usa para iniciar copias entre nubes. Para obtener más información sobre este tema y sobre los permisos, vea el artículo sobre [Creación y uso de la replicación geográfica activa](../azure-sql/database/active-geo-replication-overview.md) y [ALTER DATABASE (Transact-SQL)](/sql/t-sql/statements/alter-database-transact-sql/).
- Salvo por la extensión de comando T-SQL inicial que indica un servidor lógico de Azure SQL en Azure global, el resto del proceso de replicación geográfica activa es idéntico a la ejecución ya existente en la nube local. Para obtener pasos detallados destinados a crear una replicación geográfica activa, consulte [Creación y uso de la replicación geográfica activa](../azure-sql/database/active-geo-replication-overview.md) con una excepción en la que la base de datos secundaria se crea en el servidor lógico secundario creado en Azure global. 
- Una vez que la base de datos secundaria existe en Azure global (como copia en línea de la base de datos de Azure Alemania), el cliente puede iniciar una conmutación por error de esta base de datos de Azure Alemania a Azure global mediante el comando T-SQL ALTER DATABASE (consulte la tabla siguiente).
- Después de la conmutación por error, una vez que la base de datos secundaria se convierte en principal en Azure global, puede detener la replicación geográfica activa y quitar la base de datos secundaria de Azure Alemania en cualquier momento (consulte la tabla siguiente y los pasos del diagrama). 
- Después de la conmutación por error, la base de datos secundaria de Azure Alemania seguirá generando costos hasta que se elimine.
      
- El uso del comando `ALTER DATABASE` es la única forma de configurar la replicación geográfica activa para migrar una base de datos de Azure Alemania a Azure global. 
- Azure Portal, Azure Resource Manager, PowerShell o la CLI no están disponibles para configurar la replicación geográfica activa para esta migración. 

Para migrar una base de datos de Azure Alemania a Azure global:   

1.  Elija la base de datos de usuario de Azure Alemania, por ejemplo, `azuregermanydb`.
2.  Cree un servidor lógico en Azure global (la nube pública), por ejemplo, `globalazureserver`. Su nombre de dominio completo (FQDN) es `globalazureserver.database.windows.net`.
3.  Inicie la replicación geográfica activa de Azure Alemania a Azure global con la ejecución de este comando T-SQL en el servidor de Azure Alemania. Tenga en cuenta que el nombre DNS completo se usa para el servidor público `globalazureserver.database.windows.net`. Esto es para indicar que el servidor de destino está en Azure global y no en Azure Alemania.

    ```sql
    ALTER DATABASE [azuregermanydb] ADD SECONDARY ON SERVER [globalazureserver.database.windows.net];
    ```

4.  Cuando la replicación esté lista para mover la carga de trabajo de lectura y escritura al servidor de Azure global, inicie una conmutación por error planeada a Azure global mediante la ejecución de este comando T-SQL en el servidor de Azure global.

    ```sql
    ALTER DATABASE [azuregermanydb] FAILOVER;
    ```

5.  El vínculo de replicación geográfica activa puede terminarse antes o después del proceso de conmutación por error. Al ejecutar el comando T-SQL siguiente después de la conmutación por error planeada, se quita el vínculo de replicación geográfica con la base de datos de Azure global como copia de lectura y escritura. Debe ejecutarse en el servidor lógico de la base de datos de replicación geográfica principal actual (es decir, en el servidor de Azure global). Esto completará el proceso de migración.

    ```sql
    ALTER DATABASE [azuregermanydb] REMOVE SECONDARY ON SERVER [azuregermanyserver];
    ```

    Cuando el comando T-SQL siguiente se ejecuta antes de la conmutación por error planeada también se detiene el proceso de migración, pero en esta situación la base de datos de Azure Alemania permanecerá como copia de lectura y escritura. Este comando T-SQL también debe ejecutarse en el servidor lógico de la base de datos de replicación geográfica principal actual, en este caso en el servidor de Azure Alemania.

    ```sql
    ALTER DATABASE [azuregermanydb] REMOVE SECONDARY ON SERVER [globalazureserver];
    ```

Estos pasos para migrar bases de datos de Azure SQL de Azure Alemania a Azure global también pueden seguirse con la replicación geográfica activa.


Para obtener más información, las tablas siguientes indican los comandos de T-SQL para administrar la conmutación por error. A continuación se indican los comandos admitidos para la replicación geográfica activa entre nubes entre Azure Alemania y Azure global: 
 
|Get-Help |Descripción|
|:--|:--| 
|[ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current&preserve-view=true) |Se utiliza el argumento ADD SECONDARY ON SERVER a fin de crear una base de datos secundaria para una base de datos existente e iniciar la replicación de datos| 
|[ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current&preserve-view=true) |Se utiliza FAILOVER o FORCE_FAILOVER_ALLOW_DATA_LOSS para cambiar una base de datos de secundaria a principal e iniciar la conmutación por error.  |
|[ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current&preserve-view=true) |Se utiliza REMOVE SECONDARY ON SERVER para finalizar una replicación de datos entre una instancia de SQL Database y la base de datos secundaria especificada.  |
 
### <a name="active-geo-replication-monitoring-system-views"></a>Vistas del sistema de supervisión de la replicación geográfica activa 
 
|Get-Help |Descripción|
|:--|:--| 
|[sys.geo_replication_links](/sql/relational-databases/system-dynamic-management-views/sys-geo-replication-links-azure-sql-database?view=azuresqldb-current&preserve-view=true)|Devuelve información sobre todos los vínculos de replicación existentes para cada base de datos del servidor de Azure SQL Database. |
|[sys.dm_geo_replication_link_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-geo-replication-link-status-azure-sql-database?view=azuresqldb-current&preserve-view=true) |Obtiene la hora de la última replicación, el retraso de la última replicación y otro tipo de información sobre el vínculo de replicación para una base de datos SQL determinada. |
|[sys.dm_operation_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database?view=azuresqldb-current&preserve-view=true) | Muestra el estado de todas las operaciones de base de datos, incluido el estado de los vínculos de replicación. | 
|[sp_wait_for_database_copy_sync](/sql/relational-databases/system-stored-procedures/active-geo-replication-sp-wait-for-database-copy-sync?view=azuresqldb-current&preserve-view=true) | Hace que la aplicación espere a que se repliquen todas las transacciones confirmadas y a que las reconozca la base de datos secundaria activa. |
 

## <a name="migrate-sql-database-long-term-retention-backups"></a>Migración de copias de seguridad de retención a largo plazo de SQL Database

Al migrar una base de datos con replicación geográfica o un archivo BACPAC, las copias de seguridad de retención a largo plazo que la base de datos pueda tener en Azure Alemania no se copian. Para migrar las copias de seguridad de retención a largo plazo existentes a la región de Azure global de destino, puede usar el procedimiento de copia de seguridad de retención a largo plazo COPY. 

>[!Note]
>Los métodos de copia de seguridad de retención a largo plazo (LTR) que se documentan aquí solo se aplican a las copias de seguridad de LTR de Azure Alemania a Azure global. No se admite copiar instancias de copia de seguridad de restauración a un momento dado con estos métodos. 
>

### <a name="pre-requisites"></a>Requisitos previos

1. La base de datos de destino donde se van a copiar las copias de seguridad de LTR en Azure global debe existir antes de iniciar la operación de copia. Se recomienda migrar primero la base de datos de origen mediante la [replicación geográfica activa](#migrate-sql-database-using-active-geo-replication) y, a continuación, iniciar la copia de seguridad de LTR. Esto garantizará que las copias de seguridad de la base de datos se copien en la base de datos de destino correcta. Este paso no es necesario si se trata de copias de seguridad de LTR de una base de datos descartada. Al aplicarse a copias de seguridad de LTR de una base de datos descartada, se creará un valor DatabaseID ficticio en la región de destino. 
2. Instale este [módulo de PowerShell Az](https://www.powershellgallery.com/packages/Az.Sql/3.0.0-preview).
3. Antes de empezar, asegúrese de que se hayan concedido los [roles de RBAC de Azure](../azure-sql/database/long-term-backup-retention-configure.md#azure-roles-to-manage-long-term-retention) en el ámbito de **suscripción** o de **grupo de recursos**. Nota: Para acceder a las copias de seguridad de LTR que pertenecen a un servidor descartado, se debe conceder el permiso en el ámbito de la suscripción de dicho servidor. . 


### <a name="limitations"></a>Limitaciones  

- No se admiten grupos de conmutación por error. Esto significa que los clientes que migren bases de datos de Azure Alemania tendrán que administrar ellos mismos las cadenas de conexión durante la conmutación por error.
- No se admiten Azure Portal, las API de Azure Resource Manager, PowerShell o la CLI. Esto significa que cada una de las migraciones de Azure Alemania tendrán que administrar la configuración y la conmutación por error de la replicación geográfica activa a través de T-SQL.
- Los clientes no pueden crear varias replicaciones geográficas secundarias en Azure global para bases de datos de Azure Alemania.
- La creación de una replicación geográfica secundaria debe iniciarse desde la región de Azure Alemania.
- Los clientes pueden migrar bases de datos desde Azure Alemania solo a Azure global. Actualmente, no se admite ninguna otra migración entre nubes. 
- Los usuarios de Azure AD de las bases de datos de usuarios de Azure Alemania se migran, pero no están disponibles en el nuevo inquilino de Azure AD en el que reside la base de datos migrada. Para habilitarlos, deben eliminarse y volver a crearse de forma manual con los usuarios de Azure AD actuales disponibles en el nuevo inquilino de Azure AD donde reside la base de datos recién migrada.  


### <a name="copy-long-term-retention-backups-using-powershell"></a>Copia de copias de seguridad de retención a largo plazo con PowerShell

Se ha introducido un comando de PowerShell nuevo, **Copy-AzSqlDatabaseLongTermRetentionBackup**, que se puede usar para copiar las copias de seguridad de retención a largo plazo de Azure Alemania en regiones globales de Azure. 

1. **Copia de la copia de seguridad de LTR con el nombre de copia de seguridad** En el ejemplo siguiente se muestra cómo puede copiar una copia de seguridad de LTR de Azure Alemania a la región global de Azure, con el valor backupname.  

```powershell
# Source database and target database info
$location = "<location>"
$sourceRGName = "<source resourcegroup name>"
$sourceServerName = "<source server name>"
$sourceDatabaseName = "<source database name>"
$backupName = "<backup name>"
$targetDatabaseName = "<target database name>"
$targetSubscriptionId = "<target subscriptionID>"
$targetRGName = "<target resource group name>"
$targetServerFQDN = "<targetservername.database.windows.net>"

Copy-AzSqlDatabaseLongTermRetentionBackup 
    -Location $location 
    -ResourceGroupName $sourceRGName 
    -ServerName $sourceServerName 
    -DatabaseName $sourceDatabaseName
    -BackupName $backupName
    -TargetDatabaseName $targetDatabaseName 
    -TargetSubscriptionId $targetSubscriptionId
    -TargetResourceGroupName $targetRGName
    -TargetServerFullyQualifiedDomainName $targetServerFQDN 
```

2. **Copia de la copia de seguridad de LTR mediante resourceID de copia de seguridad** En el ejemplo siguiente se muestra cómo puede copiar una copia de seguridad de LTR de Azure Alemania a la región global de Azure, con el valor resourceID de la copia de seguridad. Este ejemplo también se puede usar para copias de seguridad de una base de datos eliminada. 

```powershell
$location = "<location>"
# list LTR backups for All databases (you have option to choose All/Live/Deleted)
$ltrBackups = Get-AzSqlDatabaseLongTermRetentionBackup -Location $location -DatabaseState All

# select the LTR backup you want to copy
$ltrBackup = $ltrBackups[0]
$resourceID = $ltrBackup.ResourceId

# Source Database and target database info
$targetDatabaseName = "<target database name>"
$targetSubscriptionId = "<target subscriptionID>"
$targetRGName = "<target resource group name>"
$targetServerFQDN = "<targetservername.database.windows.net>"

Copy-AzSqlDatabaseLongTermRetentionBackup 
    -ResourceId $resourceID 
    -TargetDatabaseName $targetDatabaseName 
    -TargetSubscriptionId $targetSubscriptionId
    -TargetResourceGroupName $targetRGName
    -TargetServerFullyQualifiedDomainName $targetServerFQDN
```


### <a name="limitations"></a>Limitaciones  

- Las copias de seguridad de [restauración a un momento dado (PITR)](../azure-sql/database/recovery-using-backups.md#point-in-time-restore) solo se toman en la base de datos principal, es así por diseño. Al migrar bases de datos de Azure Alemania mediante Geo-DR, las copias de seguridad de PITR comenzarán a realizarse en la nueva base de datos principal después de la conmutación por error. Sin embargo, no se migrarán las copias de seguridad de PITR existentes (en la instancia principal anterior de Azure Alemania). Si necesita que las copias de seguridad de PITR admitan cualquier escenario de restauración a un momento dado, debe restaurar la base de datos a partir de las copias de seguridad de PITR en Azure Alemania y, a continuación, migrar la base de datos recuperada a Azure global. 
- Las directivas de retención a largo plazo no se migran con la base de datos. Si tiene una directiva de [retención a largo plazo (LTR)](../azure-sql/database/long-term-retention-overview.md) en la base de datos en Azure Alemania, debe copiar y volver a crear manualmente la directiva LTR en la base de datos nueva después de migrar. 


### <a name="requesting-access"></a>Solicitud del acceso

Para migrar una base de datos de Azure Alemania a Azure global mediante la replicación geográfica, la suscripción de *Azure Alemania* debe estar habilitada para configurar correctamente la migración entre nubes.

Para habilitar la suscripción de Azure Alemania, debe usar el vínculo siguiente a fin de crear una solicitud de soporte técnico para la migración:   

1. Vaya a la [solicitud de soporte técnico de migración](https://portal.microsoftazure.de/#create/Microsoft.Support/Parameters/%7B%0D%0A++++%22pesId%22%3A+%22f3dc5421-79ef-1efa-41a5-42bf3cbb52c6%22%2C%0D%0A++++%22supportTopicId%22%3A+%229fc72ed5-805f-3894-eb2b-b1f1f6557d2d%22%2C%0D%0A++++%22contextInfo%22%3A+%22Migration+from+cloud+Germany+to+Azure+global+cloud+%28Azure+SQL+Database%29%22%2C%0D%0A++++%22caller%22%3A+%22NoSupportPlanCloudGermanyMigration%22%2C%0D%0A++++%22severity%22%3A+%223%22%0D%0A%7D) siguiente.

2. En la pestaña de aspectos básicos, especifique la *migración Geo-DR* como **Resumen** y, a continuación, seleccione **Siguiente: Soluciones**.
 
   :::image type="content" source="media/germany-migration-databases/support-request-basics.png" alt-text="Formulario de solicitud de soporte técnico nueva":::

3. Revise los **Pasos recomendados** y, a continuación, seleccione **Siguiente: Detalles**. 

   :::image type="content" source="media/germany-migration-databases/support-request-solutions.png" alt-text="Información de la solicitud de soporte técnico requerida":::

4. En la página de detalles, proporcione lo siguiente:

   1. En el cuadro Descripción, escriba el identificador de la suscripción de Azure global donde se va a migrar. Para migrar bases de datos a más de una suscripción, agregue una lista de los identificadores de Azure global a los que quiere migrar las bases de datos.
   1. Proporcione la información de contacto: nombre, nombre de la empresa, correo electrónico o número de teléfono.
   1. Complete el formulario y, a continuación, seleccione **Siguiente: Revisar y crear**.

   :::image type="content" source="media/germany-migration-databases/support-request-details.png" alt-text="Detalles de la solicitud de soporte técnico":::


5. Revise la solicitud de soporte técnico y, a continuación, seleccione **Crear**. 


Se pondrán en contacto con usted una vez que se procese la solicitud.



## <a name="azure-cosmos-db"></a>Azure Cosmos DB

Puede usar la herramienta de migración de datos de Azure Cosmos DB para migrar los datos a Azure Cosmos DB. La herramienta de migración de datos de Azure Cosmos DB es una solución de código abierto que importa datos a Azure Cosmos DB desde distintos orígenes, incluidos archivos JSON, MongoDB, SQL Server, archivos CSV, Azure Table Storage, Amazon DynamoDB, HBase y contenedores de Azure Cosmos.


La herramienta de migración de datos en Azure Cosmos DB está disponible como una herramienta de interfaz gráfica o como herramienta de línea de comandos. El código fuente está disponible en el repositorio de GitHub de la [herramienta de migración de datos de Azure Cosmos DB](https://github.com/azure/azure-documentdb-datamigrationtool). Una [versión compilada de la herramienta](https://www.microsoft.com/download/details.aspx?id=46436) está disponible en el Centro de descarga de Microsoft.

Para migrar recursos de Azure Cosmos DB, se recomienda que complete los pasos siguientes:

1. Revise los requisitos de tiempo de actividad de la aplicación y las configuraciones de cuentas para determinar el mejor plan de acción.
1. Clone las configuraciones de la cuenta de Azure Alemania a la nueva región mediante la ejecución de la herramienta de migración de datos.
1. Si es posible usar una ventana de mantenimiento, copie los datos desde el origen al destino mediante la ejecución de la herramienta de migración de datos.
1. Si no se puede usar una ventana de mantenimiento, copie los datos desde el origen al destino mediante la ejecución de la herramienta y, después, complete estos pasos:
   1. Utilice un enfoque basado en la configuración para realizar cambios de lectura o escritura en una aplicación.
   1. Complete la sincronización por primera vez.
   1. Configure una sincronización incremental y póngase al día con la fuente de cambios.
   1. Lea en la nueva cuenta y valide la aplicación.
   1. Detenga el proceso de escritura en la cuenta antigua, valide que la fuente de cambios esté al día y, después, escriba en la nueva cuenta.
   1. Detenga la herramienta y elimine la cuenta antigua.
1. Ejecute la herramienta para validar que los datos son coherentes entre las cuentas antiguas y nuevas.

Para obtener más información:

- Para aprender a usar la herramienta de migración de datos, consulte el [Tutorial: Use la herramienta de migración de datos para migrar los datos a Azure Cosmos DB](../cosmos-db/import-data.md).
- Para obtener información sobre Cosmos DB, consulte [Bienvenido a Azure Cosmos DB](../cosmos-db/introduction.md).


## <a name="azure-cache-for-redis"></a>Azure Cache for Redis

Tiene algunas opciones si quiere migrar una instancia de Azure Cache for Redis de Azure Alemania a Azure global. La opción que elija depende de los requisitos.

### <a name="option-1-accept-data-loss-create-a-new-instance"></a>Opción 1: Aceptar la pérdida de datos, crear una nueva instancia

Este enfoque resulta idóneo cuando se cumplen las condiciones siguientes:

- Está usando Azure Cache for Redis como una caché de datos transitorios.
- La aplicación vuelve a llenar los datos de la memoria caché automáticamente en la nueva región.

Para migrar con pérdida de datos y crear una nueva instancia:

1. Cree una nueva instancia de Azure Cache for Redis en la nueva región de destino.
1. Actualice la aplicación para que utilice la nueva instancia de la nueva región.
1. Elimine la instancia anterior de Azure Cache for Redis en la región de origen.

### <a name="option-2-copy-data-from-the-source-instance-to-the-target-instance"></a>Opción 2: Copiar los datos de la instancia de origen a la de destino

Un miembro del equipo de Azure Cache for Redis ha escrito una herramienta de código abierto que copia datos de una instancia de Azure Cache for Redis a otra sin necesidad de importar o exportar la funcionalidad. Vea el paso 4 de los pasos siguientes para más información sobre la herramienta.

Para copiar los datos de la instancia de origen a la de destino:

1. Cree una máquina virtual en la región de origen. Si su conjunto de datos en Azure Cache for Redis es grande, asegúrese de seleccionar un tamaño de máquina virtual relativamente eficaz para minimizar el tiempo de copia.
1. Cree una nueva instancia de Azure Cache for Redis en la región de destino.
1. Vacíe los datos de la instancia de **destino**. (Asegúrese de que *no* se vacía desde la instancia de **origen**. El vaciado es necesario porque la herramienta de copia *no sobrescribe* las claves existentes en la ubicación de destino).
1. Use la herramienta siguiente para copiar automáticamente los datos de la instancia de Azure Cache for Redis de origen a la de destino: [origen de la herramienta](https://github.com/deepakverma/redis-copy) y [descarga de la herramienta](https://github.com/deepakverma/redis-copy/releases/download/alpha/Release.zip).

> [!NOTE]
> Este proceso puede tardar mucho tiempo según el tamaño del conjunto de datos.

### <a name="option-3-export-from-the-source-instance-import-to-the-destination-instance"></a>Opción 3: Exportar desde la instancia de origen, importar a la instancia de destino

Este enfoque aprovecha las características que solo están disponibles en el nivel Premium.

Para exportar de la instancia de origen e importar a la instancia de destino:

1. Cree un nuevo nivel Premium de la instancia de Azure Cache for Redis en la región de destino. Use el mismo tamaño que la instancia de Azure Cache for Redis de origen.
1. [Exporte los datos desde la caché de origen](../azure-cache-for-redis/cache-how-to-import-export-data.md) o utilice el [cmdlet de PowerShell Export-AzRedisCache](/powershell/module/az.rediscache/export-azrediscache).

   > [!NOTE]
   > La cuenta de Azure Storage de exportación debe estar en la misma región que la instancia de la caché.

1. Copie los blobs exportados en una cuenta de almacenamiento de la región de destino (por ejemplo, mediante AzCopy).
1. [Importe los datos a la caché de destino](../azure-cache-for-redis/cache-how-to-import-export-data.md) o utilice el [cmdlet de PowerShell Import-AzRedisCAche](/powershell/module/az.rediscache/import-azrediscache).
1. Vuelva a configurar la aplicación para que utilice la instancia de Azure Cache for Redis de destino.

### <a name="option-4-write-data-to-two-azure-cache-for-redis-instances-read-from-one-instance"></a>Opción 4: Escribir datos en dos instancias de Azure Cache for Redis, leer desde una instancia

Para este enfoque, debe modificar la aplicación. La aplicación tiene que escribir datos en más de una instancia de caché durante la lectura de una de las instancias de caché. Este enfoque tiene sentido si los datos almacenados en Azure Cache for Redis cumplen los criterios siguientes:
- Los datos se actualizan periódicamente. 
- Todos los datos se escriben en la instancia de Azure Cache for Redis de destino.
- Se dispone de tiempo suficiente para que todos los datos se actualicen.

Para obtener más información:

- Revise la [introducción a Azure Cache for Redis](../azure-cache-for-redis/cache-overview.md).

## <a name="postgresql-and-mysql"></a>PostgreSQL y MySQL

Para obtener más información, consulte los artículos de la sección "Copia de seguridad y migración de datos" de [PostgreSQL](../postgresql/index.yml) y [MySQL](../mysql/index.yml).

![PostgreSQL y MySQL](./media/germany-migration-main/databases.png)

## <a name="next-steps"></a>Pasos siguientes

Obtenga información sobre herramientas, técnicas y recomendaciones para migrar los recursos en las siguientes categorías de servicio:

- [Proceso](./germany-migration-compute.md)
- [Redes](./germany-migration-networking.md)
- [Storage](./germany-migration-storage.md)
- [Web](./germany-migration-web.md)
- [Analytics](./germany-migration-analytics.md)
- [IoT](./germany-migration-iot.md)
- [Integración](./germany-migration-integration.md)
- [Identidad](./germany-migration-identity.md)
- [Seguridad](./germany-migration-security.md)
- [Herramientas de administración](./germany-migration-management-tools.md)
- [Elementos multimedia](./germany-migration-media.md)
