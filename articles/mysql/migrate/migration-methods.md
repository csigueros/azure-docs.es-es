---
title: Métodos de migración de la guía para la migración de MySQL en el entorno local a Azure Database for MySQL
description: La obtención de datos del origen al destino requerirá el uso de herramientas o características de MySQL para realizar la migración.
ms.service: mysql
ms.subservice: migration-guide
ms.topic: how-to
author: arunkumarthiags
ms.author: arthiaga
ms.reviewer: maghan
ms.custom: ''
ms.date: 05/26/2021
ms.openlocfilehash: 98db2aacd0b49406c3f70d0da89dfb6e991112a7
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/10/2021
ms.locfileid: "111962513"
---
# <a name="mysql-on-premises-to-azure-database-for-mysql-migration-guide-migration-methods"></a>Métodos de migración de la guía para la migración de MySQL en el entorno local a Azure Database for MySQL

La obtención de datos del origen al destino requiere el uso de herramientas o características de MySQL para realizar la migración.

Por ello, es importante completar todas las fases de evaluación y planeamiento antes de iniciar las fases siguientes. Las decisiones y los datos recopilados son la ruta de migración y las dependencias de selección de herramientas.

En esta sección se exploran las siguientes herramientas de uso frecuente:

  - MySQL Workbench

  - mysqldump

  - mydumper y myloader

  - Datos en la replicación (binlog)

### <a name="mysql-workbench"></a>MySQL Workbench

[MySQL Workbench](https://www.mysql.com/products/workbench/) proporciona una experiencia de GUI enriquecida que permite a los desarrolladores y administradores diseñar, desarrollar y administrar sus instancias de MySQL.

La versión más reciente de MySQL Workbench proporciona [funcionalidades sofisticadas de migración de objetos](https://www.mysql.com/products/workbench/migrate/) al mover una base de datos de un origen a un destino.

#### <a name="data-import-and-export"></a>Importación y exportación de datos

MySQL Workbench proporciona una interfaz de usuario basada en un asistente que le permitirá realizar una exportación completa o parcial e importar tablas y objetos de la base de datos. Para obtener un ejemplo de cómo usar MySQL Workbench, consulte [Migración de la base de datos de MySQL mediante importación y exportación](../concepts-migrate-import-export.md).

### <a name="dump-and-restore-mysqldump"></a>Volcado y restauración (mysqldump)

`mysqldump` se proporciona normalmente como parte de la instalación de MySQL. Se trata de una [utilidad de cliente](https://dev.mysql.com/doc/refman/5.7/en/mysqldump.html) que se puede ejecutar para crear copias de seguridad lógicas que equivalen a un conjunto de instrucciones SQL que se pueden reproducir para volver a generar la base de datos a un momento dado. `mysqldump` no está pensado como una solución rápida o escalable para realizar una copia de seguridad o migrar grandes cantidades de datos. La ejecución de un gran conjunto de instrucciones de inserción de SQL puede tener un rendimiento deficiente debido a la E/S de disco necesaria para actualizar los índices. Sin embargo, cuando se combina con otras herramientas que requieren el esquema original, `mysqldump` es una excelente herramienta para generar los esquemas de base de datos y de tabla. Los esquemas pueden crear el entorno de la zona de aterrizaje de destino.

La `mysqldump` utilidad proporciona características útiles durante la fase de migración de datos. Recuerde que es necesario evaluar las consideraciones de rendimiento antes de ejecutar la utilidad. Consulte [Consideraciones sobre el rendimiento](../concepts-migrate-dump-restore.md#performance-considerations).

### <a name="mydumper-and-myloader"></a>mydumper y myloader

Los entornos con bases de datos grandes que requieren una migración rápida deben usar [mydumper y myloader](https://github.com/maxbube/mydumper). Estas herramientas se escriben en C++ y usan técnicas multiproceso para enviar los datos lo más rápido posible a la instancia de MySQL de destino. `mydumper` y `myloader` aprovechan este paralelismo y pueden acelerar la migración unas 10 veces o más.

Asimismo, las versiones binarias de las herramientas disponibles para la descarga pública se han compilado para Linux. Para ejecutar estas herramientas en Windows, es necesario volver a compilar los proyectos de código abierto. Aún así, compilar código fuente y crear versiones no es una tarea sencilla para la mayoría de los usuarios.

### <a name="data-in-replication-binlog"></a>Datos en la replicación (binlog)

Igual que sucede con otros sistemas de administración de bases de datos, MySQL proporciona una característica de replicación de registros denominada [replicación binlog](https://dev.mysql.com/doc/refman/5.7/en/binlog-replication-configuration-overview.html). La característica de replicación `binlog` le ayudará a realizar la migración de datos y la creación de réplicas de lectura.

Use la replicación binlog para [migrar los datos a Azure Database for MySQL](../concepts-data-in-replication.md) en un escenario en línea. La replicación de datos ayuda a reducir el tiempo de inactividad necesario para realizar los cambios de datos finales de destino.

Para usar la característica de replicación `binlog`, hay algunos [requisitos de configuración](../howto-data-in-replication.md#link-source-and-replica-servers-to-start-data-in-replication):

  - A continuación, se recomienda usar el servidor principal para usar el motor InnoDB de MySQL. Si usa un motor de almacenamiento distinto de InnoDB, debe migrar esas tablas a InnoDB.

  - El usuario de la migración debe tener permisos para configurar el registro binario y crear nuevos usuarios en el servidor principal.

  - Si el servidor principal tiene SSL habilitado, asegúrese de que el certificado de la entidad de certificación SSL proporcionado para el dominio se ha incluido en el procedimiento almacenado "mysql.az\_replication\_change\_master". Consulte los [ejemplos](../howto-data-in-replication.md#link-source-and-replica-servers-to-start-data-in-replication) siguientes y el parámetro "master\_ssl\_ca".

  - Asegúrese de que la dirección IP del servidor principal se ha agregado a las reglas de firewall del servidor de réplica de Azure Database for MySQL. Actualice las reglas de firewall mediante Azure Portal o la CLI de Azure.

  - Asegúrese de que el equipo que hospeda el servidor maestro permite el tráfico entrante y saliente en el puerto 3306.

  - Asegúrese de que el servidor principal tiene una dirección IP accesible (pública o privada) desde el origen hasta los destinos.

Para realizar una migración mediante la replicación, consulte [Configuración de la replicación de datos de entrada en Azure Database for MySQL](../howto-data-in-replication.md#link-source-and-replica-servers-to-start-data-in-replication) para obtener más información.

El método de replicación `binlog` tiene requisitos elevados de CPU y almacenamiento adicional. Igualmente, los usuarios de la migración deben probar la carga colocada en el sistema de origen durante las migraciones en línea y determinar si es aceptable.

### <a name="azure-database-migration-service-dms"></a>Azure Database Migration Service (DMS)

[Azure Database Migration Services (DMS)](https://azure.microsoft.com/en-us/services/database-migration/) es una herramienta basada en la nube de Azure que permite a los administradores realizar un seguimiento de las distintas configuraciones de migración y reutilizarlas si es necesario. DMS funciona mediante la creación de proyectos de migración con configuraciones que apuntan a varios orígenes y destinos. Además admite [migraciones sin conexión](../../dms/tutorial-mysql-azure-mysql-offline-portal.md). Igualmente, admite cargas de trabajo de datos locales y cargas de trabajo basadas en la nube, como MySQL de Amazon Relational Database Service (RDS).

Aunque el servicio DMS es una herramienta en línea, se basa en la característica de replicación de `binlog` de MySQL para completar sus tareas. Actualmente, DMS automatiza parcialmente el proceso de migración sin conexión. Asimismo, DMS requiere la generación y aplicación del esquema correspondiente en la instancia de destino de Azure Database for MySQL. Los esquemas se pueden exportar mediante la utilidad de cliente `mysqldump`.

### <a name="fastestminimum-downtime-migration"></a>Migración más rápida o con un tiempo de inactividad mínimo

Hay varias opciones para migrar los datos. Decidir qué opción usar es una función del conjunto de aptitudes del equipo de migración, así como decidir la cantidad de tiempo de inactividad que los propietarios de la base de datos y de la aplicación están dispuestos a aceptar. Algunas herramientas admiten enfoques de migración de datos paralelos multiproceso, mientras que otras se diseñaron para realizar migraciones sencillas únicamente con datos de tablas.

La opción más rápida y completa es usar la replicación `binlog` (ya sea directamente con MySQL o a través de DMS), pero incluye los costos que supone agregar claves principales.

### <a name="decision-table"></a>Tabla de decisiones

Hay muchas opciones que WWI puede usar para migrar sus cargas de trabajo de MySQL. Hemos proporcionado una tabla con las posibles opciones, así como las ventajas y desventajas de cada una de ellas:

| Objetivos                           | Descripción                                | Herramienta                             | Prerrequisitos        | Ventajas          | Inconvenientes                                   |
|-------------------------------------|--------------------------------------------|----------------------------------|----------------------|---------------------|-------------------------------------------------|
| **Migración más rápida posible**          | Enfoque paralelo                          | mydumper y myloader            | Linux                | Altamente paralelizado | Limitación de destino                               |
| **Migración en línea**                    | Mantiene la sesión activa el máximo tiempo posible | binlog                           | Ninguno                 | Conexión directa            | Almacenamiento y procesamiento extra                    |
| **Migración sin conexión**                    | Mantiene la sesión activa el máximo tiempo posible | Database Migration Service (DMS) | Ninguno                 | Proceso repetible  | Limitado solo a datos; admite todas las versiones de MySQL |
| **Migración sin conexión altamente personalizada** | Exportación selectiva de objetos                 | mysqldump                        | Ninguno                 | Altamente personalizable | Manual                                          |
| **Migración sin conexión semiautomática**    | Exportación e importación basadas en la interfaz de usuario                 | MySQL Workbench                  | Descarga e instalación | Semiautomática      | Solo se admiten conjuntos comunes de modificadores      |

### <a name="wwi-scenario"></a>Escenario de WWI

WWI ha seleccionado su base de datos de conferencias como su primera carga de trabajo de migración. La carga de trabajo se eligió porque tenía menos riesgo y un tiempo de inactividad más disponible debido a la brecha en la programación anual de la conferencia. Además, en función de la evaluación del equipo de migración, determinaron que se intentó realizar una migración sin conexión mediante MySQL Workbench.

### <a name="migration-methods-checklist"></a>Lista de comprobación de métodos de migración

  - Asegúrese de que el método correcto está seleccionado en función de los entornos de destino y de origen.

  - Asegúrese de que el método puede cumplir los requisitos empresariales.

  - Igualmente, compruebe siempre si la carga de trabajo de datos admite el método en cuestión.  


> [!div class="nextstepaction"]
> [Test Plans](./test-plans.md)