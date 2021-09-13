---
title: Integración de Azure Automation State Configuration con los registros de Azure Monitor
description: En este artículo se indica cómo enviar datos de los informes de Desired State Configuration desde Azure Automation State Configuration a registros de Azure Monitor.
services: automation
ms.service: automation
ms.subservice: dsc
author: mgoedtel
ms.author: magoedte
ms.date: 08/16/2021
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
manager: carmonm
ms.openlocfilehash: fb968d527bea4b9daeed4e12f0fdea16158d90c4
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/17/2021
ms.locfileid: "122323106"
---
# <a name="integrate-azure-automation-state-configuration-with-azure-monitor-logs"></a>Integración de Azure Automation State Configuration con los registros de Azure Monitor

Azure Automation State Configuration conserva los datos de estado del nodo durante 30 días. Puede enviar los datos de estado del nodo a los [registros de Azure Monitor](../azure-monitor/logs/data-platform-logs.md) si prefiere conservar esos datos durante un período más largo. El estado de cumplimiento se puede ver en Azure Portal, o con PowerShell, tanto para los nodos como para los recursos de DSC individuales en las configuraciones de los nodos.

Los registros de Azure Monitor proporcionan mayor visibilidad operativa para los datos de Automation State Configuration y pueden ayudar a resolver incidentes con mayor rapidez. Con los registros de Azure Monitor, puede:

- Obtener información de cumplimiento de nodos administrados y recursos individuales.
- Desencadenar un correo electrónico o una alerta basados en el estado de cumplimiento.
- Escribir consultas avanzadas en los nodos administrados.
- Correlacionar el estado de cumplimiento en las cuentas de Automation.
- Usar vistas personalizadas y consultas de búsqueda para visualizar los resultados de runbook, el estado del trabajo de runbook y otras métricas o indicadores clave relacionados.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Prerrequisitos

Para empezar a enviar los informes de Automation State Configuration a los registros de Azure Monitor, necesita:

* Instalación del [módulo Az](/powershell/azure/new-azureps-module-az) de PowerShell. Asegúrese de disponer de la versión más reciente. Si es necesario, ejecute `Update-Module -Name Az`.
- Una cuenta de Azure Automation Para más información, consulte [Introducción a Azure Automation](automation-intro.md).
- Un área de trabajo de Log Analytics. Para más información, consulte [Introducción a los registros de Azure Monitor](../azure-monitor/logs/data-platform-logs.md).
- Al menos un nodo de Azure Automation State Configuration. Para más información, consulte [Incorporación de máquinas para administrarlas con State Configuration de Azure Automation](automation-dsc-onboarding.md).
- Módulo [xDscDiagnostics](https://www.powershellgallery.com/packages/xDscDiagnostics/2.7.0.0), versión 2.7.0.0 o posterior. Para conocer los pasos necesarios para la instalación, consulte [Solución de problemas de Desired State Configuration (DSC) de Azure Automation](./troubleshoot/desired-state-configuration.md).

## <a name="set-up-integration-with-azure-monitor-logs"></a>Configuración de la integración con registros de Azure Monitor

Para comenzar a importar datos de Azure Automation State Configuration en los registros de Azure Monitor, haga lo siguiente. Para los pasos a realizar en el portal, consulte [Reenvío de datos de un trabajo de Azure Automation a registros de Azure Monitor](./automation-manage-send-joblogs-log-analytics.md).

1. En la máquina, inicie sesión en la suscripción a Azure con el cmdlet [Connect-AzAccount](/powershell/module/Az.Accounts/Connect-AzAccount) de PowerShell y siga las instrucciones de la pantalla.

    ```powershell
    # Sign in to your Azure subscription
    $sub = Get-AzSubscription -ErrorAction SilentlyContinue
    if(-not($sub))
    {
        Connect-AzAccount
    }
    
    # If you have multiple subscriptions, set the one to use
    # Select-AzSubscription -SubscriptionId "<SUBSCRIPTIONID>"
    ```

1. Proporcione un valor adecuado para las variables `automationAccount` con el nombre real de la cuenta de Automation y `workspaceName` con el nombre real del área de trabajo de Log Analytics. Después, ejecute el script.

    ```powershell
    $automationAccount = "automationAccount"
    $law = "workspaceName"
    ```

1. Para obtener el identificador de recurso de la cuenta de Automation, ejecute los siguientes comandos de PowerShell.

   ```powershell
   # Find the ResourceId for the Automation account
   $AutomationResourceId = (Get-AzResource `
      -ResourceType 'Microsoft.Automation/automationAccounts' | 
      WHERE {$_.Name -eq $automationAccount}).ResourceId
   ```

1. Para obtener el identificador de recurso del área de trabajo de Log Analytics, ejecute los siguientes comandos de PowerShell.

   ```powershell
    # Find the ResourceId for the Log Analytics workspace
    $WorkspaceResourceId = (Get-AzResource `
        -ResourceType 'Microsoft.OperationalInsights/workspaces' | 
        WHERE {$_.Name -eq $law}).ResourceId
   ```

1. Para configurar las opciones de diagnóstico en la cuenta de Automation para reenviar los datos de registro de estado del nodo de DSC a los registros de Azure Monitor, el siguiente cmdlet de PowerShell crea una configuración de diagnóstico con ese destino.

   ```powershell
    Set-AzDiagnosticSetting `
        -ResourceId $AutomationResourceId `
        -WorkspaceId $WorkspaceResourceId `
        -Enabled $true `
        -Category 'DscNodeStatus'
   ```

   Si desea detener el reenvío de datos de registro de Azure Automation State Configuration a los registros de Azure Monitor, ejecute el siguiente cmdlet de PowerShell.

   ```powershell
    Set-AzDiagnosticSetting `
        -ResourceId $AutomationResourceId `
        -WorkspaceId $WorkspaceResourceId `
        -Enabled $false `
        -Category 'DscNodeStatus'
   ```

## <a name="view-the-state-configuration-logs"></a>Visualización de los registros de State Configuration

Puede buscar las operaciones de DSC en los registros de State Configuration buscando en los registros de Azure Monitor. Después de configurar la integración con los registros de Azure Monitor para los datos de Azure Automation State Configuration, vaya a la cuenta de Automation en [Azure Portal](https://portal.azure.com/). En **Supervisión**, seleccione **Registros**.

![Registros](media/automation-dsc-diagnostics/automation-dsc-logs-toc-item.png)

Cierre el cuadro de diálogo **Consultas**. Se abre el panel Búsqueda de registros con una región de consulta cuyo ámbito es el recurso de la cuenta de Automation. Los registros de las operaciones de DSC se almacenan en la tabla `AzureDiagnostics`. Para buscar los nodos que no son compatibles, escriba la siguiente consulta.

```Kusto
AzureDiagnostics
| where Category == "DscNodeStatus"
| where OperationName contains "DSCNodeStatusData"
| where ResultType != "Compliant"
```

Detalles de filtrado:

- Filtre por `DscNodeStatusData` para devolver las operaciones de cada nodo de State Configuration.
- Filtre por `DscResourceStatusData` para devolver las operaciones de cada recurso de DSC llamado en la configuración de nodo aplicada a ese recurso.
- Filtre por `DscResourceStatusData` para devolver información de errores de los recursos de DSC con errores.

Para más información sobre la creación de consultas de registros para buscar datos, consulte [Introducción a las consultas de registro en Azure Monitor](../azure-monitor/logs/log-query-overview.md).

### <a name="send-an-email-when-a-state-configuration-compliance-check-fails"></a>Envío de un correo electrónico cuando se produce un error en cualquier comprobación de cumplimiento de State Configuration

1. Vuelva a la consulta creada anteriormente.

1. Presione el botón "+ Nueva regla de alertas" para iniciar el flujo de creación de alerta.

1. En la consulta siguiente, reemplace `NODENAME` por el nombre real del nodo administrado y pegue la consulta revisada en el cuadro de texto **Consulta de búsqueda**:

    ```kusto
    AzureDiagnostics
    | where Category == "DscNodeStatus"
    | where NodeName_s == "NODENAME"
    | where OperationName == "DscNodeStatusData"
    | where ResultType == "Failed"
    ```

   Si ha configurado registros de más de una cuenta de Automation o suscripción a su área de trabajo, puede agrupar las alertas por suscripción o cuenta de Automation. Derive el nombre de la cuenta de Automation de la propiedad `Resource` en los resultados de la búsqueda de registros de `DscNodeStatusData`.

1. Consulte [Creación, visualización y administración de alertas de métricas mediante Azure Monitor](../azure-monitor/alerts/alerts-metric.md) para completar los pasos restantes.

### <a name="find-failed-dsc-resources-across-all-nodes"></a>Búsqueda de recursos de DSC con errores en todos los nodos

Una ventaja de usar los registros de Azure Monitor es que puede buscar comprobaciones con errores en todos los nodos. Para buscar todas las instancias de recursos de DSC con errores, use la siguiente consulta:

```kusto
AzureDiagnostics 
| where Category == "DscNodeStatus"
| where OperationName == "DscResourceStatusData"
| where ResultType == "Failed"
```

### <a name="view-historical-dsc-node-status"></a>Visualización del historial de estado del nodo de DSC

Para visualizar el historial de estado del nodo de DSC con el paso del tiempo, puede usar esta consulta:

```kusto
AzureDiagnostics 
| where ResourceProvider == "MICROSOFT.AUTOMATION" 
| where Category == "DscNodeStatus"
| where ResultType != "started"
| summarize count() by ResultType
``````

Esta consulta muestra un gráfico del estado del nodo con el paso del tiempo.

## <a name="azure-monitor-logs-records"></a>Registros de Azure Monitor

Diagnósticos de Azure Automation crea dos categorías de registros en los registros de Azure Monitor:

* Datos de estado del nodo (`DscNodeStatusData`)
* Datos de estado del recurso (`DscResourceStatusData`)

### <a name="dscnodestatusdata"></a>DscNodeStatusData

| Propiedad | Descripción |
| --- | --- |
| TimeGenerated |Fecha y hora en que se ejecutó la comprobación de cumplimiento. |
| OperationName |`DscNodeStatusData`. |
| ResultType |Valor que indica si el nodo es conforme. |
| NodeName_s |El nombre del nodo administrado. |
| NodeComplianceStatus_s |Valor de estado que especifica si el nodo es conforme. |
| DscReportStatus |Valor de estado que indica si la comprobación de cumplimiento se ejecutó correctamente. |
| ConfigurationMode | El modo que se usa para aplicar la configuración al nodo. Los valores posibles son: <ul><li>`ApplyOnly`: DSC se aplica la configuración y es lo único que hace, salvo que se inserte una nueva configuración en el nodo de destino o cuando se extrae una nueva configuración de un servidor. Después de la aplicación inicial de una nueva configuración, DSC no comprueba si se ha producido una desviación desde un estado configurado previamente. DSC intenta aplicar la configuración hasta que sea la correcta antes de que el valor de `ApplyOnly` surta efecto. </li><li>`ApplyAndMonitor`: Este es el valor predeterminado. El LCM aplica las nuevas configuraciones. Después de la aplicación inicial de una nueva configuración, si el nodo de destino se desvía del estado deseado, DSC notifica la discrepancia en los registros. DSC intenta aplicar la configuración hasta que sea la correcta antes de que el valor de `ApplyAndMonitor` surta efecto.</li><li>`ApplyAndAutoCorrect`: DSC aplica las nuevas configuraciones. Después de la aplicación inicial de una nueva configuración, si el nodo de destino se desvía del estado deseado, DSC notifica la discrepancia en los registros y vuelve a aplicar la configuración actual.</li></ul> |
| HostName_s | El nombre del nodo administrado. |
| IPAddress | La dirección IPv4 del nodo administrado. |
| Category | `DscNodeStatus`. |
| Resource | El nombre de la cuenta de Azure Automation. |
| Tenant_g | El GUID que identifica al inquilino del autor de la llamada. |
| NodeId_g | GUID que identifica el nodo administrado. |
| DscReportId_g | GUID que identifica el informe. |
| LastSeenTime_t | Fecha y hora en que el informe se vio por última vez. |
| ReportStartTime_t | Fecha y hora en que el informe se inició. |
| ReportEndTime_t | Fecha y hora en que el informe se completó. |
| NumberOfResources_d | El número de recursos de DSC que se llama en la configuración que se aplica al nodo. |
| SourceSystem | El sistema de origen que identifica cómo se han recopilado los datos de los registros de Azure Monitor. Siempre `Azure` para Azure Diagnostics. |
| ResourceId |El identificador de recurso de la cuenta de Azure Automation. |
| ResultDescription | La descripción del recurso de esta operación. |
| SubscriptionId | Identificador de la suscripción de Azure (GUID) para la cuenta de Automation. |
| ResourceGroup | nombre del grupo de recursos de la cuenta de Automation. |
| ResourceProvider | MICROSOFT.AUTOMATION. |
| ResourceType | AUTOMATIONACCOUNTS. |
| CorrelationId | Un GUID que es el identificador de correlación del informe de cumplimiento. |

### <a name="dscresourcestatusdata"></a>DscResourceStatusData

| Propiedad | Descripción |
| --- | --- |
| TimeGenerated |Fecha y hora en que se ejecutó la comprobación de cumplimiento. |
| OperationName |`DscResourceStatusData`.|
| ResultType |Si el recurso es compatible. |
| NodeName_s |El nombre del nodo administrado. |
| Category | DscNodeStatus. |
| Resource | El nombre de la cuenta de Azure Automation. |
| Tenant_g | El GUID que identifica al inquilino del autor de la llamada. |
| NodeId_g |GUID que identifica el nodo administrado. |
| DscReportId_g |GUID que identifica el informe. |
| DscResourceId_s |El nombre de la instancia del recurso de DSC. |
| DscResourceName_s |El nombre del recurso de DSC. |
| DscResourceStatus_s |Si el recurso de DSC es compatible. |
| DscModuleName_s |El nombre del módulo de PowerShell que contiene el recurso de DSC. |
| DscModuleVersion_s |La versión del módulo de PowerShell que contiene el recurso de DSC. |
| DscConfigurationName_s |El nombre de la configuración aplicada al nodo. |
| ErrorCode_s | El código de error si se produjo un error en el recurso. |
| ErrorMessage_s |El mensaje de error si se produjo un error en el recurso. |
| DscResourceDuration_d |El tiempo, en segundos, durante el que se ejecutó el recurso de DSC. |
| SourceSystem | Cómo los registros de Azure Monitor recopilan los datos. Siempre `Azure` para Azure Diagnostics. |
| ResourceId |El identificador de la cuenta de Azure Automation. |
| ResultDescription | La descripción de esta operación. |
| SubscriptionId | Identificador de la suscripción de Azure (GUID) para la cuenta de Automation. |
| ResourceGroup | nombre del grupo de recursos de la cuenta de Automation. |
| ResourceProvider | MICROSOFT.AUTOMATION. |
| ResourceType | AUTOMATIONACCOUNTS. |
| CorrelationId |El GUID que es el identificador de correlación del informe de cumplimiento. |

## <a name="next-steps"></a>Pasos siguientes

- Para obtener información general, vea [Información general de Azure Automation State Configuration](automation-dsc-overview.md).
- Para dar los primeros pasos, consulte [Introducción a Azure Automation State Configuration](automation-dsc-getting-started.md).
- Para aprender a compilar configuraciones de DSC para poder asignarlas a los nodos de destino, consulte [Compilación de configuraciones de DSC en Azure Automation State Configuration](automation-dsc-compile.md).
- Para ver una referencia de los cmdlets de PowerShell, consulte [Az.Automation](/powershell/module/az.automation).
- Para obtener información de precios, consulte [Precios de State Configuration de Azure Automation](https://azure.microsoft.com/pricing/details/automation/).
- Para ver un ejemplo del uso de Azure Automation State Configuration en una canalización de implementación continua, vea [Configuración de la implementación continua con Chocolatey](automation-dsc-cd-chocolatey.md).
- Para aprender a crear diferentes consultas de búsqueda y a revisar los registros de Automation State Configuration con los registros de Azure Monitor, consulte [Búsquedas de registros en los registros de Azure Monitor](../azure-monitor/logs/log-query-overview.md).
- Para más información sobre los registros de Azure Monitor y los orígenes de recopilación de datos, consulte [Introducción a la recopilación de datos de almacenamiento en los registros de Azure Monitor](../azure-monitor/essentials/resource-logs.md#send-to-log-analytics-workspace).
