---
title: Migración a Azure Firewall Prémium
description: Obtenga información sobre cómo migrar de Azure Firewall Estándar a Azure Firewall Premium.
author: vhorne
ms.service: firewall
services: firewall
ms.topic: how-to
ms.date: 09/13/2021
ms.author: victorh
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 580dcb11ae04aaae78d2c15f24c2c08d1df6158d
ms.sourcegitcommit: 48500a6a9002b48ed94c65e9598f049f3d6db60c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/26/2021
ms.locfileid: "129061853"
---
# <a name="migrate-to-azure-firewall-premium"></a>Migración a Azure Firewall Prémium

Puede migrar Azure Firewall Estándar a Azure Firewall Premium para aprovechar las ventajas de las nuevas funcionalidades premium. Para obtener más información sobre las características de Azure Firewall Premium, consulte [Características de Azure Firewall Premium](premium-features.md).

En los dos ejemplos siguientes se muestra cómo:
- Migrar una directiva estándar existente mediante Azure PowerShell
- Migrar un firewall estándar existente (con reglas clásicas) a Azure Firewall Prémium con una directiva prémium

Si usa Terraform para implementar Azure Firewall, puede usar Terraform para migrar a Azure Firewall Prémium. Para más información, consulte [Migración de Azure Firewall estándar a prémium con Terraform](/azure/developer/terraform/firewall-upgrade-premium?toc=/azure/firewall/toc.json&bc=/azure/firewall/breadcrumb/toc.json).

## <a name="performance-considerations"></a>Consideraciones de rendimiento

El rendimiento es un aspecto que se debe tener en cuenta al migrar desde la SKU estándar. La inspección de IDPS y TLS son operaciones que consumen numerosos recursos informáticos. La SKU premium usa una SKU de máquina virtual más potente que se escala a un rendimiento máximo de 30 Gbps comparable a la SKU estándar. El rendimiento de 30 Gbps se admite cuando se configura con IDPS en modo de alerta. El uso de IDPS en modo de denegación y la inspección de TLS aumentan el consumo de CPU. Puede producirse una degradación al máximo rendimiento. 

El rendimiento del firewall puede ser inferior a 30 Gbps si tiene una o más firmas establecidas en **Alertar y denegar** o reglas de aplicación con la **inspección de TLS** habilitada. Microsoft recomienda a sus clientes realizar pruebas a escala completa en su implementación de Azure para asegurarse de que el rendimiento del servicio de firewall cumpla sus expectativas.

## <a name="downtime"></a>Tiempo de inactividad

Migre el firewall durante un tiempo de mantenimiento planeado, ya que habrá cierto tiempo de inactividad durante la migración.

## <a name="migrate-an-existing-policy-using-azure-powershell"></a>Migrar una directiva existente con Azure PowerShell

`Transform-Policy.ps1` es un script de Azure PowerShell que crea una directiva prémium a partir de una directiva estándar existente.

Dado un identificador de directiva de firewall estándar, el script lo transforma en una directiva de Azure Firewall Prémium. El script se conecta primero a su cuenta de Azure, extrae la directiva, transforma o agrega varios parámetros y, luego, carga una nueva directiva prémium. La nueva directiva prémium se denomina `<previous_policy_name>_premium`. En caso de la transformación de una directiva secundaria, se mantendrá el vínculo a la directiva principal.

Ejemplo de uso:

`Transform-Policy -PolicyId /subscriptions/XXXXX-XXXXXX-XXXXX/resourceGroups/some-resource-group/providers/Microsoft.Network/firewallPolicies/policy-name`

> [!IMPORTANT]
> El script no migra los valores de configuración de inteligencia sobre amenazas. Esos valores deberá anotarlos antes de continuar y migrarlos manualmente.

```azurepowershell
<#
    .SYNOPSIS
        Given an Azure firewall policy id the script will transform it to a Premium Azure firewall policy. 
        The script will first pull the policy, transform/add various parameters and then upload a new premium policy. 
        The created policy will be named <previous_policy_name>_premium if no new name provided else new policy will be named as the parameter passed.  
    .Example
        Transform-Policy -PolicyId /subscriptions/XXXXX-XXXXXX-XXXXX/resourceGroups/some-resource-group/providers/Microsoft.Network/firewallPolicies/policy-name -NewPolicyName <optional param for the new policy name>
#>

param (
    #Resource id of the azure firewall policy. 
    [Parameter(Mandatory=$true)]
    [string]
    $PolicyId,

    #new filewallpolicy name, if not specified will be the previous name with the '_premium' suffix
    [Parameter(Mandatory=$false)]
    [string]
    $NewPolicyName = ""
)
$ErrorActionPreference = "Stop"
$script:PolicyId = $PolicyId
$script:PolicyName = $NewPolicyName

function ValidatePolicy {
    [CmdletBinding()]
    param (
        [Parameter(Mandatory=$true)]
        [Object]
        $Policy
    )

    Write-Host "Validating resource is as expected"

    if ($null -eq $Policy) {
        Write-Error "Recived null policy"
        exit(1)
    }
    if ($Policy.GetType().Name -ne "PSAzureFirewallPolicy") {
        Write-Host "Resource must be of type Microsoft.Network/firewallPolicies" -ForegroundColor Red
        exit(1)
    }

    if ($Policy.Sku.Tier -eq "Premium") {
        Write-Host "Policy is already premium"
        exit(1)
    }
}

function GetPolicyNewName {
    [CmdletBinding()]
    param (
        [Parameter(Mandatory=$true)]
        [Microsoft.Azure.Commands.Network.Models.PSAzureFirewallPolicy]
        $Policy
    )

    if (-not [string]::IsNullOrEmpty($script:PolicyName)) {
        return $script:PolicyName
    }

    return $Policy.Name + "_premium"
}

function TransformPolicyToPremium {
    [CmdletBinding()]
    param (
        [Parameter(Mandatory=$true)]
        [Microsoft.Azure.Commands.Network.Models.PSAzureFirewallPolicy]
        $Policy
    )    
    $NewPolicyParameters = @{
                        Name = (GetPolicyNewName -Policy $Policy) 
                        ResourceGroupName = $Policy.ResourceGroupName 
                        Location = $Policy.Location 
                        ThreatIntelMode = $Policy.ThreatIntelMode 
                        BasePolicy = $Policy.BasePolicy.Id
                        DnsSetting = $Policy.DnsSettings 
                        Tag = $Policy.Tag 
                        SkuTier = "Premium" 
    }

    Write-Host "Creating new policy"
    $premiumPolicy = New-AzFirewallPolicy @NewPolicyParameters

    Write-Host "Populating rules in new policy"
    foreach ($ruleCollectionGroup in $Policy.RuleCollectionGroups) {
        $ruleResource = Get-AzResource -ResourceId $ruleCollectionGroup.Id
        $ruleToTransfom = Get-AzFirewallPolicyRuleCollectionGroup -AzureFirewallPolicy $Policy -Name $ruleResource.Name
        $ruleCollectionGroup = @{
            FirewallPolicyObject = $premiumPolicy
            Priority = $ruleToTransfom.Properties.Priority
            Name = $ruleToTransfom.Name
        }

        if ($ruleToTransfom.Properties.RuleCollection.Count) {
            $ruleCollectionGroup["RuleCollection"] = $ruleToTransfom.Properties.RuleCollection
        }

        Set-AzFirewallPolicyRuleCollectionGroup @ruleCollectionGroup
    }
}

function ValidateAzNetworkModuleExists {
    Write-Host "Validating needed module exists"
    $networkModule = Get-InstalledModule -Name "Az.Network" -ErrorAction SilentlyContinue
    if (($null -eq $networkModule) -or ($networkModule.Version -lt 4.5.0)){
        Write-Host "Please install Az.Network module version 4.5.0 or higher, see instructions: https://github.com/Azure/azure-powershell#installation"
        exit(1)
    }
    Import-Module Az.Network -MinimumVersion 4.5.0
}

ValidateAzNetworkModuleExists
$policy = Get-AzFirewallPolicy -ResourceId $script:PolicyId
ValidatePolicy -Policy $policy
TransformPolicyToPremium -Policy $policy

```

## <a name="migrate-an-existing-standard-firewall-using-the-azure-portal"></a>Migración de un firewall estándar existente mediante Azure Portal

En este ejemplo se muestra cómo usar Azure Portal para migrar un firewall estándar (reglas clásicas) a Azure Firewall Prémium con una directiva prémium.

1. En Azure Portal, seleccione el firewall estándar. En la página **Información general**, seleccione **Migrate to firewall policy** (Migrar a la directiva de firewall).

   :::image type="content" source="media/premium-migrate/firewall-overview-migrate.png" alt-text="Migrate to firewall policy (Migrar a la directiva de firewall)":::

1. En la página **Migrate to firewall policy** (Migrar a la directiva de firewall), seleccione **Revisar y crear**.
1. Seleccione **Crear**.

   La implementación tarda unos minutos en completarse.
1. Use el [script de Azure PowerShell](#migrate-an-existing-policy-using-azure-powershell) `Transform-Policy.ps1` para transformar esta nueva directiva estándar en una directiva prémium.
1. En el portal, seleccione su recurso de firewall estándar. 
1. En **Automatización**, seleccione **Exportar plantilla**. Deje esta pestaña del explorador abierta. Volveremos a ella más adelante.
   > [!TIP]
   > Para no perder la plantilla, descárguela y guárdela en caso de que la pestaña del explorador se cierre o actualice.
1. Abra una nueva pestaña del explorador, vaya a Azure Portal y abra el grupo de recursos que contiene el firewall.
1. Elimine la instancia de firewall estándar existente.

   Este procedimiento tarda unos minutos en completarse.

1. Vuelva a la pestaña del explorador con la plantilla exportada.
1. Seleccione **Implementar** y, en la página **Implementación personalizada**, seleccione **Editar plantilla**.
1. Edite la plantilla de texto:
   
   1. En el recurso `Microsoft.Network/azureFirewalls`, en `Properties`, `sku`, cambie el valor de `tier` de "Estándar" a "Prémium".
   1. En la plantilla `Parameters`, cambie `defaultValue` por `firewallPolicies_FirewallPolicy_,<your policy name>_externalid` en:
      
       `"/subscriptions/<subscription id>/resourceGroups/<your resource group>/providers/Microsoft.Network/firewallPolicies/FirewallPolicy_<your policy name>"`

      a:

      `"/subscriptions/<subscription id>/resourceGroups/<your resource group>/providers/Microsoft.Network/firewallPolicies/FirewallPolicy_<your policy name>_premium"`
1. Seleccione **Guardar**.
1. Seleccione **Revisar + crear**.
1. Seleccione **Crear**.

Cuando finalice la implementación, podrá configurar todas las nuevas características de Azure Firewall Premium.

## <a name="next-steps"></a>Pasos siguientes

- [Más información sobre las características de Azure Firewall Prémium](premium-features.md)
