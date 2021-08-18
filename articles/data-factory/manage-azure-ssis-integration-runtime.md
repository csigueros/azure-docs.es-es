---
title: Reconfiguración de un entorno de ejecución para la integración de SSIS en Azure
description: Aprenda a reconfigurar una instancia de Integration Runtime de SSIS de Azure en Azure Data Factory después de haberlo aprovisionado.
ms.service: data-factory
ms.subservice: integration-services
ms.topic: conceptual
ms.date: 06/03/2020
author: swinarko
ms.author: sawinark
ms.openlocfilehash: f163087133e6273752663b9bffa9da4a6fb19879
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121730132"
---
# <a name="reconfigure-the-azure-ssis-integration-runtime"></a>Reconfiguración de un entorno de ejecución para la integración de SSIS en Azure

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

En este artículo se describe cómo reconfigurar un entorno de ejecución existente para la integración de SSIS en Azure. Para crear una instancia de Integration Runtime (IR) para la integración de SSIS en Azure Data Factory, vea [Creación de una instancia de Integration Runtime para la integración de SSIS en Azure](create-azure-ssis-integration-runtime.md).  

## <a name="data-factory-ui"></a>Interfaz de usuario de Data Factory 
Puede usar la interfaz de usuario de Data Factory para detener, editar y volver a configurar o eliminar un entorno de ejecución de integración de SSIS de Azure. 

1. Para abrir la interfaz de usuario de Data Factory, seleccione el icono **Author & Monitor** (Creación y supervisión) de la página principal de la factoría de datos.
2. Seleccione el centro **Administrar** debajo de los centros **Inicio**, **Editar** y **Supervisar** para mostrar el panel **Conexiones**.

### <a name="to-reconfigure-an-azure-ssis-ir"></a>Para reconfigurar un entorno de ejecución de integración de SSIS de Azure
En el panel **Connections** (Conexiones) del **centro de administración**, cambie a la página **Integration Runtimes** (Entornos de ejecución de integración) y seleccione **Refresh** (Actualizar). 

   ![Panel Connections (Conexiones)](./media/tutorial-create-azure-ssis-runtime-portal/connections-pane.png)

   Puede editar y volver a configurar su instancia de Azure-SSIS IR; para ello, seleccione su nombre. También puede seleccionar los botones pertinentes para supervisar, iniciar, detener o eliminar su instancia de Azure-SSIS IR; puede generar automáticamente una canalización de ADF con la actividad Ejecutar paquete de SSIS para que se ejecute en su instancia de Azure-SSIS IR; y puede ver el código o la carga JSON de Azure-SSIS IR.  Solo puede editar o eliminar su instancia de Azure-SSIS IR si está detenida.

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Después de aprovisionar e iniciar una instancia de Integration Runtime de SSIS de Azure, puede volver a configurarla mediante la ejecución de una secuencia de los cmdlets `Stop` - `Set` - `Start` de PowerShell de forma consecutiva. Por ejemplo, el siguiente script de PowerShell cambia el número de nodos asignados a la instancia de Integration Runtime de SSIS de Azure a cinco.

### <a name="reconfigure-an-azure-ssis-ir"></a>Reconfigurar una instancia de IR de SSIS de Azure

1. En primer lugar, detenga la instancia de Azure-SSIS Integration Runtime con el cmdlet [Stop-AzDataFactoryV2IntegrationRuntime](/powershell/module/az.datafactory/stop-Azdatafactoryv2integrationruntime). Este comando libera todos sus nodos y detiene la facturación.

   ```powershell
   Stop-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName 
   ```
2. A continuación, vuelva a configurar Azure-SSIS IR con el cmdlet [Set-AzDataFactoryV2IntegrationRuntime](/powershell/module/az.datafactory/set-Azdatafactoryv2integrationruntime). El siguiente comando de ejemplo permite escalar una instancia de Integration Runtime de SSIS de Azure a cinco nodos.

   ```powershell
   Set-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName -NodeCount 5
   ```  
3. Después, inicie Azure-SSIS Integration Runtime con el cmdlet [Start-AzDataFactoryV2IntegrationRuntime](/powershell/module/az.datafactory/start-Azdatafactoryv2integrationruntime). Este comando asigna todos sus nodos para ejecutar paquetes SSIS.   

   ```powershell
   Start-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName
   ```

### <a name="delete-an-azure-ssis-ir"></a>Eliminar una instancia de IR de SSIS de Azure
1. En primer lugar, cree una lista de todas las instancias de IR de SSIS de Azure en la factoría de datos.

   ```powershell
   Get-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName -Status
   ```
2. A continuación, detenga todas las instancias de IR de SSIS de Azure existentes en la factoría de datos.

   ```powershell
   Stop-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName -Force
   ```
3. A continuación, quite todas las instancias de IR de SSIS de Azure existentes en la factoría de datos de una en una.

   ```powershell
   Remove-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName -Force
   ```
4. Por último, quite la factoría de datos.

   ```powershell
   Remove-AzDataFactoryV2 -Name $DataFactoryName -ResourceGroupName $ResourceGroupName -Force
   ```
5. Si ha creado un nuevo grupo de recursos, quite el grupo de recursos.

   ```powershell
   Remove-AzResourceGroup -Name $ResourceGroupName -Force 
   ```

## <a name="next-steps"></a>Pasos siguientes
Consulte los siguientes temas para más información sobre Integration Runtime de SSIS de Azure: 

- [Integration Runtime de SSIS de Azure](concepts-integration-runtime.md#azure-ssis-integration-runtime). En este artículo se proporciona información conceptual acerca de Integration Runtime en general, lo que incluye Integration Runtime de SSIS de Azure. 
- [Tutorial: Implementación de paquetes SSIS en Azure](./tutorial-deploy-ssis-packages-azure.md). En este artículo se proporcionan instrucciones paso a paso para crear una instancia de Azure-SSIS IR y se usa Azure SQL Database para hospedar el catálogo de SSIS. 
- [Cómo: Creación de una instancia de Integration Runtime de SSIS de Azure](create-azure-ssis-integration-runtime.md). En este artículo se amplía el tutorial y se proporcionan instrucciones sobre el uso de Instancia administrada de Azure SQL y la unión de Integration Runtime a una red virtual. 
- [Unión de una instancia de Integration Runtime para la integración de SSIS en Azure a una red virtual](join-azure-ssis-integration-runtime-virtual-network.md). En este artículo se proporciona información conceptual sobre cómo unir una instancia de Integration Runtime de SSIS de Azure a una red virtual de Azure. También se proporcionan los pasos para configurar la red virtual mediante Azure Portal para que se una la instancia de Integration Runtime de SSIS de Azure. 
- [Monitor an Azure-SSIS IR](monitor-integration-runtime.md#azure-ssis-integration-runtime) (Supervisión de una instancia de Integration Runtime de SSIS de Azure). En este artículo se muestra cómo recuperar información sobre una instancia de IR de SSIS de Azure, junto con descripciones de los estados en la información devuelta.