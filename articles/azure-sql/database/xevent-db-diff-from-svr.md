---
title: Eventos extendidos
description: Describe los eventos extendidos (XEvents) en Azure SQL Database y cómo las sesiones de eventos difieren ligeramente de las sesiones de eventos en Microsoft SQL Server.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: reference
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.reviewer: ''
ms.date: 07/23/2021
ms.openlocfilehash: 79226ed8fa4d4e78120a0c91b672d4cfa23712fc
ms.sourcegitcommit: 98e126b0948e6971bd1d0ace1b31c3a4d6e71703
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/26/2021
ms.locfileid: "114675248"
---
# <a name="extended-events-in-azure-sql-database"></a>Eventos extendidos en Base de datos SQL de Azure 
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

[!INCLUDE [sql-database-xevents-selectors-1-include](../../../includes/sql-database-xevents-selectors-1-include.md)]

El conjunto de características de eventos extendidos en Azure SQL Database es un subconjunto sólido de las características en SQL Server e Instancia administrada de Azure SQL.

*XEvents* es un sobrenombre informal que a veces se usa para los eventos extendidos en blogs y otras referencias informales.

Encontrará información adicional sobre los eventos extendidos en:

- [Inicio rápido: eventos extendidos en SQL Server](/sql/relational-databases/extended-events/quick-start-extended-events-in-sql-server)
- [Eventos extendidos](/sql/relational-databases/extended-events/extended-events)

## <a name="prerequisites"></a>Requisitos previos

En este artículo se da por sentado que ya tiene algunos conocimientos sobre:

- [Azure SQL Database](https://azure.microsoft.com/services/sql-database/)
- [Eventos extendidos](/sql/relational-databases/extended-events/extended-events)

- La mayor parte de nuestra documentación acerca de los eventos extendidos se aplica a SQL Server, Azure SQL Database e Instancia administrada de Azure SQL.

Exposición anterior a los elementos siguientes es útil cuando se elige el archivo de eventos como [destino](#AzureXEventsTargets):

- [Servicio Azure Storage](https://azure.microsoft.com/services/storage/)

- [Azure PowerShell con Azure Storage](/powershell/module/az.storage/)

## <a name="code-samples"></a>Ejemplos de código

Los artículos relacionados proporcionan dos ejemplos de código:

- [Código de destino de búfer en anillo para eventos extendidos en Azure SQL Database](xevent-code-ring-buffer.md)

  - Breve script de Transact-SQL simple.
  - En el artículo de ejemplo de código, hacemos hincapié en que, cuando haya terminado con un destino de búfer en anillo, debe liberar sus recursos mediante la ejecución de una instrucción alter-drop `ALTER EVENT SESSION ... ON DATABASE DROP TARGET ...;`. Más adelante, puede agregar otra instancia de búfer en anillo de `ALTER EVENT SESSION ... ON DATABASE ADD TARGET ...`.

- [Código de destino del archivo de evento para eventos extendidos en Azure SQL Database](xevent-code-event-file.md)

  - Fase 1: es PowerShell para crear un contenedor de Azure Storage.
  - Fase 2: es Transact-SQL que usa el contenedor de Azure Storage

## <a name="transact-sql-differences"></a>Diferencias de Transact-SQL

- Cuando se ejecuta el comando [CREATE EVENT SESSION](/sql/t-sql/statements/create-event-session-transact-sql) en SQL Server, se usa la cláusula **ON SERVER** . Pero en Azure SQL Database se usa la cláusula **ON DATABASE** en su lugar.
- La cláusula **ON DATABASE** se aplica también a los comandos Transact-SQL [ALTER EVENT SESSION](/sql/t-sql/statements/alter-event-session-transact-sql) y [DROP EVENT SESSION](/sql/t-sql/statements/drop-event-session-transact-sql).

- Un procedimiento recomendado es incluir la opción de sesión de eventos de **STARTUP_STATE = ON** en sus instrucciones **CREATE EVENT SESSION** o **ALTER EVENT SESSION**.
  - El valor **= ON** admite un reinicio automático después de una reconfiguración de la base de datos lógica debida a una conmutación por error.

## <a name="new-catalog-views"></a>Nuevas vistas de catálogo

La característica eventos extendidos es compatible con varias [vistas de catálogo](/sql/relational-databases/system-catalog-views/catalog-views-transact-sql). Las vistas de catálogo le informan sobre *metadatos o definiciones* de sesiones de eventos creadas por el usuario en la base de datos actual. Las vistas no devuelven información acerca de las instancias de sesiones de eventos activas.

| Nombre de<br/>vista de catálogo | Descripción |
|:--- |:--- |
| `sys.database_event_session_actions` |Devuelve una fila por cada acción en cada evento de una sesión de eventos. |
| `sys.database_event_session_events` |Devuelve una fila por cada evento de una sesión de eventos. |
| `sys.database_event_session_fields` |Devuelve una fila por cada columna personalizable que se estableció de forma explícita en los eventos y destinos. |
| `sys.database_event_session_targets` |Devuelve una fila para cada destino de evento de una sesión de eventos. |
| `sys.database_event_sessions` |Devuelve una fila por cada sesión de eventos en la base de datos. |

En Microsoft SQL Server, hay vistas de catálogo similares con nombres que incluyen *.server\_* en lugar de *.database\_* . El patrón de nombre es como `sys.server_event_%`.

## <a name="new-dynamic-management-views-dmvs"></a>Nuevas vistas de administración dinámica [(DMV)](/sql/relational-databases/system-dynamic-management-views/system-dynamic-management-views)

Azure SQL Database tiene [vistas de administración dinámica (DMV)](/sql/relational-databases/system-dynamic-management-views/extended-events-dynamic-management-views) que admiten eventos extendidos. Las DMV le informan sobre las sesiones de eventos *activas* .

| Nombre de DMV | Descripción |
|:--- |:--- |
| `sys.dm_xe_database_session_event_actions` |Devuelve información sobre las acciones de la sesión de eventos. |
| `sys.dm_xe_database_session_events` |Devuelve información sobre los eventos de la sesión. |
| `sys.dm_xe_database_session_object_columns` |Muestra los valores de configuración de los objetos enlazados a una sesión. |
| `sys.dm_xe_database_session_targets` |Devuelve información acerca de los destinos de la sesión. |
| `sys.dm_xe_database_sessions` |Devuelve una fila para cada sesión de eventos del ámbito de la base de datos actual. |

En Microsoft SQL Server, las vistas de catálogo similares tienen nombres sin la parte *\_database* del nombre, como:

- `sys.dm_xe_sessions` en lugar de `sys.dm_xe_database_sessions`.

### <a name="dmvs-common-to-both"></a>DMV comunes

Para eventos extendidos hay DMV adicionales que son comunes a Azure SQL Database, Instancia administrada de Azure SQL y Microsoft SQL Server:

- `sys.dm_xe_map_values`
- `sys.dm_xe_object_columns`
- `sys.dm_xe_objects`
- `sys.dm_xe_packages`

<a name="sqlfindseventsactionstargets" id="sqlfindseventsactionstargets"></a>

## <a name="find-the-available-extended-events-actions-and-targets"></a>Búsqueda de los eventos extendidos, acciones y destinos disponibles

Para obtener una lista de los eventos, las acciones y el destino disponibles, use la consulta de ejemplo:

```sql
SELECT
        o.object_type,
        p.name         AS [package_name],
        o.name         AS [db_object_name],
        o.description  AS [db_obj_description]
    FROM
                   sys.dm_xe_objects  AS o
        INNER JOIN sys.dm_xe_packages AS p  ON p.guid = o.package_guid
    WHERE
        o.object_type in
            (
            'action',  'event',  'target'
            )
    ORDER BY
        o.object_type,
        p.name,
        o.name;
```

<a name="AzureXEventsTargets" id="AzureXEventsTargets"></a> &nbsp;

## <a name="targets-for-your-azure-sql-database-event-sessions"></a>Destinos para las sesiones de eventos de Azure SQL Database

Estos son los destinos que pueden capturar los resultados de las sesiones de eventos en Azure SQL Database:

- [Destino de búfer de anillo](/sql/relational-databases/extended-events/targets-for-extended-events-in-sql-server#ring_buffer-target) : guarda brevemente los datos en la memoria.
- [Destino del contador de eventos de](/sql/relational-databases/extended-events/targets-for-extended-events-in-sql-server#event_counter-target) :cuenta todos los eventos que se producen durante una sesión de eventos extendidos.
- [Destino de archivo de evento](/sql/relational-databases/extended-events/targets-for-extended-events-in-sql-server#event_file-target) : escribe búferes completos en un contenedor de Azure Storage.

La API [Seguimiento de eventos para Windows (ETW)](/dotnet/framework/wcf/samples/etw-tracing) no está disponible para eventos extendidos en Azure SQL Database.

## <a name="restrictions"></a>Restricciones

Hay un par de diferencias relacionadas con la seguridad que se adaptan al entorno de nube de Azure SQL Database:

- Los eventos extendidos se basan en el modelo de aislamiento de inquilino único. Una sesión de eventos en una base de datos no puede tener acceso a datos o eventos desde otra base de datos.
- No se puede emitir una instrucción `CREATE EVENT SESSION` en el contexto de la base de datos `master`.
    
## <a name="permission-model"></a>Nombre del permiso

Debe tener permiso de **Control** en la base de datos para emitir una instrucción `CREATE EVENT SESSION`. El propietario de la base de datos (dbo) tiene permiso de **Control** .

### <a name="storage-container-authorizations"></a>Autorizaciones de contenedor de almacenamiento

El token SAS genere para el contenedor de Azure Storage debe especificar **rwl** para los permisos. El valor **rwl** proporciona los siguientes permisos:

- Lectura
- Escritura
- List

## <a name="performance-considerations"></a>Consideraciones de rendimiento

Existen escenarios donde un uso intensivo de eventos extendidos puede acumular más memoria activa de la que sería conveniente para el buen estado de todo el sistema. Por ello, Azure SQL Database establece y ajusta de forma dinámica los límites en la cantidad de memoria activa que puede acumularse en una sesión de eventos. En el cálculo dinámico se incluyen muchos factores.

Hay un límite de memoria disponible para las sesiones de XEvent en Azure SQL Database:
  - En una única instancia de Azure SQL Database en el modelo de compra de DTU, cada base de datos puede usar hasta 128 MB. Esto se eleva a 256 MB solo en el nivel prémium.
  - En una única instancia de Azure SQL Database en el modelo de compra de núcleo virtual, cada base de datos puede usar hasta 128 MB.
  - En un grupo elástico, las bases de datos individuales están limitadas por los límites de base de datos única y, en total, no pueden superar los 512 MB.

Si recibe un mensaje de error que indica que se aplicó un máximo de memoria, algunas acciones correctivas que puede tomar son:

- Ejecutar menos sesiones de eventos simultáneas.
- A través de sus instrucciones **CREATE** y **ALTER** para las sesiones de eventos, reducir la cantidad de memoria que especifica en la cláusula **MAX\_MEMORY**.

### <a name="network-latency"></a>Latencia de red

El destino del **archivo de eventos** puede experimentar latencia de red o errores al almacenar datos en los blobs de Azure Storage. Otros eventos en Azure SQL Database podrían retrasarse mientras esperan a que se complete la comunicación de red. Este retraso puede reducir la carga de trabajo.

- Para mitigar este riesgo de rendimiento, evite establecer la opción **EVENT_RETENTION_MODE** en **NO_EVENT_LOSS** en las definiciones de la sesión de eventos.

## <a name="related-links"></a>Vínculos relacionados

- [Cmdlets de Azure Storage](/powershell/module/Azure.Storage)
- [Usar Azure PowerShell con Azure Storage](/powershell/module/az.storage/)
- [Uso del almacenamiento de blobs de .NET](../../storage/blobs/storage-quickstart-blobs-dotnet.md)
- [CREATE CREDENTIAL (Transact-SQL)](/sql/t-sql/statements/create-credential-transact-sql)
- [CREATE EVENT SESSION (Transact-SQL)](/sql/t-sql/statements/create-event-session-transact-sql)
- La página web de *actualizaciones del servicio* de Azure, restringida por parámetros a Azure SQL Database:
  - [https://azure.microsoft.com/updates/?service=sql-database](https://azure.microsoft.com/updates/?service=sql-database)

<!--
('lock_acquired' event.)

- Code sample for SQL Server: [Determine Which Queries Are Holding Locks](/sql/relational-databases/extended-events/determine-which-queries-are-holding-locks)
- Code sample for SQL Server: [Find the Objects That Have the Most Locks Taken on Them](/sql/relational-databases/extended-events/find-the-objects-that-have-the-most-locks-taken-on-them)
-->