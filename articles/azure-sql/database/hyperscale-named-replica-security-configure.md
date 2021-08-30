---
title: Configuración de la seguridad de réplicas con nombre para permitir el acceso aislado
description: Conozca las consideraciones relacionadas con la seguridad para configurar y administrar réplicas con nombre para que un usuario pueda acceder a las que tienen nombre, pero no a otras.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.topic: how-to
author: yorek
ms.author: damauri
ms.reviewer: ''
ms.date: 7/27/2021
ms.openlocfilehash: 88410573dbefdfdedb4628babfffc601b19bed36
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121744866"
---
# <a name="configure-isolated-access-to-a-hyperscale-named-replica"></a>Configuración del acceso aislado a una réplica con nombre de Hiperescala
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

En este artículo se describe el procedimiento para conceder acceso a una [réplica con nombre](service-tier-hyperscale-replicas.md) de Hiperescala de Azure SQL sin conceder acceso a la réplica principal o a otras réplicas con nombre. Este escenario permite el aislamiento de los recursos y la seguridad de una réplica con nombre, ya que esta se ejecutará con su propio nodo de proceso, y resulta útil siempre que se necesita acceso de solo lectura aislado a una base de datos de Hiperescala de Azure SQL. Aislado, en este contexto, significa que la CPU y la memoria no se comparten entre la réplica principal y la réplica con nombre, que las consultas que se ejecutan en la réplica con nombre no usan recursos de proceso de la réplica principal o de ninguna otra réplica y que las entidades de seguridad que acceden a la réplica con nombre no pueden acceder a otras réplicas, incluida la principal.

## <a name="create-a-login-in-the-master-database-on-the-primary-server"></a>Creación de un inicio de sesión en la base de datos maestra del servidor principal

En la base de datos `master` del servidor lógico que hospeda la base de datos *principal*, ejecute el código siguiente para crear un inicio de sesión. Use su propia contraseña segura y única.

```sql
create login [third-party-login] with password = 'Just4STRONG_PAZzW0rd!';
```

Recupere el valor hexadecimal del SID para el inicio de sesión creado desde la vista del sistema `sys.sql_logins`:

```sql
select sid from sys.sql_logins where name = 'third-party-login';
```

Deshabilite el inicio de sesión. De esta forma, se impedirá que este inicio de sesión acceda a cualquier base de datos del servidor que hospeda la réplica principal.

```sql
alter login [third-party-login] disable;
```

## <a name="create-a-user-in-the-primary-read-write-database"></a>Creación de un usuario en la base de datos de lectura y escritura principal

Una vez creado el inicio de sesión, conéctese a la réplica de lectura y escritura principal de la base de datos, por ejemplo, WideWorldImporters (puede encontrar un script de ejemplo para restaurarla aquí: [Restauración de una base de datos en Azure SQL](https://github.com/yorek/azure-sql-db-samples/tree/master/samples/01-restore-database)) y cree un usuario de base de datos para ese inicio de sesión:

```sql
create user [third-party-user] from login [third-party-login];
```

Como paso opcional, una vez creado el usuario de la base de datos, puede quitar el inicio de sesión del servidor creado en el paso anterior si existe el problema de que el inicio de sesión se vuelva a habilitar de algún modo. Conéctese a la base de datos maestra del servidor lógico que hospeda la base de datos principal y ejecute lo siguiente:

```sql
drop login [third-party-login];
```

## <a name="create-a-named-replica-on-a-different-logical-server"></a>Creación de una réplica con nombre en un servidor lógico diferente

Cree un servidor lógico de Azure SQL para aislar el acceso a la réplica con nombre. Siga las instrucciones disponibles en [Creación y administración de servidores y bases de datos únicas en Azure SQL Database](single-database-manage.md). Para crear una réplica con nombre, este servidor debe estar en la misma región de Azure que el servidor que hospeda la réplica principal.

Mediante, por ejemplo, la CLI de AZ:

```azurecli
az sql server create -g MyResourceGroup -n MyNamedReplicaServer -l MyLocation --admin-user MyAdminUser --admin-password MyStrongADM1NPassw0rd!
```

A continuación, cree una réplica con nombre para la base de datos principal de este servidor. Por ejemplo, mediante la CLI de AZ:

```azurecli
az sql db replica create -g MyResourceGroup -n WideWorldImporters -s MyPrimaryServer --secondary-type Named --partner-database WideWorldImporters_NR --partner-server MyNamedReplicaServer
```

## <a name="create-a-login-in-the-master-database-on-the-named-replica-server"></a>Creación de un inicio de sesión en la base de datos maestra del servidor de réplica con nombre

Conéctese a la base de datos `master` del servidor lógico que hospeda la réplica con nombre, creada en el paso anterior. Agregue el inicio de sesión mediante el SID recuperado de la réplica principal:

```sql
create login [third-party-login] with password = 'Just4STRONG_PAZzW0rd!', sid = 0x0...1234;
```

En este momento, los usuarios y las aplicaciones que usan `third-party-login` pueden conectarse a la réplica con nombre, pero no a la réplica principal.

## <a name="grant-object-level-permissions-within-the-database"></a>Concesión de permisos de nivel de objeto dentro de la base de datos

Una vez que haya configurado la autenticación de inicio de sesión como se describe, puede usar las instrucciones normales `GRANT`, `DENY` y `REVOKE` para administrar la autorización o los permisos de nivel de objeto dentro de la base de datos. En estas instrucciones, haga referencia al nombre del usuario que creó en la base de datos o a un rol de base de datos que incluya este usuario como miembro. Recuerde ejecutar estos comandos en la réplica principal. Los cambios se propagarán a todas las réplicas secundarias, pero solo serán efectivos en la réplica con nombre donde se creó el inicio de sesión de nivel de servidor.

Recuerde que, de forma predeterminada, a un usuario recién creado se le concede un conjunto mínimo de permisos (por ejemplo, no puede acceder a ninguna tabla de usuario). Si quiere permitir que `third-party-user` lea datos de una tabla, debe conceder explícitamente el permiso `SELECT`:

```sql
grant select on [Application].[Cities] to [third-party-user];
```

Como alternativa a la concesión de permisos individuales en cada tabla, puede agregarle al usuario el `db_datareaders` [rol de base de datos](/sql/relational-databases/security/authentication-access/database-level-roles) para permitirle el acceso de lectura a todas las tablas, o puede usar [esquemas](/sql/relational-databases/security/authentication-access/create-a-database-schema) para [permitir el acceso](/sql/t-sql/statements/grant-schema-permissions-transact-sql) a todas las tablas nuevas y existentes de un esquema.

## <a name="test-access"></a>Prueba del acceso

Puede probar esta configuración mediante cualquier herramienta de cliente e intentar conectarse a la réplica principal y a la réplica con nombre. Por ejemplo, mediante `sqlcmd`, puede intentar conectarse a la réplica principal con el usuario `third-party-login`:

```
sqlcmd -S MyPrimaryServer.database.windows.net -U third-party-login -P Just4STRONG_PAZzW0rd! -d WideWorldImporters
```

Esta acción producirá un error, ya que el usuario no tiene permiso para conectarse al servidor:

```
Sqlcmd: Error: Microsoft ODBC Driver 13 for SQL Server : Login failed for user 'third-party-login'. Reason: The account is disabled.
```

El intento de conectarse a la réplica con nombre se realiza correctamente:

```
sqlcmd -S MyNamedReplicaServer.database.windows.net -U third-party-login -P Just4STRONG_PAZzW0rd! -d WideWorldImporters_NR
```

No se devuelve ningún error y las consultas se pueden ejecutar en la réplica con nombre de acuerdo con los permisos de nivel de objeto concedidos.

Para obtener más información:

* Servidores lógicos de Azure SQL, consulte [¿Qué es un servidor en Azure SQL Database?](logical-servers.md)
* Administración del acceso y los inicios de sesión de la base de datos, consulte [Seguridad de SQL Database: administrar la seguridad del inicio de sesión y el acceso a la base de datos](logins-create-manage.md)
* Permisos de motor de base de datos, consulte [Permisos](/sql/relational-databases/security/permissions-database-engine) 
* Concesión de permisos de objeto, consulte [Permisos de objeto GRANT](/sql/t-sql/statements/grant-object-permissions-transact-sql)



