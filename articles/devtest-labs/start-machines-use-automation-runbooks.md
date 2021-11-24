---
title: Inicio de máquinas mediante runbooks de Automation
description: Aprenda a iniciar máquinas virtuales en un laboratorio en Azure DevTest Labs mediante runbooks de Azure Automation.
ms.topic: how-to
ms.date: 06/26/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 24a9b21e9a081a875653a6b368f1ce501b1cb0fb
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/12/2021
ms.locfileid: "132401903"
---
# <a name="start-virtual-machines-in-a-lab-in-order-by-using-azure-automation-runbooks"></a>Iniciar máquinas virtuales en un laboratorio en orden mediante runbooks de Azure Automation
La característica de [inicio automático](devtest-lab-set-lab-policy.md#set-autostart) de DevTest Labs le permite configurar las VM para que se inicien automáticamente a una hora determinada. Sin embargo, esta característica no es compatible con máquinas que se inician en un orden específico. Existen varios escenarios donde sería útil este tipo de automatización.  Un escenario es donde una máquina virtual de Jumpbox de un laboratorio es el punto de acceso a las otras máquinas virtuales. La máquina virtual de Jumpbox debe iniciarse antes que las otras máquinas virtuales. En este artículo se muestra cómo configurar una cuenta de Azure Automation con un runbook de PowerShell que ejecuta un script. El script utiliza etiquetas en las VM del laboratorio para permitirle controlar el orden de inicio sin tener que cambiar el script.

## <a name="setup"></a>Configurar
En este ejemplo, las VM del laboratorio deben tener la etiqueta **StartupOrder** agregada con el valor apropiado, como 0, 1, 2. Designe cualquier máquina que no tenga que iniciarse como -1.

## <a name="create-an-azure-automation-account"></a>Creación de una cuenta de Azure Automation
Cree una cuenta de Azure Automation con las instrucciones que aparecen en [este artículo](../automation/automation-create-standalone-account.md). Elija la opción **Cuentas de ejecución** al crear la cuenta. Una vez creada la cuenta de automation, abra la página **Módulos** y seleccione **Actualizar módulos de Azure** en la barra de menús. Los módulos predeterminados son de varias versiones anteriores y, sin la actualización del script, es posible que no funcionen.

## <a name="add-a-runbook"></a>Agregar un runbook
Ahora, para agregar un runbook a la cuenta de automation, seleccione **Runbooks** en el menú de la izquierda. Seleccione **Agregar un runbook** en el menú y siga las instrucciones para [crear un runbook de PowerShell](../automation/learn/powershell-runbook-managed-identity.md).

## <a name="powershell-script"></a>Script de PowerShell
El script siguiente toma el nombre de la suscripción y el nombre del laboratorio como parámetros. El flujo de script es obtener todas las VM del laboratorio y, a continuación, analizar la información de etiquetas para crear una lista de los nombres de VM y su orden de inicio. El script recorre las VM en orden y las inicia. Si existen varias VM en un número de orden específico, se inician de forma asincrónica mediante trabajos de PowerShell. En el caso de las máquinas virtuales que no tienen una etiqueta, establezca el valor de startup para que sea el último (10). Esas máquinas se inician en último lugar, de manera predeterminada.  Si no desea que la máquina virtual se inicie automáticamente, establezca el valor de etiqueta en 11 y el script omitirá la máquina virtual.

```powershell
#Requires -Version 3.0
#Requires -Module AzureRM.Resources

param
(
    [Parameter(Mandatory=$false, HelpMessage="Name of the subscription that has the lab")]
    [string] $SubscriptionName,

    [Parameter(Mandatory=$false, HelpMessage="Lab name")]
    [string] $LabName
)

# Connect and add the appropriate subscription
$Conn = Get-AutomationConnection -Name AzureRunAsConnection

Add-AzureRMAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationID $Conn.ApplicationId -Subscription $SubscriptionName -CertificateThumbprint $Conn.CertificateThumbprint

# Find the lab
$dtLab = Find-AzResource -ResourceType 'Microsoft.DevTestLab/labs' -ResourceNameEquals $LabName

# Get the VMs
$dtlAllVms = New-Object System.Collections.ArrayList
$AllVMs = Get-AzResource -ResourceId "$($dtLab.ResourceId)/virtualmachines" -ApiVersion 2016-05-15

# Get the StartupOrder tag, if missing set to be run last (10)
ForEach ($vm in $AllVMs) {
    if ($vm.Tags) {
        if ($vm.Tags['StartupOrder']) {
            $startupValue = $vm.Tags['StartupOrder']
        } else {
            $startupValue = 10
        }
        } else {
            $startupValue = 10
        }
        $dtlAllVms.Add(@{$vm.Name = $startupValue}) > $null
}

# Setup for the async multiple vm start

# Save profile
$profilePath = Join-Path $env:Temp "profile.json"
If (Test-Path $profilePath){
    Remove-Item $profilePath
}
Save-AzContext -Path $profilePath

# Job to start VMs asynch
$startVMBlock = {
    Param($devTestLab,$vmToStart,$profilePath)
    Import-AzContext -Path ($profilePath)
    Invoke-AzResourceAction `
        -ResourceId "$($devTestLab.ResourceId)/virtualmachines/$vmToStart" `
        -Action Start `
        -Force
    Write-Output "Started: $vmToStart"
}

$current = 0
# Start in order from 0 to 10

While ($current -le 10) {
# Get the VMs in the current stage
    $tobeStarted = $null
    $tobeStarted = $dtlAllVms | Where-Object { $_.Values -eq $current}
    if ($tobeStarted.Count -eq 1) {
        # Run sync – jobs not necessary for a single VM
        $returnStatus = Invoke-AzResourceAction `
                -ResourceId "$($dtLab.ResourceId)/virtualmachines/$($tobeStarted.Keys)" `
                -Action Start `
                -Force
        Write-Output "$($tobeStarted.Keys) status: $($returnStatus.status)"
    } elseif ($tobeStarted.Count -gt 1) {
        # Start multiple VMs async
        $jobs = @()
        Write-Output "Start Jobs start: $(Get-Date)"
        
        # Jobs
        $jobs += Start-Job -ScriptBlock $startVMBlock -ArgumentList $dtLab, $($singlevm.Keys), $profilePath
        Write-Output "Start Jobs end: $(Get-Date)"
    }

    # Get results from all jobs
    if($jobs.Count -ne 0) {
        Write-Output "Receive Jobs start: $(Get-Date)"
        foreach ($job in $jobs){
            $jobResult = Receive-Job -Job $job -Wait | Write-Output
        }
        Remove-Job -Job $jobs -Force
    }
    else
    {
        Write-Output "Information: No jobs available"
    }
}
```

## <a name="create-a-schedule"></a>Crear una programación
Para que este script se ejecute diariamente, [cree una programación](../automation/shared-resources/schedules.md#create-a-schedule) en la cuenta de automation. Una vez creada la programación, [vincúlela al runbook](../automation/shared-resources/schedules.md#link-a-schedule-to-a-runbook). 

En una situación a gran escala que tiene varias suscripciones con varios laboratorios, almacene la información de parámetros en un archivo para distintos laboratorios. Pase el archivo al script en lugar de pasar los parámetros individuales. El script debe modificarse, pero la ejecución principal es la misma. Aunque este ejemplo Azure Automation para ejecutar el script de PowerShell, existen otras opciones, como usar una tarea en una canalización de compilación o versión.

## <a name="next-steps"></a>Pasos siguientes
Consulte el siguiente artículo para obtener más información sobre Azure Automation: [Introducción a Azure Automation](../automation/automation-intro.md).