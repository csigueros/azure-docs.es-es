---
title: 'Procedimientos recomendados de rendimiento: Azure Database for MySQL'
description: En este artículo se describen algunas recomendaciones para supervisar y ajustar el rendimiento para Azure Database for MySQL.
author: Bashar-MSFT
ms.author: bahusse
ms.service: mysql
ms.topic: conceptual
ms.date: 1/28/2021
ms.openlocfilehash: dc47e06643f3bb4ebf986382e149e13fd9bbc968
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121781095"
---
# <a name="best-practices-for-optimal-performance-of-your-azure-database-for-mysql---single-server"></a>Procedimientos recomendados para un rendimiento óptimo de Azure Database for MySQL: servidor único

[!INCLUDE[applies-to-mysql-single-server](includes/applies-to-mysql-single-server.md)]

Obtenga información sobre cómo obtener el mejor rendimiento mientras trabaja con Azure Database for MySQL: servidor único. Al agregar nuevas funcionalidades a la plataforma, seguiremos perfeccionando las recomendaciones de esta sección.

## <a name="physical-proximity"></a>Proximidad física

 Asegúrese de implementar una aplicación y la base de datos en la misma región. Una comprobación rápida antes de iniciar cualquier ejecución de pruebas comparativas de rendimiento es determinar la latencia de red entre el cliente y la base de datos mediante una consulta SELECT 1 simple. 

## <a name="accelerated-networking"></a>Redes aceleradas

Use redes aceleradas para el servidor de aplicaciones si usa una máquina virtual de Azure, Azure Kubernetes o App Services. Las redes aceleradas habilitan la virtualización de E/S de raíz única (SR-IOV) en una máquina virtual, lo que mejora considerablemente su rendimiento en la red. Este método de alto rendimiento omite el host de la ruta de acceso de datos, lo que reduce la latencia, la inestabilidad y la utilización de la CPU para usarse con las cargas de trabajo de red más exigentes en los tipos de máquina virtual admitidos.

## <a name="connection-efficiency"></a>Eficacia de la conexión

El establecimiento de una nueva conexión siempre es una tarea costosa y que consume mucho tiempo. Cuando una aplicación solicita una conexión de base de datos, da prioridad a la asignación de las conexiones de base de datos inactivas existentes, en lugar de crear una nueva.  Estas son algunas opciones para las prácticas de conexión correctas:

- **ProxySQL**: use [ProxySQL](https://proxysql.com/), que proporciona agrupación de conexiones integrada y [equilibrio de carga de la carga de trabajo](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/load-balance-read-replicas-using-proxysql-in-azure-database-for/ba-p/880042) a varias réplicas de lectura según sea necesario a petición con cualquier cambio en el código de la aplicación.

- **Proxy de datos de Heimdall**: como alternativa, también puede aprovechar el proxy de datos de Heimdall, una solución de proxy patentada independiente del proveedor. Admite el almacenamiento en caché de consultas y la división de lectura/escritura con la detección de retrasos de replicación. También puede consultar cómo [acelerar el rendimiento de MySQL con el proxy de Heimdall](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/accelerate-mysql-performance-with-the-heimdall-proxy/ba-p/1063349).  

- **Conexión persistente o de larga duración**: si la aplicación tiene transacciones cortas o consultas normalmente con un tiempo de ejecución de < 5-10 ms, reemplace las conexiones cortas por conexiones persistentes. Reemplazar las conexiones cortas por conexiones persistentes solo requiere cambios menores en el código, pero tiene un efecto importante en cuanto a la mejora del rendimiento en muchos escenarios de aplicación típicos. Asegúrese de establecer el tiempo de espera o cerrar la conexión una vez completada la transacción.

- **Réplica**: si usa la réplica, use [ProxySQL](https://proxysql.com/) para equilibrar la carga entre el servidor principal y el servidor de la réplica secundaria legible. Aprenda [cómo configurar una instancia de ProxySQL](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/scaling-an-azure-database-for-mysql-workload-running-on/ba-p/1105847).

## <a name="data-import-configurations"></a>Configuraciones de importación de datos

- Puede escalar temporalmente la instancia a un mayor tamaño de SKU antes de iniciar una operación de importación de datos y luego reducirla verticalmente cuando la importación sea correcta.
- Puede importar los datos con un tiempo de inactividad mínimo mediante el uso de [Azure Database Migration Service (DMS)](https://datamigration.microsoft.com/) para migraciones en línea o sin conexión. 

## <a name="azure-database-for-mysql-memory-recommendations"></a>Recomendaciones de memoria para Azure Database for MySQL

Un procedimiento recomendado de rendimiento de Azure Database for MySQL es asignar suficiente RAM para que el conjunto de trabajo resida casi completamente en la memoria. 

- Compruebe si el porcentaje de memoria se está utilizando para alcanzar los [límites](./concepts-pricing-tiers.md) con las [métricas del servidor de MySQL](./concepts-monitoring.md). 
- Configure alertas con tales números para asegurarse de que, a medida que los servidores alcanzan los límites, puede realizar acciones rápidas para corregirlo. En función de los límites definidos, compruebe si se escala verticalmente la SKU de la base de datos, ya sea para un mayor tamaño de proceso o para mejorar el plan de tarifa, lo que da lugar a un aumento considerable del rendimiento. 
- Escale verticalmente hasta que los números de rendimiento dejen de caer drásticamente después de una operación de escalado. Para información sobre la supervisión de las métricas de una instancia de base de datos, consulte [Métricas de base de datos de MySQL](./concepts-monitoring.md#metrics).
 
## <a name="use-innodb-buffer-pool-warmup"></a>Uso de la preparación del grupo de búferes de InnoDB

Después de reiniciar el servidor de Azure Database for MySQL, las páginas de datos que residen en el almacenamiento se cargan cuando se consultan las tablas, lo que conduce a una mayor latencia y un menor rendimiento en la primera ejecución de las consultas. Esta situación puede no ser aceptable para cargas de trabajo sensibles a la latencia. 

El uso de la preparación del grupo de búferes de InnoDB acorta el período de preparación mediante la recarga de las páginas del disco que estaban en el grupo de búferes antes del reinicio, en lugar de esperar a que las operaciones DML o SELECT accedan a las filas correspondientes.

Puede reducir el período de preparación después de reiniciar el servidor de Azure Database for MySQL, lo que representa una ventaja de rendimiento mediante la configuración de los [parámetros del servidor del grupo de búferes de InnoDB](https://dev.mysql.com/doc/refman/8.0/en/innodb-preload-buffer-pool.html). InnoDB guarda un porcentaje de las páginas usadas más recientemente para cada grupo de búferes al cerrar el servidor y restaura estas páginas al iniciar el servidor.

También es importante tener en cuenta que el rendimiento mejorado se produce a costa del tiempo de inicio más largo para el servidor. Cuando este parámetro está habilitado, se espera que el tiempo de inicio y reinicio del servidor aumente en función de las IOPS aprovisionadas en dicho servidor. 

Se recomienda probar y supervisar el tiempo de reinicio para garantizar que el rendimiento de inicio y reinicio es aceptable, ya que el servidor no está disponible durante ese tiempo. No se recomienda usar este parámetro con menos de 1000 IOPS aprovisionadas (es decir, cuando el almacenamiento aprovisionado es inferior a 335 GB).

Para guardar el estado del grupo de búferes al cerrar el servidor, establezca el parámetro de servidor `innodb_buffer_pool_dump_at_shutdown` en `ON`. Del mismo modo, establezca el parámetro de servidor `innodb_buffer_pool_load_at_startup` en `ON` para restaurar el estado del grupo de búferes al iniciar el servidor. Puede controlar el impacto del tiempo de inicio o reinicio si reduce y ajusta el valor del parámetro de servidor `innodb_buffer_pool_dump_pct`. De manera predeterminada, este parámetro está establecido en `25`.

> [!Note]
> Los parámetros de preparación del grupo de búferes de InnoDB solo se admiten en servidores de almacenamiento de uso general con un almacenamiento de hasta 16 TB. Obtenga más información sobre las [opciones de almacenamiento de Azure Database for MySQL aquí](./concepts-pricing-tiers.md#storage).

## <a name="next-steps"></a>Pasos siguientes

- [Procedimiento recomendado para las operaciones de servidor con Azure Database for MySQL](concept-operation-excellence-best-practices.md) <br/>
- [Procedimiento recomendado para la supervisión de Azure Database for MySQL](concept-monitoring-best-practices.md)<br/>
- [Introducción a Azure Database for MySQL](quickstart-create-mysql-server-database-using-azure-portal.md)<br/>
