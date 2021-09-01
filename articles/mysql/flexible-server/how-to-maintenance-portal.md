---
title: Mantenimiento programado de un servidor flexible de Azure Database for MySQL (versión preliminar) mediante Azure Portal
description: Aprenda a configurar las opciones de mantenimiento programado para un servidor flexible de Azure Database for MySQL mediante Azure Portal.
author: niklarin
ms.author: nlarin
ms.service: mysql
ms.topic: how-to
ms.date: 9/21/2020
ms.openlocfilehash: a4ff27243643a5c0fbb833a05a07e9e117196a6f
ms.sourcegitcommit: 8b38eff08c8743a095635a1765c9c44358340aa8
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/30/2021
ms.locfileid: "122652988"
---
# <a name="manage-scheduled-maintenance-settings-for-azure-database-for-mysql--flexible-server"></a>Administración de la configuración del mantenimiento programado para un servidor flexible de Azure Database for MySQL

[[!INCLUDE[applies-to-mysql-flexible-server](../includes/applies-to-mysql-flexible-server.md)]

En la suscripción de Azure puede especificar opciones de mantenimiento para cada servidor flexible. Las opciones incluyen la programación del mantenimiento y la configuración de las notificaciones para eventos de mantenimiento próximos y finalizados.

> [!IMPORTANT]
> La opción de implementación Servidor flexible de Azure Database for MySQL está en versión preliminar.

## <a name="prerequisites"></a>Requisitos previos

Para completar esta guía, necesita:

- Un [servidor flexible de Azure Database for MySQL](quickstart-create-server-portal.md).
 
## <a name="specify-maintenance-schedule-options"></a>Especificación de las opciones de programación del mantenimiento
 
1. En la página del servidor MySQL, bajo el título **Configuración**, elija **Mantenimiento** para abrir las opciones de mantenimiento programado.
2. La programación predeterminada (administrada por el sistema) es un día de la semana aleatorio y una franja de 60 minutos para el mantenimiento que se inicia entre las 23:00 y las 7:00 (hora del servidor local). Si quiere personalizar esta programación, elija **Programación personalizada**. Después, puede seleccionar el día de la semana que prefiera y una ventana de 60 minutos para la hora de inicio del mantenimiento.
 
## <a name="notifications-about-scheduled-maintenance-events"></a>Notificaciones sobre eventos de mantenimiento programado
 
Puede usar Azure Service Health para [ver notificaciones](../../service-health/service-notifications.md) sobre el próximo mantenimiento programado y realizado en el servidor flexible. También puede [configurar](../../service-health/resource-health-alert-monitor-guide.md) alertas en Azure Service Health para recibir notificaciones sobre los eventos de mantenimiento.
 
## <a name="next-steps"></a>Pasos siguientes  
 
* Información sobre el [mantenimiento programado de un servidor flexible de Azure Database for MySQL](concepts-maintenance.md).
* Información sobre [Azure Service Health](../../service-health/overview.md).
