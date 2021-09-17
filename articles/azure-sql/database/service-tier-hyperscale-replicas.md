---
title: Réplicas secundarias de Hiperescala
description: En este artículo, se describen los distintos tipos de réplicas secundarias disponibles en el nivel de servicio Hiperescala.
services: sql-database
ms.service: sql-database
ms.subservice: service-overview
ms.topic: overview
author: yorek
ms.author: damauri
ms.reviewer: ''
ms.date: 7/27/2021
ms.openlocfilehash: 9fa0a964033439a4b1c5276bf4310528dd345ff8
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121722894"
---
# <a name="hyperscale-secondary-replicas"></a>Réplicas secundarias de Hiperescala
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Como se describe en [Arquitectura de funciones distribuidas](service-tier-hyperscale.md), Hiperescala de Azure SQL Database tiene dos tipos diferentes de nodos de proceso, también llamados réplicas:

- Principal: sirve operaciones de lectura y escritura
- Secundaria: proporciona escalado horizontal de lectura, alta disponibilidad y replicación geográfica

Las réplicas secundarias siempre son de solo lectura y pueden ser de tres tipos diferentes:

- Réplica de alta disponibilidad
- Réplica con nombre (en versión preliminar)
- Réplica geográfica (en versión preliminar)

Cada tipo tiene una arquitectura, un conjunto de características, un propósito y un costo diferentes. En función de las características que necesite, puede usar solo una o incluso las tres juntas.

## <a name="high-availability-replica"></a>Réplica de alta disponibilidad

Una réplica de alta disponibilidad (HA) usa los mismos servidores de páginas que la réplica principal, por lo que no se requiere ninguna copia de datos para agregar una réplica de alta disponibilidad. Las réplicas de alta disponibilidad se usan principalmente para aumentar la disponibilidad de la base de datos, ya que actúan como servidores en espera activa con fines de conmutación por error. Si la réplica principal deja de estar disponible, la conmutación por error a una de las réplicas de alta disponibilidad existentes es automática y rápida. No es necesario cambiar la cadena de conexión; durante la conmutación por error, las aplicaciones pueden experimentar un tiempo de inactividad mínimo debido a que se eliminan las conexiones activas. Como de costumbre en este escenario, se recomienda una lógica de reintento adecuada. Varios controladores ya proporcionan cierto grado de lógica de reintento automático. Si usa .NET, la biblioteca [Microsoft.Data.SqlClient más reciente](https://devblogs.microsoft.com/azure-sql/configurable-retry-logic-for-microsoft-data-sqlclient/) proporciona compatibilidad completa nativa con la lógica de reintento automática configurable.

Las réplicas de alta disponibilidad usan el mismo nombre de servidor y base de datos que la réplica principal. Su objetivo de nivel de servicio también es siempre el mismo que para la réplica principal. Las réplicas de alta disponibilidad no son visibles ni se pueden administrar como un recurso independiente desde el portal ni desde cualquier otra API. 

Puede haber de cero a cuatro réplicas de alta disponibilidad. Su número se puede cambiar durante la creación de una base de datos o después de que se haya creado la base de datos, mediante el punto de conexión de administración y las herramientas habituales (por ejemplo: PowerShell, la CLI de Azure, el portal o la API REST). La creación o eliminación de réplicas de alta disponibilidad no afecta a las conexiones activas que se ejecutan en la réplica principal.

### <a name="connecting-to-an-ha-replica"></a>Conexión a una réplica de alta disponibilidad

En las bases de datos de Hiperescala, el argumento `ApplicationIntent` de la cadena de conexión usada por el cliente dictamina si la conexión se enruta a la réplica de lectura y escritura principal o a una réplica de solo lectura de alta disponibilidad. Si `ApplicationIntent` está establecido en `ReadOnly` y la base de datos no tiene una réplica secundaria, la conexión se enrutará a la réplica principal y de forma predeterminada tendrá el comportamiento `ReadWrite`.

```csharp
-- Connection string with application intent
Server=tcp:<myserver>.database.windows.net;Database=<mydatabase>;ApplicationIntent=ReadOnly;User ID=<myLogin>;Password=<myPassword>;Trusted_Connection=False; Encrypt=True;
```

Todas las réplicas de alta disponibilidad son idénticas en su capacidad de recursos. Si hay más de una réplica de alta disponibilidad, la carga de trabajo de intención de lectura se distribuye arbitrariamente entre todas las réplicas de alta disponibilidad disponibles. Cuando haya varias réplicas de alta disponibilidad, tenga en cuenta que cada una de ellas podría tener una latencia de datos diferente con respecto a los cambios de datos realizados en la réplica principal. Cada réplica de alta disponibilidad utiliza los mismos datos que la principal en el mismo conjunto de servidores de páginas. No obstante, las cachés de datos locales de cada réplica de alta disponibilidad reflejan los cambios realizados en la réplica principal mediante el servicio de registro de transacciones, que reenvía las entradas del registro de la réplica principal a las réplicas de alta disponibilidad. Como resultado, en función de la carga de trabajo que procese una réplica de alta disponibilidad, la aplicación de las entradas de registro se puede producir a velocidades diferentes y, por tanto, distintas réplicas podrían tener una latencia de datos diferente en relación con la réplica principal.

## <a name="named-replica-in-preview"></a>Réplica con nombre (en versión preliminar)

Una réplica con nombre, al igual que una réplica de alta disponibilidad, usa los mismos servidores de páginas que la réplica principal. De forma similar a las réplicas de alta disponibilidad, no se necesita ninguna copia de datos para agregar una réplica con nombre. 

La diferencia con respecto a las réplicas de alta disponibilidad es que las réplicas con nombre: 

- aparecen como bases de datos normales (de solo lectura) de Azure SQL en el portal y en las llamadas de API (CLI de Azure, PowerShell, T-SQL).
- pueden tener un nombre de base de datos diferente de la réplica principal y, opcionalmente, estar ubicadas en un servidor lógico diferente (siempre que esté en la misma región que la réplica principal).
- tienen su propio objetivo de nivel de servicio, que se puede establecer y cambiar independientemente de la réplica principal.
- admiten hasta 30 réplicas con nombre (para cada réplica principal).
- admiten autenticación diferente para cada réplica con nombre mediante la creación de diferentes inicios de sesión en los servidores lógicos que hospedan las réplicas con nombre.

El objetivo principal de las réplicas con nombre es permitir un escenario de escalado horizontal de lectura de OLTP masivo y mejorar las cargas de trabajo de procesamiento transaccional y analítico híbrido (HTAP). Aquí hay ejemplos de cómo crear estas soluciones:

- [Ejemplo de escalado horizontal de OLTP](https://github.com/Azure-Samples/azure-sql-db-named-replica-oltp-scaleout)
- [Ejemplo de escalado horizontal de HTAP](https://github.com/Azure-Samples/azure-sql-db-named-replica-htap)

Además de los escenarios principales enumerados anteriormente, las réplicas con nombre ofrecen flexibilidad y elasticidad para satisfacer también muchos otros casos de uso:
- [Aislamiento del acceso](hyperscale-named-replica-security-configure.md): puede conceder acceso a una réplica con nombre específica, pero no a la réplica principal ni a otras réplicas con nombre.
- Objetivo de nivel de servicio en función de la carga de trabajo: dado que una réplica con nombre puede tener su propio objetivo de nivel de servicio, es posible usar diferentes réplicas con nombre para diferentes cargas de trabajo y casos de uso. Por ejemplo, una réplica con nombre podría utilizarse para atender solicitudes de Power BI, mientras que otra se puede usar para servir datos a Apache Spark para tareas de ciencia de datos. Cada una puede tener un objetivo de nivel de servicio independiente y escalar de forma independiente.
- Enrutamiento en función de la carga de trabajo: con hasta 30 réplicas con nombre, es posible usar réplicas con nombre en grupos para que una aplicación pueda aislarse de otra. Por ejemplo, se podría usar un grupo de cuatro réplicas con nombre para atender solicitudes procedentes de aplicaciones móviles, mientras que otro grupo de dos réplicas con nombre se puede usar para atender solicitudes procedentes de una aplicación web. Este enfoque permitiría una optimización detallada del rendimiento y los costos de cada grupo.

En el siguiente ejemplo se crea una réplica con nombre `WideWorldImporters_NR` para la base de datos `WideWorldImporters`. La réplica principal usa el objetivo de nivel de servicio HS_Gen5_4, mientras que la réplica con nombre usa HS_Gen5_2. Ambas usan el mismo servidor lógico `MyServer`. Si prefiere usar directamente la API REST, esta opción también es posible: [Bases de datos: creación de una base de datos como réplica con nombre secundaria](/rest/api/sql/2020-11-01-preview/databases/createorupdate#creates-a-database-as-named-replica-secondary).

# <a name="t-sql"></a>[T-SQL](#tab/tsql)
```sql
ALTER DATABASE [WideWorldImporters]
ADD SECONDARY ON SERVER [MyServer] 
WITH (SERVICE_OBJECTIVE = 'HS_Gen5_2', SECONDARY_TYPE = Named, DATABASE_NAME = [WideWorldImporters_NR]);
```
# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
```azurepowershell
New-AzSqlDatabaseSecondary -ResourceGroupName "MyResourceGroup" -ServerName "MyServer" -DatabaseName "WideWorldImporters" -PartnerResourceGroupName "MyResourceGroup" -PartnerServerName "MyServer" -PartnerDatabaseName "WideWorldImporters_NR" -SecondaryServiceObjectiveName HS_Gen5_2
```
# <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)
```azurecli
az sql db replica create -g MyResourceGroup -n WideWorldImporters -s MyServer --secondary-type named --partner-database WideWorldImporters_NR --partner-server MyServer --service-objective HS_Gen5_2
```

---

Dado que no hay ningún movimiento de datos implicado, en la mayoría de los casos se creará una réplica con nombre en aproximadamente un minuto. Una vez que la réplica con nombre esté disponible, será visible desde el portal o desde cualquier herramienta de la línea de comandos, como la CLI de Azure o PowerShell. Una réplica con nombre se puede usar como base de datos habitual de solo lectura. 

> [!NOTE]
> Para ver las preguntas más frecuentes sobre las réplicas con nombre de Hiperescala, consulte [Preguntas frecuentes sobre réplicas con nombre de Hiperescala de Azure SQL Database](service-tier-hyperscale-named-replicas-faq.yml).

### <a name="connecting-to-a-named-replica"></a>Conexión a una réplica con nombre

Para conectarse a una réplica con nombre, debe usar la cadena de conexión de dicha réplica, haciendo referencia a los nombres de servidor y base de datos. No es necesario especificar la opción "ApplicationIntent=ReadOnly", ya que las réplicas con nombre son siempre de solo lectura.

Al igual que en el caso de las réplicas de alta disponibilidad, aunque las réplicas principal, de alta disponibilidad y con nombre comparten los mismos datos en el mismo conjunto de servidores de páginas, las memorias caché de datos de cada réplica con nombre se mantienen sincronizadas con la réplica principal mediante el servicio de registro de transacciones, que reenvía las entradas de registro de la réplica principal a las réplicas con nombre. Como resultado, en función de la carga de trabajo que procese una réplica con nombre, la aplicación de las entradas de registro se puede producir a velocidades diferentes y, por tanto, distintas réplicas podrían tener una latencia de datos diferente en relación con la réplica principal.

### <a name="modifying-a-named-replica"></a>Modificación de una réplica con nombre

Puede definir el objetivo de nivel de servicio de una réplica con nombre al crearla, mediante el comando `ALTER DATABASE` o de cualquier otra manera compatible (CLI de Azure, PowerShell, API REST). Si tiene que cambiar el objetivo de nivel de servicio después de crear la réplica con nombre, puede hacerlo mediante el comando `ALTER DATABASE ... MODIFY` en la propia réplica con nombre. Por ejemplo, si `WideWorldImporters_NR` es la réplica con nombre de la base de datos `WideWorldImporters`, puede hacerlo como se muestra a continuación. 

# <a name="t-sql"></a>[T-SQL](#tab/tsql)
```sql
ALTER DATABASE [WideWorldImporters_NR] MODIFY (SERVICE_OBJECTIVE = 'HS_Gen5_4')
```
# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
```azurepowershell
Set-AzSqlDatabase -ResourceGroup "MyResourceGroup" -ServerName "MyServer" -DatabaseName "WideWorldImporters_NR" -RequestedServiceObjectiveName "HS_Gen5_4"
```
# <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)
```azurecli
az sql db update -g MyResourceGroup -s MyServer -n WideWorldImporters_NR --service-objective HS_Gen5_4
```

---

### <a name="removing-a-named-replica"></a>Eliminación de una réplica con nombre

Para quitar una réplica con nombre, se elimina igual que haría con una base de datos normal. Asegúrese de que está conectado a la base de datos `master` del servidor con la réplica con nombre que desea quitar y, a continuación, use el siguiente comando:

# <a name="t-sql"></a>[T-SQL](#tab/tsql)
```sql
DROP DATABASE [WideWorldImporters_NR];
```
# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
```azurepowershell
Remove-AzSqlDatabase -ResourceGroupName "MyResourceGroup" -ServerName "MyServer" -DatabaseName "WideWorldImporters_NR"
```
# <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)
```azurecli
az sql db delete -g MyResourceGroup -s MyServer -n WideWorldImporters_NR
```
---

> [!IMPORTANT]
> Las réplicas con nombre se quitarán automáticamente cuando se elimine la réplica principal desde la que se han creado.

### <a name="known-issues"></a>Problemas conocidos

#### <a name="partially-incorrect-data-returned-from-sysdatabases"></a>Datos parcialmente incorrectos devueltos desde sys.databases
Durante la versión preliminar pública, los valores de filas devueltos desde `sys.databases` para las réplicas con nombre, en las columnas que no sean `name` y `database_id`, pueden ser incoherentes e incorrectos. Por ejemplo, la columna `compatibility_level` de una réplica con nombre se podría mostrar como 140 aunque la base de datos principal a partir de la que se creó la réplica con nombre esté establecida en 150. Una solución alternativa, cuando sea posible, es obtener los mismos datos mediante la función `DATABASEPROPERTYEX()`, la cual devolverá los datos correctos.

## <a name="geo-replica-in-preview"></a>Réplica geográfica (en versión preliminar)

Con la [replicación geográfica activa](active-geo-replication-overview.md), puede crear una réplica secundaria de lectura de la base de datos de Hiperescala principal en la misma región de Azure o en otra. Las réplicas geográficas se deben crear en un servidor lógico diferente. El nombre de la base de datos de una réplica geográfica siempre coincide con el nombre de la base de datos de la réplica principal.

Al crear una réplica geográfica, todos los datos se copian desde la réplica principal a un conjunto diferente de servidores de páginas. Una réplica geográfica no comparte servidores de páginas con el servidor principal, aunque estén en la misma región. Esta arquitectura proporciona la redundancia necesaria para las conmutaciones por error geográficas.

Las replicaciones geográficas se usan para mantener una copia transaccionalmente coherente de la base de datos mediante la replicación asincrónica. Si una réplica geográfica se encuentra en una región de Azure diferente, se puede usar para la recuperación ante desastres en caso de un desastre o una interrupción en la región primaria. Las réplicas geográficas también se pueden usar para escenarios de escalado horizontal de lectura geográfica.

En Hiperescala, se debe iniciar manualmente una conmutación por error geográfica. Después de la conmutación por error, la nueva réplica principal tendrá un punto de conexión diferente, que hace referencia al nombre del servidor lógico que hospeda la nueva réplica principal. Para más información, consulte [Replicación geográfica activa](active-geo-replication-overview.md).

La replicación geográfica para bases de datos de Hiperescala se encuentra actualmente en versión preliminar, con las siguientes limitaciones:
- Solo se puede crear una réplica geográfica (en la misma región o en otra).
- No se admiten grupos de conmutación por error. 
- No se admite la conmutación por error planeada.
- No se admite la restauración a un momento dado de la réplica geográfica.
- No se admite la creación de una copia de base de datos de la réplica geográfica. 
- No se admite una réplica secundaria de otra secundaria (también conocido como "encadenamiento de réplicas geográficas"). 

## <a name="next-steps"></a>Pasos siguientes

- [Nivel de servicio Hiperescala](service-tier-hyperscale.md)
- [Replicación geográfica activa](active-geo-replication-overview.md)
- [Configuración del acceso aislado para un réplica con nombre de Hiperescala](hyperscale-named-replica-security-configure.md)
- [Preguntas frecuentes sobre réplicas con nombre de Hiperescala de Azure SQL Database](service-tier-hyperscale-named-replicas-faq.yml)
