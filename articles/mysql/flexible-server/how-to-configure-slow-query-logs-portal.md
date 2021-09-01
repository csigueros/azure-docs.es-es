---
title: Configuración de registros de consultas lentas de un servidor flexible de Azure Database for MySQL en Azure Portal
description: En este artículo se describe cómo configurar los registros de consultas lentas en el servidor flexible de Azure Database for MySQL, y acceder a ellos, desde Azure Portal.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 9/21/2020
ms.openlocfilehash: e28e11c8addc83cfe913a28cb91d3bc0d4580a8e
ms.sourcegitcommit: 8b38eff08c8743a095635a1765c9c44358340aa8
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/30/2021
ms.locfileid: "122651849"
---
# <a name="configure-and-access-slow-query-logs-for-azure-database-for-mysql---flexible-server-using-the-azure-portal"></a>Configuración de registros de consultas lentas de un servidor flexible de Azure Database for MySQL y acceso a ellos mediante Azure Portal

[[!INCLUDE[applies-to-mysql-flexible-server](../includes/applies-to-mysql-flexible-server.md)]

> [!IMPORTANT]
> Actualmente, la opción de implementación Servidor flexible de Azure Database for MySQL se encuentra en versión preliminar pública.

Los [registros de consultas lentas](concepts-slow-query-logs.md) de Azure Database for MySQL se pueden configurar, enumerar y descargar desde Azure Portal.

## <a name="prerequisites"></a>Requisitos previos

Los pasos que se describen en este artículo requieren que tenga un [servidor flexible](quickstart-create-server-portal.md).

## <a name="configure-logging"></a>registro

Configure el acceso al registro de consultas lentas de MySQL. 

1. Inicie sesión en [Azure Portal](https://portal.azure.com/).

1. Seleccione el servidor flexible.

1. En la sección **Configuración**, seleccione **Parámetros del servidor**.
   :::image type="content" source="./media/how-to-configure-slow-query-logs-portal/server-parameters.png" alt-text="Página de parámetros del servidor.":::

1. Actualice el parámetro **slow_query_log** a **ON**.
   :::image type="content" source="./media/how-to-configure-slow-query-logs-portal/slow-query-log-enable.png" alt-text="Activación de los registros de consultas lentas.":::

1. Cambie cualquier otro parámetro necesario (por ejemplo, `long_query_time`, `log_slow_admin_statements`). Consulte la documentación de los [registros de consultas lentas](./concepts-slow-query-logs.md#configure-slow-query-logging) para conocer más parámetros.  
   :::image type="content" source="./media/how-to-configure-slow-query-logs-portal/long-query-time.png" alt-text="Actualización de los parámetros relacionados con el registro de consultas lentas.":::

1. Seleccione **Guardar**. 
   :::image type="content" source="./media/how-to-configure-slow-query-logs-portal/save-parameters.png" alt-text="Guardado de los parámetros de registro de consultas lentas.":::

Desde la página **Parámetros de servidor**, puede volver a la lista de los registros cerrando la página.

## <a name="set-up-diagnostics"></a>Configuración de diagnósticos

Los registros de consultas lentas se integran en la configuración de diagnóstico de Azure Monitor para que pueda canalizar los registros a los registros de Azure Monitor, Event Hubs o Azure Storage.

1. En la sección **Supervisión** de la barra lateral, seleccione **Configuración de diagnóstico** > **Add diagnostic settings** (Agregar configuración de diagnóstico).

   :::image type="content" source="./media/how-to-configure-slow-query-logs-portal/add-diagnostic-setting.png" alt-text="Captura de pantalla de las opciones de diagnóstico":::

1. Proporcione un nombre de configuración de diagnóstico.

1. Especifique a qué destinos se envían los registros de consultas lentas (cuenta de almacenamiento, centro de eventos o área de trabajo de Log Analytics).

1. Seleccione **MySqlSlowLogs** como tipo de registro.
    :::image type="content" source="./media/how-to-configure-slow-query-logs-portal/configure-diagnostic-setting.png" alt-text="Captura de pantalla de las opciones de configuración de diagnóstico":::

1. Una vez que haya configurado los receptores de datos a los que canalizar los registros de consultas lentas, seleccione **Guardar**.
    :::image type="content" source="./media/how-to-configure-slow-query-logs-portal/save-diagnostic-setting.png" alt-text="Captura de pantalla de las opciones de configuración de diagnóstico, con la opción Guardar resaltada":::

1. Acceda a los registros de consultas lentas explorándolos en los receptores de datos que configuró. Los registros pueden tardar hasta 10 minutos en aparecer.

Si ha canalizado sus registros a los registros de Azure Monitor (Log Analytics), vea algunas [consultas de ejemplo](concepts-slow-query-logs.md#analyze-logs-in-azure-monitor-logs) que puede usar para el análisis. 

## <a name="next-steps"></a>Pasos siguientes
<!-- - See [Access slow query Logs in CLI](howto-configure-server-logs-in-cli.md) to learn how to download slow query logs programmatically.-->
- Más información sobre los [registros de consultas lentas](concepts-slow-query-logs.md)
- Para más información sobre las definiciones de parámetros y el registro de MySQL, consulte la documentación de MySQL sobre [registros](https://dev.mysql.com/doc/refman/5.7/en/slow-query-log.html).
- Más información sobre los [registros de auditoría](concepts-audit-logs.md)
