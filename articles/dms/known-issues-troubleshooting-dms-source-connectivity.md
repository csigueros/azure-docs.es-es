---
title: Problemas de conexión de bases de datos de origen
titleSuffix: Azure Database Migration Service
description: Obtenga información sobre cómo solucionar problemas conocidos y errores asociados con la con de Azure Database Migration Service a las bases de datos de origen.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: troubleshooting
ms.date: 02/20/2020
ms.openlocfilehash: e3dcc8f6bba6830eec9f20732da9e42f03d5b28e
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121724420"
---
# <a name="troubleshoot-dms-errors-when-connecting-to-source-databases"></a>Solución de errores de DMS al conectarse a las bases de datos de origen

En el siguiente artículo se proporcionan detalles sobre cómo abordar los posibles problemas que puede encontrarse al conectar Azure Database Migration Service (DMS) a su base de datos de origen. Cada una de las siguientes secciones se relaciona con un tipo específico de base de datos de origen; asimismo, se enumera el error que puede encontrar y se ofrecen detalles y vínculos a información sobre cómo solucionar los problemas de conectividad.

## <a name="sql-server"></a>SQL Server

Los problemas potenciales asociados con la conexión a una base de datos de SQL Server de origen y cómo abordarlos se proporcionan en la siguiente tabla.

| Error         | Detalles de las causas y soluciones de problemas |
| ------------- | ------------- |
| Falló la conexión SQL. Se ha producido un error relacionado con la red o específico de la instancia al establecer una conexión en SQL Server. No se encontró el servidor o no era accesible. Compruebe que el nombre de la instancia sea correcto y que SQL Server esté configurado para permitir conexiones remotas.<br> | Este error se produce si el servicio no puede localizar el servidor de origen. Para solucionar el problema, consulte el artículo [Error al conectarse a SQL Server de origen cuando se usa el puerto dinámico o instancia con nombre](./known-issues-troubleshooting-dms.md#error-connecting-to-source-sql-server-when-using-dynamic-port-or-named-instance). |
| **Error 53**: error en la conexión de SQL. (Además, para los códigos de error 1, 2, 5, 53, 233, 258, 1225, 11001)<br><br> | Este error se produce si el servicio no puede conectarse al servidor de origen. Para solucionar el problema, consulte los siguientes recursos y vuelva a intentarlo. <br><br>  [Guía de usuario interactiva para solucionar problemas de conectividad](https://support.microsoft.com/help/4009936/solving-connectivity-errors-to-sql-server)<br><br> [Requisitos previos para migrar SQL Server a Azure SQL Database](./pre-reqs.md#prerequisites-for-migrating-sql-server-to-azure-sql-managed-instance) <br><br> [Requisitos previos para migrar SQL Server a una instancia administrada de Azure SQL](./pre-reqs.md#prerequisites-for-migrating-sql-server-to-azure-sql-managed-instance) |
| **Error 18456**: error de inicio de sesión.<br> | Este error se produce si el servicio no puede conectarse a la base de datos de origen mediante las credenciales de T-SQL proporcionadas. Para solucionar el problema, compruebe las credenciales proporcionadas. También puede consultar [MSSQLSERVER_18456](/sql/relational-databases/errors-events/mssqlserver-18456-database-engine-error) o los documentos de solución de problemas enumerados en la nota que se encuentra debajo de esta tabla, e intentarlo de nuevo. |
| Se ha proporcionado un valor de AccountName con un formato incorrecto "{0}". El formato esperado para AccountName es DomainName\UserName<br> | Este error se produce si el usuario selecciona la autenticación de Windows pero proporciona el nombre de usuario en un formato que no es válido. Para solucionar el problema, proporcione un nombre de usuario en el formato correcto para realizar la autenticación de Windows o seleccione **Autenticación de SQL**. |

## <a name="aws-rds-mysql"></a>AWS RDS MySQL

Los problemas potenciales asociados con la conexión a una base de datos de MySQL de AWS RDS y cómo abordarlos se proporcionan en la siguiente tabla.

| Error         | Detalles de las causas y soluciones de problemas |
| ------------- | ------------- |
| **Error [2003]** [HY000]: error de conexión. ERROR [HY000] [MySQL][ODBC x.x(w) driver] No se puede conectar al servidor MySQL en '{server}' (10060) | Este error se produce si el controlador ODBC de MySQL no puede conectarse al servidor de origen. Para solucionar este problema, puede consultar los documentos de solución de problemas enumerados en la nota que se encuentra debajo de esta tabla, e intentarlo de nuevo.<br> |
| **Error [2005]** [HY000]: error de conexión. ERROR [HY000] [MySQL][ODBC x.x(w) driver] Host "{server}" del servidor de MySQL desconocido | Este error se produce si el servicio no puede localizar el host de origen en RDS. Este problema podría deberse a que el origen enumerado no existe o a que hay un problema con la infraestructura de RDS. Para solucionar este problema, puede consultar los documentos de solución de problemas enumerados en la nota que se encuentra debajo de esta tabla, e intentarlo de nuevo.<br> |
| **Error [1045]** [HY000]: error de conexión. ERROR [HY000] [MySQL][ODBC x.x(w) driver] Acceso denegado al usuario "{user}"@"{server}" (usa la contraseña: SÍ) | Este error se produce si el controlador ODBC de MySQL no puede conectarse al servidor de origen debido a unas credenciales no válidas. Compruebe las credenciales que ha escrito. Si el problema continúa, compruebe que el equipo de origen tenga las credenciales correctas. Es posible que deba restablecer la contraseña en la consola. Si todavía tiene este problema, puede consultar los documentos de solución de problemas enumerados en la nota que se encuentra debajo de esta tabla, e intentarlo de nuevo.<br> |
| **Error [9002]** [HY000]: error de conexión. ERROR [HY000] [MySQL][ODBC x.x(w) driver] Es posible que la cadena de conexión no sea correcta. Visite el portal para obtener referencias.| Este error se produce si se produce un error en la conexión debido a un problema con la cadena de conexión. Compruebe que la cadena de conexión proporcionada sea válida. Para solucionar este problema, puede consultar los documentos de solución de problemas enumerados en la nota que se encuentra debajo de esta tabla, e intentarlo de nuevo.<br> |
| **Error en el registro binario. La variable binlog_format tiene el valor "{value}". Cámbielo a "row".** | Este error ocurre si hay un error en el registro binario y la variable binlog_format tiene el valor incorrecto. Para solucionar este problema, cambie la variable binlog_format del grupo de parámetros a 'ROW' y reinicie la instancia. Para obtener más información, consulte [Binary Logging Options and Variables](https://dev.mysql.com/doc/refman/5.7/en/replication-options-binary-log.html) (Opciones y variables del registro binario) o [AWS RDS MySQL Database Log Files documentation](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_LogAccess.Concepts.MySQL.html) (Documentación de los archivos de registro de la base de datos MySQL de AWS RDS).<br> |

> [!NOTE]
> Para obtener más información sobre la solución de problemas relacionados con la conexión a una base de datos MySQL de AWS RDS, consulte los siguientes recursos:
> *    [Troubleshooting for Amazon RDS Connectivity issues](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_Troubleshooting.html#CHAP_Troubleshooting.Connecting) (Solución de problemas con la conectividad de Amazon RDS)
> *    [How do I resolve problems connecting to my Amazon RDS database instance?](https://aws.amazon.com/premiumsupport/knowledge-center/rds-cannot-connect) (¿Cómo resuelvo los problemas de conexión de la instancia de base de datos de Amazon RDS?)

## <a name="aws-rds-postgresql"></a>PostgreSQL de AWS RDS

Los problemas potenciales asociados con la conexión a una base de datos de PostgreSQL de AWS RDS y cómo abordarlos se proporcionan en la siguiente tabla.

| Error         | Detalles de las causas y soluciones de problemas |
| ------------- | ------------- |
| **Error [101]** [08001]: error de conexión. Error [08001]: se agotó el tiempo de espera. | Este error se produce si el controlador de Postgres no puede conectarse al servidor de origen. Para solucionar este problema, puede consultar los documentos de solución de problemas enumerados en la nota que se encuentra debajo de esta tabla, e intentarlo de nuevo. |
| **Error: El parámetro wal_level tiene el valor "{value}". Por favor, cámbielo a "logical" para permitir la replicación.** | Este error ocurre si el parámetro wal_level tiene el valor incorrecto. Para solucionar este problema, cambie la variable rds.logical_replication del grupo de parámetros a 1 y reinicie la instancia. Para obtener más información, consulte los [requisitos previos para migrar a Azure PostgreSQL mediante DMS](./tutorial-postgresql-azure-postgresql-online.md#prerequisites) o [PostgreSQL en Amazon RDS](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_PostgreSQL.html). |

> [!NOTE]
> Para obtener más información sobre la solución de problemas relacionados con la conexión a una base de datos PostgreSQL de AWS RDS, consulte los siguientes recursos:
> *    [Troubleshooting for Amazon RDS Connectivity issues](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_Troubleshooting.html#CHAP_Troubleshooting.Connecting) (Solución de problemas con la conectividad de Amazon RDS)
> *    [How do I resolve problems connecting to my Amazon RDS database instance?](https://aws.amazon.com/premiumsupport/knowledge-center/rds-cannot-connect) (¿Cómo resuelvo los problemas de conexión de la instancia de base de datos de Amazon RDS?)

## <a name="aws-rds-sql-server"></a>AWS RDS SQL Server

Los problemas potenciales asociados con la conexión a una base de datos de AWS RDS SQL Server de origen y cómo abordarlos se proporcionan en la siguiente tabla.

| Error         | Detalles de las causas y soluciones de problemas |
| ------------- | ------------- |
| **Error 53**: error en la conexión de SQL. Se ha producido un error relacionado con la red o específico de la instancia al establecer una conexión en SQL Server. No se encontró el servidor o no era accesible. Compruebe que el nombre de la instancia sea correcto y que SQL Server esté configurado para permitir conexiones remotas. (proveedor: Proveedor de canalizaciones con nombre; error: 40 - No se pudo abrir una conexión a SQL Server. | Este error se produce si el servicio no puede conectarse al servidor de origen. Para solucionar este problema, puede consultar los documentos de solución de problemas enumerados en la nota que se encuentra debajo de esta tabla, e intentarlo de nuevo. |
| **Error 18456**: error de inicio de sesión. Error de inicio de sesión del usuario "{user}" | Este error se produce si el servicio no puede conectarse a la base de datos de origen mediante las credenciales de T-SQL proporcionadas. Para solucionar el problema, compruebe las credenciales proporcionadas. También puede consultar [MSSQLSERVER_18456](/sql/relational-databases/errors-events/mssqlserver-18456-database-engine-error) o los documentos de solución de problemas enumerados en la nota que se encuentra debajo de esta tabla, e intentarlo de nuevo. |
| **Error 87**: la cadena de conexión no es válida. Se ha producido un error relacionado con la red o específico de la instancia al establecer una conexión en SQL Server. No se encontró el servidor o no era accesible. Compruebe que el nombre de la instancia sea correcto y que SQL Server esté configurado para permitir conexiones remotas. (proveedor: Interfaces de red de SQL; error: 25: la cadena de conexión no es válida). | Este error se produce si el servicio no puede conectarse al servidor de origen debido a una cadena de conexión que no es válida. Para solucionar el problema, compruebe la cadena de conexión proporcionada. Si el problema persiste, puede consultar los documentos de solución de problemas enumerados en la nota que se encuentra debajo de esta tabla, e intentarlo de nuevo. |
| **Error: el certificado del servidor no es de confianza.** Se estableció correctamente una conexión con el servidor, pero luego se produjo un error durante el proceso de inicio de sesión. (proveedor: Proveedor SSL; error: 0: la cadena de certificados la emitió una autoridad que no es de confianza.) | Este error se produce si el certificado que se usa no es de confianza. Para solucionar el problema, debe encontrar un certificado de confianza y habilitarlo en el servidor. Como alternativa, puede seleccionar la opción del certificado de confianza mientras se conecta. Realice esta acción solo si está familiarizado con el certificado utilizado y confía en él. <br> Las conexiones TLS que están cifradas con un certificado autofirmado no proporcionan una seguridad sólida y son susceptibles a ataques de tipo "Man in the middle". No se debe confiar en TLS con certificados autofirmados en un entorno de producción o en servidores que estén conectados a Internet. <br> Para obtener más información, consulte [Using SSL with a Microsoft SQL Server DB Instance](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/SQLServer.Concepts.General.SSL.Using.html) (Usar SSL con una instancia de base de datos de Microsoft SQL Server)o [Tutorial: Migrar RDS SQL Server a Azure mediante DMS ](./index.yml). |
| **Error 300**: el usuario no tiene los permisos necesarios. El permiso VIEW SERVER STATE fue denegado en el objeto "{server}" y la base de datos "{database}" | Este error se produce si el usuario no tiene permiso para realizar la migración. Para solucionar el problema, consulte [GRANT (permisos de servidor de Transact-SQL)](/sql/t-sql/statements/grant-server-permissions-transact-sql) o [ Tutorial: Migrar RDS SQL Server a Azure mediante DMS](./index.yml) para obtener más detalles. |

> [!NOTE]
> Para obtener más información sobre la solución de problemas relacionados con la conexión a un origen de AWS RDS SQL Server, consulte los siguientes recursos:
>
> *    [Solving Connectivity errors to SQL Server](https://support.microsoft.com/help/4009936/solving-connectivity-errors-to-sql-server) (Solución de problemas de conectividad en SQL Server)
> * [How do I resolve problems connecting to my Amazon RDS database instance?](https://aws.amazon.com/premiumsupport/knowledge-center/rds-cannot-connect) (¿Cómo resuelvo los problemas de conexión de la instancia de base de datos de Amazon RDS?)

## <a name="known-issues"></a>Problemas conocidos

* [Problemas conocidos y limitaciones de migración con las migraciones en línea a Azure SQL Database](./index.yml)
* [Problemas conocidos y limitaciones de migración con las migraciones en línea a Azure Database for PostgreSQL](./known-issues-azure-postgresql-online.md)

## <a name="next-steps"></a>Pasos siguientes

* Consulte el artículo [PowerShell en Azure Database Migration Service](/powershell/module/azurerm.datamigration/?view=azurermps-6.13.0&preserve-view=true#data_migration).
* Consulte el artículo [Cómo configurar parámetros del servidor en Azure Database for MySQL mediante Azure Portal](../mysql/howto-server-parameters.md).
* Consulte el artículo [Introducción a los requisitos previos para usar Azure Database Migration Service](./pre-reqs.md).
* Consulte las [preguntas más frecuentes sobre el uso de Azure Database Migration Service](./faq.yml).
