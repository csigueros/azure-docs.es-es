---
title: 'Tutorial: Auditoría de Azure Database for MySQL: servidor flexible'
description: 'Tutorial: Auditoría de Azure Database for MySQL: servidor flexible'
author: SudheeshGH
ms.author: sunaray
ms.service: mysql
ms.topic: tutorial
ms.date: 10/01/2021
ms.openlocfilehash: b4c8206071ce71cc29a9de45b46fa05c40f9cd91
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/06/2021
ms.locfileid: "129621386"
---
# <a name="tutorial-auditing-for-azure-database-for-mysql--flexible-server"></a>Tutorial: Auditoría de Azure Database for MySQL: servidor flexible
[!INCLUDE[applies-to-mysql-flexible-server](../includes/applies-to-mysql-flexible-server.md)]

El Servidor flexible de Azure Database for MySQL proporciona a los usuarios la capacidad de configurar registros de auditoría. Los registros de auditoría se pueden usar para realizar un seguimiento de la actividad de nivel de base de datos, incluidos los eventos de conexión, administración, DDL y DML. Estos tipos de registros se usan normalmente para fines de cumplimiento. Habitualmente, la auditoría de bases de datos se usa para:
* Indicar todas las acciones que se producen dentro de un esquema, tabla o fila determinados, o que afectan a un contenido concreto
* Impedir que los usuarios (u otros) puedan realizar acciones inapropiadas en función de su responsabilidad
* Investigación de actividad sospechosa
* Supervisión y recopilación de datos sobre actividades específicas de la base de datos
 
En este tutorial aprenderá a usar los registros de auditoría de MySQL, la herramienta Log Analytics o la plantilla de libros para visualizar la información de auditoría de Azure Database for MySQL con servidor flexible. 

## <a name="prerequisites"></a>Requisitos previos
- Tiene que crear una instancia de Azure Database for MySQL con servidor flexible. Para ver el procedimiento paso a paso, consulte [Creación de una instancia de Azure Database for MySQL: servidor flexible](./quickstart-create-server-portal.md)
- Tiene que crear un área de trabajo de Log Analytics. Para ver el procedimiento paso a paso, consulte [Creación de un área de trabajo de Log Analytics](../../azure-monitor/logs/quick-create-workspace.md).

En este tutorial, aprenderá a:
>[!div class="checklist"]
> * Configurar la auditoría desde Azure Portal o mediante la CLI de Azure
> * Configurar diagnósticos
> * Ver registros de auditoría mediante Log Analytics 
> * Ver registros de auditoría mediante libros 

## <a name="configure-auditing-from-portal"></a>Configurar la auditoría desde el portal 


1. Inicie sesión en [Azure Portal](https://portal.azure.com/).

1. Seleccione el servidor flexible.

1. En la sección **Configuración**, seleccione **Parámetros del servidor**.
    :::image type="content" source="./media/tutorial-configure-audit/server-parameters.png" alt-text="Parámetros del servidor":::

1. Actualice el parámetro **audit_log_enabled** a ACTIVADO.
    :::image type="content" source="./media/tutorial-configure-audit/audit-log-enabled.png" alt-text="Habilitar los registros de auditoría":::

1. Seleccione los [tipos de eventos](concepts-audit-logs.md#configure-audit-logging) que se registrarán actualizando el parámetro **audit_log_events**.
    :::image type="content" source="./media/tutorial-configure-audit/audit-log-events.png" alt-text="Eventos del registro de auditoría":::

1. Agregue los usuarios de MySQL que se van a incluir o excluir del registro mediante la actualización de los parámetros **audit_log_exclude_users** y **audit_log_include_users**. Especifique los usuarios proporcionando su nombre de usuario de MySQL.
    :::image type="content" source="./media/tutorial-configure-audit/audit-log-exclude-users.png" alt-text="Excluir usuarios del registro de auditoría":::

1. Cuando haya cambiado los parámetros, haga clic en **Guardar**. O bien puede **descartar** los cambios.
    :::image type="content" source="./media/tutorial-configure-audit/save-parameters.png" alt-text="Guardar":::



## <a name="configure-auditing--from-azure-cli"></a>Configuración de la auditoría desde la CLI de Azure
 
En caso de que desee hacer lo anterior mediante la CLI de Azure, puede habilitar y configurar la auditoría del servidor mediante la CLI. 

```azurecli
# Enable audit logs
az mysql flexible-server parameter set \
--name audit_log_enabled \
--resource-group myresourcegroup \
--server-name mydemoserver \
--value ON
```


## <a name="set-up-diagnostics"></a>Configuración de diagnósticos

Los registros de auditoría se integran en la configuración de diagnóstico de Azure Monitor para que pueda canalizar los registros a los registros de Azure Monitor, Event Hubs o Azure Storage.

1. En la sección **Supervisión** de la barra lateral, seleccione **Configuración de diagnóstico**.

1. Haga clic en "+ Agregar configuración de diagnóstico" :::image type="content" source="./media/tutorial-configure-audit/add-diagnostic-setting.png" alt-text="Agregar configuración de diagnóstico":::.

1. Proporcione un nombre de configuración de diagnóstico.

1. Especifique los destinos a los que se van a enviar los registros de auditoría (área de trabajo de Log Analytics, cuenta de almacenamiento o centro de eventos).
    >[!Note]
    > En el ámbito de este tutorial, tenemos que enviar los registros de consultas lentos al área de trabajo de Log Analytics.
    
1. Seleccione **MySqlAuditLogs** como el tipo de registro.
    :::image type="content" source="./media/tutorial-configure-audit/configure-diagnostic-setting.png" alt-text="Configurar las opciones de diagnóstico":::

1. Una vez que haya configurado los receptores de datos para canalizar los registros de auditoría, puede hacer clic en **Guardar**.
    :::image type="content" source="./media/tutorial-configure-audit/save-diagnostic-setting.png" alt-text="Guardar la configuración de diagnóstico":::

    >[!Note]
    >Debe crear receptores de datos (áreas de trabajo de Log Analytics, cuenta de almacenamiento o centro de eventos) antes de configurar las opciones de diagnóstico. Puede acceder a los registros de consultas lentos en los receptores de datos que configuró (área de trabajo de Log Analytics, cuenta de almacenamiento o centro de eventos). Los registros pueden tardar hasta 10 minutos en aparecer.

## <a name="view-audit-logs-using-log-analytics"></a>Visualización de registros de auditoría mediante Log Analytics 

Vaya a **Registros** en la sección **Supervisión**. Cierre la ventana **Consultas**.  

:::image type="content" source="./media/tutorial-configure-audit/log-query.png" alt-text="Captura de pantalla de Log Analytics":::

En la ventana de consulta puede escribir la consulta que se va a ejecutar.  Aquí se ha usado la consulta para buscar el resumen de eventos auditados en un servidor determinado.

```kusto
AzureDiagnostics
    |where Category =='MySqlAuditLogs' 
    |project TimeGenerated, Resource, event_class_s, event_subclass_s, event_time_t, user_s ,ip_s , sql_text_s 
    |summarize count() by event_class_s,event_subclass_s 
    |order by event_class_s 
```
  
:::image type="content" source="./media/tutorial-configure-audit/audit-query.png" alt-text="Captura de pantalla de consulta de Log Analytics":::

## <a name="view-audit-logs-using-workbooks"></a>Visualización de registros de auditoría mediante libros 
 
La plantilla de libro que usamos para la auditoría requiere que se cree la configuración de diagnóstico para enviar registros de plataforma. 

1.  Para enviar registros de plataforma, haga clic en la opción Registro de actividad del menú Azure Monitor y, después, en **Configuración de diagnóstico**. 

    :::image type="content" source="./media/tutorial-configure-audit/activity-diagnostics.png" alt-text="Captura de pantalla de Configuración de diagnóstico":::

2.  Agregue un nuevo valor o seleccione Editar configuración para editar una configuración existente. Cada configuración no puede tener más de uno de los tipos de destino.

    :::image type="content" source="./media/tutorial-configure-audit/activity-settings-diagnostic.png" alt-text="Captura de pantalla de selección de configuración de diagnóstico":::

    >[!Note]
    >Puede acceder a los registros de consultas lentos en los receptores de datos que configuró (área de trabajo de Log Analytics, cuenta de almacenamiento o centro de eventos). Los registros pueden tardar hasta 10 minutos en aparecer.

3.  En Azure Portal, vaya a la hoja **Supervisión** de Azure Database for MySQL: servidor flexible y seleccione **Libros**.
4.  Debería poder ver las plantillas. Seleccione **Auditoría** 

    :::image type="content" source="./media/tutorial-configure-audit/monitor-workbooks.png" alt-text="Captura de pantalla de la plantilla de libros":::
    
Podrá ver la siguiente visualización. 
>[!div class="checklist"]
> * Acciones administrativas en el servicio
> * Resumen de auditoría
> * Resumen de eventos de conexión de auditoría
> * Eventos de conexión de auditoría
> * Resumen de acceso a tablas
> * Errores identificados


:::image type="content" source="./media/tutorial-configure-audit/admin-events.png" alt-text="Captura de pantalla de eventos de administrador de la plantilla de libros":::

:::image type="content" source="./media/tutorial-configure-audit/audit-summary.png" alt-text="Captura de pantalla de eventos de resumen de auditoría de la plantilla de libros":::

>[!Note]
> * También puede editar estas plantillas y personalizarlas según sus necesidades. Para más información, consulte [Introducción a los libros de Azure Monitor: Azure Monitor](../../azure-monitor/visualize/workbooks-overview.md#editing-mode).
> * Para obtener una vista rápida, también puede hacer ping en los libros o en la consulta Log Analytics del panel. Para más información, consulte [Creación de un panel en Azure Portal](../../azure-portal/azure-portal-dashboards.md) 

Las acciones administrativas en el servicio le proporcionan detalles sobre la actividad realizada en el servicio. Ayuda a determinar la información de qué, quién y cuándo en las operaciones de escritura (PUT, POST, DELETE) realizadas en los recursos de la suscripción. 

Otras visualizaciones le ayudarán a obtener detalles de la actividad de la base de datos. La seguridad de la base de datos se compone de cuatro partes: seguridad del servidor, conexión de base de datos, control de acceso a tablas y restricción del acceso a la base de datos. La seguridad del servidor es la responsable de impedir que personal no autorizado acceda a la base de datos. En términos de conexiones de base de datos, el administrador también debe comprobar si es el personal autorizado quien realiza las actualizaciones en la base de datos. La restricción del acceso a la base de datos es importante, sobre todo para aquellos cuya base de datos se ha cargado en Internet. Esto ayudará a evitar que cualquier origen externo entre o obtenga acceso a la base de datos. 

## <a name="next-steps"></a>Pasos siguientes
- [Introducción a los libros de Azure Monitor](../../azure-monitor/visualize/workbooks-overview.md#visualizations) y más información sobre las opciones enriquecidas de visualización de libros
- Más información sobre los [registros de auditoría](concepts-audit-logs.md)