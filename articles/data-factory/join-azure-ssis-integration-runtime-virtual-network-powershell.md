---
title: Unión de una instancia de Azure-SSIS Integration Runtime a una red virtual con Azure PowerShell
description: Aprenda a conectar Azure-SSIS Integration Runtime a una red virtual de Azure mediante Azure PowerShell.
ms.service: data-factory
ms.subservice: integration-services
ms.topic: conceptual
ms.date: 07/16/2021
author: swinarko
ms.author: sawinark
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: b9d264bf4e52ec79a70d6efcd326f395685d12b2
ms.sourcegitcommit: 03e84c3112b03bf7a2bc14525ddbc4f5adc99b85
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/03/2021
ms.locfileid: "129403135"
---
# <a name="join-an-azure-ssis-integration-runtime-to-a-virtual-network-with-azure-powershell"></a>Unión de una instancia de Azure-SSIS Integration Runtime a una red virtual con Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="define-the-variables"></a>Definir las variables

```powershell
$ResourceGroupName = "[your Azure resource group name]"
$DataFactoryName = "[your data factory name]"
$AzureSSISName = "[your Azure-SSIS IR name]"
# Virtual network info: Classic or Azure Resource Manager
$VnetId = "[your virtual network resource ID or leave it empty]" # REQUIRED if you use SQL Database with IP firewall rules/virtual network service endpoints or SQL Managed Instance with private endpoint to host SSISDB, or if you require access to on-premises data without configuring a self-hosted IR. We recommend an Azure Resource Manager virtual network, because classic virtual networks will be deprecated soon.
$SubnetName = "[your subnet name or leave it empty]" # WARNING: Use the same subnet as the one used for SQL Database with virtual network service endpoints, or a different subnet from the one used for SQL Managed Instance with a private endpoint
# Public IP address info: OPTIONAL to provide two standard static public IP addresses with DNS name under the same subscription and in the same region as your virtual network
$FirstPublicIP = "[your first public IP address resource ID or leave it empty]"
$SecondPublicIP = "[your second public IP address resource ID or leave it empty]"
```

## <a name="configure-a-virtual-network"></a>Configuración de una red virtual

Para poder conectar Azure-SSIS Integration Runtime a una red virtual, debe configurar dicha red. Para configurar automáticamente los permisos y valores de la red virtual para que Azure-SSIS Integration Runtime se conecte a la red virtual, agregue el siguiente script:

```powershell
# Make sure to run this script against the subscription to which the virtual network belongs.
if(![string]::IsNullOrEmpty($VnetId) -and ![string]::IsNullOrEmpty($SubnetName))
{
    # Register to the Azure Batch resource provider
    $BatchApplicationId = "ddbf3205-c6bd-46ae-8127-60eb93363864"
    $BatchObjectId = (Get-AzADServicePrincipal -ServicePrincipalName $BatchApplicationId).Id
    Register-AzResourceProvider -ProviderNamespace Microsoft.Batch
    while(!(Get-AzResourceProvider -ProviderNamespace "Microsoft.Batch").RegistrationState.Contains("Registered"))
    {
    Start-Sleep -s 10
    }
    if($VnetId -match "/providers/Microsoft.ClassicNetwork/")
    {
        # Assign the VM contributor role to Microsoft.Batch
        New-AzRoleAssignment -ObjectId $BatchObjectId -RoleDefinitionName "Classic Virtual Machine Contributor" -Scope $VnetId
    }
}
```

## <a name="create-an-azure-ssis-ir-and-join-it-to-a-virtual-network"></a>Creación de una instancia de Integration Runtime para la integración de SSIS en Azure y su unión a una red virtual

Puede crear una instancia de Integration Runtime para la integración de SSIS en Azure y unirla a una red virtual al mismo tiempo. Para ver el script completo y las instrucciones, consulte [Creación de una instancia de Azure-SSIS Integration Runtime](create-azure-ssis-integration-runtime-powershell.md).

## <a name="join-an-existing-azure-ssis-ir-to-a-virtual-network"></a>Unión de una instancia de Integration Runtime para la integración de SSIS en Azure ya existente a una red virtual

El artículo [Creación de una instancia de Azure-SSIS Integration Runtime](create-azure-ssis-integration-runtime.md) muestra cómo crear una instancia de Azure-SSIS Integration Runtime y conectarla a una red virtual en el mismo script. Si ya tiene una instancia de Azure-SSIS IR, siga estos pasos para conectarla a la red virtual: 
1. Detenga la instancia de Integration Runtime de SSIS de Azure. 
1. Configure la instancia de Integration Runtime para la integración de SSIS en Azure para unirla a la red virtual. 
1. Inicie la instancia de Integration Runtime de SSIS de Azure. 

## <a name="stop-the-azure-ssis-ir"></a>Detener la instancia de Integration Runtime de SSIS de Azure

Debe detener Azure-SSIS Integration Runtime para poder conectarlo a una red virtual. Este comando libera todos sus nodos y detiene la facturación:

```powershell
Stop-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $DataFactoryName `
    -Name $AzureSSISName `
    -Force 
```

## <a name="configure-virtual-network-settings-for-the-azure-ssis-ir-to-join"></a>Configuración de la red virtual para que se una la instancia de Integration Runtime para la integración de SSIS en Azure

Para configurar las opciones de la red virtual a la que se conectará Azure-SSIS, use este script: 

```powershell
# Make sure to run this script against the subscription to which the virtual network belongs.
if(![string]::IsNullOrEmpty($VnetId) -and ![string]::IsNullOrEmpty($SubnetName))
{
    # Register to the Azure Batch resource provider
    $BatchApplicationId = "ddbf3205-c6bd-46ae-8127-60eb93363864"
    $BatchObjectId = (Get-AzADServicePrincipal -ServicePrincipalName $BatchApplicationId).Id
    Register-AzResourceProvider -ProviderNamespace Microsoft.Batch
    while(!(Get-AzResourceProvider -ProviderNamespace "Microsoft.Batch").RegistrationState.Contains("Registered"))
    {
        Start-Sleep -s 10
    }
    if($VnetId -match "/providers/Microsoft.ClassicNetwork/")
    {
        # Assign VM contributor role to Microsoft.Batch
        New-AzRoleAssignment -ObjectId $BatchObjectId -RoleDefinitionName "Classic Virtual Machine Contributor" -Scope $VnetId
    }
}
```

## <a name="configure-the-azure-ssis-ir"></a>Configurar la instancia de Integration Runtime de SSIS de Azure

Para conectar Azure-SSIS IR a una red virtual, ejecute el comando `Set-AzDataFactoryV2IntegrationRuntime`: 

```powershell
Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $DataFactoryName `
    -Name $AzureSSISName `
    -VnetId $VnetId `
    -Subnet $SubnetName

# Add public IP address parameters if you bring your own static public IP addresses
if(![string]::IsNullOrEmpty($FirstPublicIP) -and ![string]::IsNullOrEmpty($SecondPublicIP))
{
    $publicIPs = @($FirstPublicIP, $SecondPublicIP)
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
        -DataFactoryName $DataFactoryName `
        -Name $AzureSSISName `
        -PublicIPs $publicIPs
}
```

## <a name="start-the-azure-ssis-ir"></a>Iniciar la instancia de Integration Runtime de SSIS de Azure

Para iniciar Azure-SSIS Integration Runtime, ejecute el siguiente comando: 

```powershell
Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $DataFactoryName `
    -Name $AzureSSISName `
    -Force
```

Este comando tarda entre 20 y 30 minutos en finalizar.

## <a name="next-steps"></a>Pasos siguientes
- [Unión de una instancia de Azure-SSIS Integration Runtime a una red virtual: información general](join-azure-ssis-integration-runtime-virtual-network.md)
- [Detalles de configuración de la red virtual de Azure-SSIS Integration Runtime](azure-ssis-integration-runtime-virtual-network-configuration.md)
- [Unión de una instancia de Azure-SSIS Integration Runtime a una red virtual con la interfaz de usuario de Azure Data Factory Studio](join-azure-ssis-integration-runtime-virtual-network-ui.md)

Para más información acerca de Azure-SSIS IR, consulte los siguientes artículos: 
- [Azure-SSIS IR](concepts-integration-runtime.md#azure-ssis-integration-runtime). En este artículo se proporciona información conceptual general acerca de los entornos de ejecución de integración, incluido Azure-SSIS IR. 
- [Tutorial: Implementación de paquetes SSIS en Azure](./tutorial-deploy-ssis-packages-azure.md). En este tutorial se proporcionan instrucciones paso a paso para crear Azure-SSIS IR. Usa Azure SQL Database para hospedar el catálogo de SSIS. 
- [Creación de una instancia de Azure-SSIS Integration Runtime](create-azure-ssis-integration-runtime.md). En este artículo se amplía el tutorial. Proporciona instrucciones acerca del uso de Azure SQL Database con puntos de conexión de servicio de red virtual o una Instancia administrada de SQL en una red virtual para hospedar el catálogo SSIS. Muestra cómo conectar Azure-SSIS IR a una red virtual. 
- [Monitor an Azure-SSIS IR](monitor-integration-runtime.md#azure-ssis-integration-runtime) (Supervisión de una instancia de Integration Runtime de SSIS de Azure). En este artículo se muestra cómo obtener información acerca de Azure-SSIS IR. Proporciona descripciones del estado de la información devuelta. 
- [Administración de Integration Runtime de SSIS de Azure](manage-azure-ssis-integration-runtime.md). En este artículo se muestra cómo iniciar, detener o eliminar Azure-SSIS IR. También se muestra cómo escalar horizontalmente la instancia de Integration Runtime para la integración de SSIS en Azure mediante la adición de nodos.
