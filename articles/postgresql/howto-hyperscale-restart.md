---
title: 'Reinicio del servidor: Hiperescala (Citus) en Azure Database for PostgreSQL'
description: Reinicio de la base de datos en Azure Database for PostgreSQL para Hiperescala (Citus)
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 7/9/2021
ms.openlocfilehash: 0de8108cde0b5017221bf3ab038d98c98bb94546
ms.sourcegitcommit: 2cff2a795ff39f7f0f427b5412869c65ca3d8515
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/10/2021
ms.locfileid: "113598794"
---
# <a name="restart-azure-database-for-postgresql---hyperscale-citus"></a>Reinicio de Azure Database for PostgreSQL para Hiperescala (Citus)

Si desea reiniciar el grupo de servidores de Hiperescala (Citus), puede hacerlo desde la página **Información general** del grupo en Azure Portal. Seleccione el botón **Reiniciar** en la barra superior. Aparecerá un diálogo de confirmación. Seleccione **Reiniciar todo** para continuar.

> [!NOTE]
> Si el botón Reiniciar aún no aparece para el grupo de servidores, abra una solicitud de soporte técnico de Azure para reiniciar ese grupo.

El reinicio del grupo de servidores se aplica a todos los nodos; no se pueden reiniciar nodos individuales de forma selectiva. El reinicio se aplica a todas las máquinas virtuales de los nodos, no solo a las instancias de servidor de PostgreSQL. Cualquier aplicación que intente usar la base de datos experimentará un tiempo de inactividad de conectividad mientras se produce el reinicio.

**Pasos siguientes**

- El cambio de algunos parámetros del servidor requiere un reinicio. Consulte la lista de [todos los parámetros de servidor](reference-hyperscale-parameters.md) que se pueden configurar en Hiperescala (Citus).
