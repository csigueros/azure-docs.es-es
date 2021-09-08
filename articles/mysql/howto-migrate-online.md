---
title: 'Migración con tiempo de inactividad mínimo: Azure Database for MySQL'
description: En este artículo se describe cómo realizar una migración con tiempo de inactividad mínimo de una base de datos de MySQL a Azure Database for MySQL.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.custom: ''
ms.date: 6/19/2021
ms.openlocfilehash: 728c1d76539eb34efe5e5fc475837810ea5a5111
ms.sourcegitcommit: 8b38eff08c8743a095635a1765c9c44358340aa8
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/30/2021
ms.locfileid: "113088591"
---
# <a name="minimal-downtime-migration-to-azure-database-for-mysql"></a>Migración con tiempo de inactividad mínimo a Azure Database for MySQL

[!INCLUDE[applies-to-mysql-single-flexible-server](includes/applies-to-mysql-single-flexible-server.md)]

Puede realizar migraciones de MySQL a Azure Database for MySQL con un tiempo de inactividad mínimo mediante la replicación de datos de entrada, que limita el tiempo de inactividad de la aplicación.

También puede consultar la [Guía de migración de bases de datos](https://github.com/Azure/azure-mysql/tree/master/MigrationGuide), donde encontrará información detallada y casos de uso sobre la migración de bases de datos a Azure Database for MySQL. En esta guía se proporcionan instrucciones que conducen al planeamiento y la ejecución correctos de una migración de MySQL a Azure.

## <a name="overview"></a>Información general

Con la replicación de datos de entrada, puede configurar el origen como principal y el destino como réplica, de modo que haya una sincronización continua de las nuevas transacciones con Azure mientras la aplicación sigue ejecutándose. Una vez que los datos se ponen al día en el destino de Azure, puede detener la aplicación por un breve período de tiempo (tiempo de inactividad mínimo), esperar al último lote de datos (desde el momento en que detiene la aplicación hasta el momento en que la aplicación deja de estar disponible efectivamente para aceptar cualquier tráfico nuevo) para ponerse al día en el destino y, a continuación, actualizar la cadena de conexión para que apunte a Azure. Cuando haya terminado, la aplicación estará funcionando en Azure.

## <a name="next-steps"></a>Pasos siguientes

- Para más información sobre cómo migrar bases de datos a Azure Database for MySQL, consulte la [Guía de migración de base de datos](https://github.com/Azure/azure-mysql/tree/master/MigrationGuide).
