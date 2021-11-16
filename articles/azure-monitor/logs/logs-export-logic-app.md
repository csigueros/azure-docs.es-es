---
title: Archivado de datos de un área de trabajo de Log Analytics a Azure Storage mediante Logic Apps
description: En este artículo se describe un método para usar Azure Logic Apps para consultar datos de un área de trabajo de Log Analytics y enviarlos a Azure Storage.
ms.service: azure-monitor
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/02/2020
ms.openlocfilehash: c5f73bbb960cd5bdce4a55a2c090ad509c1a9106
ms.sourcegitcommit: 27ddccfa351f574431fb4775e5cd486eb21080e0
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/08/2021
ms.locfileid: "131997825"
---
# <a name="archive-data-from-log-analytics-workspace-to-azure-storage-using-logic-app"></a>Archivado de datos de un área de trabajo de Log Analytics a Azure Storage mediante Logic Apps
En este artículo se describe un método para usar [Azure Logic Apps](../../logic-apps/index.yml) para consultar datos de un área de trabajo de Log Analytics en Azure Monitor y enviarlos a Azure Storage. Use este proceso cuando necesite exportar los datos de registros de Azure Monitor para escenarios de auditoría y cumplimiento o para permitir que otro servicio recupere estos datos.  

## <a name="other-export-methods"></a>Otros métodos de exportación
El método que se explica en este artículo describe una exportación programada a partir de una consulta de registro mediante una instancia de Logic Apps. Otras opciones de exportación de datos para determinados escenarios son las siguientes:

- Para exportar datos del área de trabajo de Log Analytics a una cuenta de Azure Storage o un centro de eventos, use la característica de exportación de datos del área de trabajo de Log Analytics de Azure Monitor Logs. Consulte [Exportación de datos del área de trabajo de Log Analytics en Azure Monitor (versión preliminar)](logs-data-export.md).
- Exportación única mediante una instancia de Logic Apps. Consulte [Conector de Azure Monitor Logs para Logic Apps y Power Automate](logicapp-flow-connector.md).
- Exportación única a una máquina local mediante un script de PowerShell. Consulte [Invoke-AzOperationalInsightsQueryExport]](https://www.powershellgallery.com/packages/Invoke-AzOperationalInsightsQueryExport).

## <a name="overview"></a>Introducción
En este procedimiento se usa el [conector de Azure Monitor Logs](/connectors/azuremonitorlogs/) que le permite ejecutar una consulta de registro desde una aplicación lógica y usar su salida en otras acciones del flujo de trabajo. En este procedimiento se usa el [conector de Azure Blob Storage](/connectors/azureblob/) para enviar la salida de la consulta a Azure Storage. Las demás acciones se describen en las secciones siguientes.

![Introducción a Logic Apps](media/logs-export-logic-app/logic-app-overview.png)

Al exportar datos desde un área de trabajo de Log Analytics, debe filtrar y agregar los datos de registro y optimizar la consulta a fin de limitar la cantidad de datos procesados por el flujo de trabajo de la instancia de Logic Apps a los datos necesarios. Por ejemplo, si necesita archivar eventos de inicio de sesión, puede filtrar por los eventos necesarios y proyectar solo los campos obligatorios con la consulta siguiente: 

```json
SecurityEvent
| where EventID == 4624 or EventID == 4625
| project TimeGenerated , Account , AccountType , Computer
```

Cuando exporte los datos según una programación, use la función ingestion_time() en la consulta para asegurarse de no perder los datos de llegada tardía. Si los datos se retrasan debido a problemas de red o de plataforma, el uso del tiempo de ingesta garantiza que se incluirán en la ejecución siguiente de la instancia de Logic Apps. Para ver un ejemplo, consulte [Incorporación de una acción de Azure Monitor Logs](#add-azure-monitor-logs-action).

## <a name="prerequisites"></a>Requisitos previos
A continuación, se indican los requisitos previos que se deben cumplir antes de realizar este procedimiento.

- Área de trabajo de Log Analytics. El usuario que crea la aplicación lógica debe tener al menos permiso de lectura en el área de trabajo. 
- Cuenta de Azure Storage. No es necesario que la cuenta de almacenamiento esté en la misma suscripción que el área de trabajo de Log Analytics. El usuario que crea la aplicación lógica debe tener permiso de escritura en la cuenta de almacenamiento. 


## <a name="connector-limits"></a>Límites de conector
El área de trabajo de Log Analytics y las consultas de registro de Azure Monitor son servicios multiinquilino que incluyen límites para proteger y aislar a los clientes y mantener la calidad del servicio. Al consultar una gran cantidad de datos, debe tener en cuenta los límites siguientes que pueden afectar la configuración de la periodicidad de la instancia de Logic Apps y la consulta de registro:

- Las consultas de registro no pueden devolver más de 500 000 de filas.
- Las consultas de registro no pueden devolver más de 64 000 000 de bytes.
- De manera predeterminada, las consultas de registro no se pueden ejecutar durante más de 10 minutos. 
- El conector de Log Analytics está limitado a 100 llamadas por minuto.


## <a name="create-container-in-the-storage-account"></a>Creación de un contenedor en la cuenta de almacenamiento
Use el procedimiento que aparece en [Crear un contenedor](../../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container) para agregar a la cuenta de almacenamiento un contenedor que almacene los datos exportados. El nombre que se usa en el contenedor de este artículo es **loganalytics-data**, pero puede usar el que quiera.


## <a name="create-logic-app"></a>Creación de la aplicación lógica

Vaya a **Logic Apps** en Azure Portal y haga clic en **Agregar**. Seleccione las opciones de **Suscripción**, **Grupo de recursos** y **Región** para almacenar la nueva aplicación lógica y, a continuación, asígnele un nombre único. Puede activar la opción de configuración de **Log Analytics** para recopilar información sobre los eventos y datos en tiempo de ejecución, tal y como se describe en [Configuración de registros de Azure Monitor y recopilación de datos de diagnóstico para Azure Logic Apps](../../logic-apps/monitor-logic-apps-log-analytics.md). Esta configuración no es necesaria para usar el conector de Azure Monitor Logs.

![Creación de la aplicación lógica](media/logs-export-logic-app/create-logic-app.png)


Seleccione **Revisar y crear** y, a continuación, **Crear**. Cuando la implementación finalice, haga clic en **Ir al recurso** para abrir el **Diseñador de aplicaciones lógicas**.

## <a name="create-a-trigger-for-the-logic-app"></a>Creación de un desencadenador para la aplicación lógica
En **Empezar con un desencadenador común**, seleccione **Periodicidad**. Esta acción crea una aplicación lógica que se ejecuta automáticamente a intervalos regulares. En el cuadro **Frecuencia** de la acción, seleccione **Día** y, en el cuadro **Intervalo**, escriba **1** para ejecutar el flujo de trabajo una vez al día.

![Acción de periodicidad](media/logs-export-logic-app/recurrence-action.png)


### <a name="add-azure-monitor-logs-action"></a>Adición de una acción de Azure Monitor Logs
Haga clic en **+ Nuevo paso** para agregar una acción que se ejecute después de la acción de periodicidad. En **Elegir una acción**, escriba **azure monitor** y, a continuación, seleccione **Azure Monitor Logs**.

![Acción de Azure Monitor Logs](media/logs-export-logic-app/select-azure-monitor-connector.png)

Haga clic en **Azure Log Analytics: ejecutar consulta y mostrar resultados**.

![Captura de pantalla de una acción nueva agregada a un paso en el Diseñador de aplicaciones lógicas. Azure Monitor Logs aparece resaltado debajo de Elegir una acción.](media/logs-export-logic-app/select-query-action-list.png)

Se le pedirá seleccionar un inquilino y conceder acceso al área de trabajo de Log Analytics con la cuenta que el flujo de trabajo usará para ejecutar la consulta.


## <a name="add-azure-monitor-logs-action"></a>Adición de una acción de Azure Monitor Logs
La acción de Azure Monitor Logs le permite especificar la consulta que se va a ejecutar. La consulta de registro que se usa en este ejemplo está optimizada para una periodicidad por hora y recopila los datos ingeridos para ese tiempo de ejecución determinado. Por ejemplo, si el flujo de trabajo se ejecuta a las 4:35, el intervalo de tiempo sería de 4:00 a 5:00. Si cambia la instancia de Logic Apps para que se ejecute con una frecuencia distinta, también debe cambiar la consulta. Por ejemplo, si establece la periodicidad para que se ejecute de manera diaria, debería establecer el valor de startTime de la consulta en startofday(make_datetime(year,month,day,0,0)). 

Seleccione las opciones de **Suscripción** y **Grupo de recursos** para su área de trabajo de Log Analytics. Seleccione *Área de trabajo de Log Analytics* en **Tipo de recurso** y, a continuación, seleccione el nombre del área de trabajo en **Nombre del recurso**.

En la ventana **Consulta**, agregue la siguiente consulta de registro.  

```Kusto
let dt = now();
let year = datetime_part('year', dt);
let month = datetime_part('month', dt);
let day = datetime_part('day', dt);
let hour = datetime_part('hour', dt);
let startTime = make_datetime(year,month,day,hour,0)-1h;
let endTime = startTime + 1h - 1tick;
AzureActivity
| where ingestion_time() between(startTime .. endTime)
| project 
    TimeGenerated,
    BlobTime = startTime, 
    OperationName ,
    OperationNameValue ,
    Level ,
    ActivityStatus ,
    ResourceGroup ,
    SubscriptionId ,
    Category ,
    EventSubmissionTimestamp ,
    ClientIpAddress = parse_json(HTTPRequest).clientIpAddress ,
    ResourceId = _ResourceId 
```

El **Intervalo de tiempo** especifica los registros que se incluirán en la consulta en función de la columna **TimeGenerated**. Se debe establecer en un valor igual o mayor que el intervalo de tiempo seleccionado en la consulta. Como esta consulta no usa la columna **TimeGenerated**, la opción **Establecer en la consulta** no está disponible. Consulte [Ámbito de la consulta](./scope.md) para más detalles sobre el intervalo de tiempo. 

Seleccione **Últimas 4 horas** para **Intervalo de tiempo**. Esto asegurará que los registros con un tiempo de ingesta mayor que **TimeGenerated** se incluirán en los resultados.
   
![Captura de pantalla de la configuración de la acción nueva de Azure Monitor Logs denominada Ejecutar consulta y ver resultados.](media/logs-export-logic-app/run-query-list-action.png)


### <a name="add-parse-json-activity-optional"></a>Incorporación de la actividad Analizar JSON (opcional)
La salida de la acción **Ejecutar consulta y mostrar resultados** tiene formato JSON. Puede analizar estos datos y manipularlos como parte de la preparación de la acción **Componer**. 

Puede proporcionar un esquema JSON que describa la carga que espera recibir. El diseñador analiza el contenido JSON mediante este esquema y genera tokens fáciles de usar que representan las propiedades del contenido JSON. Luego puede hacer referencia fácilmente a estas propiedades y utilizarlas a lo largo del flujo de trabajo de la aplicación lógica. 


Haga clic en **+ Nuevo paso** y, a continuación, en **+ Agregar una acción**. En **Elegir una acción**, escriba **json** y, luego, seleccione **Analizar JSON**.

![Selección de la actividad Analizar JSON](media/logs-export-logic-app/select-parse-json.png)

Haga clic en el cuadro **Contenido** para mostrar una lista de valores de las actividades anteriores. Seleccione **Cuerpo** en la acción **Ejecutar consulta y mostrar resultados**. Esta es la salida de la consulta de registro.

[![Seleccionar cuerpo](media/logs-export-logic-app/select-body.png)](media/logs-export-logic-app/select-body.png#lightbox)

5.  Haga clic en **Usar una carga de ejemplo para generar el esquema**. Ejecute la consulta de registro y copie la salida que se va a usar para la carga de ejemplo.  En el caso de la consulta de ejemplo que se muestra aquí, puede usar esta salida:


```json
{
    "TimeGenerated": "2020-09-29T23:11:02.578Z",
    "BlobTime": "2020-09-29T23:00:00Z",
    "OperationName": "Returns Storage Account SAS Token",
    "OperationNameValue": "MICROSOFT.RESOURCES/DEPLOYMENTS/WRITE",
    "Level": "Informational",
    "ActivityStatus": "Started",
    "ResourceGroup": "monitoring",
    "SubscriptionId": "00000000-0000-0000-0000-000000000000",
    "Category": "Administrative",
    "EventSubmissionTimestamp": "2020-09-29T23:11:02Z",
    "ClientIpAddress": "192.168.1.100",
    "ResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/monitoring/providers/microsoft.storage/storageaccounts/my-storage-account"
}
```



![Carga de Analizar JSON](media/logs-export-logic-app/parse-json-payload.png)

## <a name="add-the-compose-action"></a>Incorporación de la acción Componer
La acción **Componer** toma la salida del JSON analizado y crea el objeto que necesita almacenar en el blob.

Haga clic en **+ Nuevo paso** y, a continuación, en **+ Agregar una acción**. En **Elegir una acción**, escriba **componer** y seleccione la acción **Componer**.

![Selección de la acción Componer](media/logs-export-logic-app/select-compose.png)


Haga clic en el cuadro **Entradas** para ver una lista de valores de las actividades anteriores. Seleccione **Cuerpo** en la acción **Analizar JSON**. Esta es la salida analizada de la consulta de registro.

[![Selección del cuerpo para la acción Componer](media/logs-export-logic-app/select-body-compose.png)](media/logs-export-logic-app/select-body-compose.png#lightbox)


## <a name="add-the-create-blob-action"></a>Incorporación de la acción Crear blob
La acción Crear blob escribe el JSON compuesto en el almacenamiento.

Haga clic en **+ Nuevo paso** y, a continuación, en **+ Agregar una acción**. En **Elegir una acción**, escriba **blob** y, luego, seleccione la acción **Crear blob**.

![Selección de Crear blob](media/logs-export-logic-app/select-create-blob.png)

Escriba un nombre para la conexión a la cuenta de almacenamiento en **Nombre de la conexión** y, luego, haga clic en el icono de carpeta en el cuadro **Ruta de acceso a la carpeta** para seleccionar el contenedor de la cuenta de almacenamiento. Haga clic en **Nombre del blob** para ver una lista de valores de las actividades anteriores. Haga clic en **Expresión** y escriba una expresión que coincida con el intervalo de tiempo. Para esta consulta que se ejecuta cada hora, la expresión siguiente establece el nombre del blob según la hora anterior: 

```json
subtractFromTime(formatDateTime(utcNow(),'yyyy-MM-ddTHH:00:00'), 1,'Hour')
```

[![Expresión de blob](media/logs-export-logic-app/blob-expression.png)](media/logs-export-logic-app/blob-expression.png#lightbox)

Haga clic en el cuadro **Contenido del blob** para mostrar una lista de valores de las actividades anteriores y, luego, seleccione **Salidas** en la sección **Componer**.


![Creación de una expresión de blob](media/logs-export-logic-app/create-blob.png)


## <a name="test-the-logic-app"></a>Prueba de la aplicación lógica
Haga clic en **Ejecutar** para probar el flujo de trabajo. Si el flujo de trabajo tiene errores, se indicará en el paso que tiene el problema. Puede ver las ejecuciones y profundizar en cada paso para ver la entrada y la salida a fin de investigar los errores. Si es necesario, consulte [Solución de problemas y diagnóstico de errores de flujo de trabajo en Azure Logic Apps](../../logic-apps/logic-apps-diagnosing-failures.md).

[![Historial de ejecuciones](media/logs-export-logic-app/runs-history.png)](media/logs-export-logic-app/runs-history.png#lightbox)


## <a name="view-logs-in-storage"></a>Visualización de registros en el almacenamiento
Vaya al menú **Cuentas de almacenamiento** de Azure Portal y seleccione su cuenta de almacenamiento. Haga clic en el icono **Blobs** y seleccione el contenedor que especificó en la acción Crear blob. Seleccione uno de los blobs y, luego, **Editar blob**.

[![Datos de blob](media/logs-export-logic-app/blob-data.png)](media/logs-export-logic-app/blob-data.png#lightbox)

## <a name="next-steps"></a>Pasos siguientes

- Más información sobre las [consultas de registro en Azure Monitor](./log-query-overview.md).
- Más información acerca de [Logic Apps](../../logic-apps/index.yml).
- Más información sobre [Power Automate](https://flow.microsoft.com).
