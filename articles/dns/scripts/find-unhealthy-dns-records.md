---
title: 'Búsqueda de registros DNS incorrectos en Azure DNS: ejemplo de script de PowerShell'
description: En este artículo, aprenderá a usar un script de Azure PowerShell para buscar registros DNS incorrectos.
author: asudbring
ms.author: allensu
ms.date: 11/10/2021
ms.topic: sample
ms.service: dns
ms.openlocfilehash: 09afc7a599004d5623eb7c4fb869528fe669e0e1
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/11/2021
ms.locfileid: "132350133"
---
# <a name="find-unhealthy-dns-records-in-azure-dns---powershell-script-sample"></a>Búsqueda de registros DNS incorrectos en Azure DNS: ejemplo de script de PowerShell

El siguiente script de Azure PowerShell busca registros DNS incorrectos en Azure DNS.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

```azurepowershell-interactive
<#
    1.  Install Pre requisites Az PowerShell modules  (https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-5.7.0)
    2.  From PowerShell prompt navigate to folder where the script is saved and run the following command
        .\ Get-AzDNSUnhealthyRecords.ps1 -SubscriptionId <subscription id> -ZoneName <zonename>
        Replace subscription id with subscription id of interest.
        ZoneName with actual zone name.
#>
param(
    # subscription if to fetch dns records from
    [String]$SubscriptionId = "All",

    #filtering zone name
    [String]$ZoneName = "All"
) 

if ($SubscriptionId -eq "All") {
    Write-Host -ForegroundColor Yellow "No subscription Id passed will process all subscriptions"
}

if ($ZoneName -eq "All") {
    Write-Host -ForegroundColor Yellow "No Zone name passed will process all zones in subscription"
}

$ErrorActionPreference = "Stop"

$AZModules = @('Az.Accounts', 'Az.Dns')
$AzLibrariesLoadStart = Get-Date
$progressItr = 1; 
$ProgessActivity = "Loading required Modules";
$StoreWarningPreference = $WarningPreference
$WarningPreference = 'SilentlyContinue'
Foreach ($module in $AZModules) {
    $progressValue = $progressItr / $AZModules.Length
    Write-Progress -Activity $ProgessActivity -Status "$module $($progressValue.ToString('P')) Complete:" -PercentComplete ($progressValue * 100)

    If (Get-Module -Name $module) {
        continue
    }
    elseif (Get-Module -ListAvailable -Name $module) {
        Import-Module -name $module -Scope Local -Force
    }
    else {
        Install-module -name $module -AllowClobber -Force -Scope CurrentUser
        Import-Module -name $module -Scope Local -Force
    }

    $progressItr = $progressItr + 1;
    If (!$(Get-Module -Name $module)) {
        Write-Error "Could not load dependant module: $module"
        throw
    }
}
$WarningPreference = $StoreWarningPreference
Write-Progress -Activity $ProgessActivity -Completed

$context = Get-AzContext;
if ($context.TokenCache -eq $null) {
    Write-host -ForegroundColor Yellow "Please Login to Azure Account using Login-AzAccount and run the script."
    exit
} 
$subscriptions = Get-AzSubscription

if ($SubscriptionId -ne "All") {
    $subscriptions = $subscriptions | Where-Object { $_.Id -eq $SubscriptionId }
    if ($subscriptions.Count -eq 0) {
        Write-host -ForegroundColor Yellow "Provided Subscription Id not found exiting."
        exit
    }
}

$scount = $subscriptions | Measure-Object
Write-Host "Subscriptions found $($scount.Count)"
if ($scount.Count -lt 1) {
    exit
}
$InvalidItems = @()
$TotalRecCount = 0;
$ProgessActivity = "Processing Subscriptions";
$progressItr = 1; 
$subscriptions | ForEach-Object {
    $progressValue = $progressItr / $scount.Count

    Select-AzSubscription -Subscription $_  | Out-Null
    Write-Progress -Activity $ProgessActivity -Status "current subscription $_  $($progressValue.ToString('P')) Complete:" -PercentComplete ($progressValue * 100)
    $progressItr = $progressItr + 1;
    $subscription = $_ 
    try {
        $dnsZones = Get-AzDnsZone -ErrorAction Continue
    }
    catch {
        Write-Host "Error retrieving DNS Zones for subscription $_"
        return;
    }

    if ($ZoneName -ne "All") {
        $dnsZones = $dnsZones | Where-Object { $_.Name -eq $ZoneName }
        if ($dnsZones.Count -eq 0) {
            Write-host -ForegroundColor Yellow "Provided ZoneName $ZoneName not found in Subscription $_."
            return;
        }
    }

    $dnsZones |  ForEach-Object {
        $allrecs = Get-AzDnsRecordSet  -Zone $_
        $sZoneName = $_.Name
        $nsrecords = $allrecs | Where-Object { $_.RecordType -eq "NS" }
        $records = $allrecs | Where-Object { ($_.RecordType -ne 'NS' ) -or ($_.Name -ne '@' ) }
        $records | ForEach-Object {    
            $rec = $_
            $Invalid = $false
            $endsWith = "*$($rec.Name)"           
            $nsrecords | ForEach-Object { if ($endsWith -like "*.$($_.Name)") { $Invalid = $true } }
            $TotalRecCount++
            if ($Invalid) {
                Write-Host -ForegroundColor Yellow "$($rec.Name) recordType $($rec.RecordType) zoneName $sZoneName subscription $subscription" 
                $hash = @{
                    Name           = $rec.Name
                    RecordType     = $rec.RecordType
                    ZoneName       = $sZoneName
                    subscriptionId = $subscription
                }
                $item = New-Object PSObject -Property $hash    
                $InvalidItems += $item
            }
            else {
                # Write-Host -ForegroundColor Green "$($rec.Name) recordType $($rec.RecordType)  zoneName $ZoneName  subscription $subscription " 
            }
        }
    }
}
Write-Progress -Activity $ProgessActivity -Completed

Write-Host "Total records processed $TotalRecCount"
$invalidMeasure = $InvalidItems | Measure-Object
Write-Host "Invalid Count  $($invalidMeasure.Count)"

Write-Host "Invalid Records "
Write-Host "==============="

$InvalidItems | Format-Table

```

## <a name="script-explanation"></a>Explicación del script

Este script usa los siguientes comandos para crear la implementación. Cada elemento de la tabla incluye vínculos a la documentación específica del comando.

| Get-Help | Notas |
|---|---|
| [Get-AzDnsZone](/powershell/module/az.dns/get-azdnszone) | Recuperación de una zona DNS. |
| [Get-AzDnsRecordSet](/powershell/module/az.dns/get-azdnsrecordset) | Recuperación de un conjunto de registros de DNS. |

## <a name="next-steps"></a>Pasos siguientes

Para obtener más información sobre el módulo de Azure PowerShell, consulte la [documentación de Azure PowerShell](/powershell/azure/).