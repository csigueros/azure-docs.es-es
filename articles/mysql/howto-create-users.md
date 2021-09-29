---
title: 'Creación de bases de datos y usuarios: Azure Database for MySQL'
description: En este artículo se describe cómo crear nuevas cuentas de usuario para interactuar con un servidor de Azure Database for MySQL.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 01/13/2021
ms.openlocfilehash: 479fe6c4f30c8272d73c9d1d085f541beeefe229
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128579666"
---
# <a name="create-databases-and-users-in-azure-database-for-mysql"></a>Creación de bases de datos y usuarios en Azure Database for MySQL

[!INCLUDE[applies-to-mysql-single-flexible-server](includes/applies-to-mysql-single-flexible-server.md)]

En este artículo se describe cómo puede crear usuarios en Azure Database for MySQL.

> [!NOTE]
> Este artículo contiene referencias al término *esclavo*, un término que Microsoft ya no usa. Cuando se elimine el término del software, se eliminará también de este artículo.

La primera vez que creó su servidor de Azure Database for MySQL, proporcionó un nombre de usuario y una contraseña de administrador del servidor. Para obtener más información, consulte el [Inicio rápido](quickstart-create-mysql-server-database-using-azure-portal.md). Puede determinar el nombre del usuario administrador del servidor en Azure Portal.

El usuario administrador del servidor tiene estos privilegios:

   SELECT, INSERT, UPDATE, DELETE, CREATE, DROP, RELOAD, PROCESS, REFERENCES, INDEX, ALTER, SHOW DATABASES, CREATE TEMPORARY TABLES, LOCK TABLES, EXECUTE, REPLICATION SLAVE, REPLICATION CLIENT, CREATE VIEW, SHOW VIEW, CREATE ROUTINE, ALTER ROUTINE, CREATE USER, EVENT y TRIGGER

Una vez creado el servidor de Azure Database for MySQL, puede usar la primera cuenta de usuario administrador del servidor para crear más usuarios y concederles acceso de administrador. Además, la cuenta de administrador del servidor puede usarse para crear usuarios con menos privilegios que tengan acceso a esquemas de base de datos individuales.

> [!NOTE]
> El privilegio SUPER y el rol DBA no se admiten. Revise los [privilegios](concepts-limits.md#privileges--data-manipulation-support) en el artículo que trata sobre las limitaciones para saber lo que no se admite en el servicio.
>
> El servicio no admite complementos de contraseña como `validate_password` y `caching_sha2_password`.

## <a name="to-create-a-database-with-a-non-admin-user-in-azure-database-for-mysql"></a>Creación de una base de datos con un usuario que no sea administrador en Azure Database for MySQL

1. Obtenga la información de conexión y el nombre de usuario administrador.
   Para conectarse al servidor de bases de datos, es preciso utilizar el nombre completo del servidor y las credenciales de inicio de sesión de administrador. Tanto el nombre del servidor como la información de inicio de sesión del mismo se pueden encontrar con facilidad en la página **Introducción** del servidor o en la página **Propiedades** de Azure Portal.

2. Use la cuenta y la contraseña de administrador para conectarse a su servidor de base de datos. Use su herramienta de cliente preferida, como MySQL Workbench, mysql.exe, HeidiSQL u otras.

   Si no sabe seguro cómo conectarse, consulte [conectarse y consultar los datos de un solo servidor](./connect-workbench.md) o [conectarse y consultar datos de un servidor flexible](./flexible-server/connect-workbench.md).

3. Modifique y ejecute el siguiente código SQL. Reemplace el valor `db_user` del marcador de posición por el nuevo nombre de usuario de vaya a usar. Reemplace el valor `testdb` del marcador de posición por el nuevo nombre de base de datos que vaya a usar.

   Este código SQL crea una nueva base de datos denominada testdb. A continuación, se crea un nuevo usuario en el servicio MySQL y concede todos los privilegios al nuevo esquema de base de datos (testdb.\*) de ese usuario.

   ```sql
   CREATE DATABASE testdb;

   CREATE USER 'db_user'@'%' IDENTIFIED BY 'StrongPassword!';

   GRANT ALL PRIVILEGES ON testdb . * TO 'db_user'@'%';

   FLUSH PRIVILEGES;
   ```

4. Compruebe las concesiones de la base de datos:

   ```sql
   USE testdb;

   SHOW GRANTS FOR 'db_user'@'%';
   ```

5. Inicie sesión en el servidor mediante el nuevo nombre de usuario y contraseña, sin olvidarse de especificar la base de datos designada. En este ejemplo se muestra la línea de comandos de mysql. Cuando use este comando, se le pedirá la contraseña del usuario. Reemplace su propio nombre de servidor, el nombre de base de datos y el nombre de usuario.

### <a name="single-server"></a>[Servidor único](#tab/single-server)

   ```azurecli-interactive
   mysql --host mydemoserver.mysql.database.azure.com --database testdb --user db_user@mydemoserver -p
   ```

### <a name="flexible-server"></a>[Servidor flexible](#tab/flexible-server)

   ```azurecli-interactive
   mysql --host mydemoserver.mysql.database.azure.com --database testdb --user db_user -p
   ```

## <a name="to-create-more-admin-users-in-azure-database-for-mysql"></a>Para crear más usuarios administradores en Azure Database for MySQL

1. Obtenga la información de conexión y el nombre de usuario administrador.
   Para conectarse al servidor de bases de datos, es preciso utilizar el nombre completo del servidor y las credenciales de inicio de sesión de administrador. Tanto el nombre del servidor como la información de inicio de sesión del mismo se pueden encontrar con facilidad en la página **Introducción** del servidor o en la página **Propiedades** de Azure Portal.

2. Use la cuenta y la contraseña de administrador para conectarse a su servidor de base de datos. Use su herramienta de cliente preferida, como MySQL Workbench, mysql.exe, HeidiSQL u otras.

   Si no está seguro de cómo conectarse, consulte [Uso de MySQL Workbench para conectarse y consultar datos](./connect-workbench.md).

3. Modifique y ejecute el siguiente código SQL. Reemplace el valor `new_master_user` del marcador de posición por el nombre de usuario nuevo. Esta sintaxis concede los privilegios enumerados en todos los esquemas de base de datos ( *.* ) al usuario (`new_master_user` en este ejemplo).

   ```sql
   CREATE USER 'new_master_user'@'%' IDENTIFIED BY 'StrongPassword!';

   GRANT SELECT, INSERT, UPDATE, DELETE, CREATE, DROP, RELOAD, PROCESS, REFERENCES, INDEX, ALTER, SHOW DATABASES, CREATE TEMPORARY TABLES, LOCK TABLES, EXECUTE, REPLICATION SLAVE, REPLICATION CLIENT, CREATE VIEW, SHOW VIEW, CREATE ROUTINE, ALTER ROUTINE, CREATE USER, EVENT, TRIGGER ON *.* TO 'new_master_user'@'%' WITH GRANT OPTION;

   FLUSH PRIVILEGES;
   ```

4. Compruebe de las concesiones:

   ```sql
   USE sys;

   SHOW GRANTS FOR 'new_master_user'@'%';
   ```

## <a name="azure_superuser"></a>azure_superuser

Todos los servidores de Azure Database for MySQL se crean con un usuario llamado "azure_superuser". Se trata de una cuenta del sistema que crea Microsoft para administrar el servidor con el fin de realizar tareas de supervisión, copias de seguridad y otros tipos de mantenimiento estándar. Los ingenieros de guardia también pueden usar esta cuenta para obtener acceso al servidor durante un incidente relacionado con la autenticación de certificados, pero deben solicitar acceso mediante procesos Just-in-Time (JIT).

## <a name="next-steps"></a>Pasos siguientes

Abra el firewall para las direcciones IP de las máquinas de los nuevos usuarios para permitirles conectarse:

* [Creación y administración de reglas de firewall en un servidor único](howto-manage-firewall-using-portal.md)
* [Creación y administración de reglas de firewall en un servidor flexible](flexible-server/how-to-connect-tls-ssl.md)

Para obtener más información acerca de la administración de cuentas de usuario, consulte la documentación del producto de MySQL relativa a la [Administración de cuentas de usuario](https://dev.mysql.com/doc/refman/5.7/en/access-control.html), la [Sintaxis GRANT](https://dev.mysql.com/doc/refman/5.7/en/grant.html) y los [Privilegios](https://dev.mysql.com/doc/refman/5.7/en/privileges-provided.html).
