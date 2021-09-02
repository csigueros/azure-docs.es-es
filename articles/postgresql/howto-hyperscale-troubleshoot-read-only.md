---
title: Solución de problemas de acceso de solo lectura - Hiperescala (Citus) - Azure Database for PostgreSQL
description: Obtenga información sobre por qué el grupo de servidores de Hiperescala (Citus) puede volverse de solo lectura y qué hacer.
keywords: conexión postgresql, solo lectura
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 08/03/2021
ms.openlocfilehash: 48ea5fbeacf1769f73b9d505e6cc8fb55e8c3a44
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121741994"
---
# <a name="troubleshoot-read-only-access-to-azure-database-for-postgresql---hyperscale-citus"></a>Solución de problemas de acceso de solo lectura en Azure Database for PostgreSQL - Hiperescala (Citus)

PostgreSQL no se puede ejecutar en una máquina sin espacio libre en disco. Para mantener el acceso a los servidores de PostgreSQL, es necesario evitar que se agote el espacio en disco.

En Hiperescala (Citus), los nodos se establecen en un estado de solo lectura (RO) cuando el disco está casi lleno. Impedir las escrituras evita que el disco se siga llenando y mantiene el nodo disponible para las lecturas. Durante el estado de solo lectura, puede adoptar medidas para liberar más espacio en disco.

En concreto, un nodo de Hiperescala (Citus) se vuelve de solo lectura cuando quedan menos de 5 GiB de almacenamiento libre. Si el servidor se vuelve de solo lectura, todas las sesiones existentes se desconectan, y las transacciones no confirmadas se revierten. Las operaciones de escritura y las confirmaciones de transacciones producirán un error, mientras que las consultas de lectura seguirán funcionando.

## <a name="ways-to-recover-write-access"></a>Maneras de recuperar el acceso de escritura

### <a name="on-the-coordinator-node"></a>En el nodo de coordinación

* [Aumente el tamaño de almacenamiento](howto-hyperscale-scale-grow.md#increase-storage-on-nodes) en el nodo de coordinación.
* Distribuya las tablas locales entre nodos de trabajo o quite datos. Tendrá que ejecutar `SET SESSION CHARACTERISTICS AS TRANSACTION READ WRITE` una vez que se haya conectado a la base de datos y antes de ejecutar otros comandos.

### <a name="on-a-worker-node"></a>En un nodo de trabajo

* [Aumente el tamaño de almacenamiento](howto-hyperscale-scale-grow.md#increase-storage-on-nodes) en el nodo de trabajo.
* [Reequilibre los datos](howto-hyperscale-scale-rebalance.md) en otros nodos o quite algunos datos.
    * Tendrá que establecer temporalmente el nodo de trabajo como de lectura y escritura. Puede conectarse directamente a los nodos de trabajo y usar `SET SESSION CHARACTERISTICS` como se describió anteriormente para el nodo de coordinación.

## <a name="prevention"></a>Prevención

Se recomienda configurar una alerta que le envíe una notificación cada vez que el almacenamiento en el servidor se acerque al umbral. De este modo, puede actuar pronto para evitar entrar en el estado de solo lectura. Para más información, consulte la documentación sobre las [alertas recomendadas](howto-hyperscale-alert-on-metric.md#suggested-alerts).

## <a name="next-steps"></a>Pasos siguientes

* [Configure alertas de Azure](howto-hyperscale-alert-on-metric.md#suggested-alerts) para recibir un previo aviso para que pueda tomar medidas antes de alcanzar el estado de solo lectura.
* Obtenga información sobre el [uso del disco](https://www.postgresql.org/docs/current/diskusage.html) en la documentación de PostgreSQL.
* Obtenga información sobre las [características de la sesión](https://www.postgresql.org/docs/13/sql-set-transaction.html) en la documentación de PostgreSQL.
