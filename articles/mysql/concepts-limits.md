---
title: 'Limitaciones: Azure Database for MySQL'
description: En este artículo, se describen limitaciones de Azure Database for MySQL tales como el número de opciones del motor de almacenamiento y de conexión.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 10/1/2020
ms.openlocfilehash: f71084e9c13bd1a30f5d5f01a04172671074db03
ms.sourcegitcommit: 70ce9237435df04b03dd0f739f23d34930059fef
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/05/2021
ms.locfileid: "111525217"
---
# <a name="limitations-in-azure-database-for-mysql"></a>Limitaciones en Azure Database for MySQL
En las siguientes secciones se describen la capacidad, la compatibilidad del motor de almacenamiento, la compatibilidad de los privilegios, la compatibilidad de las instrucciones de manipulación de datos y los límites funcionales del servicio de base de datos. Consulte también las [limitaciones generales](https://dev.mysql.com/doc/mysql-reslimits-excerpt/5.6/en/limits.html) que se aplican al motor de base de datos MySQL.

## <a name="server-parameters"></a>Parámetros del servidor

> [!NOTE]
> Si busca valores mínimos y máximos para los parámetros del servidor, como `max_connections` y `innodb_buffer_pool_size`, esta información se ha pasado al artículo sobre los **[parámetros del servidor](./concepts-server-parameters.md)** .

Azure Database for MySQL admite el ajuste de los valores de parámetros del servidor. Los valores mínimo y máximo de algunos parámetros (por ejemplo, `max_connections`, `join_buffer_size`, `query_cache_size`) vienen determinados por el plan de tarifa y los núcleos virtuales del servidor. Consulte los [parámetros del servidor](./concepts-server-parameters.md) para más información sobre estos límites.

Tras la implementación inicial, un servidor de Azure para MySQL incluye tablas de sistemas para la información de zona horaria, pero estas tablas no se rellenan. Las tablas de la zona horaria se pueden rellenar mediante una llamada al procedimiento almacenado `mysql.az_load_timezone` desde una herramienta como la línea de comandos de MySQL o MySQL Workbench. Vea los artículos de [Azure Portal](howto-server-parameters.md#working-with-the-time-zone-parameter) o de la [CLI de Azure](howto-configure-server-parameters-using-cli.md#working-with-the-time-zone-parameter) sobre cómo llamar al procedimiento almacenado y establecer las zonas horarias globales o de nivel de sesión.

Los complementos de contraseñas como "validate_password" y "caching_sha2_password" no son compatibles con el servicio.

## <a name="storage-engines"></a>Motores de almacenamiento

MySQL es compatible con muchos motores de almacenamiento. En Azure Database for MySQL, los motores de almacenamiento siguientes son compatibles y no compatibles:

### <a name="supported"></a>Compatible
- [InnoDB](https://dev.mysql.com/doc/refman/5.7/en/innodb-introduction.html)
- [MEMORY](https://dev.mysql.com/doc/refman/5.7/en/memory-storage-engine.html)

### <a name="unsupported"></a>No compatible
- [MyISAM](https://dev.mysql.com/doc/refman/5.7/en/myisam-storage-engine.html)
- [BLACKHOLE](https://dev.mysql.com/doc/refman/5.7/en/blackhole-storage-engine.html)
- [ARCHIVE](https://dev.mysql.com/doc/refman/5.7/en/archive-storage-engine.html)
- [FEDERATED](https://dev.mysql.com/doc/refman/5.7/en/federated-storage-engine.html)

## <a name="privileges--data-manipulation-support"></a>Compatibilidad con privilegios y con la manipulación de datos

Muchos parámetros y ajustes del servidor pueden reducir por error el rendimiento del servidor o invalidar las propiedades ACID del servidor de MySQL. Para mantener la integridad del servicio y el SLA en un nivel de producto, no se exponen varios roles en este servicio. 

El servicio MySQL no permite el acceso directo al sistema de archivos subyacente. No se admiten algunos comandos de manipulación de datos. 

### <a name="unsupported"></a>No compatible

No se admite lo siguiente:
- Rol DBA: restringido. De forma alternativa, puede usar el rol de administrador (generado durante la creación del nuevo servidor), que le permite ejecutar la mayoría de las instrucciones DDL y DML. 
- Privilegio SUPER: del mismo modo, el [privilegio SUPER](https://dev.mysql.com/doc/refman/5.7/en/privileges-provided.html#priv_super) también está restringido.
- DEFINER: requiere privilegios SUPER para crear y está restringido. Si importa datos mediante una copia de seguridad, quite los comandos `CREATE DEFINER` manualmente o mediante el comando `--skip-definer` durante una operación [mysqldump](https://dev.mysql.com/doc/refman/5.7/en/mysqlpump.html).
- Bases de datos del sistema: La [base de datos del sistema de MySQL](https://dev.mysql.com/doc/refman/5.7/en/system-schema.html) es de solo lectura y se usa para admitir varias funcionalidades de PaaS. No puede realizar cambios en la base de datos del sistema de `mysql`.
- `SELECT ... INTO OUTFILE`: no se admite en el servicio.
- `LOAD_FILE(file_name)`: no se admite en el servicio.

### <a name="supported"></a>Compatible
- `LOAD DATA INFILE` es compatible, pero el parámetro `[LOCAL]` debe especificarse y dirigirse a una ruta de acceso UNC (Azure Storage montado a través de SMB).

## <a name="functional-limitations"></a>Limitaciones funcionales

### <a name="scale-operations"></a>Operaciones de escalado
- El escalado dinámico hacia y desde planes de tarifa Básico no se admite en este momento.
- La reducción del tamaño de almacenamiento del servidor no se admite.

### <a name="server-version-upgrades"></a>Actualizaciones de la versión de servidor
- La migración automatizada entre versiones principales del motor de base de datos no se admite en este momento. Si quiere actualizar a la siguiente versión principal, realice un [volcado y restáurelo](./concepts-migrate-dump-restore.md) a un servidor que se haya creado con la nueva versión del motor.

### <a name="point-in-time-restore"></a>Restauración a un momento dado
- Al usar la característica PITR, el nuevo servidor se crea con la misma configuración que el servidor en el que se basa.
- La restauración a un servidor que se ha eliminado no se admite en este momento.

### <a name="vnet-service-endpoints"></a>Puntos de conexión de servicio de red virtual
- La compatibilidad con puntos de conexión de servicio de red virtual solo existe para servidores de uso general y optimizados para memoria.

### <a name="storage-size"></a>Tamaño de almacenamiento
- Consulte [Planes de tarifa](concepts-pricing-tiers.md) para obtener los límites de tamaño de almacenamiento para cada plan de tarifa.

## <a name="current-known-issues"></a>Problemas conocidos actualmente
- La instancia del servidor MySQL muestra una versión de servidor errónea después de establecer la conexión. Para obtener la versión del motor de instancias de servidor correcta, use el comando `select version();`.

## <a name="next-steps"></a>Pasos siguientes
- [Qué está disponible en cada nivel de servicio](concepts-pricing-tiers.md)
- [Versiones de base de datos de MySQL admitidas](concepts-supported-versions.md)
