---
title: Selección de las herramientas adecuadas para la migración a Azure Database for MySQL
description: En este tema se proporciona una tabla de decisiones que ayuda a los clientes a elegir las herramientas adecuadas para migrar a Azure Database for MySQL.
ms.service: mysql
author: shriram-muthukrishnan
ms.author: shriramm
ms.reviewer: maghan
ms.topic: how-to
ms.custom: ''
ms.date: 10/12/2021
ms.openlocfilehash: 686874492f44df1171a320aaacf8e1967f8cb060
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/22/2021
ms.locfileid: "130269928"
---
# <a name="select-the-right-tools-for-migration-to-azure-database-for-mysql"></a>Selección de las herramientas adecuadas para la migración a Azure Database for MySQL

[!INCLUDE[applies-to-mysql-single-flexible-server](includes/applies-to-mysql-single-flexible-server.md)]

## <a name="overview"></a>Información general

Las migraciones son proyectos de varios pasos que son difíciles de llevar a cabo. La migración de servidores de bases de datos entre plataformas implica más que la migración de datos y esquemas. También se van a migrar varios otros componentes, como parámetros de configuración del servidor, redes, reglas de control de acceso, etc. Estos componentes son necesarios para garantizar que la funcionalidad del servidor de bases de datos en la plataforma de destino nueva imita el origen. 

Para información detallada y casos de uso sobre la migración de bases de datos a Azure Database for MySQL, puede consultar la [Guía de migración de bases de datos](migrate/mysql-on-premises-azure-db/01-mysql-migration-guide-intro.md). En este documento encontrará punteros que lo ayudarán a planear y ejecutar correctamente una migración de MySQL a Azure. 

En general, las migraciones se pueden clasificar como sin conexión o en línea. 

- Con una migración sin conexión, el servidor de origen se desconecta, se realiza un volcado y una restauración de las bases de datos en el servidor de destino. 

- Con una migración en línea (migración con un tiempo de inactividad mínimo), el servidor de origen permite realizar actualizaciones y la solución de migración se encargará de replicar los cambios en curso entre el servidor de origen y el servidor de destino junto con el volcado inicial y la restauración en el destino. 

Si la aplicación puede permitirse cierto tiempo de inactividad, las migraciones sin conexión siempre son la opción preferida, ya que son sencillas y fáciles de ejecutar. Sin embargo, si la aplicación solo puede permitirse un tiempo de inactividad mínimo, la mejor opción será una migración en línea. Las migraciones de la mayoría de los sistemas OLTP, como el procesamiento de pagos y el comercio electrónico, se incluyen en esta categoría. 

## <a name="decision-table"></a>Tabla de decisiones

A fin de ayudarlo a seleccionar las herramientas adecuadas para migrar a Azure Database for MySQL, tenga en cuenta los detalles que se muestran en la tabla siguiente. 

| Escenarios | Herramientas recomendadas | Vínculos |
|-------|------|------------|
| Migraciones sin conexión para migrar bases de datos de 1 TB o más | Volcado y restauración mediante **MyDumper/MyLoader** + máquina virtual de proceso elevado | [Migración de bases de datos grandes a Azure Database for MySQL mediante mydumper/myloader](concepts-migrate-mydumper-myloader.md) <br><br> [Procedimientos recomendados para migrar bases de datos de gran tamaño a Azure Database for MySQL](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/best-practices-for-migrating-large-databases-to-azure-database/ba-p/1362699)|
| Migraciones sin conexión para migrar bases de datos de menos de 1 TB  | Si el ancho de banda de la red entre el origen y el destino es bueno (es decir, una ruta expresa de alta velocidad), utilice **Azure DMS** (servicio de migración de base de datos)  <br><br>   **O** <br><br> Si tiene un ancho de banda de red bajo entre el origen y Azure, utilice **Mydumper/Myloader + máquina virtual de proceso elevado** a fin de aprovechar la configuración de compresión para migrar de manera eficaz los datos en redes de baja velocidad  <br><br> **O** <br><br> Use la utilidad **mysqldump** y **exportación o importación de MySQL Workbench** para realizar migraciones sin conexión de bases de datos más pequeñas.  | [Tutorial: Migración de MySQL a Azure Database for MySQL sin conexión mediante DMS - Azure Database Migration Service](../dms/tutorial-mysql-azure-mysql-offline-portal.md)<br><br>  [Migración de Amazon RDS para MySQL a Azure Database for MySQL mediante MySQL Workbench](how-to-migrate-rds-mysql-workbench.md)<br><br>  [Importación y exportación: Azure Database for MySQL](concepts-migrate-import-export.md)|
| Migración en línea |  **Mydumper/Myloader con replicación de datos de entrada** <br><br> **Mysqldump con replicación de datos de entrada** se puede considerar para bases de datos pequeñas (menos de 100 GB).  Estos métodos se pueden aplicar tanto a migraciones externas como dentro de la plataforma. | [Configuración de la replicación de datos de entrada: Servidor flexible de Azure Database for MySQL](flexible-server/how-to-data-in-replication.md) <br><br> [Tutorial: Migración de Azure Database for MySQL con servidor único a Azure Database for MySQL con servidor flexible con un tiempo de inactividad mínimo](howto-migrate-single-flexible-minimum-downtime.md) |
|Migraciones de servidor único a servidor flexible |  **Sin conexión**: script de shell personalizado en [GitHub](https://github.com/Azure/azure-mysql/tree/master/azuremysqltomysqlmigrate) Este script también mueve otros componentes del servidor, como la configuración de seguridad y las configuraciones de parámetros del servidor. <br><br>**En línea**: **Mydumper/Myloader con replicación de datos de entrada** |  [Migración de Azure Database for MySQL: servidor único a servidor flexible en cinco sencillos pasos](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/migrate-from-azure-database-for-mysql-single-server-to-flexible/ba-p/2674057)<br><br>   [Tutorial: Migración de Azure Database for MySQL con servidor único a Azure Database for MySQL con servidor flexible con un tiempo de inactividad mínimo](howto-migrate-single-flexible-minimum-downtime.md)| 

## <a name="next-steps"></a>Pasos siguientes
* [Migración de MySQL en el entorno local a Azure Database for MySQL](migrate/mysql-on-premises-azure-db/01-mysql-migration-guide-intro.md)

<br><br>
