---
title: Uso de una plantilla de Azure Resource Manager para crear un entorno de ejecución de integración
description: Obtenga información sobre cómo usar Azure Resource Manager para crear un entorno de ejecución de integración de Azure SSIS en Azure Data Factory para que pueda implementar y ejecutar paquetes SSIS en Azure.
ms.service: data-factory
ms.subservice: integration-services
ms.topic: conceptual
ms.date: 09/27/2021
author: swinarko
ms.author: sawinark
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 3825639ce9ffdf568fdb6500b1826a006b035c72
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2021
ms.locfileid: "129219408"
---
# <a name="use-an-azure-resource-manager-template-to-create-an-integration-runtime"></a>Uso de una plantilla de Azure Resource Manager para crear un entorno de ejecución de integración

En esta sección, se usa una plantilla de Azure Resource Manager para crear un entorno de ejecución de integración de Azure SSIS. 

## <a name="sample-azure-resource-manager-template"></a>Plantilla de Azure Resource Manager de ejemplo

Los pasos siguientes permiten crear un entorno de ejecución de integración Azure-SSIS con una plantilla de Azure Resource Manager:

1. Cree un archivo JSON con la siguiente plantilla de Azure Resource Manager. Reemplace los valores de los corchetes angulares (marcadores de posición) por sus propios valores.

    ```json
    {
        "contentVersion": "1.0.0.0",
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "parameters": {},
        "variables": {},
        "resources": [{
            "name": "<Specify a name for your data factory>",
            "apiVersion": "2018-06-01",
            "type": "Microsoft.DataFactory/factories",
            "location": "East US",
            "properties": {},
            "resources": [{
                "type": "integrationruntimes",
                "name": "<Specify a name for your Azure-SSIS IR>",
                "dependsOn": [ "<The name of the data factory you specified at the beginning>" ],
                "apiVersion": "2018-06-01",
                "properties": {
                    "type": "Managed",
                    "typeProperties": {
                        "computeProperties": {
                            "location": "East US",
                            "nodeSize": "Standard_D8_v3",
                            "numberOfNodes": 1,
                            "maxParallelExecutionsPerNode": 8
                        },
                        "ssisProperties": {
                            "catalogInfo": {
                                "catalogServerEndpoint": "<Azure SQL Database server name>.database.windows.net",
                                "catalogAdminUserName": "<Azure SQL Database server admin username>",
                                "catalogAdminPassword": {
                                    "type": "SecureString",
                                    "value": "<Azure SQL Database server admin password>"
                                },
                                "catalogPricingTier": "Basic"
                            }
                        }
                    }
                }
            }]
        }]
    }
    ```

2. Para implementar la plantilla de Resource Manager, ejecute el comando `New-AzResourceGroupDeployment`, tal y como se muestra en el ejemplo siguiente. En el ejemplo, `ADFTutorialResourceGroup` es el nombre del grupo de recursos. `ADFTutorialARM.json` es el archivo que contiene la definición de JSON de la factoría de datos y del entorno de ejecución de integración de Azure SSIS.

    ```powershell
    New-AzResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile ADFTutorialARM.json
    ```

    Este comando crea la factoría de datos y la instancia de Azure-SSIS IR en ella, pero no inicia el entorno de ejecución de integración.

3. Para iniciar Azure-SSIS IR, ejecute el comando `Start-AzDataFactoryV2IntegrationRuntime`:

    ```powershell
    Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName "<Resource Group Name>" `
        -DataFactoryName "<Data Factory Name>" `
        -Name "<Azure SSIS IR Name>" `
        -Force
    ```

> [!NOTE]
> Aparte del tiempo de configuración personalizada, este proceso debería realizarse en 5 minutos. Sin embargo, Azure-SSIS IR puede tardar entre 20 y 30 minutos en unirse a una red virtual.
>
> Si usa SSISDB, el servicio Data Factory se conectará al servidor de bases de datos para prepararlo. También configura los permisos o los valores de la red virtual, si se especifica, y une la instancia de Azure-SSIS IR a la red virtual.
> 
> Al aprovisionar Azure-SSIS IR, también se instalan el acceso redistribuible y el paquete de característica de Azure para SSIS. Estos componentes proporcionan conectividad a archivos de Excel y Access, y a diversos orígenes de datos de Azure, además de a aquellos que ya admiten los componentes integrados. Para obtener más información sobre los componentes integrados o preinstalados, vea el artículo [Componentes integrados y preinstalados en Azure-SSIS Integration Runtime](./built-in-preinstalled-components-ssis-integration-runtime.md). Para obtener más información sobre los componentes adicionales que puede instalar, consulte [Personalización de la instalación en una instancia de Azure-SSIS Integration Runtime](./how-to-configure-azure-ssis-ir-custom-setup.md).

## <a name="next-steps"></a>Pasos siguientes

- [Más información sobre cómo aprovisionar Azure-SSIS IR mediante Azure Portal](create-azure-ssis-integration-runtime-portal.md).
- [Más información sobre cómo aprovisionar Azure-SSIS IR mediante Azure PowerShell](create-azure-ssis-integration-runtime-powershell.md).
- [Implementación y ejecución de los paquetes SSIS en Azure Data Factory](create-azure-ssis-integration-runtime-deploy-packages.md).

Consulte otros temas sobre Azure-SSIS IR en esta documentación:

- [Integration Runtime de SSIS de Azure](concepts-integration-runtime.md#azure-ssis-integration-runtime). En este artículo se proporciona información sobre los entornos de ejecución de integración en general, incluido Azure-SSIS IR.
- [Monitor an Azure-SSIS IR](monitor-integration-runtime.md#azure-ssis-integration-runtime) (Supervisión de una instancia de Integration Runtime de SSIS de Azure). En este artículo se muestra cómo recuperar y comprender la información acerca de Azure-SSIS IR.
- [Administración de Integration Runtime de SSIS de Azure](manage-azure-ssis-integration-runtime.md). En este artículo se muestra cómo iniciar, detener o eliminar Azure-SSIS IR. También se muestra cómo escalar horizontalmente la instancia de Azure SSIS IR mediante la adición de más nodos.
- [Implementación, ejecución y supervisión de paquetes de SSIS en Azure](/sql/integration-services/lift-shift/ssis-azure-deploy-run-monitor-tutorial)   
- [Conexión a SSISDB en Azure](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database)
- [Connect to on-premises data sources with Windows authentication](/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth) (Conexión a orígenes de datos locales con autenticación de Windows) 
- [Programación de la ejecución de paquetes en Azure](/sql/integration-services/lift-shift/ssis-azure-schedule-packages)
