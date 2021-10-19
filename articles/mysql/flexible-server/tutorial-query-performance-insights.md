---
title: 'Tutorial: Información de rendimiento de consultas en Azure Database for MySQL con servidor flexible'
description: 'Tutorial: Información de rendimiento de consultas en Azure Database for MySQL con servidor flexible'
author: SudheeshGH
ms.author: sunaray
ms.service: mysql
ms.topic: tutorial
ms.date: 10/01/2021
ms.openlocfilehash: ed78f493021c94c8beeecb8d5470d9a846b19d8d
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/06/2021
ms.locfileid: "129621498"
---
# <a name="tutorial-query-performance-insights-for-azure-database-for-mysql--flexible-server"></a>Tutorial: Información de rendimiento de consultas en Azure Database for MySQL con servidor flexible
[!INCLUDE[applies-to-mysql-flexible-server](../includes/applies-to-mysql-flexible-server.md)]

Información de rendimiento de consultas pretende proporcionar análisis de consultas inteligente para las bases de datos. La información preferida son los patrones de carga de trabajo y las consultas de ejecución más largas. Esto ayuda a averiguar qué consultas se deben optimizar para mejorar el rendimiento general y usar eficazmente los recursos disponibles. Información de rendimiento de consultas ayuda a dedicar menos tiempo a solucionar problemas de rendimiento de bases de datos, ya que proporciona detalles como:
* Las N consultas de larga duración principales y su tendencia.
* La capacidad de explorar en profundidad los detalles de una consulta para ver el texto de la consulta, así como el historial de ejecución con el tiempo de consulta mínimo, máximo, promedio y la desviación estándar.
* El uso de recursos (CPU, memoria y almacenamiento)
 
En este tutorial aprenderá a usar los registros de consultas lentos de MySQL, la herramienta Log Analytics o la plantilla de libros para visualizar la información de rendimiento de consultas de Azure Database for MySQL con servidor flexible. 

## <a name="prerequisites"></a>Requisitos previos
- Tiene que crear una instancia de Azure Database for MySQL con servidor flexible. Para ver el procedimiento paso a paso, consulte [Creación de una instancia de Azure Database for MySQL: servidor flexible](./quickstart-create-server-portal.md)
- Tiene que crear un área de trabajo de Log Analytics. Para ver el procedimiento paso a paso, consulte [Creación de un área de trabajo de Log Analytics](../../azure-monitor/logs/quick-create-workspace.md).

En este tutorial, aprenderá a:
>[!div class="checklist"]
> * Configurar los registros de consultas lentos desde el portal o mediante la CLI de Azure.
> * Configuración de diagnósticos
> * Ver las consultas lentas mediante Log Analytics. 
> * Ver las consultas lentas mediante libros. 

## <a name="configure-slow-query-logs-from-portal"></a>Configurar los registros de consultas lentos en el portal. 


1. Inicie sesión en [Azure Portal](https://portal.azure.com/).

1. Seleccione el servidor flexible.

1. En la sección **Configuración**, seleccione **Parámetros del servidor**.
   :::image type="content" source="./media//tutorial-query-performance-insights/server-parameters.png" alt-text="Página de parámetros del servidor.":::

1. Actualice el parámetro **slow_query_log** a **ON**.
   :::image type="content" source="./media/tutorial-query-performance-insights/slow-query-log-enable.png" alt-text="Activación de los registros de consultas lentas.":::

1. Cambie cualquier otro parámetro necesario (por ejemplo, `long_query_time`, `log_slow_admin_statements`). Consulte la documentación de los [registros de consultas lentas](./concepts-slow-query-logs.md#configure-slow-query-logging) para conocer más parámetros.  
   :::image type="content" source="./media/tutorial-query-performance-insights/long-query-time.png" alt-text="Actualización de los parámetros relacionados con el registro de consultas lentas.":::

1. Seleccione **Guardar**. 
   :::image type="content" source="./media/tutorial-query-performance-insights/save-parameters.png" alt-text="Guardado de los parámetros de registro de consultas lentas.":::

Desde la página **Parámetros de servidor**, puede volver a la lista de los registros cerrando la página.



## <a name="configure-slow-query-logs-from-azure-cli"></a>Configuración de registros de consultas lentos con la CLI de Azure
 
En caso de que desee hacer lo anterior mediante la CLI de Azure , puede habilitar y configurar registros de consultas lentos para el servidor mediante la CLI. 

> [!IMPORTANT]
> Se recomienda registrar solo los tipos de evento y los usuarios necesarios con fines de auditoría para asegurarse de que el rendimiento del servidor no se ve afectado en gran medida.

Habilite y configure los registros de consultas lentas del servidor.

```azurecli
# Turn on statement level log

az mysql flexible-server parameter set \
--name log_statement \
--resource-group myresourcegroup \
--server-name mydemoserver \
--value all


# Set log_min_duration_statement time to 10 sec

# This setting will log all queries executing for more than 10 sec. Please adjust this threshold based on your definition for slow queries

az mysql server configuration set \
--name log_min_duration_statement \
--resource-group myresourcegroup \
--server mydemoserver \
--value 10000

# Enable Slow query logs



az mysql flexible-server parameter set \
--name slow_query_log \
--resource-group myresourcegroup \
--server-name mydemoserver \
--value ON
```

## <a name="set-up-diagnostics"></a>Configuración de diagnósticos

Los registros de consultas lentas se integran en la configuración de diagnóstico de Azure Monitor para que pueda canalizar los registros a los registros de Azure Monitor, Event Hubs o Azure Storage.

1. En la sección **Supervisión** de la barra lateral, seleccione **Configuración de diagnóstico** > **Add diagnostic settings** (Agregar configuración de diagnóstico).

   :::image type="content" source="./media/tutorial-query-performance-insights/add-diagnostic-setting.png" alt-text="Captura de pantalla de las opciones de diagnóstico":::

1. Proporcione un nombre de configuración de diagnóstico.

1. Especifique a qué destinos se envían los registros de consultas lentos (cuenta de almacenamiento, centro de eventos o área de trabajo de Log Analytics).

    >[!Note]
    > En el ámbito de este tutorial, tenemos que enviar los registros de consultas lentos al área de trabajo de Log Analytics.

1. Seleccione **MySqlSlowLogs** como tipo de registro.
    :::image type="content" source="./media/tutorial-query-performance-insights/configure-diagnostic-setting.png" alt-text="Captura de pantalla de las opciones de configuración de diagnóstico":::

1. Una vez que haya configurado los receptores de datos a los que canalizar los registros de consultas lentas, seleccione **Guardar**.
    :::image type="content" source="./media/tutorial-query-performance-insights/save-diagnostic-setting.png" alt-text="Captura de pantalla de las opciones de configuración de diagnóstico, con la opción Guardar resaltada":::

    >[!Note]
    >Debe crear receptores de datos (áreas de trabajo de Log Analytics, cuenta de almacenamiento o centro de eventos) antes de configurar las opciones de diagnóstico. Puede acceder a los registros de consultas lentos en los receptores de datos que configuró (área de trabajo de Log Analytics, cuenta de almacenamiento o centro de eventos). Los registros pueden tardar hasta 10 minutos en aparecer.
 
## <a name="view-query-insights-using-log-analytics"></a>Visualización de la información de consulta mediante Log Analytics 

Vaya a **Registros** en la sección **Supervisión**. Cierre la ventana **Consultas**.

:::image type="content" source="./media/tutorial-query-performance-insights/log-query.png" alt-text="Captura de pantalla de Log Analytics":::

En la ventana de consulta puede escribir la consulta que se va a ejecutar.  Aquí hemos usado una consulta para buscar consultas de más de 10 segundos en un servidor determinado.

```kusto
 AzureDiagnostics
    | where Category == 'MySqlSlowLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, start_time_t , query_time_d, sql_text_s 
    | where query_time_d > 10
```
    
Seleccione el **intervalo de tiempo** y **ejecute** la consulta. A continuación, verá los resultados de la consulta.  

:::image type="content" source="./media/tutorial-query-performance-insights/slow-query.png" alt-text="Captura de pantalla del registro de consulta lento":::

## <a name="view-query-insights-using-workbooks"></a>Visualización de la información de consulta mediante libros 

1.  En Azure Portal, vaya a la hoja **Supervisión** de Azure Database for MySQL: servidor flexible y seleccione **Libros**.
2.  Debería poder ver las plantillas. Seleccione **Información de rendimiento de consultas**. 

    :::image type="content" source="./media/tutorial-query-performance-insights/monitor-workbooks.png" alt-text="Captura de pantalla de la plantilla de libros":::

Podrá ver la siguiente visualización. 
>[!div class="checklist"]
> * Carga de consultas
> * Conexiones activas totales
> * Tendencia de consultas lentas (Tiempo de consulta > 10 segundos)
> * Detalles de la consulta lenta
> * Lista de las 5 consultas de mayor duración
> * Resuma las consultas lentas por tiempo de consulta mínimo, máximo, promedio y de desviación estándar.

    
:::image type="content" source="./media/tutorial-query-performance-insights/long-query.png" alt-text="Captura de pantalla de consultas largas":::

>[!Note]
> * Para el uso de recursos, puede usar la plantilla Información general.
> * También puede editar estas plantillas y personalizarlas según sus necesidades. Para obtener más detalles, consulte [Introducción a los libros de Azure Monitor](../../azure-monitor/visualize/workbooks-overview.md#editing-mode).
> * Para obtener una vista rápida, también puede hacer ping en los libros o en la consulta Log Analytics del panel. Para más información, consulte [Creación de un panel en Azure Portal](../../azure-portal/azure-portal-dashboards.md) 

Dos métricas de Información de rendimiento de consultas pueden ayudarle a detectar posibles cuellos de botella: duración y recuento de ejecuciones. Las consultas de larga ejecución tienen el máximo potencial para bloquear recursos durante más tiempo, bloquear otros usuarios y limitar la escalabilidad. En algunos casos, un recuento de ejecuciones alto puede provocar más recorridos de ida y vuelta de red. Los recorridos de ida y vuelta afectan al rendimiento. Están sujetos a la latencia de red y a la latencia del servidor que sigue en la cadena. Por lo tanto, el recuento de ejecuciones puede ayudar a encontrar consultas ejecutadas con frecuencia. Estas consultas son las mejores candidatas para la optimización. 

## <a name="next-steps"></a>Pasos siguientes
- [Introducción a los libros de Azure Monitor](../../azure-monitor/visualize/workbooks-overview.md#visualizations) y más información sobre las opciones enriquecidas de visualización de libros
- Más información sobre los [registros de consultas lentas](concepts-slow-query-logs.md)


