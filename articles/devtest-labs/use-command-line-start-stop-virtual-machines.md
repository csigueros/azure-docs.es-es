---
title: Uso de las herramientas de la línea de comandos para iniciar y detener máquinas virtuales
description: Aprenda a usar las herramientas de la línea de comandos para iniciar y detener las máquinas virtuales en Azure DevTest Labs.
ms.topic: how-to
ms.date: 10/22/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 58440d00f888816f95aac0159063a7b6d6fc5289
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2021
ms.locfileid: "131430198"
---
# <a name="use-command-line-tools-to-start-and-stop-azure-devtest-labs-virtual-machines"></a>Uso de las herramientas de la línea de comandos para iniciar y detener las máquinas virtuales en Azure DevTest Labs

En este artículo se muestra cómo iniciar o detener máquinas virtuales de laboratorio en Azure DevTest Labs. Puede crear scripts de Azure PowerShell o de la CLI de Azure para automatizar estas operaciones. 

## <a name="prerequisites"></a>Prerrequisitos
- Si usa PowerShell, necesitará tener instalado el [módulo Az](/powershell/azure/new-azureps-module-az) en la estación de trabajo. Asegúrese de disponer de la versión más reciente. Si es necesario, ejecute `Update-Module -Name Az`.

- Si quiere usar la CLI de Azure y todavía no la ha instalado, consulte [Instalación de la CLI de Azure](/cli/azure/install-azure-cli).

- Una máquina virtual en un laboratorio de DevTest Labs.

## <a name="overview"></a>Información general

Azure DevTest Labs ofrece una forma sencilla de crear entornos de desarrollo/pruebas rápidos, sencillos y ligeros. Los laboratorios le permiten administrar los costos, crear rápidamente máquinas virtuales y minimizar los residuos. Puede usar las características de Azure Portal para iniciar y detener automáticamente las máquinas virtuales en momentos concretos. Pero es posible que quiera automatizar el inicio y la detención de máquinas virtuales desde scripts. Estas son algunas situaciones en las que ejecutar estas tareas mediante el uso de scripts sería útil.

- Cuando se usa una aplicación de tres niveles como parte de un entorno de prueba y los niveles se deben iniciar en secuencia. 
- Para apagar una máquina virtual cuando se cumple un criterio personalizado a fin de ahorrar dinero. 
- Como una tarea dentro de un flujo de trabajo de integración continua y entrega continua para comenzar al principio y, después, detener las máquinas virtuales cuando se complete el proceso. Un ejemplo de esto sería la fábrica de imágenes personalizada con Azure DevTest Labs.  

## <a name="azure-powershell"></a>Azure PowerShell

El siguiente script de PowerShell puede iniciar o detener una máquina virtual en un laboratorio. [Invoke-AzResourceAction](/powershell/module/az.resources/invoke-azresourceaction) es el enfoque principal para este script. El parámetro **ResourceId** es el identificador de recurso completo para la máquina virtual del laboratorio. El parámetro **Action** es donde las opciones **Start** o **Stop** se establecen según lo que se necesite.

1. En la estación de trabajo, inicie sesión en la suscripción de Azure con el cmdlet [Connect-AzAccount](/powershell/module/Az.Accounts/Connect-AzAccount) de PowerShell y siga las instrucciones en pantalla.

    ```powershell
    # Sign in to your Azure subscription
    $sub = Get-AzSubscription -ErrorAction SilentlyContinue
    if(-not($sub))
    {
        Connect-AzAccount
    }
    
    # If you have multiple subscriptions, set the one to use
    # Set-AzContext -SubscriptionId "<SUBSCRIPTIONID>"
    ```

1. Proporcione un valor adecuado para las variables y, después, ejecute el script.

    ```powershell
    $devTestLabName = "yourlabname"
    $vMToStart = "vmname"
    
    # The action on the virtual machine (Start or Stop)
    $vmAction = "Start"
    ```

1. Inicie o detenga la máquina virtual en función del valor que haya pasado a `$vmAction`.

    ```powershell
    # Get the lab information
    $devTestLab = Get-AzResource -ResourceType 'Microsoft.DevTestLab/labs' -ResourceName $devTestLabName
    
    # Start or stop the VM and return a succeeded or failed status
    $returnStatus = Invoke-AzResourceAction `
                        -ResourceId "$($devTestLab.ResourceId)/virtualmachines/$vMToStart" `
                        -Action $vmAction `
                        -Force
    
    if ($returnStatus.Status -eq 'Succeeded') {
        Write-Output "##[section] Successfully updated DTL machine: $vMToStart, Action: $vmAction"
    }
    else {
        Write-Error "##[error]Failed to update DTL machine: $vMToStart, Action: $vmAction"
    }
    ```

## <a name="azure-cli"></a>Azure CLI

La [CLI de Azure](/cli/azure/get-started-with-azure-cli) es otra manera de automatizar el inicio y la detención de máquinas virtuales de DevTest Labs. El siguiente script proporciona comandos para iniciar y detener una máquina virtual de un laboratorio. El uso de variables en esta sección se basa en un entorno de Windows. Se necesitarán ligeras variaciones para Bash u otros entornos.

1. Reemplace `SubscriptionID`, `yourlabname`, `yourVM` y `action` por los valores adecuados. A continuación, ejecute el script.

    ```azurecli
    set SUBSCIPTIONID=SubscriptionID
    set DEVTESTLABNAME=yourlabname
    set VMNAME=yourVM
    
    REM The action on the virtual machine (Start or Stop)
    set ACTION=action
    ```

1. Inicie sesión en la suscripción de Azure y obtenga el nombre del grupo de recursos que contiene el laboratorio.

    ```azurecli
    az login
    
    REM If you have multiple subscriptions, set the one to use
    REM az account set --subscription %SUBSCIPTIONID%

    az resource list --resource-type "Microsoft.DevTestLab/labs" --name %DEVTESTLABNAME% --query "[0].resourceGroup"
    ```

1. Reemplace `resourceGroup` por el valor obtenido en el paso anterior. A continuación, ejecute el script.

    ```azurecli
    set RESOURCEGROUP=resourceGroup
    ```

1. Inicie o detenga la máquina virtual en función del valor que haya pasado a `ACTION`.

    ```azurecli
    az lab vm %ACTION% --lab-name %DEVTESTLABNAME% --name %VMNAME% --resource-group %RESOURCEGROUP%
    ```

## <a name="next-steps"></a>Pasos siguientes

Consulte el artículo siguiente para usar Azure Portal para realizar estas operaciones: [Reinicio de una máquina virtual](devtest-lab-restart-vm.md).
