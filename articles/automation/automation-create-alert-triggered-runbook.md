---
title: Uso de una alerta para desencadenar un runbook de Azure Automation
description: En este artículo se indica cómo desencadenar un runbook para que se ejecute cuando se genere una alerta de Azure.
services: automation
ms.subservice: process-automation
ms.date: 09/22/2021
ms.topic: how-to
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: ee8d8929dc444d72539893a7978b828fc5c3742d
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/01/2021
ms.locfileid: "129352546"
---
# <a name="use-an-alert-to-trigger-an-azure-automation-runbook"></a>Uso de una alerta para desencadenar un runbook de Azure Automation

Puede usar [Azure Monitor](../azure-monitor/overview.md) para supervisar métricas de nivel básico y registros para la mayoría de los servicios de Azure. Puede llamar a runbooks de Azure Automation mediante [grupos de acciones](../azure-monitor/alerts/action-groups.md) para automatizar tareas basadas en alertas. En este artículo se muestra cómo configurar y ejecutar un runbook mediante alertas.


## <a name="prerequisites"></a>Prerrequisitos

* Una cuenta de Azure Automation con al menos una identidad administrada asignada por el usuario. Para más información, consulte [Uso de una identidad administrada asignada por el usuario para una cuenta de Azure Automation](./add-user-assigned-identity.md).
* Módulos de az: `Az.Accounts` y `Az.Compute` se han importado en la cuenta de Automation. Para más información, consulte [Importación de módulos Az](./shared-resources/modules.md#import-az-modules).
* Una [máquina virtual de Azure](../virtual-machines/windows/quick-create-powershell.md).
* El [módulo Azure Az de PowerShell](/powershell/azure/new-azureps-module-az) instalado en la máquina. Para instalar o actualizar, vea [Instalación del módulo Azure Az de PowerShell](/powershell/azure/install-az-ps).
* Familiaridad general con los [runbooks de Automation](./manage-runbooks.md).

## <a name="alert-types"></a>Tipos de alerta

Puede usar runbooks de Automation con tres tipos de alerta:

* Alertas comunes
* Alertas de registros de actividad
* Alertas de métricas casi en tiempo real

> [!NOTE]
> El esquema de alerta común normaliza la experiencia de consumo de notificaciones de alerta en Azure. Históricamente, los tres tipos de alerta de Azure (métrica, registro y registro de actividad) han tenido sus propias plantillas de correo electrónico, esquemas de webhook, etc. Para más información, consulte [Esquema de alertas comunes](../azure-monitor/alerts/alerts-common-schema.md).

Cuando una alerta llama a un runbook, la llamada real es una solicitud HTTP POST al webhook. El cuerpo de la solicitud POST contiene un objeto con formato JSON que tiene propiedades útiles relacionadas con la alerta. En la tabla siguiente se enumeran vínculos al esquema de carga de cada tipo de alerta:

|Alerta  |Descripción|Esquema de carga  |
|---------|---------|---------|
|[Alerta común](../azure-monitor/alerts/alerts-common-schema.md)|Esquema de alerta común que normaliza la experiencia de consumo de notificaciones de alerta en Azure en la actualidad.|Esquema de carga de alertas comunes.|
|[Alerta de registro de actividad](../azure-monitor/alerts/activity-log-alerts.md) |Envía una notificación cuando cualquier evento nuevo del registro de actividad de Azure coincide con las condiciones específicas. Por ejemplo, cuando una operación `Delete VM` se produce en **myProductionResourceGroup** o cuando aparece un nuevo evento de Azure Service Health con un estado Activo.| [Esquema de carga de alertas de registros de actividad](../azure-monitor/alerts/activity-log-alerts-webhook.md)     |
|[Alertas de métricas casi en tiempo real](../azure-monitor/alerts/alerts-metric-near-real-time.md) | Envía una notificación con más rapidez que las alertas de métrica cuando una o varias métricas de nivel de plataforma cumplen las condiciones especificadas. Por ejemplo, cuando el valor de **% de CPU** en una VM es mayor que 90 y el valor de **Entrada de red** es mayor que 500 MB durante los últimos cinco minutos.| [Esquema de carga de alertas de métricas casi en tiempo real](../azure-monitor/alerts/alerts-webhooks.md#payload-schema)          |

Dado que los datos que cada tipo de alerta proporciona son diferentes, cada tipo de alerta se administra de manera diferente. En la siguiente sección, aprenderá a crear un runbook para administrar distintos tipos de alertas.

## <a name="assign-permissions-to-managed-identities"></a>Asignación de permisos a las identidades administradas

Asigne permisos a la [identidad administrada](./automation-security-overview.md#managed-identities-preview) apropiada para permitirle detener una máquina virtual. El runbook puede usar la identidad administrada asignada por el sistema de la cuenta de Automation o una identidad administrada asignada por el usuario. Se proporcionan los pasos necesarios para asignar permisos a cada identidad. En los pasos siguientes se usa PowerShell. Si prefiere utilizar Azure Portal, consulte [Asignación de roles de Azure mediante Azure Portal](./../role-based-access-control/role-assignments-portal.md).

1. Inicie sesión en Azure de forma interactiva con el cmdlet [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) y siga las instrucciones.

    ```powershell
    # Sign in to your Azure subscription
    $sub = Get-AzSubscription -ErrorAction SilentlyContinue
    if(-not($sub))
    {
        Connect-AzAccount
    }
    
    # If you have multiple subscriptions, set the one to use
    # Select-AzSubscription -SubscriptionId <SUBSCRIPTIONID>
    ```

1. Proporcione un valor adecuado para las variables siguientes y, a continuación, ejecute el script.

    ```powershell
    $resourceGroup = "resourceGroup"
    $automationAccount = "AutomationAccount"
    $userAssignedManagedIdentity = "userAssignedManagedIdentity"
    ```

1. Use el cmdlet de PowerShell [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment) para asignar un rol a la identidad administrada asignada por el sistema.

    ```powershell
    $SAMI = (Get-AzAutomationAccount -ResourceGroupName $resourceGroup -Name $automationAccount).Identity.PrincipalId
    New-AzRoleAssignment `
        -ObjectId $SAMI `
        -ResourceGroupName $resourceGroup `
        -RoleDefinitionName "DevTest Labs User"
    ```

1. Asigne un rol a una identidad administrada asignada por el usuario.

    ```powershell
    $UAMI = (Get-AzUserAssignedIdentity -ResourceGroupName $resourceGroup -Name $userAssignedManagedIdentity)
    New-AzRoleAssignment `
        -ObjectId $UAMI.PrincipalId `
        -ResourceGroupName $resourceGroup `
        -RoleDefinitionName "DevTest Labs User"
    ```

1. Para la identidad administrada asignada por el sistema, muestre `ClientId` y registre el valor para su uso posterior.

   ```powershell
   $UAMI.ClientId
   ```

## <a name="create-a-runbook-to-handle-alerts"></a>Creación de un runbook para administrar las alertas

Para usar Automation con alertas, necesita un runbook que administre la carga de JSON de alerta que se pasa al runbook. El siguiente runbook de ejemplo se debe llamar desde una alerta de Azure.

Como se ha descrito en la sección anterior, cada tipo de alerta tiene un esquema diferente. El script toma los datos del webhook de una alerta del parámetro de entrada del runbook `WebhookData`. Después, el script evalúa la carga de JSON para determinar qué tipo de alerta se usa.

En este ejemplo se usa una alerta de una máquina virtual (VM) de Azure. Los datos de la VM se recuperan de la carga y luego se usa esa información para detener dicha VM. La conexión debe estar configurada en la cuenta de Automation donde se ejecuta el runbook. Al usar alertas para desencadenar runbooks, es importante comprobar el estado de la alerta en el runbook que se desencadena. El runbook se desencadena cada vez que la alerta cambia el estado. Las alertas tienen varios estados, y los dos más comunes son Activado y Resuelto. Compruebe el estado de la lógica del runbook para asegurarse de que no se ejecuta más de una vez. En el ejemplo de este artículo se muestra cómo buscar únicamente alertas con el estado Activado.

El runbook usa la [identidad administrada asignada por el sistema](./automation-security-overview.md#managed-identities-preview) de la cuenta de Automation para autenticarse en Azure, con el fin de realizar la acción de administración en la máquina virtual. El runbook se puede modificar fácilmente para que use una identidad administrada asignada por el usuario.

Use este ejemplo para crear un runbook llamado **AzureVmInResponsetoVMAlert Stop**. Puede modificar el script de PowerShell y usarlo con muchos recursos diferentes.

1. Inicie sesión en [Azure Portal](https://portal.azure.com/) y vaya a la cuenta de Automation.

1. En **Automatización de procesos**, seleccione **Runbooks**.

1. Seleccione **+ Crear un runbook**.
    1. Asigne al runbook el nombre `Stop-AzureVmInResponsetoVMAlert`.
    1. En la lista desplegable **Tipo de Runbook**, seleccione **PowerShell**.
    1. Seleccione **Crear**.

1. En el editor de runbooks, pegue el código siguiente:

    ```powershell
    [OutputType("PSAzureOperationResponse")]
    param
    (
        [Parameter (Mandatory=$false)]
        [object] $WebhookData
    )
    $ErrorActionPreference = "stop"
    
    if ($WebhookData)
    {
        # Get the data object from WebhookData
        $WebhookBody = (ConvertFrom-Json -InputObject $WebhookData.RequestBody)
    
        # Get the info needed to identify the VM (depends on the payload schema)
        $schemaId = $WebhookBody.schemaId
        Write-Verbose "schemaId: $schemaId" -Verbose
        if ($schemaId -eq "azureMonitorCommonAlertSchema") {
            # This is the common Metric Alert schema (released March 2019)
            $Essentials = [object] ($WebhookBody.data).essentials
            # Get the first target only as this script doesn't handle multiple
            $alertTargetIdArray = (($Essentials.alertTargetIds)[0]).Split("/")
            $SubId = ($alertTargetIdArray)[2]
            $ResourceGroupName = ($alertTargetIdArray)[4]
            $ResourceType = ($alertTargetIdArray)[6] + "/" + ($alertTargetIdArray)[7]
            $ResourceName = ($alertTargetIdArray)[-1]
            $status = $Essentials.monitorCondition
        }
        elseif ($schemaId -eq "AzureMonitorMetricAlert") {
            # This is the near-real-time Metric Alert schema
            $AlertContext = [object] ($WebhookBody.data).context
            $SubId = $AlertContext.subscriptionId
            $ResourceGroupName = $AlertContext.resourceGroupName
            $ResourceType = $AlertContext.resourceType
            $ResourceName = $AlertContext.resourceName
            $status = ($WebhookBody.data).status
        }
        elseif ($schemaId -eq "Microsoft.Insights/activityLogs") {
            # This is the Activity Log Alert schema
            $AlertContext = [object] (($WebhookBody.data).context).activityLog
            $SubId = $AlertContext.subscriptionId
            $ResourceGroupName = $AlertContext.resourceGroupName
            $ResourceType = $AlertContext.resourceType
            $ResourceName = (($AlertContext.resourceId).Split("/"))[-1]
            $status = ($WebhookBody.data).status
        }
        elseif ($schemaId -eq $null) {
            # This is the original Metric Alert schema
            $AlertContext = [object] $WebhookBody.context
            $SubId = $AlertContext.subscriptionId
            $ResourceGroupName = $AlertContext.resourceGroupName
            $ResourceType = $AlertContext.resourceType
            $ResourceName = $AlertContext.resourceName
            $status = $WebhookBody.status
        }
        else {
            # Schema not supported
            Write-Error "The alert data schema - $schemaId - is not supported."
        }
    
        Write-Verbose "status: $status" -Verbose
        if (($status -eq "Activated") -or ($status -eq "Fired"))
        {
            Write-Verbose "resourceType: $ResourceType" -Verbose
            Write-Verbose "resourceName: $ResourceName" -Verbose
            Write-Verbose "resourceGroupName: $ResourceGroupName" -Verbose
            Write-Verbose "subscriptionId: $SubId" -Verbose
    
            # Determine code path depending on the resourceType
            if ($ResourceType -eq "Microsoft.Compute/virtualMachines")
            {
                # This is an Resource Manager VM
                Write-Verbose "This is an Resource Manager VM." -Verbose
    
                # Ensures you do not inherit an AzContext in your runbook
                Disable-AzContextAutosave -Scope Process
     
                # Connect to Azure with system-assigned managed identity
                $AzureContext = (Connect-AzAccount -Identity).context
  
                # set and store context
                $AzureContext = Set-AzContext -SubscriptionName $AzureContext.Subscription -DefaultProfile $AzureContext
    
                # Stop the Resource Manager VM
                Write-Verbose "Stopping the VM - $ResourceName - in resource group - $ResourceGroupName -" -Verbose
                Stop-AzVM -Name $ResourceName -ResourceGroupName $ResourceGroupName -DefaultProfile $AzureContext -Force
                # [OutputType(PSAzureOperationResponse")]
            }
            else {
                # ResourceType not supported
                Write-Error "$ResourceType is not a supported resource type for this runbook."
            }
        }
        else {
            # The alert status was not 'Activated' or 'Fired' so no action taken
            Write-Verbose ("No action taken. Alert status: " + $status) -Verbose
        }
    }
    else {
        # Error
        Write-Error "This runbook is meant to be started from an Azure alert webhook only."
    }
    ```

1. Si desea que el runbook se ejecute con la identidad administrada asignada por el sistema, deje el código tal y como está. Si prefiere usar una identidad administrada asignada por el usuario, haga lo siguiente:
    1. En la línea 78, quite `$AzureContext = (Connect-AzAccount -Identity).context`.
    1. Reemplácelo por `$AzureContext = (Connect-AzAccount -Identity -AccountId <ClientId>).context`.
    1. Escriba el identificador de cliente que obtuvo anteriormente.

1. Seleccione **Guardar**, luego **Publicar** y, finalmente, **Sí** cuando se le pida confirmación.

1. Cierre la página **Runbook** para volver a la página **Cuenta de Automation**.

## <a name="create-the-alert"></a>Crear la alerta

Las alertas usan grupos de acciones, que son colecciones de acciones que la alerta desencadena. Los runbooks son simplemente una de las muchas acciones que puede usar con los grupos de acciones.

1. En la cuenta de Automation, en **Supervisión**, seleccione **Alertas**.

1. Seleccione **+ Nueva regla de alertas** para abrir la página **Crear regla de alertas**.

   :::image type="content" source="./media/automation-create-alert-triggered-runbook/create-alert-rule-portal.png" alt-text="La página para crear regla de alerta y sus subsecciones.":::

1. En **Ámbito**, seleccione **Editar recurso**. 

1. En la página **Seleccionar un recurso**, en la lista desplegable **Filtrar por tipo de recurso**, seleccione **Máquinas virtuales**.

1. Active la casilla situada junto a las máquinas virtuales que desea que se supervisen. Luego, seleccione **Listo**, para volver a la página **Crear regla de alertas**.

1. En **Condición**, seleccione **Agregar condición**.

1. En la página **Seleccionar una señal**, escriba `Percentage CPU` en el cuadro de texto de búsqueda y seleccione **Porcentaje de CPU** en los resultados.

1. En la página **Configurar lógica de señal**, en **Valor del umbral**, escriba un valor bajo inicial con fines de prueba, como `5`. Puede volver y actualizar este valor una vez que haya confirmado que la alerta funciona según lo esperado. Luego, seleccione **Listo**, para volver a la página **Crear regla de alertas**.

   :::image type="content" source="./media/automation-create-alert-triggered-runbook/configure-signal-logic-portal.png" alt-text="Especificación del valor del umbral de porcentaje de CPU.":::
 
1. En **Acciones**, seleccione **Agregar grupos de acciones** y después **+ Crear grupo de acciones**.

   :::image type="content" source="./media/automation-create-alert-triggered-runbook/create-action-group-portal.png" alt-text="La página Crear grupo de acciones con la pestaña Aspectos básicos abierta.":::

1. En la página **Crear grupo de acciones**:
    1. En la pestaña **Aspectos básicos**, escriba los valores correspondientes en **Nombre del grupo de acciones** y **Nombre para mostrar**.
    1. En la pestaña **Acciones**, en el cuadro de texto **Nombre**, escriba un nombre. A continuación, en la lista desplegable **Tipo de acción**, seleccione **Runbook de Automation** para abrir la página **Configurar runbook**.
        1. En el elemento **Origen de Runbook**, seleccione **Usuario**.  
        1. En la lista desplegable **Suscripción**, seleccione una suscripción.
        1. En la lista desplegable **Cuenta de Automation**, seleccione su cuenta de Automation.
        1. En la lista desplegable **Runbook**, seleccione **Stop-AzureVmInResponsetoVMAlert**.
        1. En el elemento **Habilitar el esquema de alerta común**, seleccione **Sí**.
        1. Seleccione **Aceptar** para volver a la página **Crear grupo de acciones**.
        
            :::image type="content" source="./media/automation-create-alert-triggered-runbook/configure-runbook-portal.png" alt-text="Configure la página del runbook con los valores adecuados.":::

    1. Seleccione **Revisar y crear** y, después, **Crear** para volver a la página **Crear regla de alertas**.

1. En **Detalles de la regla de alertas**, para el cuadro de texto **Nombre de la regla de alertas**.

1. Seleccione **Crear regla de alertas**.  Puede usar el grupo de acciones en las [alertas de registro de actividad](../azure-monitor/alerts/activity-log-alerts.md) y las [alertas casi en tiempo real](../azure-monitor/alerts/alerts-overview.md) que cree.

## <a name="verification"></a>Comprobación

Asegúrese de que la máquina virtual se está ejecutando. Vaya al runbook **Stop-AzureVmInResponsetoVMAlert** y observe la lista **Trabajos recientes** para rellenarla. Una vez que aparezca un trabajo completado, selecciónelo y revise la salida. Compruebe también si la máquina virtual se ha detenido.

   :::image type="content" source="./media/automation-create-alert-triggered-runbook/job-result-portal.png" alt-text="Se muestra la salida de un trabajo.":::


## <a name="next-steps"></a>Pasos siguientes

* Para descubrir diferentes maneras de iniciar un runbook, vea [Iniciar un runbook](./start-runbooks.md).
* Para crear una alerta de registro de actividad, vea [Creación de alertas del registro de actividad](../azure-monitor/alerts/activity-log-alerts.md).
* Para aprender a crear una alerta casi en tiempo real, consulte [Creación de una regla de alertas en Azure Portal](../azure-monitor/alerts/alerts-metric.md?toc=/azure/azure-monitor/toc.json).

