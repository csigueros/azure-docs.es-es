---
title: Creación de usuarios en Azure Database for MariaDB
description: En este artículo se describe cómo puede crear nuevas cuentas de usuario para interactuar con un servidor de Azure Database for MariaDB.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: how-to
ms.date: 01/18/2021
ms.openlocfilehash: c66c7886589b1568d1c386786d15d2e52bb1d020
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128610116"
---
# <a name="create-users-in-azure-database-for-mariadb"></a>Crear usuarios en Azure Database for MariaDB

En este artículo se describe cómo puede crear usuarios en Azure Database for MariaDB.

La primera vez que creó su instancia de Azure Database for MariaDB, proporcionó un nombre de usuario y una contraseña de inicio de sesión de administrador del servidor. Para más información, puede seguir la [Guía de inicio rápido](quickstart-create-mariadb-server-database-using-azure-portal.md). Puede encontrar su nombre de usuario de inicio de sesión de administrador del servidor en Azure Portal.

> [!NOTE]
> Este artículo contiene referencias al término *esclavo*, un término que Microsoft ya no usa. Cuando se elimine el término del software, se eliminará también de este artículo.

El usuario administrador del servidor obtiene ciertos privilegios para el servidor, según se indica a continuación: SELECT, INSERT, UPDATE, DELETE, CREATE, DROP, RELOAD, PROCESS, REFERENCES, INDEX, ALTER, SHOW DATABASES, CREATE TEMPORARY TABLES, LOCK TABLES, EXECUTE, REPLICATION SLAVE, REPLICATION CLIENT, CREATE VIEW, SHOW VIEW, CREATE ROUTINE, ALTER ROUTINE, CREATE USER, EVENT y TRIGGER

Una vez creado el servidor de Azure Database for MariaDB, puede usar la primera cuenta de usuario administrador del servidor para crear más usuarios y concederles acceso de administrador. Además, la cuenta de administrador del servidor puede usarse para crear usuarios con menos privilegios que tengan acceso a esquemas de base de datos individuales.

> [!NOTE]
> El privilegio SUPER y el rol DBA no se admiten. Revise los [privilegios](concepts-limits.md#privileges--data-manipulation-support) en el artículo que trata sobre las limitaciones para saber lo que no se admite en el servicio.<br><br>
> Los complementos de contraseñas como "validate_password" y "caching_sha2_password" no son compatibles con el servicio.

## <a name="create-more-admin-users"></a>Creación de más usuarios administradores

1. Obtenga la información de conexión y el nombre de usuario administrador.
   Para conectarse al servidor de bases de datos, es preciso utilizar el nombre completo del servidor y las credenciales de inicio de sesión de administrador. Puede encontrar fácilmente el nombre del servidor y la información de inicio de sesión en la página **Información general** del servidor o en la página **Propiedades** de Azure Portal.

2. Use la cuenta y la contraseña de administrador para conectarse a su servidor de base de datos. Use la herramienta preferida de cliente, como MySQL Workbench, mysql.exe, HeidiSQL u otras.
   Si no está seguro de cómo conectarse, consulte [Uso de MySQL Workbench para conectarse y consultar datos](./connect-workbench.md)

3. Modifique y ejecute el siguiente código SQL. Reemplace el valor de marcador de posición `new_master_user` por el nuevo nombre de usuario. Esta sintaxis concede los privilegios enumerados en todos los esquemas de base de datos ( *.* ) al nombre de usuario (new_master_user en este ejemplo). 

   ```sql
   CREATE USER 'new_master_user'@'%' IDENTIFIED BY 'StrongPassword!';
   
   GRANT SELECT, INSERT, UPDATE, DELETE, CREATE, DROP, RELOAD, PROCESS, REFERENCES, INDEX, ALTER, SHOW DATABASES, CREATE TEMPORARY TABLES, LOCK TABLES, EXECUTE, REPLICATION SLAVE, REPLICATION CLIENT, CREATE VIEW, SHOW VIEW, CREATE ROUTINE, ALTER ROUTINE, CREATE USER, EVENT, TRIGGER ON *.* TO 'new_master_user'@'%' WITH GRANT OPTION; 
   
   FLUSH PRIVILEGES;
   ```

4. Compruebe las concesiones.

   ```sql
   USE sys;
   
   SHOW GRANTS FOR 'new_master_user'@'%';
   ```

## <a name="create-database-users"></a>Crear usuarios de base de datos

1. Obtenga la información de conexión y el nombre de usuario administrador.
   Para conectarse al servidor de bases de datos, es preciso utilizar el nombre completo del servidor y las credenciales de inicio de sesión de administrador. Puede encontrar fácilmente el nombre del servidor y la información de inicio de sesión en la página **Información general** del servidor o en la página **Propiedades** de Azure Portal. 

2. Use la cuenta y la contraseña de administrador para conectarse a su servidor de base de datos. Use la herramienta preferida de cliente, como MySQL Workbench, mysql.exe, HeidiSQL u otras.
   Si no está seguro de cómo conectarse, consulte [Uso de MySQL Workbench para conectarse y consultar datos](./connect-workbench.md).

3. Modifique y ejecute el siguiente código SQL. Reemplace el valor de marcador de posición `db_user` por su nuevo nombre de usuario deseado y el valor de marcador de posición `testdb` por su propio nombre de base de datos.

   Esta sintaxis de código sql crea una nueva base de datos denominada testdb para que sirva de ejemplo. A continuación, crea un nuevo usuario en el servicio Azure Database for MariaDB y concede todos los privilegios al nuevo esquema de base de datos (testdb.\*) de ese usuario. 

   ```sql
   CREATE DATABASE testdb;
   
   CREATE USER 'db_user'@'%' IDENTIFIED BY 'StrongPassword!';
   
   GRANT ALL PRIVILEGES ON testdb . * TO 'db_user'@'%';
   
   FLUSH PRIVILEGES;
   ```

4. Compruebe las concesiones dentro de la base de datos.

   ```sql
   USE testdb;
   
   SHOW GRANTS FOR 'db_user'@'%';
   ```

5. Inicie sesión en el servidor mediante el nuevo nombre de usuario y contraseña, sin olvidarse de especificar la base de datos designada. En este ejemplo se muestra la línea de comandos de mysql. Con este comando, se le pedirá la contraseña del nombre de usuario. Reemplace su propio nombre de servidor, nombre de base de datos y nombre de usuario.

   ```bash
   mysql --host mydemoserver.mariadb.database.azure.com --database testdb --user db_user@mydemoserver -p
   ```

   Para obtener más información respecto a la administración de cuentas de usuario, consulte la documentación del producto MariaDB relativa a la [administración de cuentas de usuario](https://mariadb.com/kb/en/library/user-account-management/), la [sintaxis GRANT](https://mariadb.com/kb/en/library/grant/) y los [privilegios](https://mariadb.com/kb/en/library/grant/#privilege-levels).

## <a name="azure_superuser"></a>azure_superuser

Todos los servidores de Azure Database for MySQL se crean con un usuario llamado "azure_superuser". Se trata de una cuenta del sistema que crea Microsoft para administrar el servidor con el fin de realizar tareas de supervisión, copias de seguridad y otros tipos de mantenimiento estándar. Los ingenieros de guardia también pueden usar esta cuenta para obtener acceso al servidor durante un incidente relacionado con la autenticación de certificados, pero deben solicitar acceso mediante procesos Just-in-Time (JIT).

## <a name="next-steps"></a>Pasos siguientes

Abra el firewall para las direcciones IP de las máquinas de los nuevos usuarios para permitirles conectarse: [Crear y administrar reglas de firewall de Azure Database for MariaDB mediante Azure Portal](howto-manage-firewall-portal.md)  

<!--or [Azure CLI](howto-manage-firewall-using-cli.md).-->
