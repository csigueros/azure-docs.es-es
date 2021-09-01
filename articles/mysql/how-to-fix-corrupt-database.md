---
title: 'Corrección de daños en bases de datos: Azure Database for MySQL'
description: En este artículo, aprenderá a solucionar problemas de daños en bases de datos en Azure Database for MySQL.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 09/21/2020
ms.openlocfilehash: d9b79ba83613a854610eae8fee6392d6e12ef935
ms.sourcegitcommit: 8b38eff08c8743a095635a1765c9c44358340aa8
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/30/2021
ms.locfileid: "122652972"
---
# <a name="troubleshoot-database-corruption-in-azure-database-for-mysql"></a>Solución de daños en bases de datos en Azure Database for MySQL

[!INCLUDE[applies-to-mysql-single-server](includes/applies-to-mysql-single-server.md)]

Los daños en las bases de datos pueden provocar tiempos de inactividad en su aplicación. También es fundamental resolver los problemas de daños a tiempo para evitar la pérdida de datos. Cuando se produzcan daños en una base de datos, en los registros del servidor verá este error: `InnoDB: Database page corruption on disk or a failed.`

En este artículo, aprenderá a resolver problemas de daños en bases de datos o en tablas. Azure Database for MySQL usa el motor InnoDB. Incluye operaciones de reparación y comprobación de daños automatizadas. InnoDB comprueba si hay páginas dañadas mediante la ejecución de sumas de comprobación en cada página que lee. Si encuentra una discrepancia de suma de comprobación, detendrá automáticamente el servidor MySQL.

Pruebe las siguientes opciones para mitigar rápidamente los problemas de daños en las bases de datos.

## <a name="restart-your-mysql-server"></a>Reinicio del servidor de MySQL

Normalmente, observará que una base de datos o una tabla están dañadas cuando la aplicación accede a la tabla o la base de datos. InnoDB incluye un mecanismo de recuperación tras un bloqueo que puede resolver la mayoría de los problemas al reiniciar el servidor. Por lo tanto, el reinicio del servidor debería ayudar a que este se recuperase de un bloqueo que hizo que la base de datos estuviera en mal estado.

## <a name="use-the-dump-and-restore-method"></a>Uso de los métodos de copia de seguridad y restauración

Se recomienda resolver los problemas de daños usando un método de *seguridad y restauración*. Este método implica lo siguiente:
1. Acceder a la tabla dañada.
1. Usar la utilidad mysqldump para crear una copia de seguridad lógica de la tabla. La copia de seguridad conservará la estructura de la tabla y los datos que contiene.
1. Volver a cargar la tabla en la base de datos.

### <a name="back-up-your-database-or-tables"></a>Copia de seguridad de las tablas o bases de datos

> [!Important]
> - Asegúrese de que ha configurado una regla de firewall para acceder al servidor desde el equipo cliente. Para obtener más información, consulte los artículos sobre cómo [configurar una regla de firewall en un solo servidor](howto-manage-firewall-using-portal.md) y [configurar una regla de firewall en un servidor flexible](flexible-server/how-to-connect-tls-ssl.md).
> - Use la opción `--ssl-cert` de SSL para mysqldump si SSL está habilitado.

Cree un archivo de copia de seguridad desde la línea de comandos mediante mysqldump. Use este comando:

```
$ mysqldump [--ssl-cert=/path/to/pem] -h [host] -u [uname] -p[pass] [dbname] > [backupfile.sql]
```

Descripciones de parámetro:
- `[ssl-cert=/path/to/pem]`: la ruta de acceso al certificado SSL. Descargue el certificado SSL en el equipo cliente y establezca la ruta de acceso en el comando. No use este parámetro si SSL está deshabilitado.
- `[host]`: un servidor de Azure Database for MySQL.
- `[uname]`: su nombre de usuario administrador del servidor.
- `[pass]`: la contraseña del usuario administrador.
- `[dbname]`: Nombre de la base de datos.
- `[backupfile.sql]`: el nombre de archivo de la copia de seguridad de la base de datos.

> [!Important]
> - Para un solo servidor, use el formato `admin-user@servername` para reemplazar `myserveradmin` en los siguientes comandos.
> - Para un servidor flexible, use el formato `admin-user` para reemplazar `myserveradmin` en los siguientes comandos.

Si solo están dañadas ciertas tablas, seleccione las tablas específicas de la base de datos para las que quiere realizar una copia de seguridad:
```
$ mysqldump --ssl-cert=</path/to/pem> -h mydemoserver.mysql.database.azure.com -u myserveradmin -p testdb table1 table2 > testdb_tables_backup.sql
```

Para hacer una copia de seguridad de una o varias bases de datos, use el conmutador `--database` y enumere los nombres de las bases de datos en una lista separados por espacios:

```
$ mysqldump --ssl-cert=</path/to/pem>  -h mydemoserver.mysql.database.azure.com -u myserveradmin -p --databases testdb1 testdb3 testdb5 > testdb135_backup.sql
```

### <a name="restore-your-database-or-tables"></a>Restauración de tablas o bases de datos

En los pasos siguientes se muestra cómo restaurar bases de datos o tablas. Después de crear el archivo de copia de seguridad, puede restaurar las tablas o bases de datos mediante la utilidad mysql. Ejecute este comando:

```
mysql  --ssl-cert=</path/to/pem> -h [hostname] -u [uname] -p[pass] [db_to_restore] < [backupfile.sql]
```
Este es un ejemplo que restaura `testdb` a partir de un archivo de copia de seguridad creado con mysqldump: 

> [!Important]
> - Para un solo servidor, use el formato `admin-user@servername` para reemplazar `myserveradmin` en el siguiente comando.
> - Para un servidor flexible, use el formato ```admin-user``` para reemplazar `myserveradmin` en el siguiente comando. 

```
$ mysql --ssl-cert=</path/to/pem> -h mydemoserver.mysql.database.azure.com -u myserveradmin -p testdb < testdb_backup.sql
```

## <a name="next-steps"></a>Pasos siguientes
Si los pasos anteriores no resuelven el problema, siempre puede restaurar todo el servidor:
- [Restauración de un solo servidor en Azure Database for MySQL](howto-restore-server-portal.md)
- [Restauración de un servidor flexible en Azure Database for MySQL](flexible-server/how-to-restore-server-portal.md)



