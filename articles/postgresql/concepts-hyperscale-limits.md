---
title: 'Límites y limitaciones: Hiperescala (Citus) para Azure Database for PostgreSQL'
description: Límites actuales de los grupos de servidores de Hiperescala (Citus)
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 08/03/2021
ms.openlocfilehash: 5bf02173d105ab81807bdc4ee68e3b8f9bc8e0a4
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121746319"
---
# <a name="azure-database-for-postgresql--hyperscale-citus-limits-and-limitations"></a>Azure Database for PostgreSQL: límites y limitaciones de Hiperescala (Citus)

En la siguiente sección se describen los límites de capacidad y funcionales en el servicio de Hiperescala (Citus).

## <a name="maximum-connections"></a>Número máximo de conexiones

Cada conexión de PostgreSQL (incluso inactiva) usa al menos 10 MB de memoria, por lo que es importante limitar las conexiones simultáneas. Estos son los límites que elegimos para mantener los nodos en buen estado:

* Nodo de coordinación
   * Núm. máximo de conexiones
       * 300 para una cantidad de 0 a 3 núcleos virtuales
       * 500 para una cantidad de 4 a 15 núcleos virtuales
       * 1000 para una cantidad superior a 16 núcleos virtuales
   * Número máximo de conexiones de usuario
       * 297 para 0-3 núcleos virtuales
       * 497 para 4-15 núcleos virtuales
       * 997 para más de 16 núcleos virtuales
* Nodo de trabajo
   * Núm. máximo de conexiones
       * 600

Se producirá un error al intentar conectarse más allá de estos límites. El sistema reserva tres conexiones para supervisar los nodos, por lo que hay tres menos conexiones disponibles para las consultas de usuario que el total de conexiones.

### <a name="connection-pooling"></a>Agrupación de conexiones

Puede modificar todavía más la escala de las conexiones mediante la [agrupación de conexiones](concepts-hyperscale-connection-pool.md). Hiperescala (Citus) ofrece una agrupación de conexiones pgBouncer administrada configurada para un máximo de 2000 conexiones cliente simultáneas.

## <a name="storage-scaling"></a>Escalado de almacenamiento

El almacenamiento en el coordinador y los nodos de trabajo se pueden escalar verticalmente (aumentar), pero no se puede reducir verticalmente (disminuir).

## <a name="storage-size"></a>Tamaño de almacenamiento

Se admite hasta 2 TiB de almacenamiento en los nodos de coordinador y de trabajo. Consulte las opciones de almacenamiento disponibles y el cálculo de IOPS [anterior](concepts-hyperscale-configuration-options.md#compute-and-storage) para los tamaños de nodo y clúster.

## <a name="database-creation"></a>Creación de base de datos

Azure Portal proporciona credenciales para conectarse exactamente a una base de datos por grupo de servidores de Hiperescala (Citus), la base de datos de `citus`. Actualmente no se permite crear otra base de datos, y el comando CREATE DATABASE producirá un error.

## <a name="columnar-storage"></a>Almacenamiento en columnas

Actualmente, Hiperescala (Citus) tiene estas limitaciones con las [tablas en columnas](concepts-hyperscale-columnar.md):

* La compresión está en disco, no en memoria.
* Solo anexar (sin compatibilidad con UPDATE/DELETE).
* No hay recuperación de espacio (por ejemplo, las transacciones de reversión pueden seguir consumiendo espacio en disco).
* Sin compatibilidad con índices, exámenes de índices o exámenes de índices de mapa de bits.
* Sin tidscans.
* Sin exámenes de ejemplo.
* Sin compatibilidad con TOAST (se admiten valores grandes insertados).
* No se admiten instrucciones ON CONFLICT (excepto las acciones DO NOTHING sin ningún destino especificado).
* No se admiten bloqueos de tupla (SELECT ... FOR SHARE, SELECT ... FOR UPDATE).
* No se admite el nivel de aislamiento serializable.
* Compatibilidad solo con las versiones 12 y posteriores del servidor PostgreSQL.
* No se admiten claves externas, restricciones de unicidad ni restricciones de exclusión.
* No se admite la descodificación lógica.
* No se admiten exámenes paralelos dentro del nodo.
* No se admiten los desencadenadores AFTER ... FOR EACH ROW.
* Sin tablas en columnas UNLOGGED.
* Sin tablas en columnas TEMPORARY.

## <a name="next-steps"></a>Pasos siguientes

* Obtenga información sobre cómo [crear un grupo de servidores Hiperescala (Citus) en el portal](quickstart-create-hyperscale-portal.md).
* Aprenda a habilitar la [agrupación de conexiones](concepts-hyperscale-connection-pool.md).
