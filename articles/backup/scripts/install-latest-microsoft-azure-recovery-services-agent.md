---
title: Script de muestra. Instale el agente MARS más reciente en los servidores Windows locales.
description: Obtenga información sobre cómo usar un script para instalar el agente MARS más reciente en los servidores Windows locales en una cuenta de almacenamiento.
ms.topic: sample
ms.date: 06/23/2021
ms.openlocfilehash: 1080149d33eebed160449865d58f422e6eba36b1
ms.sourcegitcommit: f0168d80eb396ce27032aa02fe9da5a0c10b5af3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/23/2021
ms.locfileid: "112560870"
---
# <a name="powershell-script-to-install-the-latest-mars-agent-on-an-on-premises-windows-server"></a>Script de PowerShell para instalar el agente MARS más reciente en un servidor Windows local

Este script le ayuda a instalar el agente MARS más reciente en el servidor Windows local.

## <a name="sample-script"></a>Script de ejemplo

```azurepowershell
<#

.SYNOPSIS
Sets MARS agent

.DESCRIPTION
Sets MARS agent

.ROLE
Administrators

#>
Set-StrictMode -Version 5.0
$ErrorActionPreference = "Stop"
Try {
    $agentPath = $env:TEMP + '\MARSAgentInstaller.exe'
    Invoke-WebRequest -Uri 'https://aka.ms/azurebackup_agent' -OutFile $agentPath
    & $agentPath /q | out-null

    $env:PSModulePath = (Get-ItemProperty -Path 'HKLM:\System\CurrentControlSet\Control\Session Manager\Environment' -Name PSModulePath).PSModulePath
    $azureBackupModuleName = 'MSOnlineBackup'
    $azureBackupModule = Get-Module -ListAvailable -Name $azureBackupModuleName
    if ($azureBackupModule) {
        $true
    }
    else {
        $false
    }
}
Catch {
    if ($error[0].ErrorDetails) {
        throw $error[0].ErrorDetails
    }
    throw $error[0]
}

```

## <a name="next-steps"></a>Pasos siguientes

[Obtenga más información](../backup-client-automation.md) sobre cómo usar PowerShell para implementar y administrar copias de seguridad locales mediante el agente de MARS.