---
title: Envío de un correo electrónico desde un runbook de Azure Automation
description: En este artículo se indica cómo enviar un correo electrónico desde un runbook.
services: automation
ms.subservice: process-automation
ms.date: 09/21/2021
ms.topic: how-to
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 3f781b32c000ec42c876fa61a90d9ef70c3eb01c
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/01/2021
ms.locfileid: "129354559"
---
# <a name="send-an-email-from-am-automation-runbook"></a>Envío de un correo electrónico desde un runbook de Azure Automation

Puede enviar un correo electrónico desde un runbook con [SendGrid](https://sendgrid.com/solutions) mediante PowerShell. 

Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar.

## <a name="prerequisites"></a>Requisitos previos

* [Una cuenta de SendGrid](https://docs.sendgrid.com/for-developers/partners/microsoft-azure-2021#create-a-sendgrid-account).
* Comprobación del remitente de SendGrid. [Dominio o remitente único](https://sendgrid.com/docs/for-developers/sending-email/sender-identity/) 
* La [clave de API de SendGrid](https://docs.sendgrid.com/for-developers/partners/microsoft-azure-2021#to-find-your-sendgrid-api-key).

* Una cuenta de Azure Automation con al menos una identidad administrada asignada por el usuario. Para más información, consulte [Habilitación de identidades administradas](./quickstarts/enable-managed-identity.md).
* Módulos de az: `Az.Accounts` y `Az.KeyVault` se han importado en la cuenta de Automation. Para más información, consulte [Importación de módulos Az](./shared-resources/modules.md#import-az-modules).
* El [módulo Azure Az de PowerShell](/powershell/azure/new-azureps-module-az) instalado en la máquina. Para instalar o actualizar, vea [Instalación del módulo Azure Az de PowerShell](/powershell/azure/install-az-ps).

## <a name="create-an-azure-key-vault"></a>Crear una instancia de Azure Key Vault

Cree una instancia de Azure Key Vault y una [directiva de acceso de Key Vault](../key-vault/general/assign-access-policy-portal.md) que permita a la credencial obtener y establecer los secretos del almacén de claves especificado.

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
    $resourceGroup = "<Resource group>"
    $automationAccount = "<Automation account>"
    $region = "<Region>"
    $SendGridAPIKey = "<SendGrid API key>"
    $VaultName = "<A universally unique vault name>"

    $userAssignedManagedIdentity = "<User-assigned managed identity>"
    ```

1. Creación del almacén de claves y asignación de permisos

    ```powershell
    # Create the new key vault
    $newKeyVault = New-AzKeyVault `
        -VaultName $VaultName `
        -ResourceGroupName $resourceGroup `
        -Location $region

    $resourceId = $newKeyVault.ResourceId
    
    # Convert the SendGrid API key into a SecureString
    $Secret = ConvertTo-SecureString -String $SendGridAPIKey `
        -AsPlainText -Force

    Set-AzKeyVaultSecret -VaultName $VaultName `
        -Name 'SendGridAPIKey' `
        -SecretValue $Secret
    
    # Grant Key Vault access to the Automation account's system-assigned managed identity.
    $SA_PrincipalId = (Get-AzAutomationAccount `
        -ResourceGroupName $resourceGroup `
        -Name $automationAccount).Identity.PrincipalId

    Set-AzKeyVaultAccessPolicy `
        -VaultName $vaultName `
        -ObjectId $SA_PrincipalId `
        -PermissionsToSecrets Set, Get

    # Grant Key Vault access to the user-assigned managed identity.
    $UAMI = Get-AzUserAssignedIdentity `
        -ResourceGroupName $resourceGroup `
        -Name $userAssignedManagedIdentity

    Set-AzKeyVaultAccessPolicy `
        -VaultName $vaultName `
        -ObjectId $UAMI.PrincipalId `
        -PermissionsToSecrets Set, Get
    ```

   Para ver otras formas de crear un almacén de Azure Key Vault y almacenar un secreto, consulte los [inicios rápidos de Key Vault](../key-vault/index.yml).

## <a name="assign-permissions-to-managed-identities"></a>Asignación de permisos a las identidades administradas

Asigne permisos a la [identidad administrada](./automation-security-overview.md#managed-identities-preview) adecuada. El runbook puede usar la identidad administrada asignada por el sistema de la cuenta de Automation o una identidad administrada asignada por el usuario. Se proporcionan los pasos necesarios para asignar permisos a cada identidad. En los pasos siguientes se usa PowerShell. Si prefiere utilizar Azure Portal, consulte [Asignación de roles de Azure mediante Azure Portal](./../role-based-access-control/role-assignments-portal.md).

1. Use el cmdlet de PowerShell [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment) para asignar un rol a la identidad administrada asignada por el sistema.

    ```powershell
    New-AzRoleAssignment `
        -ObjectId $SA_PrincipalId `
        -ResourceGroupName $resourceGroup `
        -RoleDefinitionName "Reader"
    ```

1. Asigne un rol a una identidad administrada asignada por el usuario.

    ```powershell
    New-AzRoleAssignment `
        -ObjectId $UAMI.PrincipalId`
        -ResourceGroupName $resourceGroup `
        -RoleDefinitionName "Reader"
    ```

1. Para la identidad administrada asignada por el sistema, muestre `ClientId` y registre el valor para su uso posterior.

   ```powershell
   $UAMI.ClientId
   ```

## <a name="create-the-runbook-to-send-an-email"></a>Creación del runbook para enviar un correo electrónico

Después de crear un almacén de claves y de almacenar la clave de API de `SendGrid`, es el momento de crear el runbook que recupere la clave de API y envíe un correo electrónico. Vamos a usar un runbook que utiliza la [identidad administrada asignada por el sistema](./automation-security-overview.md#managed-identities-preview) para autenticarse en Azure con el fin de recuperar el secreto de Azure Key Vault. Llamaremos al runbook **Send-GridMailMessage**. Puede modificar el script de PowerShell utilizado para distintos escenarios.

1. Inicie sesión en [Azure Portal](https://portal.azure.com) y vaya a su cuenta de Automation.

1. En la página de la cuenta de Automation abierta, en **Automatización de procesos**, seleccione **Runbooks**.

1. Seleccione **+ Crear un runbook**.
    1. Asigne al runbook el nombre `Send-GridMailMessage`.
    1. En la lista desplegable **Tipo de Runbook**, seleccione **PowerShell**.
    1. Seleccione **Crear**.

   ![Crear Runbook](./media/automation-send-email/automation-send-email-runbook.png)

1. Se crea el runbook y se abre la página Editar Runbook de PowerShell.
   ![Edición del runbook](./media/automation-send-email/automation-send-email-edit.png)

1. Copie el siguiente ejemplo de PowerShell en la página Editar. Asegúrese de que `VaultName` especifique el nombre que haya elegido para la instancia de Key Vault.

    ```powershell
    Param(
      [Parameter(Mandatory=$True)]
      [String] $destEmailAddress,
      [Parameter(Mandatory=$True)]
      [String] $fromEmailAddress,
      [Parameter(Mandatory=$True)]
      [String] $subject,
      [Parameter(Mandatory=$True)]
      [String] $content,
      [Parameter(Mandatory=$True)]
      [String] $ResourceGroupName
    )

    # Ensures you do not inherit an AzContext in your runbook
    Disable-AzContextAutosave -Scope Process
    
    # Connect to Azure with system-assigned managed identity
    $AzureContext = (Connect-AzAccount -Identity).context

    # set and store context
    $AzureContext = Set-AzContext -SubscriptionName $AzureContext.Subscription -DefaultProfile $AzureContext 

    $VaultName = "<Enter your vault name>"

    $SENDGRID_API_KEY = Get-AzKeyVaultSecret `
        -VaultName $VaultName `
        -Name "SendGridAPIKey" `
        -AsPlainText -DefaultProfile $AzureContext

    $headers = New-Object "System.Collections.Generic.Dictionary[[String],[String]]"
    $headers.Add("Authorization", "Bearer " + $SENDGRID_API_KEY)
    $headers.Add("Content-Type", "application/json")

    $body = @{
    personalizations = @(
        @{
            to = @(
                    @{
                        email = $destEmailAddress
                    }
            )
        }
    )
    from = @{
        email = $fromEmailAddress
    }
    subject = $subject
    content = @(
        @{
            type = "text/plain"
            value = $content
        }
    )
    }

    $bodyJson = $body | ConvertTo-Json -Depth 4

    $response = Invoke-RestMethod -Uri https://api.sendgrid.com/v3/mail/send -Method Post -Headers $headers -Body $bodyJson
    ```

1. Si desea que el runbook se ejecute con la identidad administrada asignada por el sistema, deje el código tal y como está. Si prefiere usar una identidad administrada asignada por el usuario, haga lo siguiente:
    1. En la línea 18, elimine `$AzureContext = (Connect-AzAccount -Identity).context`.
    1. Reemplácelo por `$AzureContext = (Connect-AzAccount -Identity -AccountId <ClientId>).context`.
    1. Escriba el identificador de cliente que obtuvo anteriormente.

1. Seleccione **Guardar**, luego **Publicar** y, finalmente, **Sí** cuando se le pida confirmación.

Para comprobar que el runbook se ejecuta correctamente, puede seguir los pasos descritos en [Prueba de un runbook](manage-runbooks.md#test-a-runbook) o de [Inicio de un runbook](start-runbooks.md).

Si no ve inicialmente el correo electrónico de prueba, compruebe las carpetas de **correo no deseado** y de **spam**.

## <a name="clean-up-resources"></a>Limpieza de recursos

1. Cuando ya no sea necesario el runbook, selecciónelo en la lista de runbooks y seleccione **Eliminar**.

1. Elimine el almacén de claves mediante el cmdlet [Remove-AzKeyVault](/powershell/module/az.keyvault/remove-azkeyvault).

    ```powershell
    $VaultName = "<your KeyVault name>"
    $resourceGroup = "<your ResourceGroup name>"
    Remove-AzKeyVault -VaultName $VaultName -ResourceGroupName $resourceGroup
    ```

## <a name="next-steps"></a>Pasos siguientes

* Para enviar datos de trabajo de runbook al área de trabajo de Log Analytics, consulte [Reenvío de datos de un trabajo de Azure Automation a registros de Azure Monitor](automation-manage-send-joblogs-log-analytics.md).
* Para supervisar los registros y las métricas de nivel básico, consulte [Uso de una alerta para desencadenar un runbook de Azure Automation](automation-create-alert-triggered-runbook.md).
