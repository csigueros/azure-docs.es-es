---
title: 'Traslado de regiones de Azure en Azure Portal: Azure Database for MySQL'
description: Mueva un servidor de Azure Database for MySQL de una región de Azure a otra mediante una réplica de lectura y Azure Portal.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.custom: subject-moving-resources
ms.date: 06/26/2020
ms.openlocfilehash: 4f7bb09b193da84b4d3f613fdd9c96790976f128
ms.sourcegitcommit: 8b38eff08c8743a095635a1765c9c44358340aa8
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/30/2021
ms.locfileid: "122652823"
---
# <a name="move-an-azure-database-for-mysql-server-to-another-region-by-using-the-azure-portal"></a>Traslado de un servidor de Azure Database for MySQL a otra región mediante Azure Portal

[!INCLUDE[applies-to-mysql-single-server](includes/applies-to-mysql-single-server.md)]

Hay varios escenarios para mover un servidor de Azure Database for MySQL existente de una región a otra. Por ejemplo, es posible que quiera mover un servidor de producción a otra región como parte de la planificación de la recuperación ante desastres.

Puede usar una [réplica de lectura entre regiones](concepts-read-replicas.md#cross-region-replication) de Azure Database for MySQL para completar el traslado a otra región. Para ello, primero cree una réplica de lectura en la región de destino. A continuación, detenga la replicación en el servidor de réplica de lectura para convertirlo en un servidor independiente que acepte el tráfico tanto de lectura como de escritura. 

> [!NOTE]
> Este artículo se centra en el traslado del servidor a otra región. Si desea mover el servidor a otro grupo de recursos o suscripción, consulte el artículo de [traslado](../azure-resource-manager/management/move-resource-group-and-subscription.md). 

## <a name="prerequisites"></a>Requisitos previos

- La característica de réplica de lectura solo está disponible para servidores de Azure Database for MySQL en los planes de tarifa De uso general u Optimizada para memoria. Asegúrese de que el servidor de origen esté en uno de estos planes de tarifa.

- Asegúrese de que el servidor de origen de Azure Database for MySQL se encuentre en la región de Azure desde la que desea moverlo.

## <a name="prepare-to-move"></a>Preparación para la migración

Para crear un servidor de réplica de lectura entre regiones en la región de destino mediante Azure Portal, siga estos pasos:

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com/).
1. Seleccione el servidor de Azure Database for MySQL existente que desea utilizar como servidor de origen. Esta acción abre la página **Información general**.
1. Seleccione **Replicación** en el menú, en **CONFIGURACIÓN**.
1. Seleccione **Agregar réplica**.
1. Escriba un nombre para el servidor de réplica.
1. Seleccione la ubicación del servidor de réplica. La ubicación predeterminada es la misma que la del servidor de origen. Verifique haber seleccionado la ubicación de destino en la que desea implementar la réplica.
1. Seleccione **Aceptar** para confirmar la creación de la réplica. Durante la creación de la réplica, los datos se copian del servidor de origen a la réplica. El tiempo de creación puede tardar varios minutos o más, en proporción con el tamaño del servidor de origen.

>[!NOTE]
> Al crear una réplica, no hereda los puntos de conexión de servicio de red virtual del servidor de origen. Estas reglas se deben configurar de forma independiente para la réplica.

## <a name="move"></a>Move

> [!IMPORTANT]
> Este servidor independiente no puede volver a convertirse en una réplica.
> Asegúrese de que la réplica tenga todos los datos que necesita antes de detener la replicación en una réplica de lectura.

Detener la replicación en el servidor de réplicas hace que se convierta en servidor independiente. Para detener la replicación en la réplica desde Azure Portal, siga estos pasos:

1. Una vez que se haya creado la réplica, busque y seleccione el servidor de origen de Azure Database for MySQL. 
1. Seleccione **Replicación** en el menú, en **CONFIGURACIÓN**.
1. Seleccione el servidor de réplica.
1. Seleccione **Detener replicación**.
1. Para confirmar que desea detener la replicación, haga clic en **Aceptar**.

## <a name="clean-up-source-server"></a>Limpieza del servidor de origen

Puede que desee eliminar el servidor de Azure Database for MySQL de origen. Para ello, siga estos pasos:

1. Una vez que se haya creado la réplica, busque y seleccione el servidor de origen de Azure Database for MySQL.
1. En la ventana **Información general**, seleccione **Eliminar**.
1. Escriba el nombre del servidor de origen para confirmar que desea eliminarlo.
1. Seleccione **Eliminar**.

## <a name="next-steps"></a>Pasos siguientes

En este tutorial, ha movido un servidor de Azure Database for MySQL de una región a otra mediante Azure Portal y, a continuación, ha limpiado los recursos de origen innecesarios. 

- Obtenga más información sobre [las réplicas de lectura](concepts-read-replicas.md)
- Obtenga más información sobre la [administración de réplicas de lectura en Azure Portal](howto-read-replicas-portal.md)
- Más información sobre las opciones de [continuidad del negocio](concepts-business-continuity.md).