---
title: 'Compatibilidad de controladores y herramientas: Azure Database for MariaDB'
description: En este artículo se describen las herramientas de administración y los controladores de MariaDB compatibles con Azure Database for MariaDB.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 08db679737c4d7823ed369e36c40b27f14b9632c
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/15/2021
ms.locfileid: "130036481"
---
# <a name="mariadb-drivers-and-management-tools-compatible-with-azure-database-for-mariadb"></a>Herramientas de administración y controladores de MariaDB compatibles con Azure Database for MariaDB

En este artículo se describen las herramientas de administración y los controladores compatibles con Azure Database for MariaDB.

## <a name="mariadb-drivers"></a>Controladores de MariaDB

Azure Database for MariaDB usa la edición de la comunidad del servidor de MariaDB. Por lo tanto, es compatible con una amplia variedad de controladores y lenguajes de programación. La API y el protocolo de MariaDB son compatibles con los usados en MySQL. Esto significa que los conectores que funcionan con MySQL deben funcionar también con MariaDB.

El objetivo es la compatibilidad con las tres versiones más recientes de los controladores de MariaDB, y continúan las labores con los autores de la comunidad de código abierto para mejorar constantemente la funcionalidad y la facilidad de uso de los controladores de MariaDB. En la tabla siguiente se proporciona una lista de controladores que ha demostrado ser compatibles con Azure Database for MariaDB 10.2:

**Controlador** | **Vínculos** | **Versiones compatibles** | **Versiones incompatibles** | **Notas**
---|---|---|---|---
PHP | https://secure.php.net/downloads.php | 5.5, 5.6, 7.x | 5.3 | Para la conexión PHP 7.0 con SSL MySQLi, agregue MYSQLI_CLIENT_SSL_DONT_VERIFY_SERVER_CERT en la cadena de conexión. <br> ```mysqli_real_connect($conn, $host, $username, $password, $db_name, 3306, NULL, MYSQLI_CLIENT_SSL_DONT_VERIFY_SERVER_CERT);```<br> PDO: establezca la opción ```PDO::MYSQL_ATTR_SSL_VERIFY_SERVER_CERT``` en false.
.NET | [MySqlConnector en GitHub](https://github.com/mysql-net/MySqlConnector) <br> [Paquete de instalación de NuGet](https://www.nuget.org/packages/MySqlConnector/) | 0.27 y posterior | 0.26.5 y anterior |
Conector MySQL/NET | [Conector MySQL/NET](https://github.com/mysql/mysql-connector-net) | 8.0, 7.0, 6.10 |  | Un error de codificación puede provocar errores de conexión en algunos sistemas Windows que no sean UTF8.
Node.js |  [MySQLjs en GitHub](https://github.com/mysqljs/mysql/) <br> Paquete de instalación de NPM:<br> Ejecute `npm install mysql` en NPM | 2.15 | 2.14.1 y anterior
GO | https://github.com/go-sql-driver/mysql/releases | 1.3, 1.4 | 1.2 y anterior | Use `allowNativePasswords=true` en la cadena de conexión para la versión 1.3. La versión 1.4 contiene una corrección y `allowNativePasswords=true` ya no es necesario.
Python | https://pypi.python.org/pypi/mysql-connector-python | 1.2.3, 2.0, 2.1, 2.2 | 1.2.2 y anterior |
Java | https://downloads.mariadb.org/connector-java/ | 2.1, 2.0, 1.6 | 1.5.5 y anterior |

## <a name="management-tools"></a>Herramientas de administración

La ventaja de compatibilidad se amplía también a las herramientas de administración de base de datos. Sus herramientas actuales deben continuar funcionando con Azure Database for MariaDB, siempre y cuando la manipulación de la base de datos tenga lugar dentro de los límites establecidos por los permisos de usuario. En la tabla siguiente se indican las tres herramientas comunes de administración de base de datos que se han demostrado ser compatibles con Azure Database for MariaDB 10.2:

| Acción | **MySQL Workbench 6.x y superiores** | **Navicat 12** | **PHPMyAdmin 4.x y superiores**
---|---|---|---
Crear, actualizar, leer, escribir, eliminar | X | X | X
Conexión SSL | X | X | X
Completado automático de consultas SQL | X | X |
Importar y exportar datos | X | X | X
Exportación en varios formatos | X | X | X
Copias de seguridad y restauración |  | X |
Muestra de parámetros de servidor | X | X | X
Muestra de conexiones de cliente | X | X | X

## <a name="next-steps"></a>Pasos siguientes

- [Solución de problemas de conexión a Azure Database for MariaDB](howto-troubleshoot-common-connection-issues.md)