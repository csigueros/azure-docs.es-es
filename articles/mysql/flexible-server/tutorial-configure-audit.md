---
title: 'Tutorial: Configuración de registros de auditoría mediante el servidor flexible de Azure Database for MySQL'
description: Este tutorial le indica cómo configurar registros de auditoría mediante el servidor flexible de Azure Database for MySQL.
author: SudheeshGH
ms.author: sunaray
ms.service: mysql
ms.topic: tutorial
ms.date: 10/01/2021
ms.openlocfilehash: 4a819814b8c5bb8cf6d4bea949463fddd05405a8
ms.sourcegitcommit: 4abfec23f50a164ab4dd9db446eb778b61e22578
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/15/2021
ms.locfileid: "130065870"
---
# <a name="tutorial-configure-audit-logs-by-using-azure-database-for-mysql-flexible-server"></a>Tutorial: Configuración de registros de auditoría mediante el servidor flexible de Azure Database for MySQL

[!INCLUDE[applies-to-mysql-flexible-server](../includes/applies-to-mysql-flexible-server.md)]

Puede usar el servidor flexible de Azure Database for MySQL para configurar los registros de auditoría. Los registros de auditoría se pueden usar para realizar un seguimiento de la actividad en el nivel de la base de datos, incluidos los eventos de conexión, administración, lenguaje de definición de datos (DDL) y lenguaje de manipulación de datos (DML). Estos tipos de registros se usan normalmente para fines de cumplimiento. Normalmente se usa la auditoría de base de datos para:
* Reconocer todas las acciones que se suceden dentro de un esquema, tabla o fila determinados, o que afectan a contenido específico.
* Impedir que los usuarios (u otros) puedan realizar acciones inapropiadas en función de su responsabilidad.
* Investigar una actividad sospechosa.
* Supervisar y recopilar datos sobre actividades específicas de la base de datos.
 
En este artículo se describe cómo usar los registros de auditoría de MySQL, las herramientas de Log Analytics o una plantilla de libro para visualizar información de auditoría del servidor flexible de Azure Database for MySQL.

En este tutorial, aprenderá a:
>[!div class="checklist"]
> * Configurar la auditoría mediante Azure Portal o la CLI de Azure
> * Configuración de diagnósticos
> * Visualizar los registros de auditoría mediante Log Analytics 
> * Visualizar los registros de auditoría mediante libros  

## <a name="prerequisites"></a>Requisitos previos

- [Cree una instancia del servidor flexible de Azure Database for MySQL](./quickstart-create-server-portal.md).
- [Crear un área de trabajo de Log Analytics](../../azure-monitor/logs/quick-create-workspace.md).

## <a name="configure-auditing-by-using-the-azure-portal"></a>Configuración de la auditoría con Azure Portal 

1. Inicie sesión en [Azure Portal](https://portal.azure.com/).

1. Seleccione la instancia de servidor flexible.

1. En el panel izquierdo, en **Configuración**, seleccione **Parámetros del servidor**.

    :::image type="content" source="./media/tutorial-configure-audit/server-parameters.png" alt-text="Captura de pantalla que muestra la lista &quot;Parámetros del servidor&quot;.":::

1. Para el parámetro **audit_log_enabled**, seleccione **ACTIVADO**.

    :::image type="content" source="./media/tutorial-configure-audit/audit-log-enabled.png" alt-text="Captura de pantalla que muestra el parámetro &quot;audit_log_enabled&quot;, cambiado a &quot;ACTIVADO&quot;.":::

1. En el parámetro **audit_log_events**, en la lista desplegable, seleccione los [tipos de eventos](concepts-audit-logs.md#configure-audit-logging) que se van a registrar.

    :::image type="content" source="./media/tutorial-configure-audit/audit-log-events.png" alt-text="Captura de pantalla de las opciones de evento en la lista desplegable &quot;audit_log_events&quot;.":::

1. En los parámetros **audit_log_exclude_users** y **audit_log_include_users** especifique todos los usuarios de MySQL que se van a incluir o excluir del registro. Para ello, proporcione sus nombres de usuario de MySQL.

    :::image type="content" source="./media/tutorial-configure-audit/audit-log-exclude-users.png" alt-text="Captura de pantalla que muestra los nombres de usuario de MySQL que se incluirán o excluirán del registro.":::

1. Seleccione **Guardar**.

    :::image type="content" source="./media/tutorial-configure-audit/save-parameters.png" alt-text="Captura de pantalla del botón &quot;Guardar&quot; para guardar los cambios en los valores de parámetro.":::


## <a name="configure-auditing-by-using-the-azure-cli"></a>Configuración de la auditoría mediante la CLI de Azure
 
Como alternativa, puede habilitar y configurar la auditoría para el servidor flexible desde la CLI de Azure mediante el siguiente comando: 

```azurecli
# Enable audit logs
az mysql flexible-server parameter set \
--name audit_log_enabled \
--resource-group myresourcegroup \
--server-name mydemoserver \
--value ON
```


## <a name="set-up-diagnostics"></a>Configuración de diagnósticos

Los registros de auditorías se integran con la configuración de diagnóstico de Azure Monitor para permitirle canalizar sus registros a cualquiera de los tres receptores de datos.
* Un área de trabajo de Log Analytics
* Un centro de eventos
* Una cuenta de almacenamiento

>[!Note]
>Debe crear los receptores de datos antes de realizar la configuración de diagnóstico. Puede acceder a los registros de auditoría de los receptores de datos que ha configurado. Los registros pueden tardar hasta 10 minutos en aparecer.

1. En el panel de la izquierda, seleccione **Configuración de diagnóstico** en **Supervisión**.

1. En el panel **Configuración de diagnóstico**, seleccione **Agregar configuración de diagnóstico**.

    :::image type="content" source="./media/tutorial-configure-audit/add-diagnostic-setting.png" alt-text="Captura de pantalla del vínculo &quot;Agregar configuración de diagnóstico&quot; en el panel &quot;Configuración de diagnóstico&quot;.":::

1. En el cuadro **Nombre**, escriba un nombre para la configuración de diagnóstico.

    :::image type="content" source="./media/tutorial-configure-audit/configure-diagnostic-setting.png" alt-text="Captura de pantalla del panel &quot;Configuración de diagnóstico&quot; para seleccionar opciones de configuración.":::

1. Especifique los destinos (área de trabajo de Log Analytics, un centro de eventos o una cuenta de almacenamiento) a los que enviar los registros de auditoría; para ello, active sus casillas correspondientes.

    >[!Note]
    > En este tutorial, enviará los registros de auditorías a un área de trabajo de Log Analytics.
    
1. En **Registro**, como tipo de registro, active la casilla **MySqlAuditLogs**.

1. Una vez que haya configurado los receptores de datos a los que canalizar los registros de auditorías, seleccione **Guardar**.

    :::image type="content" source="./media/tutorial-configure-audit/save-diagnostic-setting.png" alt-text="Captura de pantalla del botón &quot;Guardar&quot; en la parte superior del panel &quot;Configuración de diagnóstico&quot;.":::

## <a name="view-audit-logs-by-using-log-analytics"></a>Visualizar los registros de auditoría mediante Log Analytics 

1. En el panel izquierdo de Log Analytics, en **Supervisión**, seleccione **Registros**.

1. Cierre la ventana **Consultas**.  

   :::image type="content" source="./media/tutorial-configure-audit/log-query.png" alt-text="Captura de pantalla del panel &quot;Consultas&quot; de Log Analytics.":::

1. En la ventana de consulta, puede escribir la consulta que se va a ejecutar. Por ejemplo, para buscar un resumen de los eventos auditados en un servidor determinado, hemos usado la consulta siguiente: 

    ```kusto
    AzureDiagnostics
        |where Category =='MySqlAuditLogs' 
        |project TimeGenerated, Resource, event_class_s, event_subclass_s, event_time_t, user_s ,ip_s , sql_text_s 
        |summarize count() by event_class_s,event_subclass_s 
        |order by event_class_s 
    ```
    
    :::image type="content" source="./media/tutorial-configure-audit/audit-query.png" alt-text="Captura de pantalla de una consulta de Log Analytics de ejemplo que busca encontrar un resumen de los eventos auditados en un servidor determinado.":::

## <a name="view-audit-logs-by-using-workbooks"></a>Visualizar los registros de auditoría mediante libros 
 
La plantilla de libro que usamos para la auditoría requiere que se cree la configuración de diagnóstico para enviar registros de plataforma. 

1. En Azure Monitor, en el panel izquierdo, seleccione **Registro de actividad** y, a continuación, seleccione **Configuración de diagnóstico**. 

    :::image type="content" source="./media/tutorial-configure-audit/activity-diagnostics.png" alt-text="Captura de pantalla que muestra la pestaña &quot;Configuración de diagnóstico&quot; en el panel &quot;Registro de actividad&quot; de Azure Monitor.":::

1. En el panel **Configuración de diagnóstico**, puede agregar una nueva configuración o editar una existente. Cada configuración no puede tener más de uno de los tipos de destino.

    :::image type="content" source="./media/tutorial-configure-audit/activity-settings-diagnostic.png" alt-text="Captura de pantalla del panel &quot;Configuración de diagnóstico&quot; de Azure Monitor para seleccionar destinos de registro.":::

    > [!Note]
    > Puede acceder a los registros de consultas lentos en los receptores de datos que ya ha configurado (área de trabajo de Log Analytics, cuenta de almacenamiento o centro de eventos). Los registros pueden tardar hasta 10 minutos en aparecer.

1. En el panel izquierdo de Azure Portal, en la hoja **Supervisión** de la instancia del servidor flexible de Azure Database for MySQL, seleccione **Libros**.
1. Seleccione el libro **Auditoría**. 

    :::image type="content" source="./media/tutorial-configure-audit/monitor-workbooks.png" alt-text="Captura de pantalla que muestra todos los libros de la galería de libros.":::
    
En el libro, puede observar las visualizaciones siguientes: 
>[!div class="checklist"]
> * Acciones administrativas en el servicio
> * Resumen de auditoría
> * Resumen de eventos de conexión de auditoría
> * Eventos de conexión de auditoría
> * Resumen de acceso a tablas
> * Errores identificados


:::image type="content" source="./media/tutorial-configure-audit/admin-events.png" alt-text="Captura de pantalla de la plantilla de libro &quot;Acciones administrativas en el servicio&quot;.":::

:::image type="content" source="./media/tutorial-configure-audit/audit-summary.png" alt-text="Captura de pantalla de la plantilla de libro &quot;Auditoría de eventos de conexión&quot;.":::

>[!Note]
> * También puede editar estas plantillas y personalizarlas según sus necesidades. Para más información, consulte la sección "Modo de edición" de la [introducción a los libros de Azure Monitor](../../azure-monitor/visualize/workbooks-overview.md#editing-mode).
> * Para obtener una vista rápida, también puede hacer anclar los libros o la consulta de Log Analytics al panel. Para más información, consulte [Creación de un panel en Azure Portal](../../azure-portal/azure-portal-dashboards.md). 

La vista *Acciones administrativas en el servicio* le proporciona detalles sobre la actividad realizada en el servicio. Ayuda a determinar la información de *qué, quién y cuándo* en las operaciones de escritura (PUT, POST, DELETE) que se realizan en los recursos de la suscripción. 

Puede usar otras visualizaciones para ayudarle a comprender los detalles de la actividad de la base de datos. La seguridad de la base de datos tiene cuatro partes: 
* **Seguridad del servidor**: es la responsable de impedir que personal no autorizado acceda a la base de datos.  
* **Conexión de base de datos**: el administrador debe comprobar si el personal autorizado ha realizado alguna actualización de la base de datos.
* **Control de acceso a tablas**: muestra las claves de acceso de los usuarios autorizados y qué tablas de la base de datos están autorizados a controlar cada uno de ellos.
* **Restricción de acceso a la base de datos**: especialmente importante para aquellos que han cargado una base de datos en Internet y ayuda a evitar que orígenes externos obtengan acceso a la base de datos. 

## <a name="next-steps"></a>Pasos siguientes
- [Más información sobre los libros de Azure Monitor](../../azure-monitor/visualize/workbooks-overview.md#visualizations) y sus opciones de visualización enriquecidas.
- Más información sobre los [registros de auditoría](concepts-audit-logs.md)
