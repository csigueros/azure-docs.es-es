---
title: Integración de Azure Database for MySQL con Service Connector
description: Integración de Azure Database for MySQL en la aplicación con Service Connector
author: shizn
ms.author: xshi
ms.service: serviceconnector
ms.topic: how-to
ms.date: 10/29/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: ce2839d614298d33f5bb1697c328b258463b6269
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131090958"
---
# <a name="integrate-azure-database-for-mysql-with-service-connector"></a>Integración de Azure Database for MySQL con Service Connector

En esta página se muestran los tipos de autenticación admitidos y los tipos de cliente de Azure Database for MySQL mediante el uso de Service Connector. Es posible que todavía pueda conectarse a Azure Database for MySQL en otros lenguajes de programación sin usar Service Connector. Esta página también muestra el nombre y el valor predeterminados de la variable de entorno (o configuración de Spring Boot) que se obtiene al crear la conexión de servicio. Puede obtener más información sobre la [convención de nomenclatura de variables de entorno de Service Connector](concept-service-connector-internals.md).

## <a name="supported-compute-service"></a>Servicio de proceso admitido

- Azure App Service
- Azure Spring Cloud

## <a name="supported-authentication-types-and-client-types"></a>Tipos de autenticación y tipos de cliente admitidos

| Tipo de cliente | Identidad administrada asignada por el sistema | Identidad administrada asignada por el usuario | Secret/ConnectionString | Entidad de servicio |
| --- | --- | --- | --- | --- |
| .Net (MySqlConnector) | | | ![icono sí](./media/green-check.png) | |
| Java (JDBC) | | | ![icono sí](./media/green-check.png) | |
| Java - Spring Boot (JDBC) | | | ![icono sí](./media/green-check.png) | |
| Node.js (mysql) | | | ![icono sí](./media/green-check.png) | |
| Python (mysql-connector-python) | | | ![icono sí](./media/green-check.png) | |
| Python-Django | | | ![icono sí](./media/green-check.png) | |
| Go (go-sql-driver for mysql) | | | ![icono sí](./media/green-check.png) | |
| PHP (mysqli) | | | ![icono sí](./media/green-check.png) | |
| Ruby (mysql2) | | | ![icono sí](./media/green-check.png) | |

## <a name="default-environment-variable-names-or-application-properties"></a>Nombres de variables de entorno o propiedades de aplicación predeterminados

### <a name="net-mysqlconnector"></a>.NET (MySqlConnector) 

**Secret/ConnectionString**

| Nombre de la variable de entorno predeterminado | Descripción | Valor de ejemplo |
| --- | --- | --- |
| AZURE_MYSQL_CONNECTIONSTRING | Cadena de conexión de MySQL de ADO.NET | `Server={MySQLName}.mysql.database.azure.com;Database={MySQLDbName};Port=3306;SSL Mode=Required;User Id={MySQLUsername};Password={TestDbPassword}` |

### <a name="java-jdbc"></a>Java (JDBC)

**Secret/ConnectionString**

| Nombre de la variable de entorno predeterminado | Descripción | Valor de ejemplo |
| --- | --- | --- |
| AZURE_MYSQL_CONNECTIONSTRING | Cadena de conexión de MySQL de JDBC | `jdbc:mysql://{MySQLName}.mysql.database.azure.com:3306/{MySQLDbName}?sslmode=required&user={MySQLUsername}&password={Uri.EscapeDataString(TestDbPassword)}` |

### <a name="java---spring-boot-jdbc"></a>Java - Spring Boot (JDBC)

**Secret/ConnectionString**

| Propiedades de la aplicación | Descripción | Valor de ejemplo |
| --- | --- | --- |
| spring.datatsource.url | URL de la base de datos JDBC de Spring Boot | `jdbc:mysql://{MySQLName}.mysql.database.azure.com:3306/{MySQLDbName}?sslmode=required` |
| spring.datatsource.username | Nombre de usuario de la base de datos | `{MySQLUsername}@{MySQLName}` |
| spring.datatsource.password | Contraseña de la base de datos | `****` |

### <a name="nodejs-mysql"></a>Node.js (mysql) 

**Secret/ConnectionString**

| Nombre de la variable de entorno predeterminado | Descripción | Valor de ejemplo |
|---------|---------|---------|
| AZURE_MYSQL_HOST | Dirección URL del host de base de datos  | `{MySQLName}.mysql.database.azure.com` |
| AZURE_MYSQL_USER | Nombre de usuario de la base de datos | `MySQLDbName` |
| AZURE_MYSQL_PASSWORD | Contraseña de la base de datos | `****` |
| AZURE_MYSQL_DATABASE | Nombre de la base de datos  | `{MySQLUsername}@{MySQLName}` |
| AZURE_MYSQL_PORT | Número de puerto | `3306` |
| AZURE_MYSQL_SSL | Opción SSL | `true` |

### <a name="python-mysql-connector-python"></a>Python (mysql-connector-python)

**Secret/ConnectionString**

| Nombre de la variable de entorno predeterminado | Descripción | Valor de ejemplo |
| --- | --- | --- |
| AZURE_MYSQL_HOST | Dirección URL del host de base de datos  | `{MySQLName}.mysql.database.azure.com` |
| AZURE_MYSQL_NAME | Nombre de la base de datos | `{MySQLDbName}` |
| AZURE_MYSQL_PASSWORD | Contraseña de la base de datos  | `****` |
| AZURE_MYSQL_USER | Nombre de usuario de la base de datos  | `{MySQLUsername}@{MySQLName}` |

### <a name="python-django"></a>Python-Django

**Secret/ConnectionString**

| Nombre de la variable de entorno predeterminado | Descripción | Valor de ejemplo |
| --- | --- | --- |
| AZURE_MYSQL_HOST | Dirección URL del host de base de datos  | `{MySQLName}.mysql.database.azure.com` |
| AZURE_MYSQL_USER | Nombre de usuario de la base de datos | `{MySQLUsername}@{MySQLName}` |
| AZURE_MYSQL_PASSWORD | Contraseña de la base de datos | `****` |
| AZURE_MYSQL_NAME | Nombre de la base de datos | `MySQLDbName` |


### <a name="go-go-sql-driver-for-mysql"></a>Go (go-sql-driver for mysql)

**Secret/ConnectionString**

| Nombre de la variable de entorno predeterminado | Descripción | Valor de ejemplo |
| --- | --- | --- |
| AZURE_MYSQL_CONNECTIONSTRING | Cadena de conexión de go-sql-driver | `{MySQLUsername}@{MySQLName}:{Password}@tcp({ServerHost}:{Port})/{Database}?tls=true` |


### <a name="php-mysqli"></a>PHP (mysqli)

**Secret/ConnectionString**

| Nombre de la variable de entorno predeterminado | Descripción | Valor de ejemplo |
|---------|---------|---------|
| AZURE_MYSQL_HOST | Dirección URL del host de base de datos | `{MySQLName}.mysql.database.azure.com` |
| AZURE_MYSQL_USERNAME | Nombre de usuario de la base de datos | `{MySQLUsername}@{MySQLName}` |
| AZURE_MYSQL_PASSWORD | Contraseña de la base de datos | `****` |
| AZURE_MYSQL_DBNAME | Nombre de la base de datos | `{MySQLDbName}` |
| AZURE_MYSQL_PORT | Número de puerto  | `3306` |
| AZURE_MYSQL_FLAG | SSL u otras marcas | `MYSQLI_CLIENT_SSL` |

### <a name="ruby-mysql2"></a>Ruby (mysql2)

**Secret/ConnectionString**

| Nombre de la variable de entorno predeterminado | Descripción | Valor de ejemplo |
|---------|---------|---------|
| AZURE_MYSQL_HOST | Dirección URL del host de base de datos  | `{MySQLName}.mysql.database.azure.com` |
| AZURE_MYSQL_USERNAME | Nombre de usuario de la base de datos | `{MySQLUsername}@{MySQLName}` |
| AZURE_MYSQL_PASSWORD | Contraseña de la base de datos | `****` |
| AZURE_MYSQL_DATABASE | Nombre de la base de datos | `{MySQLDbName}` |
| AZURE_MYSQL_SSLMODE | Opción SSL | `required` |

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre Service Connector, siga los tutoriales que se enumeran a continuación.

> [!div class="nextstepaction"]
> [Más información sobre los conceptos de Service Connector](./concept-service-connector-internals.md)
