---
title: Registro de actividad de Azure
description: Vea el registro de actividad de Azure y envíelo a los registros de Azure Monitor, Azure Event Hubs y Azure Storage.
author: bwren
services: azure-monitor
ms.topic: conceptual
ms.date: 06/12/2020
ms.author: bwren
ms.openlocfilehash: d9628c9d10818b2b7a8a731b14537e4b533af74e
ms.sourcegitcommit: 6c6b8ba688a7cc699b68615c92adb550fbd0610f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121862100"
---
# <a name="azure-activity-log"></a>Registro de actividad de Azure
El Registro de actividad es un [registro de plataforma](./platform-logs-overview.md) de Azure que proporciona información de los eventos de nivel de suscripción. Esto incluye información como cuándo se modificó un recurso o cuándo se inició una máquina virtual. Puede ver el registro de actividad en Azure Portal o recuperar entradas con PowerShell y la CLI. Para obtener más funciones, debe crear una configuración de diagnóstico para enviar el registro de actividad a los [registros de Azure Monitor](../logs/data-platform-logs.md), a Azure Event Hubs para reenviarlo fuera de Azure o a Azure Storage para archivarlo. En este artículo se proporcionan detalles sobre cómo visualizar el registro de actividad y enviarlo a diversos destinos.

Vea [Creación de una configuración de diagnóstico para enviar registros de plataforma y métricas a diferentes destinos](./diagnostic-settings.md) para obtener más detalles sobre la creación y la configuración de las opciones de diagnóstico.

> [!NOTE]
> Las entradas del registro de actividad son generadas por el sistema y no se pueden cambiar ni eliminar.

## <a name="view-the-activity-log"></a>Visualización del registro de actividad
Puede acceder al registro de actividad desde la mayoría de los menús de Azure Portal. El menú en el que lo abra determinará el filtro inicial. Si lo abre desde el menú **Supervisión**, el único filtro estará en la suscripción. Si lo abre desde el menú de un recurso, el filtro se establecerá en ese recurso. Aun así, siempre puede cambiar el filtro para ver todas las demás entradas. Haga clic en **Agregar filtro** para agregar propiedades adicionales al filtro.

![Visualización del registro de actividad](./media/activity-log/view-activity-log.png)

Para obtener una descripción de las categorías del registro de actividad, vea [Esquema de eventos del registro de actividad de Azure](activity-log-schema.md#categories).

## <a name="download-the-activity-log"></a>Descargar el registro de actividad
Seleccione **Descargar como archivo .csv** para descargar los eventos en la vista actual.

![Descargar registro de actividad](media/activity-log/download-activity-log.png)

### <a name="view-change-history"></a>Visualización del historial de cambios

En el caso de algunos eventos, puede ver el historial de cambios, en el que se muestran los cambios que se han producido durante la hora del evento. Seleccione un evento del registro de actividad del que desee obtener información más detallada. Seleccione la pestaña **Historial de cambios (versión preliminar)** para ver los cambios asociados a ese evento.

![Lista del historial de cambios para un evento](media/activity-log/change-history-event.png)

Si hay cambios asociados con el evento, verá una lista de cambios que puede seleccionar. Se abrirá la página **Historial de cambios (versión preliminar)** . En esta página verá los cambios realizados en el recurso. El siguiente ejemplo, puede ver no solo que la máquina virtual cambió de tamaño, sino también cuál era el tamaño de la máquina virtual antes de ese cambio y a qué cambió. Para obtener más información sobre el historial de cambios, vea [Obtención de los cambios del recurso](../../governance/resource-graph/how-to/get-resource-changes.md).

![Página de historial de cambios que muestra las diferencias](media/activity-log/change-history-event-details.png)


### <a name="other-methods-to-retrieve-activity-log-events"></a>Otros métodos para recuperar eventos del registro de actividad
También puede acceder a los eventos del registro de actividad mediante los métodos siguientes.

- Use el cmdlet [Get-AzLog](/powershell/module/az.monitor/get-azlog) para recuperar el registro de actividad de PowerShell. Vea [Ejemplos de PowerShell de Azure Monitor](../powershell-samples.md#retrieve-activity-log).
- Use [az monitor activity-log](/cli/azure/monitor/activity-log) para recuperar el registro de actividad de la CLI.  Consulte los [ejemplos de CLI de Azure Monitor](../cli-samples.md#view-activity-log).
- Use la [API REST de Azure Monitor](/rest/api/monitor/) para recuperar el registro de actividad de un cliente de REST. 


## <a name="send-to-log-analytics-workspace"></a>Envío al área de trabajo de Log Analytics
 Envíe el registro de actividad a un área de trabajo de Log Analytics para habilitar las características de los [registros de Azure Monitor](../logs/data-platform-logs.md), entre lo que se incluye lo siguiente:

- Correlacionar los datos del registro de actividad con otros datos de supervisión recopilados por Azure Monitor.
- Consolidar las entradas de registro de varias suscripciones e inquilinos de Azure en una ubicación para su análisis conjunto.
- Usar las consultas de registro para realizar un análisis complejo y obtener información detallada sobre las entradas del registro de actividad.
- Usar las alertas de registro con entradas de actividad, lo que permite una lógica de alertas más compleja.
- Almacenar las entradas del registro de actividad durante más de 90 días.
- No se generan cargos por ingesta para los datos del registro de actividad almacenados en un área de trabajo de Log Analytics.
- No se generan cargos por retención de datos hasta un período de 90 días para los datos del registro de actividad almacenados en un área de trabajo de Log Analytics.

[Cree una configuración de diagnóstico](./diagnostic-settings.md) para enviar el registro de actividad a un área de trabajo de Log Analytics. Puede enviar el registro de actividad desde cualquier suscripción única hasta un máximo de cinco áreas de trabajo. 

Los datos del registro de actividad de un área de trabajo de Log Analytics se almacenan en una tabla denominada *AzureActivity* que se puede recuperar con una [consulta de registro](../logs/log-query-overview.md) en [Log Analytics](../logs/log-analytics-tutorial.md). La estructura de esta tabla varía en función de la [categoría de la entrada de registro](activity-log-schema.md). Para obtener una descripción de las propiedades de la tabla, vea la [referencia de datos de Azure Monitor](/azure/azure-monitor/reference/tables/azureactivity).

Por ejemplo, para ver un recuento de las entradas del registro de actividad para cada categoría, use la consulta siguiente.

```kusto
AzureActivity
| summarize count() by CategoryValue
```

Para recuperar todos los registros de la categoría administrativa, use la consulta siguiente.

```kusto
AzureActivity
| where CategoryValue == "Administrative"
```


## <a name="send-to-azure-event-hubs"></a>Envío a Azure Event Hubs
Envíe el registro de actividad a Azure Event Hubs para enviar entradas fuera de Azure, por ejemplo, a un SIEM de terceros o a otras soluciones de Log Analytics. Los eventos del registro de actividad de los centros de eventos se consumen en formato JSON con un elemento `records` que contiene los registros de cada carga. El esquema depende de la categoría y se describe en [Esquema de la cuenta de almacenamiento y los centros de eventos](activity-log-schema.md).

A continuación se muestran datos de salida de ejemplo de Event Hubs para un registro de actividad:

``` JSON
{
    "records": [
        {
            "time": "2019-01-21T22:14:26.9792776Z",
            "resourceId": "/subscriptions/s1/resourceGroups/MSSupportGroup/providers/microsoft.support/supporttickets/115012112305841",
            "operationName": "microsoft.support/supporttickets/write",
            "category": "Write",
            "resultType": "Success",
            "resultSignature": "Succeeded.Created",
            "durationMs": 2826,
            "callerIpAddress": "111.111.111.11",
            "correlationId": "c776f9f4-36e5-4e0e-809b-c9b3c3fb62a8",
            "identity": {
                "authorization": {
                    "scope": "/subscriptions/s1/resourceGroups/MSSupportGroup/providers/microsoft.support/supporttickets/115012112305841",
                    "action": "microsoft.support/supporttickets/write",
                    "evidence": {
                        "role": "Subscription Admin"
                    }
                },
                "claims": {
                    "aud": "https://management.core.windows.net/",
                    "iss": "https://sts.windows.net/72f988bf-86f1-41af-91ab-2d7cd011db47/",
                    "iat": "1421876371",
                    "nbf": "1421876371",
                    "exp": "1421880271",
                    "ver": "1.0",
                    "http://schemas.microsoft.com/identity/claims/tenantid": "00000000-0000-0000-0000-000000000000",
                    "http://schemas.microsoft.com/claims/authnmethodsreferences": "pwd",
                    "http://schemas.microsoft.com/identity/claims/objectidentifier": "2468adf0-8211-44e3-95xq-85137af64708",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn": "admin@contoso.com",
                    "puid": "20030000801A118C",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier": "9vckmEGF7zDKk1YzIY8k0t1_EAPaXoeHyPRn6f413zM",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname": "John",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname": "Smith",
                    "name": "John Smith",
                    "groups": "cacfe77c-e058-4712-83qw-f9b08849fd60,7f71d11d-4c41-4b23-99d2-d32ce7aa621c,31522864-0578-4ea0-9gdc-e66cc564d18c",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name": " admin@contoso.com",
                    "appid": "c44b4083-3bq0-49c1-b47d-974e53cbdf3c",
                    "appidacr": "2",
                    "http://schemas.microsoft.com/identity/claims/scope": "user_impersonation",
                    "http://schemas.microsoft.com/claims/authnclassreference": "1"
                }
            },
            "level": "Information",
            "location": "global",
            "properties": {
                "statusCode": "Created",
                "serviceRequestId": "50d5cddb-8ca0-47ad-9b80-6cde2207f97c"
            }
        }
    ]
}
```


## <a name="send-to--azure-storage"></a>Envío a Azure Storage
Envíe el registro de actividad a una cuenta de Azure Storage si quiere conservar los datos de registro durante más de 90 días para la auditoría, el análisis estático o la copia de seguridad. Si solo necesita conservar los eventos durante 90 días o menos, no es necesario configurar el archivado en una cuenta de almacenamiento, ya que los eventos del registro de actividades se conservan en la plataforma de Azure durante 90 días.

Cuando envíe el registro de actividad a Azure, se creará un contenedor de almacenamiento en la cuenta de almacenamiento en cuanto se produzca un evento. Los blobs del contenedor usan la siguiente convención de nomenclatura:

```
insights-activity-logs/resourceId=/SUBSCRIPTIONS/{subscription ID}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

Por ejemplo, un blob podría tener un nombre similar al siguiente:

```
insights-logs-networksecuritygrouprulecounter/resourceId=/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/y=2020/m=06/d=08/h=18/m=00/PT1H.json
```

Cada blob PT1H.json contiene un blob JSON de eventos que se produjeron en la hora especificada en la dirección URL del blob (por ejemplo h=12). Durante la hora en cuestión, los eventos se anexan al archivo PT1H.json a medida que se producen. El valor en minutos (m = 00) siempre es 00, ya que los eventos del registro de recurso se dividen en blobs individuales por hora.

Cada evento se almacena en el archivo PT1H.json con el siguiente formato que usa un esquema de nivel superior común, pero es único para cada categoría, tal como se describe en [Esquema del registro de actividad](activity-log-schema.md).

``` JSON
{ "time": "2020-06-12T13:07:46.766Z", "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/MY-RESOURCE-GROUP/PROVIDERS/MICROSOFT.COMPUTE/VIRTUALMACHINES/MV-VM-01", "correlationId": "0f0cb6b4-804b-4129-b893-70aeeb63997e", "operationName": "Microsoft.Resourcehealth/healthevent/Updated/action", "level": "Information", "resultType": "Updated", "category": "ResourceHealth", "properties": {"eventCategory":"ResourceHealth","eventProperties":{"title":"This virtual machine is starting as requested by an authorized user or process. It will be online shortly.","details":"VirtualMachineStartInitiatedByControlPlane","currentHealthStatus":"Unknown","previousHealthStatus":"Unknown","type":"Downtime","cause":"UserInitiated"}}}
```


## <a name="legacy-collection-methods"></a>Métodos de recopilación heredados
En esta sección se describen los métodos heredados para recopilar el registro de actividad que se usó antes de la configuración de diagnóstico. Si usa estos métodos, debería considerar la posibilidad de realizar la transición a la configuración de diagnóstico, ya que proporciona una mejor funcionalidad y coherencia con los registros de recursos.

### <a name="log-profiles"></a>Perfiles de registro
Los perfiles de registro son el método heredado para enviar el registro de actividad a Azure Storage o Event Hubs. Use el siguiente procedimiento para seguir trabajando con un perfil de registro o deshabilitarlo como preparación para la migración a una configuración de diagnóstico.

1. En el menú **Azure Monitor** de Azure Portal, seleccione **Registro de actividad**.
3. Haga clic en **Configuración de diagnóstico**.

   ![Configuración de diagnóstico](media/activity-log/diagnostic-settings.png)

4. Haga clic en el banner de color púrpura de la experiencia heredada.

    ![Experiencia heredada](media/activity-log/legacy-experience.png)



### <a name="configure-log-profile-using-powershell"></a>Configuración del perfil de registro mediante PowerShell

Si ya existe un perfil de registro, primero debe quitarlo y luego crear uno nuevo.

1. Use `Get-AzLogProfile` para identificar si existe un perfil de registro.  Si existe un perfil de registro, busque la propiedad *name*.

1. Use `Remove-AzLogProfile` para quitar el perfil de registro mediante el valor de la propiedad *name*.

    ```powershell
    # For example, if the log profile name is 'default'
    Remove-AzLogProfile -Name "default"
    ```

3. Use `Add-AzLogProfile` para crear un nuevo perfil de registro:

    ```powershell
    Add-AzLogProfile -Name my_log_profile -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey -Location global,westus,eastus -RetentionInDays 90 -Category Write,Delete,Action
    ```

    | Propiedad | Obligatorio | Descripción |
    | --- | --- | --- |
    | Nombre |Sí |Nombre de su perfil de registro. |
    | StorageAccountId |No |Identificador de recurso de la cuenta de almacenamiento donde se debe guardar el registro de actividad. |
    | serviceBusRuleId |No |Identificador de regla de Service Bus para el espacio de nombres de Service Bus donde desea que se creen centros de eventos. Se trata de una cadena con este formato: `{service bus resource ID}/authorizationrules/{key name}`. |
    | Location |Sí |Lista separada por comas de las regiones para las que desea recopilar eventos del registro de actividad. |
    | RetentionInDays |Sí |Número de días que deben retenerse los eventos en la cuenta de almacenamiento, entre 1 y 365. Con el valor cero, se almacenan los registros indefinidamente. |
    | Category |No |Lista separada por comas de las categorías de eventos que deben recopilarse. Los valores posibles son _Write_, _Delete_ y _Action_. |

### <a name="example-script"></a>Script de ejemplo
A continuación se muestra un script de ejemplo de PowerShell para crear un perfil de registro que escribe el registro de actividad en una cuenta de almacenamiento y en un centro de eventos.

   ```powershell
   # Settings needed for the new log profile
   $logProfileName = "default"
   $locations = (Get-AzLocation).Location
   $locations += "global"
   $subscriptionId = "<your Azure subscription Id>"
   $resourceGroupName = "<resource group name your event hub belongs to>"
   $eventHubNamespace = "<event hub namespace>"

   # Build the service bus rule Id from the settings above
   $serviceBusRuleId = "/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.EventHub/namespaces/$eventHubNamespace/authorizationrules/RootManageSharedAccessKey"

   # Build the storage account Id from the settings above
   $storageAccountId = "/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.Storage/storageAccounts/$storageAccountName"

   Add-AzLogProfile -Name $logProfileName -Location $locations -StorageAccountId  $storageAccountId -ServiceBusRuleId $serviceBusRuleId
   ```


### <a name="configure-log-profile-using-azure-cli"></a>Configuración del perfil de registro mediante la CLI de Azure

Si ya existe un perfil de registro, primero debe quitar el perfil de registro existente y luego crear un nuevo perfil de registro.

1. Use `az monitor log-profiles list` para identificar si existe un perfil de registro.
2. Use `az monitor log-profiles delete --name "<log profile name>` para quitar el perfil de registro mediante el valor de la propiedad *name*.
3. Use `az monitor log-profiles create` para crear un nuevo perfil de registro:

   ```azurecli-interactive
   az monitor log-profiles create --name "default" --location null --locations "global" "eastus" "westus" --categories "Delete" "Write" "Action"  --enabled false --days 0 --service-bus-rule-id "/subscriptions/<YOUR SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventHub/namespaces/<EVENT HUB NAME SPACE>/authorizationrules/RootManageSharedAccessKey"
   ```

    | Propiedad | Obligatorio | Descripción |
    | --- | --- | --- |
    | name |Sí |Nombre de su perfil de registro. |
    | storage-account-id |Sí |Identificador de recurso de la cuenta de almacenamiento donde se deben guardar los registros de actividades. |
    | locations |Sí |Lista separada por espacios de las regiones para las que desea recopilar eventos del registro de actividad. Puede ver una lista de todas las regiones para la suscripción con `az account list-locations --query [].name`. |
    | days |Sí |Número de días que deben retenerse los eventos, entre 1 y 365. Con el valor cero, se almacenarán los registros indefinidamente (de manera indefinida).  Si el valor es cero, el parámetro enabled se debe establecer en false. |
    |enabled | Sí |True o False.  Se usa para habilitar o deshabilitar la directiva de retención.  Si el valor es True, el parámetro de días debe ser un valor mayor que 0.
    | categories |Sí |Lista separada por espacios de las categorías de eventos que deben recopilarse. Los valores posibles son Write, Delete y Action. |


### <a name="log-analytics-workspace"></a>Área de trabajo de Log Analytics
El método heredado para enviar el registro de actividad en un área de trabajo de Log Analytics está conectando el registro en la configuración del área de trabajo. 

1. Desde el menú **Áreas de trabajo de Log Analytics** en Azure Portal, seleccione el área de trabajo para recopilar el registro de actividad.
1. En la sección **Orígenes de datos del área de trabajo** del menú del área de trabajo, seleccione **Registro de actividad de Azure**.
1. Haga clic en la suscripción que desea conectar.

    ![Captura de pantalla que muestra el área de trabajo de Log Analytics con un registro de actividad de Azure seleccionado.](media/activity-log/workspaces.png)

2. Haga clic en **Conectar** para conectar el registro de actividad en la suscripción al área de trabajo seleccionada. Si la suscripción ya está conectada a otra área de trabajo, haga clic en **Desconectar** primero para desconectarla.

    ![Conexión de áreas de trabajo](media/activity-log/connect-workspace.png)


Para deshabilitar la configuración, realice el mismo procedimiento y haga clic en **Desconectar** para quitar la suscripción del área de trabajo.

### <a name="data-structure-changes"></a>Cambios en la estructura de datos
La configuración de diagnóstico envía los mismos datos que el método heredado que se usa para enviar el registro de actividad con algunos cambios en la estructura de la tabla *AzureActivity*.

Las columnas de la tabla siguiente están en desuso en el esquema actualizado. Todavía existen en *AzureActivity*, pero no tienen datos. El reemplazo de estas columnas no es nuevo, pero contiene los mismos datos que la columna en desuso. Tienen un formato distinto, por lo que es posible que tenga que modificar las consultas de registro que las utilizan. 

| Columna en desuso | Columna de reemplazo |
|:---|:---|
| ActivityStatus    | ActivityStatusValue    |
| ActivitySubstatus | ActivitySubstatusValue |
| Category          | CategoryValue          |
| OperationName     | OperationNameValue     |
| ResourceProvider  | ResourceProviderValue  |

> [!IMPORTANT]
> En algunos casos, los valores de estas columnas pueden estar en mayúsculas. Si tiene una consulta que incluye estas columnas, debe usar el [operador =~](/azure/kusto/query/datatypes-string-operators) para realizar una comparación sin distinguir entre mayúsculas y minúsculas.

La columna siguiente se ha agregado a *AzureActivity* en el esquema actualizado:

- Authorization_d
- Claims_d
- Properties_d

## <a name="activity-log-analytics-monitoring-solution"></a>Solución de supervisión de Activity Log Analytics
La solución de supervisión de Azure Log Analytics entrará en desuso pronto y se reemplazará por un libro que use el esquema actualizado en el área de trabajo de Log Analytics. Puede seguir usando la solución si ya está habilitada, pero solo se puede usar si va a recopilar el registro de actividad mediante la configuración heredada. 



### <a name="use-the-solution"></a>Uso de la solución
A las soluciones de supervisión se accede desde el menú **Supervisar** de Azure Portal. Seleccione **Más** en la sección **Información detallada** para abrir la página **Información general** con los iconos de la solución. El icono **Registros de actividad de Azure** muestra un recuento del número de registros **AzureActivity** del área de trabajo.

![Icono Registros de actividad de Azure](media/activity-log/azure-activity-logs-tile.png)


Haga clic en el icono **Registros de actividad de Azure** para abrir la vista **Registros de actividad de Azure**. La vista incluye los elementos de visualización de la tabla siguiente. Cada uno de ellos muestra hasta otros diez elementos que coinciden con sus criterios para el intervalo de tiempo especificado. Puede ejecutar una consulta de registros que devuelve todos los registros coincidentes; para ello, haga clic en **Ver todo** en la parte inferior del elemento.

![Panel Registros de actividad de Azure](media/activity-log/activity-log-dash.png)


### <a name="enable-the-solution-for-new-subscriptions"></a>Habilitación de la solución para nuevas suscripciones
Pronto dejará de poder agregar la solución de Activity Logs Analytics a la suscripción mediante Azure Portal. Puede agregarlo mediante el siguiente procedimiento con una plantilla de Resource Manager. 

1. Copie el siguiente JSON en un archivo denominado *ActivityLogTemplate*

    ```json
    {
    "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workspaceName": {
            "type": "String",
            "defaultValue": "my-workspace",
            "metadata": {
              "description": "Specifies the name of the workspace."
            }
        },
        "location": {
            "type": "String",
            "allowedValues": [
              "east us",
              "west us",
              "australia central",
              "west europe"
            ],
            "defaultValue": "australia central",
            "metadata": {
              "description": "Specifies the location in which to create the workspace."
            }
        }
      },
        "resources": [
        {
            "type": "Microsoft.OperationalInsights/workspaces",
            "name": "[parameters('workspaceName')]",
            "apiVersion": "2015-11-01-preview",
            "location": "[parameters('location')]",
            "properties": {
                "features": {
                    "searchVersion": 2
                }
            }
        },
        {
            "type": "Microsoft.OperationsManagement/solutions",
            "apiVersion": "2015-11-01-preview",
            "name": "[concat('AzureActivity(', parameters('workspaceName'),')')]",
            "location": "[parameters('location')]",
            "dependsOn": [
                "[resourceId('microsoft.operationalinsights/workspaces', parameters('workspaceName'))]"
            ],
            "plan": {
                "name": "[concat('AzureActivity(', parameters('workspaceName'),')')]",
                "promotionCode": "",
                "product": "OMSGallery/AzureActivity",
                "publisher": "Microsoft"
            },
            "properties": {
                "workspaceResourceId": "[resourceId('microsoft.operationalinsights/workspaces', parameters('workspaceName'))]",
                "containedResources": [
                    "[concat(resourceId('microsoft.operationalinsights/workspaces', parameters('workspaceName')), '/views/AzureActivity(',parameters('workspaceName'))]"
                ]
            }
        },
        {
          "type": "Microsoft.OperationalInsights/workspaces/datasources",
          "kind": "AzureActivityLog",
          "name": "[concat(parameters('workspaceName'), '/', subscription().subscriptionId)]",
          "apiVersion": "2015-11-01-preview",
          "location": "[parameters('location')]",
          "dependsOn": [
              "[parameters('WorkspaceName')]"
          ],
          "properties": {
              "linkedResourceId": "[concat(subscription().Id, '/providers/microsoft.insights/eventTypes/management')]"
          }
        }
      ]
    }    
    ```

2. Implemente la plantilla con los siguientes comandos de PowerShell:

    ```PowerShell
    Connect-AzAccount
    Select-AzSubscription <SubscriptionName>
    New-AzResourceGroupDeployment -Name activitysolution -ResourceGroupName <ResourceGroup> -TemplateFile <Path to template file>
    ```



## <a name="next-steps"></a>Pasos siguientes

* [Introducción a los registros de plataforma Azure](./platform-logs-overview.md)
* [Revisión del esquema de eventos del registro de actividad](activity-log-schema.md)
* [Creación de la configuración de diagnóstico para enviar registros de actividad a otros destinos](./diagnostic-settings.md)
