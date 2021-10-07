---
title: 'Creación de usuarios en Azure Database for PostgreSQL: servidor único'
description: En este artículo se describe cómo puede crear cuentas de usuario para interactuar con Azure Database for PostgreSQL con un único servidor.
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.topic: how-to
ms.date: 09/22/2019
ms.openlocfilehash: a91b334b5494d8db44c86352c95fd7b4d1ea2c14
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128604082"
---
# <a name="create-users-in-azure-database-for-postgresql---single-server"></a>Creación de usuarios en Azure Database for PostgreSQL con un único servidor

En este artículo se describe cómo puede crear usuarios en un servidor de Azure Database for PostgreSQL.

Si quiere obtener información sobre cómo crear y administrar usuarios de suscripciones de Azure y sus privilegios, puede consultar el [artículo sobre el control de acceso basado en rol (RBAC) de Azure](../role-based-access-control/built-in-roles.md) o ver [cómo personalizar los roles](../role-based-access-control/custom-roles.md).

## <a name="the-server-admin-account"></a>La cuenta de administrador del servidor

La primera vez que creó su instancia de Azure Database for PostgreSQL, proporcionó un nombre de usuario y una contraseña de administrador del servidor. Para más información, puede seguir la [Guía de inicio rápido](quickstart-create-server-database-portal.md) para consultar un método paso a paso. Como el nombre de usuario administrador del servidor es un nombre personalizado, puede buscar el nombre de usuario administrador del servidor en Azure Portal.

El servidor de Azure Database for PostgreSQL se creó con los tres roles predeterminados definidos. Puede ver estos roles con la ejecución del comando: `SELECT rolname FROM pg_roles;`.

- azure_pg_admin
- azure_superuser
- usuario administrador del servidor

El usuario administrador del servidor es un miembro del rol azure_pg_admin. No obstante, la cuenta de administrador del servidor no forma parte del rol azure_superuser. Como este servicio es un servicio PaaS administrado, solo Microsoft forma parte del rol de superusuario.

El motor de PostgreSQL usa privilegios para controlar el acceso a objetos de base de datos, como se describe en la [documentación de productos de PostgreSQL](https://www.postgresql.org/docs/current/static/sql-createrole.html). En Azure Database for PostgreSQL, se le conceden estos privilegios al usuario administrador del servidor: LOGIN, NOSUPERUSER, INHERIT, CREATEDB, CREATEROLE y NOREPLICATION

La cuenta de usuario administrador del servidor puede usarse para crear usuarios adicionales y conceder a tales usuarios el rol azure_pg_admin. Además, la cuenta de administrador del servidor puede usarse para crear usuarios con menos privilegios y roles que tengan acceso a esquemas y base de datos individuales.

## <a name="how-to-create-additional-admin-users-in-azure-database-for-postgresql"></a>Cómo crear usuarios administradores adicionales en Azure Database for PostgreSQL

1. Obtenga la información de conexión y el nombre de usuario administrador.
   Para conectarse al servidor de bases de datos, es preciso utilizar el nombre completo del servidor y las credenciales de inicio de sesión de administrador. Puede encontrar fácilmente el nombre del servidor y la información de inicio de sesión en la página **Información general** del servidor o en la página **Propiedades** de Azure Portal.

2. Use la cuenta y la contraseña de administrador para conectarse a su servidor de base de datos. Use la herramienta de cliente preferida, como pgAdmin o psql.
   Si no está seguro de cómo conectarse, consulte la [guía de inicio rápido](./quickstart-create-server-database-portal.md).

3. Modifique y ejecute el siguiente código SQL. Reemplace el nuevo nombre de usuario por el valor de marcador de posición <new_user> y reemplace la contraseña de marcador de posición con la contraseña segura anterior. 

   ```sql
   CREATE ROLE <new_user> WITH LOGIN NOSUPERUSER INHERIT CREATEDB CREATEROLE NOREPLICATION PASSWORD '<StrongPassword!>';

   GRANT azure_pg_admin TO <new_user>;
   ```

## <a name="how-to-create-database-users-in-azure-database-for-postgresql"></a>Creación de usuarios de base de datos en Azure Database for PostgreSQL

1. Obtenga la información de conexión y el nombre de usuario administrador.
   Para conectarse al servidor de bases de datos, es preciso utilizar el nombre completo del servidor y las credenciales de inicio de sesión de administrador. Puede encontrar fácilmente el nombre del servidor y la información de inicio de sesión en la página **Información general** del servidor o en la página **Propiedades** de Azure Portal.

2. Use la cuenta y la contraseña de administrador para conectarse a su servidor de base de datos. Use la herramienta de cliente preferida, como pgAdmin o psql.

3. Modifique y ejecute el siguiente código SQL. Reemplace el valor de marcador de posición `<db_user>` por su nuevo nombre de usuario deseado y el valor de marcador de posición `<newdb>` por su propio nombre de base de datos. Reemplace la contraseña de marcador de posición con su propia contraseña segura.

   Esta sintaxis de código sql crea nueva base de datos denominada testdb para que sirva de ejemplo. A continuación, crea un usuario en el servicio PostgreSQL y concede privilegios de conexión a la nueva base de datos de dicho usuario.

   ```sql
   CREATE DATABASE <newdb>;
   
   CREATE ROLE <db_user> WITH LOGIN NOSUPERUSER INHERIT CREATEDB NOCREATEROLE NOREPLICATION PASSWORD '<StrongPassword!>';
   
   GRANT CONNECT ON DATABASE <newdb> TO <db_user>;
   ```

4. Con el uso de una cuenta de administrador, puede que deba conceder privilegios adicionales para proteger los objetos de la base de datos. Consulte la [documentación de PostgreSQL](https://www.postgresql.org/docs/current/static/ddl-priv.html) para obtener información adicional sobre los privilegios y roles de base de datos. Por ejemplo:

   ```sql
   GRANT ALL PRIVILEGES ON DATABASE <newdb> TO <db_user>;
   ```

   Si un usuario crea una tabla "rol", la tabla pertenece a ese usuario. Si otro usuario necesita acceso a la tabla, debe conceder privilegios al otro usuario en el nivel de tabla.

   Por ejemplo: 

    ```sql
    GRANT SELECT ON ALL TABLES IN SCHEMA <schema_name> TO <db_user>;
    ```

5. Inicie sesión en el servidor mediante el nuevo nombre de usuario y contraseña, sin olvidarse de especificar la base de datos designada. En este ejemplo se muestra la línea de comandos de psql. Con este comando, se le pedirá la contraseña del nombre de usuario. Reemplace su propio nombre de servidor, nombre de base de datos y nombre de usuario.

   ```shell
   psql --host=mydemoserver.postgres.database.azure.com --port=5432 --username=db_user@mydemoserver --dbname=newdb
   ```

## <a name="next-steps"></a>Pasos siguientes

Abra el firewall para las direcciones IP de las máquinas de los nuevos usuarios para permitirles conectarse: [Creación y administración de reglas de firewall de Azure Database for PostgreSQL mediante Azure Portal](howto-manage-firewall-using-portal.md) o la [CLI de Azure](howto-manage-firewall-using-cli.md).

Para más información respecto a la administración de cuentas de usuario, consulte la documentación de productos de PostgreSQL relativa a los [privilegios y roles de base de datos](https://www.postgresql.org/docs/current/static/user-manag.html), la [sintaxis GRANT](https://www.postgresql.org/docs/current/static/sql-grant.html) y los [privilegios](https://www.postgresql.org/docs/current/static/ddl-priv.html).
