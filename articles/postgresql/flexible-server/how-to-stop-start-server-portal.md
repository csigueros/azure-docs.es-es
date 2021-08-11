---
title: 'Inicio y detención de Azure Database for PostgreSQL con Azure Portal: servidor flexible'
description: En este artículo se explica cómo iniciar y detener operaciones en Azure Database for PostgreSQL mediante Azure Portal.
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: a08c9244644228bd6d9154bdc4882fc91334bc62
ms.sourcegitcommit: 7f59e3b79a12395d37d569c250285a15df7a1077
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/02/2021
ms.locfileid: "110797380"
---
# <a name="stopstart-an-azure-database-for-postgresql---flexible-server-preview-using-azure-portal"></a>Inicio y detención de Azure Database for PostgreSQL, servidor flexible (versión preliminar) mediante Azure Portal

> [!IMPORTANT]
> Azure Database for PostgreSQL: servidor flexible está actualmente en versión preliminar.

En este artículo se proporcionan instrucciones paso a paso para iniciar y detener un servidor flexible.

## <a name="pre-requisites"></a>Requisitos previos

Para completar esta guía, necesita:

-   Debe tener Azure Database for PostgreSQL: servidor flexible.

## <a name="stop-a-running-server"></a>Detención de un servidor en ejecución

1.  En [Azure Portal](https://portal.azure.com/), elija el servidor flexible que quiera detener.

2.  En la página **Información general**, haga clic en el botón **Detener** de la barra de herramientas.

> [!NOTE]
> Una vez detenido el servidor, las demás operaciones de administración no estarán disponibles para el servidor flexible.

Tenga en cuenta que los servidores detenidos se volverán a iniciar automáticamente transcurridos siete días. Las actualizaciones de mantenimiento pendientes se aplicarán la próxima vez que el servidor se inicie.

## <a name="start-a-stopped-server"></a>Inicio de un servidor detenido

1.  En [Azure Portal](https://portal.azure.com/), elija el servidor flexible que quiera iniciar.

2.  En la página **Información general**, haga clic en el botón **Iniciar** de la barra de herramientas.

> [!NOTE]
> Una vez iniciado el servidor, todas las operaciones de administración estarán disponibles para el servidor flexible.

## <a name="next-steps"></a>Pasos siguientes

- Obtenga más información sobre las [opciones de proceso y almacenamiento de Azure Database for PostgreSQL: servidor flexible](./concepts-compute-storage.md).
