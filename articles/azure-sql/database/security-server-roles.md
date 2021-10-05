---
title: Roles del servidor
titleSuffix: Azure SQL Database
description: En este artículo se proporciona una introducción a los roles del servidor lógico de Azure SQL Database.
ms.service: sql-database
ms.subservice: security
author: AndreasWolter
ms.author: anwolter
ms.topic: conceptual
ms.date: 09/02/2021
ms.reviewer: vanto
ms.openlocfilehash: cfb24ee98bf00fa46f75b0bef321a7b1694e2113
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128630269"
---
# <a name="azure-sql-database-server-roles-for-permission-management"></a>Roles del servidor de Azure SQL Database para la administración de permisos

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

En Azure SQL Database, el servidor es un concepto lógico y no se pueden conceder permisos en un nivel de servidor. Para simplificar la administración de permisos, Azure SQL Database proporciona un conjunto de roles fijos de nivel de servidor para ayudarle a administrar los permisos en un [servidor lógico](logical-servers.md). Los roles son entidades de seguridad que agrupan inicios de sesión.

> [!NOTE]
> En este artículo, el concepto de *roles* es similar al de los *grupos* del sistema operativo Windows.

Estos roles fijos de nivel de servidor especiales usan el prefijo **##MS_** y el sufijo **##** para distinguirse de otras entidades de seguridad normales creadas por el usuario.

Como sucede con las instancias locales de SQL Server, los permisos de servidor se organizan jerárquicamente. Los permisos que mantienen estos roles de nivel de servidor se pueden propagar a los permisos de base de datos. Para que los permisos se propaguen eficazmente a la base de datos, un inicio de sesión debe tener una cuenta de usuario en la base de datos.

Por ejemplo, el rol de nivel de servidor **##MS_ServerStateReader##** contiene el permiso **VIEW SERVER STATE**. Si un inicio de sesión que es miembro de este rol tiene una cuenta de usuario en las bases de datos *maestra* y *WideWorldImporters*, este usuario tendrá el permiso **VIEW DATABASE STATE** en esas dos bases de datos. 

> [!NOTE]
> Cualquier permiso se puede denegar dentro de las bases de datos de usuario, lo que invalida la concesión en todo el servidor mediante la pertenencia a roles. Pero en la base de datos del sistema *maestra*, no se pueden conceder ni denegar permisos.

Azure SQL Database proporciona actualmente tres roles fijos de servidor. Los permisos que se conceden a los roles fijos de servidor no se pueden cambiar y no pueden tener otros roles fijos como miembros. Puede agregar inicios de sesión SQL de nivel servidor como miembros a roles de nivel de servidor.

> [!IMPORTANT]
> Cada miembro de un rol fijo de servidor puede agregar otros inicios de sesión a ese mismo rol.

Para obtener más información sobre los inicios de sesión y los usuarios de Azure SQL Database, vea [Autorización del acceso de base de datos a SQL Database, Instancia administrada de SQL y Azure Synapse Analytics](logins-create-manage.md).
  
## <a name="built-in-server-level-roles"></a>Roles de nivel de servidor integrados

En la tabla siguiente se muestran los roles fijos de nivel de servidor y sus capacidades.  
  
|Rol de nivel de servidor integrado|Descripción|  
|------------------------------|-----------------|  
|**##MS_DefinitionReader##**|Los miembros del rol fijo de servidor **##MS_DefinitionReader##** pueden leer todas las vistas de catálogo cubiertas por **VIEW ANY DEFINITION**, respectivamente **VIEW DEFINITION** en cualquier base de datos en la que el miembro de este rol tenga una cuenta de usuario.|  
|**##MS_ServerStateReader##**|Los miembros del rol fijo de servidor **##MS_ServerStateReader##** pueden leer todas las vistas de administración dinámica (DMV) y las funciones cubiertas por **VIEW SERVER STATE**, respectivamente **VIEW DATABASE STATE** en cualquier base de datos en la que el miembro de este rol tenga una cuenta de usuario.|
|**##MS_ServerStateManager##**|Los miembros del rol fijo de servidor **##MS_ServerStateManager##** tienen los mismos permisos que el rol **##MS_ServerStateReader###.** Además, contiene el permiso **ALTER SERVER STATE**, que permite el acceso a varias operaciones de administración, como `DBCC FREEPROCCACHE`, `DBCC FREESYSTEMCACHE ('ALL')` y `DBCC SQLPERF()`; |  


## <a name="permissions-of-fixed-server-roles"></a>Permisos de roles fijos de servidor

Cada rol integrado de nivel de servidor tiene determinados permisos asignados. En la tabla siguiente se muestran los permisos asignados a los roles de nivel de servidor. También se muestran los permisos de nivel de base de datos heredados si existe una cuenta de usuario en la base de datos.
  
|Rol fijo de nivel de servidor|Permisos a nivel de servidor|Permisos de nivel de base de datos (si existe un usuario de base de datos que coincida con el inicio de sesión)  
|-------------|----------|-----------------|  
|**##MS_DefinitionReader##**|VIEW ANY DATABASE, VIEW ANY DEFINITION, VIEW ANY SECURITY DEFINITION|VIEW DEFINITION, VIEW SECURITY DEFINITION|  
|**##MS_ServerStateReader##**|VIEW SERVER STATE, VIEW SERVER PERFORMANCE STATE, VIEW SERVER SECURITY STATE|VIEW DATABASE STATE, VIEW DATABASE PERFORMANCE STATE, VIEW DATABASE SECURITY STATE|  
|**##MS_ServerStateManager##**|ALTER SERVER STATE, VIEW SERVER STATE, VIEW SERVER PERFORMANCE STATE, VIEW SERVER SECURITY STATE|VIEW DATABASE STATE, VIEW DATABASE PERFORMANCE STATE, VIEW DATABASE SECURITY STATE|   
  
  
## <a name="working-with-server-level-roles"></a>Trabajo con roles de nivel de servidor

En la tabla siguiente se explican las vistas del sistema y las funciones que se pueden utilizar para trabajar con roles de nivel de servidor en Azure SQL Database.  
  
|Característica|Tipo|Descripción|  
|-------------|----------|-----------------|  
|[IS_SRVROLEMEMBER &#40;Transact-SQL&#41;](/sql/t-sql/functions/is-srvrolemember-transact-sql)|Metadatos|Indica si un inicio de sesión de SQL es miembro del rol de nivel de servidor especificado.|  
|[sys.server_role_members &#40;Transact-SQL&#41;](/sql/relational-databases/system-catalog-views/sys-server-role-members-transact-sql)|Metadatos|Devuelve una fila por cada miembro de cada rol de nivel de servidor.|
|[sys.sql_logins &#40;Transact-SQL&#41;](/sql/relational-databases/system-catalog-views/sys-sql-logins-transact-sql)|Metadatos|Devuelve una fila por cada inicio de sesión de SQL.|
|[ALTER SERVER ROLE &#40;Transact-SQL&#41;](/sql/t-sql/statements/alter-server-role-transact-sql)|Get-Help|Cambia la pertenencia de un rol del servidor.| 

## <a name="examples"></a><a name="_examples"></a> Ejemplos

En los ejemplos de esta sección se muestra cómo trabajar con roles de nivel de servidor en Azure SQL Database.  

### <a name="a-adding-a-sql-login-to-a-server-level-role"></a>A Adición de un inicio de sesión de SQL a un rol de nivel de servidor

En el ejemplo siguiente se agrega el inicio de sesión de SQL "Jiao" al rol de nivel de servidor ##MS_ServerStateReader##. Esta instrucción se debe ejecutar en la base de datos maestra virtual.
  
```sql  
ALTER SERVER ROLE ##MS_ServerStateReader##
    ADD MEMBER Jiao;  
GO
```  

### <a name="b-listing-all-principals-sql-authentication-which-are-members-of-a-server-level-role"></a>B Enumeración de todas las entidades de seguridad (autenticación SQL) que son miembros de un rol de nivel de servidor

La instrucción siguiente devuelve todos los miembros de cualquier rol fijo de nivel de servidor mediante las vistas de catálogo `sys.server_role_members` y `sys.sql_logins`. Esta instrucción se debe ejecutar en la base de datos maestra virtual.
  
```sql  
SELECT
        sql_logins.principal_id         AS MemberPrincipalID
    ,   sql_logins.name                 AS MemberPrincipalName
    ,   roles.principal_id              AS RolePrincipalID
    ,   roles.name                      AS RolePrincipalName
FROM sys.server_role_members AS server_role_members
INNER JOIN sys.server_principals AS roles
    ON server_role_members.role_principal_id = roles.principal_id
INNER JOIN sys.sql_logins AS sql_logins 
    ON server_role_members.member_principal_id = sql_logins.principal_id
;  
GO  
```  
### <a name="c-complete-example-adding-a-login-to-a-server-level-role-retrieving-metadata-for-role-membership-and-permissions-and-running-a-test-query"></a>C. Ejemplo completo: Adición de un inicio de sesión a un rol de nivel de servidor, recuperación de metadatos para la pertenencia a roles y permisos, y ejecución de una consulta de prueba

#### <a name="part-1-preparing-role-membership-and-user-account"></a>Parte 1: Preparación de la pertenencia a roles y la cuenta de usuario

Ejecute este comando desde la base de datos maestra virtual.

```sql  
ALTER SERVER ROLE ##MS_ServerStateReader##
    ADD MEMBER Jiao

-- check membership in metadata:
select IS_SRVROLEMEMBER('##MS_ServerStateReader##', 'Jiao')
--> 1 = Yes

SELECT
        sql_logins.principal_id         AS MemberPrincipalID
    ,   sql_logins.name                 AS MemberPrincipalName
    ,   roles.principal_id              AS RolePrincipalID
    ,   roles.name                      AS RolePrincipalName
FROM sys.server_role_members AS server_role_members
INNER JOIN sys.server_principals AS roles
    ON server_role_members.role_principal_id = roles.principal_id
INNER JOIN sys.sql_logins AS sql_logins 
    ON server_role_members.member_principal_id = sql_logins.principal_id
;   
GO  
``` 

El conjunto de resultados es el siguiente:
  
```
MemberPrincipalID MemberPrincipalName RolePrincipalID RolePrincipalName        
------------- ------------- ------------------ -----------   
6         Jiao      11            ##MS_ServerStateReader##   
```  

Ejecute este comando desde una base de datos de usuario.

```sql  
-- Creating a database-User for 'Jiao'
CREATE USER Jiao
    FROM LOGIN Jiao
;   
GO  
``` 

#### <a name="part-2-testing-role-membership"></a>Parte 2: Prueba de la pertenencia a roles

Inicie sesión como `Jiao` y conéctese a la base de datos de usuario utilizada en el ejemplo.

```sql  
-- retrieve server-level permissions of currently logged on User
SELECT * FROM sys.fn_my_permissions(NULL, 'Server')
;  

-- check server-role membership for `##MS_ServerStateReader##` of currently logged on User
SELECT USER_NAME(), IS_SRVROLEMEMBER('##MS_ServerStateReader##')
--> 1 = Yes

-- Does the currently logged in User have the `VIEW DATABASE STATE`-permission?
SELECT HAS_PERMS_BY_NAME(NULL, 'DATABASE', 'VIEW DATABASE STATE'); 
--> 1 = Yes

-- retrieve database-level permissions of currently logged on User
SELECT * FROM sys.fn_my_permissions(NULL, 'DATABASE')
GO 

-- example query:
SELECT * FROM sys.dm_exec_query_stats
--> will return data since this user has the necessary permission

``` 

## <a name="limitations-of-server-level-roles"></a>Limitaciones de los roles de nivel de servidor

- Las asignaciones de roles pueden tardar hasta cinco minutos en surtir efecto. Además, para las sesiones existentes, los cambios en las asignaciones de roles del servidor no tienen efecto hasta que la conexión se cierra y se vuelve a abrir. Esto se debe a la arquitectura distribuida entre la base de datos *maestra* y otras bases de datos en el mismo servidor lógico.
  - Solución alternativa parcial: para reducir el período de espera y asegurarse de que las asignaciones de roles del servidor están actualizadas en una base de datos, un administrador del servidor o un administrador de Azure AD puede ejecutar `DBCC FLUSHAUTHCACHE` en las bases de datos de usuario a las que tiene acceso el inicio de sesión. Los usuarios que han iniciado sesión actualmente tienen que volver a conectarse después de ejecutar `DBCC FLUSHAUTHCACHE` para que los cambios de pertenencia surtan efecto en ellos.

- `IS_SRVROLEMEMBER()` no se admite en la base de datos *maestra*.


## <a name="see-also"></a>Consulte también

- [Roles de nivel de base de datos](/sql/relational-databases/security/authentication-access/database-level-roles)   
- [Vistas de catálogo de seguridad &#40;Transact-SQL&#41;](/sql/relational-databases/system-catalog-views/security-catalog-views-transact-sql)   
- [Funciones de seguridad &#40;Transact-SQL&#41;](/sql/t-sql/functions/security-functions-transact-sql)   
- [Permisos &#40;motor de base de datos&#41;](/sql/relational-databases/security/permissions-database-engine)
- [DBCC FLUSHAUTHCACHE (Transact-SQL)](/sql/t-sql/database-console-commands/dbcc-flushauthcache-transact-sql)
