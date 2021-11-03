---
title: Integración de Azure Database for PostgreSQL en Service Connector
description: Integre Azure Database for PostgreSQL en una aplicación con Service Connector.
author: shizn
ms.author: xshi
ms.service: serviceconnector
ms.topic: how-to
ms.date: 10/29/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 6c8cfb866da9a57c54f443558212c5d88269fe0a
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131017444"
---
# <a name="integrate-azure-database-for-postgresql-with-service-connector"></a>Integración de Azure Database for PostgreSQL en Service Connector

En esta página se muestran los tipos de autenticación admitidos y los tipos de cliente de Azure Database for PostgreSQL mediante el uso de Service Connector. Es posible que todavía pueda conectarse a Azure Database for PostgreSQL en otros lenguajes de programación sin usar Service Connector. Esta página también muestra el nombre y el valor predeterminados de la variable de entorno (o configuración de Spring Boot) que se obtiene al crear la conexión de servicio. Puede obtener más información sobre la [convención de nomenclatura de variables de entorno de Service Connector](concept-service-connector-internals.md).

## <a name="supported-compute-service"></a>Servicio de proceso admitido

- Azure App Service
- Azure Spring Cloud

## <a name="supported-authentication-types-and-client-types"></a>Tipos de autenticación y tipos de cliente admitidos

| Tipo de cliente | Identidad administrada asignada por el sistema | Identidad administrada asignada por el usuario | Secret/ConnectionString | Entidad de servicio |
| --- | --- | --- | --- | --- |
| .Net (ADO.NET) | | | ![icono sí](./media/green-check.png) | |
| Java (JDBC) | | | ![icono sí](./media/green-check.png) | |
| Java - Spring Boot (JDBC) | | | ![icono sí](./media/green-check.png) | |
| Node.js (pg) | | | ![icono sí](./media/green-check.png) | |
| Python (psycopg2) | | | ![icono sí](./media/green-check.png) | |
| Python-Django | | | ![icono sí](./media/green-check.png) | |
| Go (pg) | | | ![icono sí](./media/green-check.png) | |
| PHP (nativo) | | | ![icono sí](./media/green-check.png) | |
| Ruby (ruby-pg) | | | ![icono sí](./media/green-check.png) | |

## <a name="default-environment-variable-names-or-application-properties"></a>Nombres de variables de entorno o propiedades de aplicación predeterminados

### <a name="net-adonet"></a>.NET (ADO.NET) 

**Secret/ConnectionString**

| Nombre de la variable de entorno predeterminado | Descripción | Valor de ejemplo |
| --- | --- | --- |
| AZURE_POSTGRESQL_CONNECTIONSTRING | Cadena de conexión de ADO.NET para PostgreSQL | `Server={your-postgres-server-name}.postgres.database.azure.com;Database={database-name};Port=5432;Ssl Mode=Require;User Id={username}@{servername};Password=****;` |

### <a name="java-jdbc"></a>Java (JDBC)

**Secret/ConnectionString**

| Nombre de la variable de entorno predeterminado | Descripción | Valor de ejemplo |
| --- | --- | --- |
| AZURE_POSTGRESQL_CONNECTIONSTRING | Cadena de conexión de JDBC para PostgreSQL | `jdbc:postgresql://{your-postgres-server-name}.postgres.database.azure.com:5432/{database-name}?sslmode=require&user={username}%40{servername}l&password=****` |

### <a name="java---spring-boot-jdbc"></a>Java - Spring Boot (JDBC)

**Secret/ConnectionString**

| Propiedades de la aplicación | Descripción | Valor de ejemplo |
| --- | --- | --- |
| spring.datatsource.url | Dirección URL de la base de datos | `jdbc:postgresql://{your-postgres-server-name}.postgres.database.azure.com:5432/{database-name}?sslmode=require` |
| spring.datatsource.username | Nombre de usuario de la base de datos | `{username}@{servername}` |
| spring.datatsource.password | Contraseña de la base de datos | `****` |

### <a name="nodejs-pg"></a>Node.js (pg) 

**Secret/ConnectionString**

| Nombre de la variable de entorno predeterminado | Descripción | Valor de ejemplo |
|---------|---------|---------|
| AZURE_POSTGRESQL_HOST | Dirección URL del host de base de datos | `{your-postgres-server-name}.postgres.database.azure.com` |
| AZURE_POSTGRESQL_USER | Nombre de usuario de la base de datos | `{username}@{servername}` |
| AZURE_POSTGRESQL_PASSWORD | Contraseña de la base de datos | `****` |
| AZURE_POSTGRESQL_DATABASE | Nombre de la base de datos | `{database-name}` |
| AZURE_POSTGRESQL_PORT | Número de puerto  | `5432` |
| AZURE_POSTGRESQL_SSL | Opción SSL  | `true` |

### <a name="python-psycopg2"></a>Python (psycopg2)

**Secret/ConnectionString**

| Nombre de la variable de entorno predeterminado | Descripción | Valor de ejemplo |
| --- | --- | --- |
| AZURE_POSTGRESQL_CONNECTIONSTRING | Cadena de conexión de psycopg2 | `dbname={database-name} host={your-postgres-server-name}.postgres.database.azure.com port=5432 sslmode=require user={username}@{servername} password=****` |

### <a name="python-django"></a>Python-Django

**Secret/ConnectionString**

| Nombre de la variable de entorno predeterminado | Descripción | Valor de ejemplo |
| --- | --- | --- |
| AZURE_POSTGRESQL_HOST | Dirección URL del host de base de datos | `{your-postgres-server-name}.postgres.database.azure.com` |
| AZURE_POSTGRESQL_USER | Nombre de usuario de la base de datos | `{username}@{servername}` |
| AZURE_POSTGRESQL_PASSWORD | Contraseña de la base de datos | `****` |
| AZURE_POSTGRESQL_NAME | Nombre de la base de datos | `{database-name}` |


### <a name="go-pg"></a>Go (pg)

**Secret/ConnectionString**

| Nombre de la variable de entorno predeterminado | Descripción | Valor de ejemplo |
| --- | --- | --- |
| AZURE_POSTGRESQL_CONNECTIONSTRING | Cadena de conexión de Go pg | `host={your-postgres-server-name}.postgres.database.azure.com dbname={database-name} sslmode=require user={username}@{servername} password=****` |


### <a name="php-native"></a>PHP (nativo)

**Secret/ConnectionString**

| Nombre de la variable de entorno predeterminado | Descripción | Valor de ejemplo |
| --- | --- | --- |
| AZURE_POSTGRESQL_CONNECTIONSTRING | Cadena de conexión de PHP nativo para postgres | `host={your-postgres-server-name}.postgres.database.azure.com port=5432 dbname={database-name} sslmode=requrie user={username}@{servername} password=****` |

### <a name="ruby-ruby-pg"></a>Ruby (ruby-pg)

**Secret/ConnectionString**

| Nombre de la variable de entorno predeterminado | Descripción | Valor de ejemplo |
| --- | --- | --- |
| AZURE_POSTGRESQL_CONNECTIONSTRING | Cadena de conexión de Ruby pg | `host={your-postgres-server-name}.postgres.database.azure.com port=5432 dbname={database-name} sslmode=require user={username}@{servername} password=****` |

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre Service Connector, siga los tutoriales que se enumeran a continuación.

> [!div class="nextstepaction"]
> [Más información sobre los conceptos de Service Connector](./concept-service-connector-internals.md)
