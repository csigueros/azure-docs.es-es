---
title: 'Procedimientos operativos recomendados para MySQL Server: Azure Database for MySQL'
description: En este artículo se describen los procedimientos recomendados para operar una base de datos MySQL en Azure.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: conceptual
ms.date: 11/23/2020
ms.openlocfilehash: 85048e9a5fccc520cab5bb319ee0b6f81b06f4fd
ms.sourcegitcommit: 8b38eff08c8743a095635a1765c9c44358340aa8
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/30/2021
ms.locfileid: "122653053"
---
# <a name="best-practices-for-server-operations-on-azure-database-for-mysql--single-server"></a>Procedimientos recomendados para las operaciones de servidor en Azure Database for MySQL: servidor único

[!INCLUDE[applies-to-mysql-single-server](includes/applies-to-mysql-single-server.md)]

Obtenga información sobre los procedimientos recomendados para trabajar con Azure Database for MySQL. A medida que agregamos nuevas funcionalidades a la plataforma, seguiremos centrándonos en perfeccionar los procedimientos recomendados que se detallan en esta sección.

## <a name="azure-database-for-mysql-operational-guidelines"></a>Instrucciones operativas de Azure Database for MySQL 

A continuación, se muestran las instrucciones operativas que deben seguirse al trabajar con Azure Database for MySQL para mejorar el rendimiento de la base de datos: 

* **Colocalización**: para reducir la latencia de la red, coloque el cliente y el servidor de base de datos en la misma región de Azure.

* **Supervisión del uso de la memoria, la CPU y el almacenamiento**: Puede [configurar alertas](howto-alert-on-metric.md) para recibir una notificación cuando cambien los patrones de uso o cuando se alcance la capacidad de la implementación, de modo que pueda mantener el rendimiento y la disponibilidad del sistema. 

* **Escalado vertical de la instancia de base de datos**: puede [escalar verticalmente](howto-create-manage-server-portal.md) al aproximarse a los límites de la capacidad de almacenamiento. Debe tener algún búfer en el almacenamiento y la memoria para admitir los aumentos imprevistos por parte de las aplicaciones. También puede [habilitar la característica de crecimiento automático del almacenamiento](howto-auto-grow-storage-portal.md) para que el servicio escale automáticamente el almacenamiento cuando se aproxime a los límites del almacenamiento. 

* **Configuración de copias de seguridad**: habilite las [copias de seguridad con redundancia geográfica o locales](howto-restore-server-portal.md#set-backup-configuration) según el requisito del negocio. Además, puede modificar el período de retención durante el cual las copias de seguridad estarán disponibles para la continuidad empresarial. 

* **Aumento de la capacidad de E/S**: si la carga de trabajo de la base de datos requiere más E/S de las que ha aprovisionado, la recuperación u otras operaciones transaccionales para la base de datos serán lentas. Para aumentar la capacidad de E/S de una instancia de servidor, realice una o todas las acciones siguientes: 

    * Azure Database for MySQL proporciona escalado por IOPS a la razón de tres IOPS por GB de almacenamiento aprovisionado. [Aumente el almacenamiento aprovisionado](howto-create-manage-server-portal.md#scale-storage-up) para escalar el IOPS para obtener un mejor rendimiento. 

    * Si ya usa el almacenamiento de IOPS aprovisionado, aprovisione [más capacidad de rendimiento](howto-create-manage-server-portal.md#scale-storage-up). 

* **Escalado de proceso**: la carga de trabajo de la base de datos también se puede limitar debido a la CPU o la memoria y esto puede tener un impacto importante en el procesamiento de transacciones. Tenga en cuenta que el proceso (plan de tarifa) se puede escalar o reducir verticalmente solo entre los niveles [De uso general u Optimizado para memoria](concepts-pricing-tiers.md). 

* **Prueba para la conmutación por error**: pruebe manualmente la conmutación por error de la instancia del servidor para entender cuánto tiempo tarda el proceso en el caso de uso en cuestión y para asegurarse de que la aplicación que accede a la instancia del servidor puede conectarse automáticamente a la nueva instancia del servidor después de la conmutación por error.

* **Uso de la clave principal**: asegúrese de que las tablas tienen una clave principal o única mientras trabaja en Azure Database for MySQL. Esto ayuda enormemente a la creación de copias de seguridad y réplicas, entre otros, y mejora el rendimiento.

* **Configuración del valor TTL**: si la aplicación cliente almacena en caché los datos del servicio de nombres de dominio (DNS) de las instancias de servidor, establezca un valor de período de vida (TTL) de menos de 30 segundos. Como que la dirección IP subyacente de una instancia de servidor puede cambiar después de una conmutación por error, el almacenamiento en caché de los datos del DNS durante un tiempo prolongado puede provocar errores de conexión si la aplicación intenta conectarse a una dirección IP que ya no está en servicio.

* Use la agrupación de conexiones para evitar alcanzar los [límites máximos de conexión](concepts-server-parameters.md#max_connections) y use la lógica de reintento para evitar problemas de conexión intermitentes. 

* Si usa la réplica, use [ProxySQL para equilibrar la carga](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/scaling-an-azure-database-for-mysql-workload-running-on/ba-p/1105847) entre el servidor principal y el servidor de la réplica secundaria legible. Vea aquí los pasos de la configuración. </br> 

* Al aprovisionar el recurso, asegúrese de haber [habilitado el crecimiento automático](howto-auto-grow-storage-portal.md) para Azure Database for MySQL. Esto no agrega ningún costo adicional y protegerá la base de datos de cualquier cuello de botella de almacenamiento con el que pueda encontrarse. </br> 


### <a name="using-innodb-with-azure-database-for-mysql"></a>Uso de InnoDB con Azure Database for MySQL

*   Si usa la característica `ibdata1`, que es un espacio de tabla del sistema, el archivo de datos no se puede reducir ni purgar quitando los datos de la tabla ni moviendo la tabla a `tablespaces` de archivo por tabla.

* En el caso de una base de datos con un tamaño superior a 1 TB, debe crear la tabla en `tablespace` **innodb_file_per_table**. En el caso de una tabla única con un tamaño superior a 1 TB, debe crear la tabla de [particiones](https://dev.mysql.com/doc/refman/5.7/en/partitioning.html).

*   En el caso de un servidor con una gran cantidad de `tablespace`, el inicio del motor será muy lento debido al recorrido de espacio de tabla secuencial durante el inicio o la conmutación por error de MySQL. 

* Establezca innodb_file_per_table = ON antes de crear una tabla si el número total de tablas es inferior a 500.

* Si tiene más de 500 tablas en una base de datos, revise el tamaño de cada tabla individual. En el caso de una tabla de tamaño grande, de todos modos debe considerar usar el espacio de tabla archivo por tabla para evitar que el archivo del espacio de tabla del sistema alcance el límite máximo de almacenamiento.

> [!NOTE]
> En el caso de las tablas con un tamaño inferior a 5 GB, considere la posibilidad de usar el espacio de tabla del sistema. 
> ```sql
>    CREATE TABLE tbl_name ... *TABLESPACE* = *innodb_system*;
> ```

* [Cree particiones](https://dev.mysql.com/doc/refman/5.7/en/partitioning.html) de la tabla al crear la tabla si tiene una tabla muy grande que pudiera llegar a tener más de 1 TB.

* Use varios servidores MySQL y distribuya las tablas en esos servidores. Evite colocar demasiadas tablas en un solo servidor si tiene alrededor de 10 000 tablas o más. 

## <a name="next-steps"></a>Pasos siguientes
- [Procedimiento recomendado para el rendimiento de Azure Database for MySQL](concept-performance-best-practices.md)
- [Procedimiento recomendado para la supervisión de Azure Database for MySQL](concept-monitoring-best-practices.md)
