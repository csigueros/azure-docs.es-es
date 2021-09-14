---
title: 'Agrupación de conexiones: Citus (Azure Database for PostgreSQL)'
description: Escalado de conexiones de base de datos cliente
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 08/03/2021
ms.openlocfilehash: 8331cd2f0fa0df52f550acfdac1d8d3506e415f2
ms.sourcegitcommit: 851b75d0936bc7c2f8ada72834cb2d15779aeb69
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/31/2021
ms.locfileid: "123318618"
---
# <a name="azure-database-for-postgresql--hyperscale-citus-connection-pooling"></a>Azure Database for PostgreSQL: agrupación de conexiones de Hiperescala (Citus)

El establecimiento de las nuevas conexiones lleva su tiempo. Esto funciona con la mayoría de las aplicaciones, que solicitan muchas conexiones de corta duración. Se recomienda usar un agrupador de conexiones para reducir las transacciones inactivas y reutilizar las conexiones existentes. Para más información, visite nuestra [entrada de blog](https://techcommunity.microsoft.com/t5/azure-database-for-postgresql/not-all-postgres-connection-pooling-is-equal/ba-p/825717).

Puede ejecutar su propio concentrador de conexión o usar PgBouncer administrado por Azure.

## <a name="managed-pgbouncer"></a>PgBouncer administrado

Los concentradores de conexión, como PgBouncer, permiten que más clientes se conecten al nodo de coordinación a la vez. Las aplicaciones se conectan al concentrador, que retransmite comandos a la base de datos de destino.

Cuando los clientes se conectan a través de PgBouncer, el número de conexiones que se pueden ejecutar activamente en la base de datos no cambia. En su lugar, PgBouncer pone en cola el exceso de conexiones y las ejecuta cuando la base de datos está lista.

Hiperescala (Citus) ahora ofrece una instancia administrada de PgBouncer para grupos de servidores. Admite hasta 2000 conexiones de cliente simultáneas.  Para conectarse a través de PgBouncer, siga estos pasos:

1. Vaya a la página **Cadenas de conexión** del grupo de servidores en Azure Portal.
2. Active la casilla **Cadenas de conexión de PgBouncer**. (Las cadenas de conexión enumeradas cambiarán).

   > [!IMPORTANT]
   >
   > Si la casilla no existe, PgBouncer aún no está habilitado para el grupo de servidores. PgBouncer administrado se está implantando en todas las [regiones admitidas](concepts-hyperscale-configuration-options.md#regions).  Cuando se haya habilitado en una región, se añadirá a los grupos de servidores existentes en la región durante un evento de [mantenimiento programado](concepts-hyperscale-maintenance.md).

3. Actualice las aplicaciones cliente para conectarse con la nueva cadena.

## <a name="next-steps"></a>Pasos siguientes

Obtenga más información sobre los [límites y limitaciones](concepts-hyperscale-limits.md) de Hiperescala (Citus).
