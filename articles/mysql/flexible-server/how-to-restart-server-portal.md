---
title: Reinicio de un servidor flexible de Azure Database for MySQL con Azure Portal
description: En este artículo se describe cómo reiniciar un servidor flexible de Azure Database for MySQL mediante Azure Portal.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 10/26/2020
ms.openlocfilehash: 35c9650d7e476fd0e7c498d2c3db7102a8801ff1
ms.sourcegitcommit: 8b38eff08c8743a095635a1765c9c44358340aa8
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/30/2021
ms.locfileid: "122653063"
---
# <a name="restart-azure-database-for-mysql-flexible-server-using-azure-portal"></a>Reinicio de un servidor flexible de Azure Database for MySQL mediante Azure Portal

[[!INCLUDE[applies-to-mysql-flexible-server](../includes/applies-to-mysql-flexible-server.md)]

En este tema se describe cómo reiniciar un servidor flexible de Azure Database for MySQL. Es posible que deba reiniciar el servidor por motivos de mantenimiento, lo que causa una breve interrupción del servicio mientras el servidor realiza la operación.

Si el servicio está ocupado, se bloqueará el reinicio del servidor. Por ejemplo, el servicio puede estar procesando una operación solicitada anteriormente, como el escalado de núcleos virtuales.

El tiempo necesario para completar un reinicio depende el proceso de recuperación de MySQL. Para reducir el tiempo de reinicio, se recomienda que minimice la cantidad de actividades que se ejecutan en el servidor antes del reinicio.

## <a name="prerequisites"></a>Requisitos previos

Para completar esta guía, necesita:
- Un [servidor flexible de Azure Database for MySQL](quickstart-create-server-portal.md)

## <a name="perform-server-restart"></a>Realización del reinicio del servidor

Los pasos siguientes reinician el servidor de MySQL:

1. En Azure Portal, seleccione el servidor flexible de Azure Database for MySQL.

2. En la barra de herramientas de la página de **información general** del servidor, haga clic en **Restart** (reiniciar).

   :::image type="content" source="./media/how-to-restart-server-portal/2-server.png" alt-text="Azure Database for MySQL - Información general - Botón Reiniciar":::

3. Haga clic en **Sí** para confirmar el reinicio del servidor.

   :::image type="content" source="./media/how-to-restart-server-portal/3-restart-confirm.png" alt-text="Azure Database for MySQL - Confirmación del reinicio":::

4. Observe que el estado del servidor cambia a "Reiniciando".

   :::image type="content" source="./media/how-to-restart-server-portal/4-restarting-status.png" alt-text="Azure Database for MySQL - Estado del reinicio":::

5. Confirme que el reinicio del servidor es correcto.

   :::image type="content" source="./media/how-to-restart-server-portal/5-restart-success.png" alt-text="Azure Database for MySQL - Reinicio correcto":::

## <a name="next-steps"></a>Pasos siguientes

[Inicio rápido: creación de un servidor flexible de Azure Database for MySQL mediante Azure Portal](quickstart-create-server-portal.md)
