---
title: 'Replicación de datos de entrada: Azure Database for MySQL flexible'
description: Obtenga información sobre cómo utilizar la replicación de datos de entrada para sincronizar datos de un servidor externo con el servicio flexible de Azure Database for MySQL.
author: SudheeshGH
ms.author: sunaray
ms.service: mysql
ms.topic: conceptual
ms.date: 06/08/2021
ms.openlocfilehash: 071672c5c2d3c741abd14dad94c8c150e427a3ce
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/22/2021
ms.locfileid: "114464780"
---
# <a name="replicate-data-into-azure-database-for-mysql-flexible--server-preview"></a>Replicación de datos en el servidor flexible de Azure Database for MySQL (versión preliminar)

[!INCLUDE[applies-to-mysql-flexible-server](../includes/applies-to-mysql-flexible-server.md)]

La replicación de datos de entrada permite sincronizar datos de una instancia externa de MySQL Server con el servicio flexible de Azure Database for MySQL. El servidor externo puede ser local, de máquinas virtuales, servidor único de Azure Database for MySQL o un servicio de base de datos hospedado por otros proveedores de nube. La replicación de datos de entrada se basa en la posición del archivo de registro binario (binlog). Para obtener más información acerca de la replicación de binlog, consulte la [Introducción a la replicación de binlog de MySQL](https://dev.mysql.com/doc/refman/5.7/en/binlog-replication-configuration-overview.html).

> [!Note]
> Actualmente, la replicación basada en GTID no se admite para los servidores flexibles de Azure Database for MySQL.<br>
> No se admite la configuración de la replicación de los datos de entrada para servidores de alta disponibilidad con redundancia de zona. 

## <a name="when-to-use-data-in-replication"></a>Cuándo utilizar la replicación de datos de entrada

Los escenarios principales que se deben tener en cuenta para usar la replicación de datos de entrada son los siguientes:

- **Sincronización de datos híbrida:** con la replicación de datos de entrada, se pueden mantener los datos sincronizados entre los servidores locales y el servidor flexible de Azure Database for MySQL. Esta sincronización resulta útil para crear aplicaciones híbridas. Este método resulta atractivo cuando se tiene un servidor de base de datos local existente, pero quiere mover los datos a una región más cercana a los usuarios finales.
- **Sincronización de varias nubes:** para soluciones de nube complejas, use la replicación de datos de entrada a fin de sincronizar datos entre el servidor flexible de Azure Database for MySQL y distintos proveedores de nube, incluidas las máquinas virtuales y los servicios de base de datos hospedados en dichas nubes.
- **Migración:** los clientes pueden realizar una migración de tiempo mínimo con herramientas de código abierto como [MyDumper/MyLoader](https://centminmod.com/mydumper.html) con la replicación de datos de entrada. Con la replicación de datos de entrada es posible realizar una migración total selectiva de la carga de producción desde la base de datos de origen a la de destino. 

Para los escenarios de migración, use [Azure Database Migration Service](https://azure.microsoft.com/services/database-migration/) (DMS).

## <a name="limitations-and-considerations"></a>Limitaciones y consideraciones

### <a name="data-not-replicated"></a>Datos no replicados

La [*base de datos del sistema mysql*](https://dev.mysql.com/doc/refman/5.7/en/system-schema.html) del servidor de origen no se replica. Además, los cambios en las cuentas y en los permisos del servidor de origen no se replican. Si crea una cuenta en el servidor de origen y esta cuenta necesita acceder al servidor de réplica, cree manualmente la misma cuenta en el servidor de réplica. Para reconocer qué tablas se encuentran en la base de datos del sistema, vea el [manual de MySQL](https://dev.mysql.com/doc/refman/5.7/en/system-schema.html).

### <a name="data-in-replication-not-supported-on-ha-enabled-servers"></a>No se admite la replicación de datos de entrada en servidores habilitados para la alta disponibilidad. 
No se admite la configuración de la replicación de datos de entrada para servidores de alta disponibilidad con redundancia de zona. En los servidores en los que se ha habilitado la alta disponibilidad, los procedimientos almacenados `mysql.az_replication_*` para la replicación no estarán disponibles. 

### <a name="filtering"></a>Filtrado

La modificación del parámetro `replicate_wild_ignore_table` que se usaba para crear un filtro de replicación para las tablas no se admite actualmente para Azure Database for MySQL con la opción de servidor flexible. 

### <a name="requirements"></a>Requisitos

- La versión del servidor de origen debe ser, al menos, MySQL 5.7.
- Nuestra recomendación es tener la misma versión para las versiones de servidor de origen y réplica. Por ejemplo, ambas deben ser MySQL versión 5.7 o ambas deben ser MySQL versión 8.0.
- Nuestra recomendación es tener una clave principal en cada tabla. Si tenemos una tabla sin clave principal, podría encontrarse con lentitud en la replicación.
- El servidor de origen debe usar el motor InnoDB de MySQL.
- El usuario debe tener permisos para configurar el registro binario y crear nuevos usuarios en el servidor de origen.
- Si el servidor de origen tiene SSL habilitado, asegúrese de que el certificado de entidad de certificación de SSL proporcionado para el dominio se haya incluido en el procedimiento almacenado `mysql.az_replication_change_master`. Consulte los [ejemplos](./how-to-data-in-replication.md#link-source-and-replica-servers-to-start-data-in-replication) siguientes y el parámetro `master_ssl_ca`.
- Asegúrese de que la máquina que hospeda el servidor de origen permite el tráfico entrante y saliente en el puerto 3306.
- Asegúrese de que el servidor de origen tenga una **dirección IP pública**, de que el DNS sea accesible públicamente o de que el servidor de origen tenga un nombre de dominio completo (FQDN).
- En el caso del acceso público, asegúrese de que el servidor de origen tenga una dirección IP pública, que DNS sea accesible públicamente y que el servidor de origen tenga un nombre de dominio completo (FQDN).
- En caso de acceso privado, asegúrese de que el nombre del servidor de origen se pueda resolver y sea accesible desde la red virtual en la que se ejecuta la instancia de Azure Database for MySQL . Para más información, consulte [Resolución de nombres para recursos en redes virtuales de Azure](../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md).

## <a name="next-steps"></a>Pasos siguientes

- Información acerca de cómo [configurar la replicación de datos internos](how-to-data-in-replication.md)
- Información acerca de cómo [replicar en Azure con réplicas de lectura](concepts-read-replicas.md)
