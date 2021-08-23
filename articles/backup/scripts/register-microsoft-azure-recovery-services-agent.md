---
title: 'Muestra de script: registre el servidor Windows local o el equipo cliente en un almacén de Recovery Services'
description: Aprenda a utilizar un script para registrar el servidor Windows local o el equipo cliente en un almacén de Recovery Services.
ms.topic: sample
ms.date: 06/23/2021
ms.openlocfilehash: a35d241ba86868c666916e284fb0700b59034eee
ms.sourcegitcommit: f0168d80eb396ce27032aa02fe9da5a0c10b5af3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/23/2021
ms.locfileid: "112560852"
---
# <a name="powershell-script-to-register-an-on-premises-windows-server-or-a-client-machine-with-recovery-services-vault"></a>Script de PowerShell para registrar el servidor Windows local o el equipo cliente con un almacén de Recovery Services

Este script le ayuda a registrar el servidor Windows local o el equipo cliente en un almacén de Recovery Services. 

## <a name="sample-script"></a>Script de ejemplo

```azurepowershell
<#

.SYNOPSIS
Registers MARS agent

.DESCRIPTION
Registers MARS agent

.ROLE
Administrators

#>
param (
    [Parameter(Mandatory = $true)]
    [String]
    $vaultcredPath,
    [Parameter(Mandatory = $true)]
    [String]
    $passphrase
)
Set-StrictMode -Version 5.0
$env:PSModulePath = (Get-ItemProperty -Path 'HKLM:\System\CurrentControlSet\Control\Session Manager\Environment' -Name PSModulePath).PSModulePath
Import-Module MSOnlineBackup
$ErrorActionPreference = "Stop"
Try {
    $date = Get-Date
    Start-OBRegistration -VaultCredentials $vaultcredPath -Confirm:$false
    $securePassphrase = ConvertTo-SecureString -String $passphrase -AsPlainText -Force
    Set-OBMachineSetting -EncryptionPassphrase $securePassphrase -SecurityPIN " "
}
Catch {
    if ($error[0].ErrorDetails) {
        throw $error[0].ErrorDetails
    }
    throw $error[0]
}

```

## <a name="how-to-execute-the-script"></a>Cómo ejecutar el script

1. Guarde el script anterior en la máquina con el nombre que prefiera y la extensión .ps1.
1. Ejecute el script con los parámetros siguientes:
   - – vaultcredPath: ruta de acceso completa del archivo de credenciales del almacén descargado
   - – frase de contraseña: cadena de texto sin formato convertida en cadena segura mediante el cmdlet [ConvertTo-SecureString](/powershell/module/microsoft.powershell.security/convertto-securestring?view=powershell-7.1&preserve-view=true).

>[!Note]
>También debe proporcionar el PIN de seguridad generado a partir de Azure Portal. Para generar el PIN, vaya a **Configuración** -> **Propiedades** -> **PIN de seguridad** en la hoja almacén de Recovery Services y, a continuación, seleccione **Generar**.

## <a name="next-steps"></a>Pasos siguientes

[Obtenga más información](../backup-client-automation.md) sobre cómo usar PowerShell para implementar y administrar copias de seguridad locales mediante el agente de MARS.

