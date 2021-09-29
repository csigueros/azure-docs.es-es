---
title: Creación de un runbook de PowerShell mediante una identidad administrada de Azure Automation
description: En este tutorial, aprenderá a usar identidades administradas con un runbook de PowerShell en Azure Automation.
services: automation
ms.subservice: process-automation
ms.date: 08/16/2021
ms.topic: tutorial
ms.openlocfilehash: 0620aacb1b4f2a6cb7c6214c1ce92add3bec8f63
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128610681"
---
# <a name="tutorial-create-automation-powershell-runbook-using-managed-identity"></a>Tutorial: Creación de un runbook de PowerShell de Automation mediante una identidad administrada

Este tutorial le guía a través de la creación de un [runbook de PowerShell](../automation-runbook-types.md#powershell-runbooks) en Azure Automation que usa [identidades administradas](../automation-security-overview.md#managed-identities-preview), en lugar de la cuenta de ejecución, para interactuar con los recursos. Los runbooks de PowerShell están basados en Windows PowerShell. Una identidad administrada de Azure Active Directory (Azure AD) permite al runbook acceder fácilmente a otros recursos protegidos por Azure AD.

En este tutorial, aprenderá a:

> [!div class="checklist"]
> * Asignación de permisos a las identidades administradas
> * Creación de un runbook de PowerShell

Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar.

## <a name="prerequisites"></a>Prerrequisitos

* Una cuenta de Azure Automation con al menos una identidad administrada asignada por el usuario. Para más información, consulte [Uso de una identidad administrada asignada por el usuario para una cuenta de Azure Automation](../add-user-assigned-identity.md).
* Los módulos de az `Az.Accounts`, `Az.Automation`, `Az.ManagedServiceIdentity` y `Az.Compute` importados en la cuenta de Automation. Para más información, consulte [Importación de módulos Az](../shared-resources/modules.md#import-az-modules).
* El [módulo Azure Az de PowerShell](/powershell/azure/new-azureps-module-az) instalado en la máquina. Para instalar o actualizar, vea [Instalación del módulo Azure Az de PowerShell](/powershell/azure/install-az-ps).
* Una [máquina virtual de Azure](../../virtual-machines/windows/quick-create-powershell.md). Dado que va a detener e iniciar esta máquina, no debería ser una máquina virtual de producción.
* Familiaridad general con los [runbooks de Automation](../manage-runbooks.md).

## <a name="assign-permissions-to-managed-identities"></a>Asignación de permisos a las identidades administradas

Asigne permisos a las identidades administradas para permitirles detener e iniciar una máquina virtual.

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
    $resourceGroup = "resourceGroupName"
    
    # These values are used in this tutorial
    $automationAccount = "xAutomationAccount"
    $userAssignedManagedIdentity = "xUAMI"
    ```

1. Use el cmdlet de PowerShell [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment) para asignar un rol a la identidad administrada asignada por el sistema.

    ```powershell
    $role1 = "DevTest Labs User"
    
    $SAMI = (Get-AzAutomationAccount -ResourceGroupName $resourceGroup -Name $automationAccount).Identity.PrincipalId
    New-AzRoleAssignment `
        -ObjectId $SAMI `
        -ResourceGroupName $resourceGroup `
        -RoleDefinitionName $role1
    ```

1. Se necesita la misma asignación de roles para la identidad administrada asignada por el usuario.

    ```powershell
    $UAMI = (Get-AzUserAssignedIdentity -ResourceGroupName $resourceGroup -Name $userAssignedManagedIdentity).PrincipalId
    New-AzRoleAssignment `
        -ObjectId $UAMI `
        -ResourceGroupName $resourceGroup `
        -RoleDefinitionName $role1
    ```

1. La identidad administrada asignada por el sistema necesita permisos adicionales para ejecutar los cmdlets `Get-AzUserAssignedIdentity` y `Get-AzAutomationAccount` tal como se usan en este tutorial.

    ```powershell
    $role2 = "Reader"
    New-AzRoleAssignment `
        -ObjectId $SAMI `
        -ResourceGroupName $resourceGroup `
        -RoleDefinitionName $role2
    ```

## <a name="create-powershell-runbook"></a>Creación de runbook de PowerShell

Cree un runbook que permita la ejecución por cualquiera de las identidades administradas. El runbook iniciará una máquina virtual detenida o detendrá una máquina virtual en ejecución.

1. Inicie sesión en [Azure Portal](https://portal.azure.com/) y vaya a la cuenta de Automation.

1. En **Automatización de procesos**, seleccione **Runbooks**.

1. Seleccione **Crear un runbook**.
    1. Asigne al runbook el nombre `miTesting`.
    1. En el menú desplegable **Tipo de Runbook**, seleccione **PowerShell**.
    1. Seleccione **Crear**.

1. En el editor de runbooks, pegue el código siguiente:

    ```powershell
    Param(
     [string]$resourceGroup,
     [string]$VMName,
     [string]$method,
     [string]$UAMI 
    )
    
    $automationAccount = "xAutomationAccount"
    
    # Ensures you do not inherit an AzContext in your runbook
    Disable-AzContextAutosave -Scope Process | Out-Null
    
    # Connect using a Managed Service Identity
    try {
            Connect-AzAccount -Identity -ErrorAction stop -WarningAction SilentlyContinue | Out-Null
        }
    catch{
            Write-Output "There is no system-assigned user identity. Aborting."; 
            exit
        }
    
    # set and store context
    $subID = (Get-AzContext).Subscription.Id
    $AzureContext = Set-AzContext -SubscriptionId $subID
    
    if ($method -eq "SA")
        {
            Write-Output "Using system-assigned managed identity"
        }
    elseif ($method -eq "UA")
        {
            Write-Output "Using user-assigned managed identity"
    
            # Connects using the Managed Service Identity of the named user-assigned managed identity
            $identity = Get-AzUserAssignedIdentity -ResourceGroupName $resourceGroup -Name $UAMI -DefaultProfile $AzureContext
    
            # validates assignment only, not perms
            if ((Get-AzAutomationAccount -ResourceGroupName $resourceGroup -Name $automationAccount -DefaultProfile $AzureContext).Identity.UserAssignedIdentities.Values.PrincipalId.Contains($identity.PrincipalId))
                {
                    Connect-AzAccount -Identity -AccountId $identity.ClientId | Out-Null
    
                    # set and store context
                    $AzureContext = Set-AzContext -SubscriptionId ($identity.id -split "/")[2]
                }
            else {
                    Write-Output "Invalid or unassigned user-assigned managed identity"
                    exit
                }
        }
    else {
            Write-Output "Invalid method. Choose UA or SA."
            exit
         }
    
    # Get current state of VM
    $status = (Get-AzVM -ResourceGroupName $resourceGroup -Name $VMName -Status -DefaultProfile $AzureContext).Statuses[1].Code
    
    Write-Output "`r`n Beginning VM status: $status `r`n"
    
    # Start or stop VM based on current state
    if($status -eq "Powerstate/deallocated")
        {
            Start-AzVM -Name $VMName -ResourceGroupName $resourceGroup -DefaultProfile $AzureContext
        }
    elseif ($status -eq "Powerstate/running")
        {
            Stop-AzVM -Name $VMName -ResourceGroupName $resourceGroup -DefaultProfile $AzureContext -Force
        }
    
    # Get new state of VM
    $status = (Get-AzVM -ResourceGroupName $resourceGroup -Name $VMName -Status -DefaultProfile $AzureContext).Statuses[1].Code  
    
    Write-Output "`r`n Ending VM status: $status `r`n `r`n"
    
    Write-Output "Account ID of current context: " $AzureContext.Account.Id
    ```

1. En el editor, en la línea 8, modifique el valor de la variable `$automationAccount` según sea necesario.

1. Seleccione **Guardar** y, luego, **Panel de prueba**.

1. Rellene los parámetros `RESOURCEGROUP` y `VMNAME` con los valores adecuados. Escriba `SA` en el parámetro `METHOD` y `xUAMI` en el parámetro `UAMI`. El runbook intentará cambiar el estado de energía de la máquina virtual mediante la identidad administrada asignada por el sistema.

1. Seleccione **Inicio**. Una vez completado el runbook, la salida debería ser similar a la siguiente:

    ```output
     Beginning VM status: PowerState/deallocated
    
    OperationId : 5b707401-f415-4268-9b43-be1f73ddc54b
    Status      : Succeeded
    StartTime   : 8/3/2021 10:52:09 PM
    EndTime     : 8/3/2021 10:52:50 PM
    Error       : 
    Name        : 
    
     Ending VM status: PowerState/running 
     
    Account ID of current context: 
    MSI@50342
    ```

1. Cambie el valor del parámetro `METHOD` a `UA`.

1. Seleccione **Inicio**. El runbook intentará cambiar el estado de energía de la máquina virtual mediante la identidad administrada asignada por el usuario con nombre. Una vez completado el runbook, la salida debería ser similar a la siguiente:

    ```output
    Using user-assigned managed identity
    
     Beginning VM status: PowerState/running 
    
    OperationId : 679fcadf-d0b9-406a-9282-66bc211a9fbf
    Status      : Succeeded
    StartTime   : 8/3/2021 11:06:03 PM
    EndTime     : 8/3/2021 11:06:49 PM
    Error       : 
    Name        : 
    
     Ending VM status: PowerState/deallocated 
     
    Account ID of current context: 
    9034f5d3-c46d-44d4-afd6-c78aeab837ea
    ```

## <a name="clean-up-resources"></a>Limpieza de recursos

Para quitar los recursos que ya no son necesarios, ejecute el siguiente runbook.

```powershell
#Remove runbook
Remove-AzAutomationRunbook `
    -ResourceGroupName $resourceGroup `
    -AutomationAccountName $automationAccount `
    -Name "miTesting" `
    -Force

# Remove role assignments
Remove-AzRoleAssignment `
    -ObjectId $UAMI `
    -ResourceGroupName $resourceGroup `
    -RoleDefinitionName $role1

Remove-AzRoleAssignment `
    -ObjectId $SAMI `
    -ResourceGroupName $resourceGroup `
    -RoleDefinitionName $role2

Remove-AzRoleAssignment `
    -ObjectId $SAMI `
    -ResourceGroupName $resourceGroup `
    -RoleDefinitionName $role1
```

## <a name="next-steps"></a>Pasos siguientes

En este tutorial ha creado un [runbook de PowerShell](../automation-runbook-types.md#powershell-runbooks) en Azure Automation que usa [identidades administradas](../automation-security-overview.md#managed-identities-preview), en lugar de la cuenta de ejecución, para interactuar con los recursos. Para ver los runbooks de flujo de trabajo de PowerShell, consulte:

> [!div class="nextstepaction"]
> [Tutorial: Creación de un runbook de flujo de trabajo de PowerShell](automation-tutorial-runbook-textual.md)




