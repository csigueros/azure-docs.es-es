---
title: 'Tutorial: Información de rendimiento de consultas en el servidor flexible de Azure Database for MySQL'
description: En este artículo se muestran las herramientas para ayudar a visualizar la información de rendimiento de consultas en el servidor flexible de Azure Database for MySQL.
author: SudheeshGH
ms.author: sunaray
ms.service: mysql
ms.topic: tutorial
ms.date: 10/01/2021
ms.openlocfilehash: 63cec8fa00af3e4711c4c6383c68cc09d32ce3d3
ms.sourcegitcommit: 4abfec23f50a164ab4dd9db446eb778b61e22578
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/15/2021
ms.locfileid: "130065718"
---
# <a name="tutorial-query-performance-insight-for-azure-database-for-mysql-flexible-server"></a>Tutorial: Información de rendimiento de consultas en el servidor flexible de Azure Database for MySQL
[!INCLUDE[applies-to-mysql-flexible-server](../includes/applies-to-mysql-flexible-server.md)]

Información de rendimiento de consultas pretende proporcionar análisis de consultas inteligente para las bases de datos. La información preferida que se extrae son los patrones de carga de trabajo y las consultas de larga duración. Comprender esta información puede ayudarle a encontrar qué consultas se deben optimizar para mejorar el rendimiento general y para usar los recursos disponibles de forma eficaz. 

Información de rendimiento de consultas es una herramienta diseñada para ayudarle a dedicar menos tiempo a solucionar problemas de rendimiento de la base de datos, al proporcionar:
* Las *N* principales consultas de larga duración y sus tendencias.
* Los detalles de la consulta: se puede ver el texto de la consulta, así como el historial de ejecución con el tiempo de consulta mínimo, máximo, promedio y la desviación estándar.
* El uso de recursos (CPU, memoria y almacenamiento).
 
En este artículo se describe cómo usar los registros de consultas lentas de MySQL, la herramienta Log Analytics y las plantillas de libros para visualizar la información de rendimiento de consultas en el servidor flexible de Azure Database for MySQL.

En este tutorial, aprenderá a:
>[!div class="checklist"]
> * Configurar los registros de consultas lentas desde Azure Portal o mediante la CLI de Azure.
> * Configuración de diagnósticos
> * Ver los registros de consultas lentas mediante Log Analytics. 
> * Ver los registros de consultas lentas mediante libros. 

## <a name="prerequisites"></a>Requisitos previos

- [Creación de una instancia del servidor flexible de Azure Database for MySQL](./quickstart-create-server-portal.md)
- [Crear un área de trabajo de Log Analytics](../../azure-monitor/logs/quick-create-workspace.md).


## <a name="configure-slow-query-logs-by-using-the-azure-portal"></a>Configuración de registros de consultas lentas mediante Azure Portal 

1. Inicie sesión en [Azure Portal](https://portal.azure.com/).

1. Seleccione la instancia de servidor flexible.

1. En el panel izquierdo, en **Configuración**, seleccione **Parámetros del servidor**.

   :::image type="content" source="./media//tutorial-query-performance-insights/server-parameters.png" alt-text="Captura de pantalla que muestra la lista &quot;Parámetros del servidor&quot;.":::

1. Para el parámetro **slow_query_log**, seleccione **ACTIVADO**.

   :::image type="content" source="./media/tutorial-query-performance-insights/slow-query-log-enable.png" alt-text="Captura de pantalla que muestra el parámetro &quot;slow_query_log&quot; cambiado a &quot;ACTIVADO&quot;.":::

1. Para los demás parámetros, como **long_query_time** y **log_slow_admin_statements**, consulte la documentación de los [registros de consultas lentas](./concepts-slow-query-logs.md#configure-slow-query-logging).  

   :::image type="content" source="./media/tutorial-query-performance-insights/long-query-time.png" alt-text="Captura de pantalla que muestra los valores actualizados del resto de parámetros relacionados con el registro de consultas lentas.":::

1. Seleccione **Guardar**. 

   :::image type="content" source="./media/tutorial-query-performance-insights/save-parameters.png" alt-text="Captura de pantalla del botón &quot;Guardar&quot; para guardar los cambios en los valores de parámetro.":::

Para volver a la lista de registros, cierre la página **Parámetros del servidor**.

## <a name="configure-slow-query-logs-by-using-the-azure-cli"></a>Configuración de registros de consultas lentas con la CLI de Azure
 
Como alternativa, puede habilitar y configurar registros de consultas lentas para el servidor flexible desde la CLI de Azure mediante el siguiente comando: 

> [!IMPORTANT]
> Para tener la seguridad de que el rendimiento del servidor flexible no se vea gravemente afectado, se recomienda registrar solo los tipos de eventos y los usuarios necesarios con fines de auditoría.

```azurecli
# Turn on statement level log.

az mysql flexible-server parameter set \
--name log_statement \
--resource-group myresourcegroup \
--server-name mydemoserver \
--value all


# Set log_min_duration_statement time to 10 seconds. This setting will log all queries that execute for more than 10 seconds. Adjust this threshold based on your definition for slow queries.

az mysql server configuration set \
--name log_min_duration_statement \
--resource-group myresourcegroup \
--server mydemoserver \
--value 10000

# Enable slow query logs.

az mysql flexible-server parameter set \
--name slow_query_log \
--resource-group myresourcegroup \
--server-name mydemoserver \
--value ON
```

## <a name="set-up-diagnostics"></a>Configuración de diagnósticos

Los registros de consultas lentas se integran con la configuración de diagnóstico de Azure Monitor para permitirle canalizar sus registros a cualquiera de los tres receptores de datos:
* Un área de trabajo de Log Analytics
* Un centro de eventos
* Una cuenta de almacenamiento

>[!Note]
>Debe crear los receptores de datos antes de realizar la configuración de diagnóstico. Puede acceder a los registros de consultas lentas de los receptores de datos que ha configurado. Los registros pueden tardar hasta 10 minutos en aparecer.

1. En el panel de la izquierda, seleccione **Configuración de diagnóstico** en **Supervisión**.

1. En el panel **Configuración de diagnóstico**, seleccione **Agregar configuración de diagnóstico**.

   :::image type="content" source="./media/tutorial-query-performance-insights/add-diagnostic-setting.png" alt-text="Captura de pantalla del vínculo &quot;Agregar configuración de diagnóstico&quot; en el panel &quot;Configuración de diagnóstico&quot;.":::

1. En el cuadro **Nombre**, escriba un nombre para la configuración de diagnóstico.

1. Especifique los destinos (área de trabajo de Log Analytics, un centro de eventos o una cuenta de almacenamiento) a los que enviar los registros de consultas lentas; para ello, active sus casillas correspondientes.

    >[!Note]
    > En este tutorial, enviará los registros de consultas lentas a un área de trabajo de Log Analytics.

1. En **Registro**, como tipo de registro, active la casilla **MySqlSlowLogs**.

    :::image type="content" source="./media/tutorial-query-performance-insights/configure-diagnostic-setting.png" alt-text="Captura de pantalla del panel &quot;Configuración de diagnóstico&quot; para seleccionar opciones de configuración.":::

1. Una vez que haya configurado los receptores de datos a los que canalizar los registros de consultas lentas, seleccione **Guardar**.

    :::image type="content" source="./media/tutorial-query-performance-insights/save-diagnostic-setting.png" alt-text="Captura de pantalla de las opciones de configuración de diagnóstico, con la opción Guardar resaltada":::

## <a name="view-query-insights-by-using-log-analytics"></a>Visualización de la información de consulta mediante Log Analytics 

1. En el panel izquierdo de Log Analytics, en **Supervisión**, seleccione **Registros**.

1. Cierre la ventana **Consultas** que se abre.

   :::image type="content" source="./media/tutorial-query-performance-insights/log-query.png" alt-text="Captura de pantalla del panel &quot;Consultas&quot; de Log Analytics.":::

1. En la ventana de consulta, puede escribir la consulta que se va a ejecutar. Para buscar consultas de más de 10 segundos en un servidor determinado, se ha usado el código siguiente:

   ```kusto
   AzureDiagnostics
      | where Category == 'MySqlSlowLogs'
      | project TimeGenerated, LogicalServerName_s, event_class_s, start_time_t , query_time_d, sql_text_s 
      | where query_time_d > 10
   ```
    
1. Seleccione el **intervalo de tiempo** y ejecute la consulta. Los resultados se muestran en la imagen siguiente:  

   :::image type="content" source="./media/tutorial-query-performance-insights/slow-query.png" alt-text="Captura de pantalla del registro de consultas lentas.":::

## <a name="view-query-insights-by-using-workbooks"></a>Visualización de la información de consulta mediante libros 

1. En el panel izquierdo de Azure Portal, en la hoja **Supervisión** de la instancia del servidor flexible de Azure Database for MySQL, seleccione **Libros**.

1.  Seleccione la plantilla **Información de rendimiento de consultas**. 

    :::image type="content" source="./media/tutorial-query-performance-insights/monitor-workbooks.png" alt-text="Captura de pantalla que muestra todos los libros de la galería de libros.":::

En el libro, puede observar las visualizaciones siguientes: 
>[!div class="checklist"]
> * Carga de consultas
> * Conexiones activas totales
> * Tendencia de consultas lentas (Tiempo de consulta > 10 segundos)
> * Detalles de la consulta lenta
> * Lista de las 5 consultas de mayor duración
> * Resuma las consultas lentas por tiempo de consulta mínimo, máximo, promedio y de desviación estándar.

    
:::image type="content" source="./media/tutorial-query-performance-insights/long-query.png" alt-text="Captura de pantalla que muestra dos consultas largas.":::

>[!Note]
> * Para ver el uso de recursos, puede utilizar la plantilla Información general.
> * También puede editar estas plantillas y personalizarlas según sus necesidades. Para más información, consulte [Introducción a los libros de Azure Monitor](../../azure-monitor/visualize/workbooks-overview.md#editing-mode).
> * Para obtener una vista rápida, también puede hacer anclar los libros o la consulta de Log Analytics al panel. Para más información, consulte [Creación de un panel en Azure Portal](../../azure-portal/azure-portal-dashboards.md). 

En Información de rendimiento de consultas, hay dos métricas que pueden ayudarle a detectar posibles cuellos de botella: *duración* y *recuento de ejecuciones*. Las consultas de larga ejecución tienen el máximo potencial para bloquear recursos durante más tiempo, bloquear otros usuarios y limitar la escalabilidad. 

En algunos casos, un recuento de ejecuciones alto puede provocar más recorridos de ida y vuelta de red. Los recorridos de ida y vuelta afectan al rendimiento. Están sujetos a la latencia de red y a la latencia del servidor que sigue en la cadena. Por lo tanto, el recuento de ejecuciones puede ayudar a encontrar consultas ejecutadas con frecuencia. Estas consultas son las mejores candidatas para la optimización. 

## <a name="next-steps"></a>Pasos siguientes
- [Más información sobre los libros de Azure Monitor](../../azure-monitor/visualize/workbooks-overview.md#visualizations) y sus opciones de visualización enriquecidas.
- [Más información sobre los registros de consultas lentas](concepts-slow-query-logs.md).


