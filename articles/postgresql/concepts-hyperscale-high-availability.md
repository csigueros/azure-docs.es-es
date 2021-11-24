---
title: 'Alta disponibilidad en Azure Database for PostgreSQL: Hiperescala (Citus)'
description: Conceptos de alta disponibilidad y recuperación ante desastres
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 11/15/2021
ms.openlocfilehash: d708bb832673d424b1737ad0bea00716f1f3f278
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/16/2021
ms.locfileid: "132524861"
---
# <a name="high-availability-in-azure-database-for-postgresql--hyperscale-citus"></a>Alta disponibilidad en Azure Database for PostgreSQL: Hiperescala (Citus)

La alta disponibilidad (HA) evita el tiempo de inactividad de la base de datos al mantener réplicas en espera de todos los nodos de un grupo de servidores. Si un nodo deja de funcionar, Hiperescala (Citus) cambia las conexiones entrantes del nodo con errores a su estado en espera. La conmutación por error se produce en pocos minutos y los nodos promocionados siempre tienen datos actualizados a través de la replicación de transmisión sincrónica por secuencias de PostgreSQL.

Incluso sin la alta disponibilidad habilitada, cada nodo de Hiperescala (Citus) tiene su propio almacenamiento con redundancia local (LRS), con tres réplicas sincrónicas que mantiene el servicio Azure Storage.  Si se produce un error en una sola réplica, se detecta mediante dicho servicio y se vuelve a crear de forma transparente. Para obtener información sobre la durabilidad del almacenamiento LRS, consulte las métricas de [esta página](../storage/common/storage-redundancy.md#summary-of-redundancy-options).

Cuando la alta disponibilidad *está* habilitada, Hiperescala (Citus) ejecuta un solo nodo en espera por cada nodo principal del grupo de servidores. Los nodos principal y en espera usan la replicación sincrónica de PostgreSQL. Con esta replicación los clientes pueden tener un tiempo de inactividad predecible si se produce un error en un nodo principal. En pocas palabras, nuestro servicio detecta un error en los nodos principales y conmuta a los nodos en espera sin pérdida de datos.

Para aprovechar la alta disponibilidad en el nodo coordinador, las aplicaciones de base de datos deben detectar y reintentar las conexiones quitadas y las transacciones con errores. Se podrá acceder al coordinador recién promocionado con la misma cadena de conexión.

La recuperación puede dividirse en tres fases: detección, conmutación por error y recuperación completa.  Hiperescala (Citus) realiza comprobaciones periódicas de mantenimiento en todos los nodos y después de cuatro comprobaciones con errores, determina que un nodo está fuera de servicio. Después, Hiperescala (Citus) promueve un estado de espera a un nodo principal (conmutación por error) y aprovisiona un nuevo modo de espera.
La replicación de transmisión por secuencias comienza y el nuevo nodo se actualiza.  Una vez que se han replicado todos los datos, el nodo ha alcanzado la recuperación completa.

### <a name="next-steps"></a>Pasos siguientes

- Obtenga información sobre cómo [habilitar la alta disponibilidad](howto-hyperscale-high-availability.md) en un grupo de servidores de Hiperescala (Citus).
