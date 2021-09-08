---
title: Resolución de diferencias de T-SQL durante la migración
description: Instrucciones de T-SQL que no son totalmente compatibles con Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: migration
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: reference
author: mokabiru
ms.author: mokabiru
ms.reviewer: mathoma
ms.date: 06/17/2021
ms.openlocfilehash: 479800be0301d40fb1f20127f3d35fd11e7ba69e
ms.sourcegitcommit: a038863c0a99dfda16133bcb08b172b6b4c86db8
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/29/2021
ms.locfileid: "113004599"
---
# <a name="t-sql-differences-between-sql-server-and-azure-sql-database"></a>Diferencias T-SQL entre SQL Server y Azure SQL Database

Cuando [se migra la base de datos](migrate-to-database-from-sql-server.md) de SQL Server a Azure SQL Database, es posible que descubra que la base de datos de SQL Server requiere cierta reingeniería antes de que se pueda migrar. En este artículo se proporcionan instrucciones para ayudarle a realizar esta reingeniería y entender las razones subyacentes por las que es necesaria. Para detectar incompatibilidades y migrar bases de datos a Azure SQL Database, use [Data Migration Assistant (DMA)](/sql/dma/dma-overview).

## <a name="overview"></a>Información general

La mayoría de las características de T-SQL que usan las aplicaciones se admiten en Microsoft SQL Server y Azure SQL Database. Por ejemplo, los componentes principales de SQL, como los tipos de datos, los operadores, las funciones de cadena, aritméticas, lógicas y de cursor, funcionan de la misma forma en SQL Server y SQL Database. Pero hay algunas diferencias de T-SQL en los elementos DDL (lenguaje de definición de datos) y DML (lenguaje de manipulación de datos) que generan instrucciones y consultas de T-SQL que solo se admiten parcialmente (como se describe más adelante en este artículo).

Además, hay algunas características y sintaxis que no se admiten en absoluto porque Azure SQL Database se diseñó para aislar las características de cualquier dependencia de las bases de datos del sistema y el sistema operativo. Por lo tanto, la mayoría de las características de nivel de instancia no se admiten en SQL Database. Las instrucciones y opciones de T-SQL no están disponibles si configuran opciones de nivel de instancia y componentes del sistema operativo, o especifican la configuración del sistema de archivos. Cuando se necesitan estas capacidades, suele estar disponible una alternativa adecuada en alguna otra manera de SQL Database o de otra función o servicio de Azure.

Por ejemplo, la alta disponibilidad se compila en Azure SQL Database. Las instrucciones de T-SQL relacionadas con los grupos de disponibilidad no son compatibles con SQL Database y tampoco se admiten las vistas de administración dinámica relacionadas con los grupos de disponibilidad AlwaysOn.

Para obtener una lista de las funciones compatibles y no compatibles con SQL Database, vea [Características de Azure SQL Database](features-comparison.md). Esta página complementa ese artículo y se centra en las instrucciones T-SQL.

## <a name="t-sql-syntax-statements-with-partial-differences"></a>Instrucciones de sintaxis de T-SQL con diferencias parciales

Las instrucciones DDL principales están disponibles, pero no se admiten las extensiones de instrucciones DDL relacionadas con características no admitidas, como la selección de ubicación de archivos en el disco.

- En SQL Server, las instrucciones `CREATE DATABASE` y `ALTER DATABASE` tienen más de 30 opciones. Las instrucciones incluyen la ubicación de archivos, FILESTREAM y opciones de agente de servicio que solo se aplican a SQL Server. Puede que esto no sea importante si se crean bases de datos en SQL Database antes de la migración, pero si se va a migrar código de T-SQL que crea bases de datos, se debe comparar [CREATE DATABASE (Azure SQL Database)](/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-current&preserve-view=true) con la sintaxis de SQL Server en [CREATE DATABASE (SQL Server T-SQL)](/sql/t-sql/statements/create-database-transact-sql?view=sql-server-ver15&preserve-view=true) para asegurarse de que se admiten todas las opciones que se usan. `CREATE DATABASE` para Azure SQL Database también tiene opciones de objetivo de servicio y grupo elástico que se aplican únicamente a SQL Database.
- Las instrucciones`CREATE TABLE` y `ALTER TABLE` tienen las opciones `FILETABLE` y `FILESTREAM` que no se pueden usar en SQL Database porque no se admiten estas características.
- Se admiten las instrucciones `CREATE LOGIN` y `ALTER LOGIN`, pero no ofrecen todas las opciones disponibles en SQL Server. Para que la base de datos sea más portable, SQL Database promueve la utilización de usuarios de base de datos independiente en lugar de inicios de sesión siempre que sea posible. Para más información, consulte [CREATE LOGIN](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-current&preserve-view=true) y [ALTER LOGIN](/sql/t-sql/statements/alter-login-transact-sql?view=azuresqldb-current&preserve-view=true) y [Administración de inicios de sesión y usuarios](logins-create-manage.md).

## <a name="t-sql-syntax-not-supported-in-azure-sql-database"></a>Sintaxis de T-SQL no admitida en Azure SQL Database

Además de las instrucciones de T-SQL relacionadas con las funciones no admitidas descritas en [Comparación de características de Azure SQL Database](features-comparison.md), no se admiten las instrucciones y grupos de instrucciones siguientes. Por tanto, si la base de datos que se va a migrar usa alguna de las siguientes características, vuelva a diseñar su aplicación para eliminar estas características e instrucciones de T-SQL.

- Intercalación de objetos del sistema.
- Conexión relacionada: instrucciones del punto de conexión. SQL Database no admite la autenticación de Windows, pero admite la autenticación de Azure Active Directory. Esto incluye la autenticación de entidades de seguridad federadas de Active Directory con Azure Active Directory. Para más información, vea [Conexión a SQL Database o a Azure Synapse Analytics mediante autenticación de Azure Active Directory](authentication-aad-overview.md).
- Consultas entre bases de datos y entre instancias mediante tres o cuatro nombres de partes. Se admiten los nombres de tres partes que hacen referencia a la base de datos `tempdb` y a la base de datos actual. La [consulta elástica](elastic-query-overview.md) admite referencias de solo lectura a tablas de otras bases de datos MSSQL.
- Encadenamiento de propiedad entre bases de datos y la propiedad de base de datos `TRUSTWORTHY`.
- `EXECUTE AS LOGIN`. En su lugar, use `EXECUTE AS USER`.
- Administración extensible de claves (EKM) para claves de cifrado. Las [claves administradas por el cliente](transparent-data-encryption-byok-overview.md) del Cifrado de datos transparente (TDE) y las [claves maestras de columna](always-encrypted-azure-key-vault-configure.md) de Always Encrypted se pueden almacenar en Azure Key Vault.
- Eventos: notificaciones de eventos, notificaciones de consulta.
- Propiedades de archivo: sintaxis relacionada con el nombre de archivo de base de datos, la selección de ubicación, el tamaño y otras propiedades de archivo administradas automáticamente por SQL Database.
- Alta disponibilidad: sintaxis relacionada con la alta disponibilidad y la recuperación de bases de datos, que administra SQL Database. Esto incluye la sintaxis de las copias de seguridad, restauración, Always On, creación de reflejos de las bases de datos, trasvase de registros, modos de recuperación.
- Sintaxis relacionada con la replicación de instantáneas, transaccionales y de mezcla, que no está disponible en SQL Database. Se admiten [suscripciones de replicación](replication-to-sql-database.md).
- Funciones: `fn_get_sql`, `fn_virtualfilestats` y `fn_virtualservernodes`.
- Configuración de instancia: sintaxis relacionada con la memoria del servidor, subprocesos de trabajo, afinidad de CPU y marcas de seguimiento. Use niveles de servicio y tamaños de proceso en su lugar.
- `KILL STATS JOB`.
- `OPENQUERY`, `OPENDATASOURCE` y nombres de cuatro partes.
- .NET Framework: integración CLR
- Búsqueda semántica
- Credenciales de servidor: en su lugar, use [credenciales con ámbito de base de datos](/sql/t-sql/statements/create-database-scoped-credential-transact-SQL).
- Permisos de nivel de servidor: no se admiten los permisos de nivel de servidor `GRANT`, `REVOKE` y `DENY`. Algunos permisos de nivel de servidor se reemplazan por permisos de nivel de base de datos o se conceden implícitamente por roles de servidor integrados. Algunas vistas de catálogo y DMV de nivel de servidor tienen vistas de nivel de base de datos similares.
- `SET REMOTE_PROC_TRANSACTIONS`
- `SHUTDOWN`
- `sp_addmessage`
- `sp_configure` y `RECONFIGURE`. Se admite [ALTER DATABASE SCOPED CONFIGURATION](/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql).
- `sp_helpuser`
- `sp_migrate_user_to_contained`
- Agente SQL Server: Sintaxis que se basa en el Agente SQL Server o la base de datos MSDB: alertas, operadores, servidores de administración central. Use el scripting como PowerShell en su lugar.
- Auditoría de SQL Server: use [auditoría](auditing-overview.md) de SQL Database en su lugar.
- Seguimiento de SQL Server.
- Marcas de seguimiento.
- Depuración de T-SQL
- Desencadenadores LOGON o con ámbito de servidor
- Instrucción `USE`: para cambiar el contexto de base de datos a una base de datos distinta, debe crear una nueva conexión a esa base de datos.

## <a name="full-t-sql-reference"></a>Referencia completa de T-SQL

Para obtener más información sobre la gramática de T-SQL, su uso y ejemplos, consulte [Referencia de T-SQL (motor de base de datos)](/sql/t-sql/language-reference).

### <a name="about-the-applies-to-tags"></a>Acerca de las etiquetas "Se aplica a"

La referencia de T-SQL incluye artículos relacionados con todas las versiones recientes de SQL Server. Debajo del título del artículo hay una barra de iconos donde se muestran las plataformas de MSSQL y se indica la aplicabilidad. Por ejemplo, los grupos de disponibilidad se introdujeron en SQL Server 2012. En el artículo [CREATE AVAILABILITY GROUP](/sql/t-sql/statements/create-availability-group-transact-sql) se indica que la instrucción se aplica a **SQL Server (a partir de 2012)** . La instrucción no se aplica a SQL Server 2008, SQL Server 2008 R2, Azure SQL Database, Azure Synapse Analytics o Almacenamiento de datos paralelos.

En algunos casos, la línea general de un artículo se puede usar en un producto, pero existen diferencias poco significativas entre los productos. Las diferencias se indican en los puntos medios del artículo según corresponda. Por ejemplo, el artículo `CREATE TRIGGER` está disponible en SQL Database. No obstante, la opción `ALL SERVER` para desencadenadores de nivel de servidor indica que no se pueden usar desencadenadores de nivel de servidor en SQL Database. Use desencadenadores de nivel de base de datos en su lugar.

## <a name="next-steps"></a>Pasos siguientes

Para obtener una lista de las funciones compatibles y no compatibles con SQL Database, vea [Características de Azure SQL Database](features-comparison.md).

Para detectar problemas de compatibilidad en las bases de datos de SQL Server antes de migrar a Azure SQL Database y para migrar las bases de datos, use [Data Migration Assistant (DMA)](/sql/dma/dma-overview).