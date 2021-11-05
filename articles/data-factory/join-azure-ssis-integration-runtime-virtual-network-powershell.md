---
title: Unión de una instancia de entorno de ejecución de integración de Azure-SSIS a una red virtual con Azure PowerShell
description: Descubra cómo unir una instancia de entorno de ejecución de integración de Azure-SSIS a una red virtual con Azure PowerShell.
ms.service: data-factory
ms.subservice: integration-services
ms.topic: conceptual
ms.date: 10/27/2021
author: swinarko
ms.author: sawinark
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: a4d06256407fd42bdfa9f92cc2306df7dd33983c
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131087863"
---
# <a name="join-azure-ssis-integration-runtime-to-a-virtual-network-via-azure-powershell"></a>Unión de una instancia de entorno de ejecución de integración de Azure-SSIS a una red virtual con Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

En este artículo se muestra cómo unir su entorno de ejecución de integración (IR) de Azure-SQL Server Integration Services (SSIS) en Azure Data Factory (ADF) existente a una red virtual mediante Azure PowerShell. 

## <a name="create-variables"></a>Creación de variables

```powershell
$ResourceGroupName = "[your Azure resource group name]"
$DataFactoryName = "[your data factory name]"
$AzureSSISName = "[your Azure-SSIS IR name]"
# Virtual network info: Azure Resource Manager or Classic
$VnetId = "[your virtual network resource ID or leave it empty]" # REQUIRED if you use Azure SQL Database server configured with a private endpoint/IP firewall rule/virtual network service endpoint or Azure SQL Managed Instance that joins a virtual network to host SSISDB, or if you require access to on-premises data without configuring a self-hosted IR. We recommend Azure Resource Manager virtual network, because classic virtual network will be deprecated soon.
$SubnetName = "[your subnet name or leave it empty]" # WARNING: Use the same subnet as the one used for Azure SQL Database server configured with a virtual network service endpoint or a different subnet from the one used for Azure SQL Managed Instance that joins a virtual network
$SubnetId = $VnetId + '/subnets/' + $SubnetName 
# Virtual network injection method: Standard or Express. For comparison, see https://docs.microsoft.com/azure/data-factory/azure-ssis-integration-runtime-virtual-network-configuration.
$VnetInjectionMethod = "Standard" # Standard by default, whereas Express lets you use the express virtual network injection method
# Public IP address info: OPTIONAL to provide two standard static public IP addresses with DNS name under the same subscription and in the same region as your virtual network
$FirstPublicIP = "[your first public IP address resource ID or leave it empty]"
$SecondPublicIP = "[your second public IP address resource ID or leave it empty]"
```

## <a name="configure-a-virtual-network"></a>Configuración de una red virtual

Para poder conectar Azure-SSIS Integration Runtime a una red virtual, debe configurar dicha red. Para configurar automáticamente los permisos y valores de la red virtual para que Azure-SSIS Integration Runtime se una a la red virtual, agregue el siguiente script:

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

El artículo [Creación de una instancia de Azure-SSIS Integration Runtime](create-azure-ssis-integration-runtime-powershell.md) muestra cómo crear una instancia de Azure-SSIS Integration Runtime y conectarla a una red virtual en el mismo script. Si ya tiene una instancia de Azure-SSIS IR, siga estos pasos para unirla a la red virtual: 
1. Detenga su instancia de Azure-SSIS IR. 
1. Configure su instancia Azure-SSIS IR para unirse a una red virtual. 
1. Inicie su instancia de Azure-SSIS IR. 

## <a name="stop-your-azure-ssis-ir"></a>Detenga su instancia de Azure-SSIS IR.

Debe detener su instancia de Azure-SSIS IR para poder realizar la unión a una red virtual. Este comando libera todos sus nodos y detiene la facturación:

```powershell
Stop-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $DataFactoryName `
    -Name $AzureSSISName `
    -Force 
```

## <a name="configure-your-azure-ssis-ir-to-join-a-virtual-network"></a>Configure su instancia Azure-SSIS IR para unirse a una red virtual.

Para conectar Azure-SSIS IR a una red virtual, ejecute el comando `Set-AzDataFactoryV2IntegrationRuntime`: 

```powershell
Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $DataFactoryName `
    -Name $AzureSSISName `
    -SubnetId $SubnetId `
    -VNetInjectionMethod $VnetInjectionMethod

# Add public IP address parameters if you use the standard virtual network injection method and bring your own static public IP addresses
if($VnetInjectionMethod -eq "Standard")
{
    if(![string]::IsNullOrEmpty($FirstPublicIP) -and ![string]::IsNullOrEmpty($SecondPublicIP))
    {
        $publicIPs = @($FirstPublicIP, $SecondPublicIP)
        Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
            -DataFactoryName $DataFactoryName `
            -Name $AzureSSISName `
            -PublicIPs $publicIPs
    }
}
```

## <a name="start-your-azure-ssis-ir"></a>Inicie su instancia de Azure-SSIS IR.

Para iniciar su instancia de Azure-SSIS IR, ejecute el siguiente comando: 

```powershell
Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $DataFactoryName `
    -Name $AzureSSISName `
    -Force
```

Si usa el método de inserción de red virtual express o estándar, este comando tardará entre 5 y 20-30 minutos en finalizar, respectivamente.

## <a name="next-steps"></a>Pasos siguientes

- [Configuración de una red virtual para insertar Azure-SSIS IR](azure-ssis-integration-runtime-virtual-network-configuration.md)
- [Método rápido de inserción de la red virtual](azure-ssis-integration-runtime-express-virtual-network-injection.md)
- [Método estándar de inserción de red virtual](azure-ssis-integration-runtime-standard-virtual-network-injection.md)
- [Unión de Azure-SSIS IR a una red virtual mediante la interfaz de usuario de ADF](join-azure-ssis-integration-runtime-virtual-network-ui.md)

Para más información acerca de Azure-SSIS IR, consulte los siguientes artículos: 

- [Azure-SSIS IR](concepts-integration-runtime.md#azure-ssis-integration-runtime). En este artículo se proporciona información conceptual general acerca de los entornos de ejecución de integración, incluido Azure-SSIS IR. 
- [Tutorial: Implementación de paquetes SSIS en Azure](tutorial-deploy-ssis-packages-azure.md). En este tutorial se proporcionan instrucciones paso a paso para crear Azure-SSIS IR. Usa el servidor de Azure SQL Database para hospedar SSISDB. 
- [Creación de una instancia de Azure-SSIS Integration Runtime](create-azure-ssis-integration-runtime.md). En este artículo se amplía el tutorial. Proporciona instrucciones sobre el uso de un servidor de Azure SQL Database configurado con un punto de conexión de servicio de red virtual, una regla de firewall de IP, un punto de conexión privado o Azure SQL Managed Instance que se conecta a una red virtual para hospedar SSISDB. Muestra cómo conectar Azure-SSIS IR a una red virtual. 
- [Monitor an Azure-SSIS IR](monitor-integration-runtime.md#azure-ssis-integration-runtime) (Supervisión de una instancia de Integration Runtime de SSIS de Azure). En este artículo se muestra cómo recuperar y comprender la información acerca de Azure-SSIS IR.
- [Administración de Integration Runtime de SSIS de Azure](manage-azure-ssis-integration-runtime.md). En este artículo se muestra cómo iniciar, detener o eliminar Azure-SSIS IR. También se muestra cómo escalar horizontalmente la instancia de Azure SSIS IR mediante la adición de más nodos.
