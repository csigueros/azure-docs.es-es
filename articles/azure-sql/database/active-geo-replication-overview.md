---
title: Replicación geográfica activa
description: La replicación geográfica activa se usa para crear bases de datos secundarias legibles de bases de datos individuales en Azure SQL Database en las mismas regiones, o en otras.
ms.service: sql-database
ms.subservice: high-availability
ms.custom: sqldbrb=1
ms.topic: conceptual
author: emlisa
ms.author: emlisa
ms.reviewer: mathoma
ms.date: 10/25/2021
ms.openlocfilehash: ca958a3e7a43864caa673cd31736b1e661e7f608
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2021
ms.locfileid: "131465371"
---
# <a name="active-geo-replication"></a>Replicación geográfica activa
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

La replicación geográfica activa es una característica que permite crear una base de datos secundaria legible que se sincroniza continuamente para una base de datos principal. La base de datos secundaria legible puede estar en la misma región de Azure que la principal o, lo que es más común, en otra región. Este tipo de bases de datos secundarias legibles también se conocen como geográficas secundarias o réplicas geográficas.

La replicación geográfica activa se ha diseñado como solución de continuidad empresarial que permite realizar una recuperación rápida de bases de datos individuales si se produce un desastre a nivel regional o una interrupción a gran escala. Una vez configurada la replicación geográfica, puede iniciar una conmutación por error geográfica a una base de datos geográfica secundaria que se encuentre en otra región de Azure. La conmutación por error geográfica la inicia mediante programación la aplicación, o bien lo hace manualmente el usuario.

> [!NOTE]
> La replicación geográfica activa para Hiperescala de Azure SQL [ahora se encuentra en versión preliminar pública](https://aka.ms/hsgeodr). Entre las limitaciones actuales se incluyen: solo se admite una base de datos de replicación geográfica secundaria en la misma región o en otra diferente, actualmente no se admite la conmutación por error forzada ni planeada, no se admite la restauración de la base de datos a partir de una base de datos de replicación geográfica secundaria y tampoco se admite el uso de una base de datos de replicación geográfica secundaria como base de datos de origen para la copia de base de datos ni como base de datos de replicación geográfica principal para otra secundaria.
> 
> En caso de que necesite convertir la base de datos secundaria geográfica en principal (base de datos grabable), siga estos pasos:
> 1. Divida el vínculo de replicación geográfica mediante el cmdlet [Remove-AzSqlDatabaseSecondary](/powershell/module/az.sql/remove-azsqldatabasesecondary) de PowerShell o [az sql db replica delete-link](/cli/azure/sql/db/replica#az_sql_db_replica_delete_link) para la CLI de Azure; esto convertirá la base de datos secundaria en una base independiente de lectura y escritura. Se perderán todos los cambios de datos confirmados en la base de datos principal que no se hayan replicado todavía en la secundaria. Estos cambios se pueden recuperar cuando la base de datos principal anterior está disponible o, en algunos casos, mediante la restauración de la base de datos principal anterior al punto más reciente disponible en el tiempo.
> 2. Si la base de datos principal anterior está disponible, elimínela y, a continuación, configure la replicación geográfica para la nueva base de datos principal (se establecerá una nueva base de datos secundaria). 
> 3. Actualice las cadenas de conexión de la aplicación en consecuencia.

> [!NOTE]
> La replicación geográfica activa no es compatible con Instancia administrada de Azure SQL. Para la conmutación por error geográfica de Instancia administrada de SQL, use [Grupos de conmutación por error automática](auto-failover-group-overview.md).

> [!NOTE]
> Para migrar bases de datos SQL desde Azure Alemania mediante la replicación geográfica activa, consulte [Migración de bases de datos SQL mediante la replicación geográfica activa](../../germany/germany-migration-databases.md#migrate-sql-database-using-active-geo-replication).

Si la aplicación requiere un punto de conexión estable y compatibilidad automática con la conmutación por error geográfica, además de la replicación geográfica, use [grupos de conmutación por error automática](auto-failover-group-overview.md).

En el siguiente diagrama se ilustra una configuración típica de una aplicación de nube con redundancia geográfica mediante la replicación geográfica activa.

![replicación geográfica activa](./media/active-geo-replication-overview/geo-replication.png)

Si por algún motivo se produce un error en la base de datos principal, puede iniciar una conmutación por error geográfica a cualquiera de las bases de datos secundarias. Cuando una base de datos secundaria se promociona al rol de principal, las restantes bases de datos secundarias se vinculan automáticamente a la nueva principal.

Para administrar la replicación geográfica e iniciar una conmutación por error geográfica puede usar:

- [Azure Portal](active-geo-replication-configure-portal.md)
- [PowerShell: base de datos única](scripts/setup-geodr-and-failover-database-powershell.md)
- [PowerShell: grupo elástico](scripts/setup-geodr-and-failover-elastic-pool-powershell.md)
- [Transact-SQL: base de datos única o grupo elástico](/sql/t-sql/statements/alter-database-azure-sql-database)
- [API REST: base de datos única](/rest/api/sql/replicationlinks)

La replicación geográfica activa aprovecha la tecnología de [grupos de disponibilidad Always On](/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server) para replicar de forma asincrónica el registro de transacciones generado en la réplica principal en todas las réplicas geográficas. Mientras que, en cualquier momento dado, una base de datos secundaria puede ir ligeramente por detrás de la base de datos principal, se garantiza que los datos de la base secundaria van a ser coherentes, desde un punto de vista transaccional. En otras palabras, los cambios realizados por transacciones no confirmadas no se ven. 

> [!NOTE]
> La replicación geográfica activa replica los cambios mediante la transmisión en secuencias del registro de transacciones de la base de datos desde la réplica principal a las secundarias. No está relacionada con la [replicación transaccional](/sql/relational-databases/replication/transactional/transactional-replication), que replica los cambios mediante la ejecución de comandos DML (INSERT, UPDATE, DELETE) en suscriptores.

La redundancia regional que proporciona la replicación geográfica permite a las aplicaciones se recuperen rápidamente de la pérdida permanente de toda una región de Azure, o de partes de ella, causada por desastres naturales, errores humanos catastróficos o actos malintencionados. El objetivo de punto de recuperación de la replicación geográfica se puede encontrar en [Introducción a la continuidad empresarial](business-continuity-high-availability-disaster-recover-hadr-overview.md).

En la siguiente ilustración, se muestra un ejemplo de replicación geográfica activa configurada con una base de datos principal en la región Centro-norte de EE. UU. y una secundaria en la región Centro-sur de EE. UU.

![Relación de replicación geográfica](./media/active-geo-replication-overview/geo-replication-relationship.png)

Además de para la recuperación ante desastres, la replicación geográfica activa se puede usar en los siguientes escenarios:

- **Migración de bases de datos**: puede usar la replicación geográfica activa para migrar una base de datos de un servidor a otro con un tiempo de inactividad mínimo.
- **Actualizaciones de aplicaciones**: puede crear una base de datos secundaria adicional como copia de conmutación por recuperación durante las actualizaciones de aplicaciones.

Para lograr una continuidad empresarial completa, agregar redundancia regional de base de datos es solo una parte de la solución. Para recuperar una aplicación (un servicio) de un extremo a otro tras un error catastrófico, es necesario recuperar todos los componentes que constituyen el servicio y cualquier servicio dependiente. Algunos ejemplos de estos componentes son el software cliente (por ejemplo, un explorador con JavaScript personalizado), los front-end web, el almacenamiento y DNS. Es fundamental que todos los componentes sean resistentes a los mismos errores y que estén disponibles en el plazo del objetivo de tiempo de recuperación (RTO) de la aplicación. Por lo tanto, debe identificar todos los servicios dependientes y comprender las garantías y capacidades que ofrecen. A continuación, debe seguir los pasos adecuados para asegurarse de que el servicio funcione durante la conmutación por error de los servicios de los que depende. Para más información sobre el diseño de soluciones para la recuperación ante desastres, consulte [Diseño de soluciones de nube para la continuidad empresarial mediante la replicación geográfica activa](designing-cloud-solutions-for-disaster-recovery.md).

## <a name="active-geo-replication-terminology-and-capabilities"></a>Funcionalidades y terminología de la replicación geográfica activa

- **Replicación asincrónica automática**

  Solo puede crear una base de datos geográfica secundaria para una base de datos existente. Esta base de datos se puede crear en cualquier servidor lógico, que no sea el servidor con la base de datos principal. Una vez creada, la réplica geográfica secundaria se rellena con los datos de la base de datos principal. Este proceso se conoce como propagación. Tras crear y propagar la base de datos secundaria geográfica, las actualizaciones de la base de datos principal se replican de forma automática y asincrónica en la réplica secundaria geográfica. La replicación asincrónica significa que las transacciones se confirman en la base de datos principal antes de replicarse.

- **Réplicas geográficas secundarias legibles**

  Una aplicación puede acceder a una réplica secundaria geográfica para ejecutar consultas de solo lectura con las mismas entidades de seguridad que se usan para acceder a la base de datos principal o con otras. Para más información, consulte [Uso de réplicas de solo lectura para descargar cargas de trabajo de consulta de solo lectura](read-scale-out.md).

   > [!IMPORTANT]
   > Puede usar la replicación geográfica para crear réplicas secundarias en la misma región que la principal. Estas réplicas secundarias se pueden usar estas secundarias para satisfacer escenarios de escalado horizontal de lectura en la misma región. Sin embargo, una réplica secundaria en la misma región no proporciona resistencia adicional a errores catastróficos o interrupciones a gran escala y, por tanto, no es un destino de conmutación por error adecuado para fines de recuperación ante desastres. Tampoco garantiza el aislamiento de la zona de disponibilidad. Use la [configuración con redundancia de zona](high-availability-sla.md#premium-and-business-critical-service-tier-zone-redundant-availability) de los niveles de servicio Crítico para la empresa o Prémium con o la [configuración con redundancia de zona](high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview) del nivel de servicio De uso general para lograr el aislamiento de la zona de disponibilidad.
   >

- **Conmutación por error geográfica planeada**

  La conmutación por error geográfica planeada cambia los roles de las bases de datos geográficas principales y secundarias después de completar la sincronización de datos completa. Una conmutación por error planeada no genera una pérdida de datos. La duración de la conmutación por error geográfica planeada depende del tamaño del registro de transacciones de la principal que debe sincronizarse con la base de datos geográfica secundaria. La conmutación por error geográfica planeada está diseñada para los siguientes escenarios:

  - Realizar simulacros de recuperación ante desastres en producción cuando no es aceptable la pérdida de datos. 
  - Reubicar la base de datos en otra región. 
  - Devolver la base de datos a la región primaria después de que se ha solucionado la interrupción (conmutación por recuperación).

- **Conmutación por error geográfica sin planificar**

  La conmutación por error geográfica no planeada, o forzada, cambia inmediatamente la base de datos geográfica secundaria al rol de principal sin sincronización con la principal. Se pierden todas las transacciones confirmadas en la base de datos principal que no se hayan replicado aún en la secundaria. Esta operación está diseñada como método de recuperación durante las interrupciones cuando no se puede acceder a la base de datos principal, pero la disponibilidad de la base de datos debe restaurarse rápidamente. Cuando la base de datos principal original esté de nuevo en línea, se volverá a conectar automáticamente, se volverá a inicializar mediante los datos principales actuales y se convertirá en una nueva base de datos geográfica secundaria.

  > [!IMPORTANT]
  > Después de la conmutación por error geográfica planeada o no planeada, el punto de conexión de la nueva base de datos principal cambia, ya que la nueva base de datos principal se encuentra ahora en otro servidor lógico.

- **Varias bases de datos geográficas secundarias legibles**

  Se pueden crear hasta cuatro bases de datos geográficas secundarias para una principal. Si solo hay una base de datos secundaria y se produce un error, la aplicación está expuesta a un mayor riesgo hasta que se crea otra. Si existen varias bases de datos secundarias, la aplicación sigue estando protegida aunque se produzca un error en una de ellas. También se pueden usar bases de datos secundarias adicionales para escalar horizontalmente cargas de trabajo de solo lectura.

  > [!TIP]
  > Si usa la replicación geográfica activa para compilar una aplicación distribuida globalmente y necesita proporcionar acceso de solo lectura a los datos en más de cuatro regiones, puede crear una base de datos secundaria a partir de otra base de datos secundaria (este proceso se conoce como encadenamiento) para crear réplicas geográficas adicionales. El retraso de la replicación en las réplicas geográficas encadenadas puede ser mayor que en las réplicas geográficas conectadas directamente a la base de datos principal. La configuración de topologías de replicación geográfica encadenada solo se admite mediante programación, no desde Azure Portal.

- **Replicación geográfica de bases de datos en un grupo elástico**

  Cada base de datos geográfica secundaria puede ser una base de datos única o una de un grupo elástico. La opción de un grupo elástico para cada base de datos geográfica secundaria es independiente, no depende de la configuración de ninguna otra réplica de la topología (principal o secundaria). Cada grupo elástico se encuentra dentro de un único servidor lógico. Dado que los nombres de base de datos de un servidor lógico deben ser únicos, varias bases de datos geográficas secundarias de la misma base de datos principal nunca pueden compartir un grupo elástico.

- **Conmutación por error y conmutación por recuperación geográfica controladas por el usuario**

  Cualquier base de datos geográfica secundaria que haya finalizado la inicialización inicial se puede cambiar explícitamente al rol principal (conmutado por error) en cualquier momento por parte de la aplicación o el usuario. Durante una interrupción en la que no se pueda acceder a la base de datos principal, solo se puede usar una conmutación por error geográfica no planeada. Esto promueve inmediatamente una base de datos geográfica secundaria para que sea la nueva base de datos principal. Cuando se mitiga la interrupción, el sistema convierte automáticamente la base de datos principal recuperada en geográfica secundaria y la pone al día con la nueva principal. Dada la naturaleza asincrónica de la replicación geográfica, es posible que las transacciones recientes se pierdan durante las conmutaciones por error geográficas no planeadas si se produce un error en la principal antes de que estas transacciones se repliquen en una base de datos geográfica secundaria. Cuando una base de datos principal con varias bases de datos geográficas secundarias realiza una conmutación por error, el sistema vuelve a configurar automáticamente las relaciones de replicación y vincula las bases de datos geográficas secundarias restantes a la base de datos principal recién promovida sin necesidad de que intervenga el usuario. Tras la interrupción que ha provocado la mitigación de la conmutación por error geográfica, sería conveniente devolver la base de datos principal a su región original. Para ello, invoque una conmutación por error geográfica planeada.

## <a name="prepare-for-geo-failover"></a><a name="preparing-secondary-database-for-failover"></a> Preparación para la conmutación por error geográfica

Para asegurarse de que la aplicación puede acceder de inmediato a la nueva base de datos principal después de la conmutación por error geográfica, compruebe que tanto la autenticación como el acceso de red al servidor secundaria están configurados correctamente. Para obtener más información, consulte [Administración de la seguridad de Azure SQL Database después de la recuperación ante desastres](active-geo-replication-security-configure.md). Valide también que la directiva de retención de copia de seguridad de la base de datos secundaria coincide con la de la principal. Esta configuración no forma parte de la base de datos y no se replica desde la base de datos principal. De forma predeterminada, la base de datos geográfica secundaria se configura con un período de retención de restauración a un momento dado predeterminado de siete días. Para más información, consulte [Copias de seguridad automatizadas de SQL Database](automated-backups-overview.md).

> [!IMPORTANT]
> Si la base de datos es miembro de un grupo de conmutación por error, no puede iniciar su conmutación por error mediante el comando de conmutación por error de replicación geográfica. Use el comando de conmutación por error para el grupo. Si necesita realizar la conmutación por error de una base de datos individual, primero debe quitarla del grupo de conmutación por error. Para más información, consulte [Grupos de conmutación por error](auto-failover-group-overview.md).

## <a name="configure-geo-secondary"></a><a name="configuring-secondary-database"></a>Configuración de una base de datos geográfica secundaria

Es necesario que la base de datos principal y la geográfica secundaria tengan el mismo nivel de servicio. También se recomienda encarecidamente que la base de datos geográfica secundaria se cree con la misma redundancia de almacenamiento de copia de seguridad y el mismo tamaño de proceso (unidades de transacción de base de datos o núcleos virtuales) que la principal. Si la base de datos principal sufre una carga de trabajo de escritura intensiva, es posible que una base de datos geográfica secundaria con un tamaño de proceso menor no pueda mantenerla, lo que provocará un retraso de replicación en la base de datos geográfica secundaria y, finalmente, podría provocar la falta de disponibilidad de la base de datos geográfica secundaria. Para mitigar estos riesgos, una replicación geográfica activa reducirá (limitará) la velocidad de los registros de transacciones de la base de datos principal, en caso de que sea necesario para permitir que sus bases de datos secundarias se pongan al día.

Otra consecuencia de una configuración de una base de datos geográfica secundaria no equilibrada es que después de una conmutación por error, el rendimiento de la aplicación puede verse afectado por a una capacidad de proceso insuficiente de la nueva base de datos principal. En ese caso, se deberá escalar verticalmente la base de datos para tener suficientes recursos, lo que puede tardar mucho tiempo y requerirá una conmutación por error de [alta disponibilidad](high-availability-sla.md) al final del proceso de escalado vertical, lo que interrumpirá las cargas de trabajo de la aplicación.

Si decide crear la base de datos geográfica secundaria con un tamaño de proceso inferior, debe supervisar la tasa de E/S del registro en la base de datos principal a lo largo del tiempo. Esto le permite calcular el tamaño de proceso mínimo de la base de datos geográfica secundaria necesario para mantener la carga de replicación. Por ejemplo, si la base de datos principal es P6 (1000 DTU) y su E/S de registro es 50 % de forma sostenida, la base de datos geográfica secundaria debe ser, al menos, P4 (500 DTU). Para recuperar datos históricos de E/S del registro, use la vista [sys.resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database). Para recuperar los datos de E/S de registro recientes con una granularidad mayor que refleje mejor los picos a corto plazo, utilice la vista [sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database).

> [!TIP]
> La limitación de la E/S del registro de transacciones en la base de datos principal debido a un tamaño de proceso inferior en una base de datos geográfica secundaria se envía mediante el tipo de espera HADR_THROTTLE_LOG_RATE_MISMATCHED_SLO, que se puede ver en las vistas de bases de datos [sys.dm_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) y [sys.dm_os_wait_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-wait-stats-transact-sql).
>
> La E/S del registro de transacciones de la base de datos principal puede estar limitada por motivos no relacionados con el tamaño de proceso inferior de una base de datos geográfica secundaria. Este tipo de limitación puede producirse aunque la base de datos geográfica secundaria tenga el mismo tamaño de proceso que la principal, o un tamaño superior. Para más información, lo que incluye los tipos de espera para diferentes tipos de limitaciones de la E/S del registro, consulte [Gobernanza de la velocidad del registro de transacciones](resource-limits-logical-server.md#transaction-log-rate-governance).

De forma predeterminada, la redundancia del almacenamiento de copia de seguridad de la base de datos geográfica secundaria es la misma que la de la base de datos principal. Puede configurar una base de datos geográfica secundaria con una redundancia de almacenamiento de copia de seguridad diferente. Las copias de seguridad siempre se realizan en la base de datos principal. Si la secundaria está configurada con una redundancia de almacenamiento de copia de seguridad diferente, después de una conmutación por error geográfica cuando la base de datos geográfica secundaria se promueve a la principal, las nuevas copias de seguridad se almacenarán y facturarán según el tipo de almacenamiento (RA-GRS, ZRS, LRS) seleccionado en la nueva base de datos principal (anteriormente secundaria). 

## <a name="cross-subscription-geo-replication"></a>Replicación geográfica entre suscripciones

Para crear una base de datos geográfica secundaria en una suscripción que no sea la suscripción de la principal (ya sea en el mismo inquilino de Azure Active Directory o no), siga los pasos de esta sección.

1. Agregue la dirección IP de la máquina cliente que ejecuta los comandos T-SQL siguientes a los firewalls de servidor de **ambos** servidores, el principal y el secundario. Esa dirección IP se puede confirmar ejecutando la siguiente consulta mientras se está conectado al servidor principal desde la misma máquina cliente.
  
   ```sql
   select client_net_address from sys.dm_exec_connections where session_id = @@SPID;
   ``` 

   Para más información, consulte [Configuración de un firewall](firewall-configure.md).

2. En la base de datos maestra del servidor **principal**, cree un inicio de sesión de autenticación SQL dedicado a la configuración de la replicación geográfica activa. Ajuste el Id. de inicio de sesión y la contraseña según sea necesario.

   ```sql
   create login geodrsetup with password = 'ComplexPassword01';
   ```

3. En la misma base de datos, cree un usuario para el inicio de sesión y agréguélo al rol `dbmanager`:

   ```sql
   create user geodrsetup for login geodrsetup;
   alter role dbmanager add member geodrsetup;
   ```

4. Anote el valor del identificador de seguridad del nuevo inicio de sesión. Obtenga el valor del identificador de seguridad, para lo que debe usar la siguiente consulta.

   ```sql
   select sid from sys.sql_logins where name = 'geodrsetup';
   ```

5. Conéctese a la base de datos **principal** (no a la base de datos maestra) y cree un usuario para el mismo inicio de sesión.

   ```sql
   create user geodrsetup for login geodrsetup;
   ```

6. En la misma base de datos, agregue el usuario al rol `db_owner`.

   ```sql
   alter role db_owner add member geodrsetup;
   ```

7. En la base de datos maestra del servidor **secundario**, cree el mismo inicio de sesión que en el servidor principal, para lo que debe usar el mismo nombre, contraseña e identificador de seguridad. Reemplace el valor hexadecimal del identificador de seguridad en el comando de ejemplo siguiente por el obtenido en el paso 4.

   ```sql
   create login geodrsetup with password = 'ComplexPassword01', sid=0x010600000000006400000000000000001C98F52B95D9C84BBBA8578FACE37C3E;
   ```

8. En la misma base de datos, cree un usuario para el inicio de sesión y agréguélo al rol `dbmanager`.

   ```sql
   create user geodrsetup for login geodrsetup;
   alter role dbmanager add member geodrsetup;
   ```

9. Conéctese a la base de datos maestra en el servidor **principal** mediante el nuevo inicio de sesión `geodrsetup` e inicie la creación de la base de datos geográfica secundaria en el servidor secundario. Ajuste el nombre de la base de datos y el nombre del servidor secundario según sea necesario. Una vez ejecutado el comando, puede supervisar la creación de la base de datos geográfica secundaria. Para ello, debe consultar la vista [sys.dm_geo_replication_link_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-geo-replication-link-status-azure-sql-database) de la base de datos **principal** y la vista [sys.dm_operation_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) en la base de datos maestra del servidor **principal**. El tiempo necesario para crear una base de datos geográfica secundaria depende del tamaño de la base de datos principal.

   ```sql
   alter database [dbrep] add secondary on server [servername];
   ```

10. Una vez creada correctamente la base de datos secundaria geográfica, se pueden quitar los usuarios, los inicios de sesión y las reglas de firewall que ha creado este procedimiento.

> [!NOTE]
> Las operaciones de replicación geográfica entre suscripciones, incluidas la configuración y la conmutación por error geográfica, solo se admiten mediante comandos de SQL.
> 
> No se admite una instancia de replicación geográfica secundaria si los servidores principal o secundario tienen un [punto de conexión privado](private-endpoint-overview.md) configurado y [se deniega el acceso a la red pública](connectivity-settings.md#deny-public-network-access). Si el punto de conexión privado está configurado, pero se permite el acceso a la red pública, se admite la adición de una instancia de replicación geográfica secundaria al conectarse al servidor principal desde una dirección IP pública. Una vez que se agrega una instancia de replicación geográfica secundaria, se puede denegar el acceso público.
> 
> No se admite la creación de una base de datos geográfica secundaria en un servidor lógico en otro inquilino de Azure cuando la autenticación [exclusiva de Azure Active Directory](https://techcommunity.microsoft.com/t5/azure-sql/azure-active-directory-only-authentication-for-azure-sql/ba-p/2417673) para Azure SQL está activa (habilitada) en el servidor lógico principal o secundario.

## <a name="keep-credentials-and-firewall-rules-in-sync"></a><a name="keeping-credentials-and-firewall-rules-in-sync"></a> Mantenimiento de las credenciales y las reglas de firewall sincronizadas

Al usar el acceso de la red pública para conectarse a la base de datos, se recomienda usar [reglas de firewall de IP de nivel de base de datos](firewall-configure.md) para bases de datos con replicación geográfica. Estas reglas se replican con la base de datos, lo que garantiza que todas las bases de datos geográficas secundarias tienen las mismas reglas de firewall de IP que la principal. Este enfoque elimina la necesidad de que los clientes configuren y mantengan manualmente las reglas de firewall en los servidores que hospedan tanto la base de datos principal como las secundarias. Del mismo modo, la utilización de [usuarios de bases de datos independientes](logins-create-manage.md) para el acceso a datos garantiza que tanto la base de datos principal como las secundarias siempre tienen las mismas credenciales de autenticación. De esta forma, después de una conmutación por error geográfica, no hay interrupciones debido a errores de coincidencia de credenciales de autenticación.

## <a name="scale-primary-database"></a><a name="upgrading-or-downgrading-primary-database"></a> Modificación de la escala de una base de datos principal

Puede escalar verticalmente o reducir verticalmente la base de datos principal a un tamaño de proceso diferente (en el mismo nivel de servicio) sin desconectar las secundarias. Cuando se escala verticalmente, se recomienda escalar verticalmente primero la secundaria geográfica y, después, escalar verticalmente la principal. Al reducir verticalmente, invierta el orden: primero escale verticalmente la principal y, a continuación, escale verticalmente la secundaria.

> [!NOTE]
> Si creó una base de datos secundaria geográfica como parte de la configuración del grupo de conmutación por error, no se recomienda reducirla verticalmente. De este modo, se garantiza que la capa de datos tiene la capacidad suficiente para procesar la carga de trabajo habitual después de una conmutación por error.

> [!IMPORTANT]
> La base de datos principal de un grupo de conmutación por error no puede escalar a un nivel de servicio (edición) superior, a menos que la base de datos secundaria se escale primero al nivel superior. Por ejemplo, si desea escalar verticalmente la base de datos principal de De uso general a Crítico para la empresa, primero debe escalar la base de datos geográfica secundaria a Crítico para la empresa. Si intenta escalar la base de datos principal o la geográfica secundaria de una forma tal que infrinja esta regla, recibirá el siguiente error:
>
> `The source database 'Primaryserver.DBName' cannot have higher edition than the target database 'Secondaryserver.DBName'. Upgrade the edition on the target before upgrading the source.`
>

## <a name="prevent-loss-of-critical-data"></a><a name="preventing-the-loss-of-critical-data"></a> Evitación la pérdida de datos críticos

Debido a la elevada latencia de las redes de área extensa, la replicación geográfica usa un mecanismo de replicación asincrónica. La replicación asincrónica hace que la posibilidad de perder datos sea inevitable si se produce un error en la principal. Para proteger las transacciones críticas contra la pérdida de datos, un desarrollador de aplicaciones puede llamar al procedimiento almacenado [sp_wait_for_database_copy_sync](/sql/relational-databases/system-stored-procedures/active-geo-replication-sp-wait-for-database-copy-sync) inmediatamente después de confirmar la transacción. La llamada a `sp_wait_for_database_copy_sync` bloquea el subproceso de llamada hasta que se transmite y protege la última transacción confirmada en el registro de transacciones de la base de datos secundaria. Sin embargo, no espera a que las transacciones transmitidas se reproduzcan (vuelvan a hacerse) en la base de datos secundaria. `sp_wait_for_database_copy_sync` está limitado a un vínculo de replicación geográfica específico. Cualquier usuario con derechos de conexión para la base de datos principal puede llamar a este procedimiento.

> [!NOTE]
> `sp_wait_for_database_copy_sync` evita la pérdida de datos después de la conmutación por error geográfica para transacciones específicas, pero no garantiza la sincronización completa para el acceso de lectura. El retraso provocado por una llamada al procedimiento `sp_wait_for_database_copy_sync` puede ser considerable y depende del tamaño del registro de transacciones que todavía no se transmiten en la principal en el momento de la llamada.

## <a name="monitor-geo-replication-lag"></a><a name="monitoring-geo-replication-lag"></a>Supervisión del retardo de la replicación geográfica

Para supervisar el retardo con respecto a RPO, utilice la columna *replication_lag_sec* de [sys.dm_geo_replication_link_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-geo-replication-link-status-azure-sql-database) en la base de datos principal. Muestra el retardo, en segundos, entre las transacciones confirmadas en la base de datos principal y las reforzadas en el registro de transacciones de la secundaria. Por ejemplo, si el retardo es de un segundo, significa que si la principal se ve afectada por una interrupción en este momento y se inicia una conmutación por error geográfica, se perderán las transacciones confirmadas en el último segundo.

Para medir el retardo con respecto a los cambios en la base de datos principal que se han protegido en la base de datos geográfica secundaria, compare el tiempo de *last_commit* en la base de datos geográfica secundaria con el mismo valor en la principal.

> [!TIP]
> Si en la base de datos principal *replication_lag_sec* tiene el valor NULL, significa que la base de datos principal no sabe actualmente lo lejos que está una base de datos geográfica secundaria. Esto ocurre típicamente después de reiniciar el proceso y debería ser una condición temporal. Considere la posibilidad de enviar una alerta si *replication_lag_sec* devuelve NULL durante un período prolongado. Puede indicar que la base de datos geográfica secundaria no se puede comunicar con la principal debido a un error de conectividad.
> 
> También hay condiciones que podrían provocar que la diferencia entre el tiempo de *last_commit* en la base de datos geográfica secundaria y en la principal llegue a ser considerable. Por ejemplo, si se realiza una confirmación en la base de datos principal después de un largo período sin cambios, la diferencia saltará a un valor grande antes de volver rápidamente a cero. Considere la posibilidad de enviar una alerta si la diferencia entre estos dos valores permanece grande durante mucho tiempo.

## <a name="programmatically-manage-active-geo-replication"></a><a name="programmatically-managing-active-geo-replication"></a> Administración mediante programación de la replicación geográfica activa

Como se dijo antes, la replicación geográfica activa también se puede administrar mediante programación con T-SQL, Azure PowerShell y API REST. En las tablas siguientes se describe el conjunto de comandos disponibles. La replicación geográfica activa incluye un conjunto de API de Azure Resource Manager para la administración, en el que se incluyen la [API REST de Azure SQL Database](/rest/api/sql/) y los [cmdlets de Azure PowerShell](/powershell/azure/). Estas API admiten el control de acceso basado en roles de Azure (Azure RBAC). Para más información sobre cómo implementar los roles de acceso, consulte [Control de acceso basado en roles de Azure (Azure RBAC)](../../role-based-access-control/overview.md).

### <a name="t-sql-manage-geo-failover-of-single-and-pooled-databases"></a><a name="t-sql-manage-failover-of-single-and-pooled-databases"></a> T-SQL: Administración de la conmutación por error geográfica de bases de datos únicas y agrupadas

> [!IMPORTANT]
> Estos comandos de T-SQL solo se aplican a la replicación geográfica activa, no a los grupos de conmutación por error. Por lo tanto, tampoco se aplican a Azure SQL Managed Instance, que solo admite grupos de conmutación por error.

| Get-Help | Descripción |
| --- | --- |
| [ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql?preserve-view=true&view=azuresqldb-current) |El argumento **ADD SECONDARY ON SERVER** se usa para crear una base de datos secundaria para una base de datos existente e iniciar la replicación de datos. |
| [ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql?preserve-view=true&view=azuresqldb-current) |**FAILOVER** o **FORCE_FAILOVER_ALLOW_DATA_LOSS** se usan para cambiar una base de datos de secundaria a principal e iniciar la conmutación por error. |
| [ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql?preserve-view=true&view=azuresqldb-current) |**REMOVE SECONDARY ON SERVER** se usa para finalizar una replicación de datos entre una instancia de SQL Database y la base de datos secundaria especificada. |
| [sys.geo_replication_links](/sql/relational-databases/system-dynamic-management-views/sys-geo-replication-links-azure-sql-database) |Devuelve información sobre todos los vínculos de replicación existentes para cada base de datos en un servidor. |
| [sys.dm_geo_replication_link_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-geo-replication-link-status-azure-sql-database) |Obtiene la hora de la última replicación, el retraso de la última replicación y otro tipo de información sobre el vínculo de replicación para una base de datos determinada. |
| [sys.dm_operation_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) |Muestra el estado de todas las operaciones de base de datos, entre las que se incluyen los cambios en los vínculos de replicación. |
| [sys.sp_wait_for_database_copy_sync](/sql/relational-databases/system-stored-procedures/active-geo-replication-sp-wait-for-database-copy-sync) |Hace que la aplicación espere hasta que todas las transacciones confirmadas se refuercen en el registro de transacciones de una base de datos secundaria geográfica. |
|  | |

### <a name="powershell-manage-geo-failover-of-single-and-pooled-databases"></a><a name="powershell-manage-failover-of-single-and-pooled-databases"></a>PowerShell: Administración de la conmutación por error geográfica de bases de datos únicas y agrupadas

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
> [!IMPORTANT]
> El módulo de Azure Resource Manager para PowerShell todavía es compatible con Azure SQL Database, pero todo el desarrollo futuro se realizará para el módulo Az.Sql. Para estos cmdlets, consulte [AzureRM.Sql](/powershell/module/AzureRM.Sql/). Los argumentos para los comandos del módulo Az y los módulos AzureRm son esencialmente idénticos.

| Cmdlet | Descripción |
| --- | --- |
| [Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase) |Obtiene una o más bases de datos. |
| [New-AzSqlDatabaseSecondary](/powershell/module/az.sql/new-azsqldatabasesecondary) |Crea una base de datos secundaria para una base de datos existente e inicia la replicación de datos. |
| [Set-AzSqlDatabaseSecondary](/powershell/module/az.sql/set-azsqldatabasesecondary) |Convierte una base de datos secundaria en principal para iniciar la conmutación por error. |
| [Remove-AzSqlDatabaseSecondary](/powershell/module/az.sql/remove-azsqldatabasesecondary) |Finaliza una replicación de datos entre SQL Database y la base de datos secundaria especificada. |
| [Get-AzSqlDatabaseReplicationLink](/powershell/module/az.sql/get-azsqldatabasereplicationlink) |Obtiene los vínculos de replicación geográfica de una base de datos. |
|  | |

> [!TIP]
> Si desea scripts de ejemplo, consulte [Configuración y conmutación por error de una base de datos única mediante la replicación geográfica activa](scripts/setup-geodr-and-failover-database-powershell.md) y [Configuración y conmutación por error de una base de datos agrupada mediante la replicación geográfica activa](scripts/setup-geodr-and-failover-elastic-pool-powershell.md).

### <a name="rest-api-manage-geo-failover-of-single-and-pooled-databases"></a><a name="rest-api-manage-failover-of-single-and-pooled-databases"></a>API REST: Administración de la conmutación por error geográfica de bases de datos únicas y agrupadas

| API | Descripción |
| --- | --- |
| [Crear o actualizar base de datos (createMode=Restore)](/rest/api/sql/databases/createorupdate) |Crea, actualiza o restaura una base de datos principal o secundaria. |
| [Obtener el estado de creación o actualización de la base de datos](/rest/api/sql/databases/createorupdate) |Devuelve el estado durante una operación de creación. |
| [Establecer la base de datos secundaria como principal (conmutación por error planeada)](/rest/api/sql/replicationlinks/failover) |Define qué base de datos secundaria es principal mediante la conmutación por error desde la base de datos principal. **Esta opción no se admite para Instancia administrada de SQL.**|
| [Establecer la base de datos secundaria como principal (conmutación por error no planeada)](/rest/api/sql/replicationlinks/failoverallowdataloss) |Define qué base de datos secundaria es principal mediante la conmutación por error desde la base de datos principal. Esta operación puede ocasionar pérdida de datos. **Esta opción no se admite para Instancia administrada de SQL.**|
| [Obtener vínculo de replicación](/rest/api/sql/replicationlinks/get) |Obtiene un vínculo de replicación específico para una base de datos determinada en una asociación de replicación geográfica. Recupera la información visible en la vista de catálogo sys.geo_replication_links. **Esta opción no se admite para Instancia administrada de SQL.**|
| [Vínculos de replicación: lista por base de datos](/rest/api/sql/replicationlinks/listbydatabase) | Obtiene todos los vínculos de replicación para una base de datos determinada en una asociación de replicación geográfica. Recupera la información visible en la vista de catálogo sys.geo_replication_links. |
| [Eliminar vínculo de replicación](/rest/api/sql/replicationlinks/delete) | Elimina un vínculo de replicación de base de datos. No se puede realizar durante la conmutación por error. |
|  | |

## <a name="next-steps"></a>Pasos siguientes

- Para los scripts de ejemplo, vea:
  - [Configuración y conmutación por error de una base de datos única mediante la replicación geográfica activa](scripts/setup-geodr-and-failover-database-powershell.md).
  - [Configuración y conmutación por error de una base de datos agrupada mediante la replicación geográfica activa](scripts/setup-geodr-and-failover-elastic-pool-powershell.md).
- SQL Database también admite grupos de conmutación por error automática. Para más información, consulte cómo se usan los [grupos de conmutación por error automática](auto-failover-group-overview.md).
- Para obtener una descripción general y los escenarios de la continuidad empresarial, consulte [Información general sobre la continuidad empresarial](business-continuity-high-availability-disaster-recover-hadr-overview.md).
- Para saber en qué consisten las copias de seguridad automatizadas de Azure SQL Database, consulte [Información general: copias de seguridad automatizadas de SQL Database](automated-backups-overview.md).
- Si quiere saber cómo usar las copias de seguridad automatizadas para procesos de recuperación, consulte [Recuperación de una base de datos a partir de copias de seguridad iniciadas por un servicio](recovery-using-backups.md).
- Para obtener información acerca de los requisitos de autenticación para un nuevo servidor principal y la base de datos, consulte [Administración de la seguridad de Azure SQL Database después de la recuperación ante desastres](active-geo-replication-security-configure.md).
