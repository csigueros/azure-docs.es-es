---
title: 'Tutorial: Uso de runbooks de Azure Automation para crear clústeres: Azure HDInsight'
description: Aprenda a crear y eliminar clústeres de Azure HDInsight con scripts que se ejecutan en la nube mediante runbooks de Azure Automation.
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: tutorial
ms.date: 12/27/2019
ms.openlocfilehash: d8091bd5e1bb95ff29d0be948a464f587a49f62f
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124819752"
---
# <a name="tutorial-create-azure-hdinsight-clusters-with-azure-automation"></a>Tutorial: Creación de clústeres de Azure HDInsight con Azure Automation

Azure Automation permite crear scripts que se ejecutan en la nube y administrar recursos de Azure a petición o según una programación. En este artículo se describe cómo crear runbooks de PowerShell para crear y eliminar clústeres de Azure HDInsight.

En este tutorial, aprenderá a:

> [!div class="checklist"]
> * Instalar los módulos necesarios para interactuar con HDInsight.
> * Crear y almacenar las credenciales necesarias durante la creación del clúster.
> * Crear un runbook de Azure Automation para crear un clúster de HDInsight.

Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar.

## <a name="prerequisites"></a>Prerrequisitos

* Una [cuenta de Azure Automation](../automation/quickstarts/create-account-portal.md) existente.
* Una [cuenta de Azure Storage](../storage/common/storage-account-create.md) existente, que se usará como almacenamiento de clúster.

## <a name="install-hdinsight-modules"></a>Instalación de módulos de HDInsight

1. Inicie sesión en [Azure Portal](https://portal.azure.com).
1. Seleccione las cuentas de Azure Automation.
1. Seleccione **Galería de módulos** en **Recursos compartidos**.
1. Escriba **AzureRM.Profile** en el cuadro y presione Entrar para buscarlo. Seleccione el resultado de búsqueda disponible.
1. En la pantalla **AzureRM.profile**, seleccione **Importar**. Active la casilla para actualizar los módulos de Azure y, después, seleccione **Aceptar**.

    :::image type="content" source="./media/manage-clusters-runbooks/import-azurermprofile-module.png" alt-text="Importación del módulo AzureRM.profile" border="false":::

1. Vuelva a la galería de módulos; para ello, seleccione **Galería de módulos** en **Recursos compartidos**.
1. Escriba **HDInsight**. Seleccione **AzureRM.HDInsight**.

    :::image type="content" source="./media/manage-clusters-runbooks/browse-modules-hdinsight.png" alt-text="examinar los módulos de HDInsight" border="true":::

1. En el panel **AzureRM.HDInsight**, seleccione **Importar** y **Aceptar**.

    :::image type="content" source="./media/manage-clusters-runbooks/import-azurermhdinsight-module.png" alt-text="importar el módulo AzureRM.HDInsight" border="true":::

## <a name="create-credentials"></a>Crear credenciales

1. En **Recursos compartidos**, seleccione **Credenciales**.
1. Seleccione **Agregar una credencial**.
1. Escriba la información necesaria en el panel **Nueva credencial**. Esta credencial es para almacenar la contraseña del clúster, lo que le permitirá iniciar sesión en Ambari.

    | Propiedad | Value |
    | --- | --- |
    | Nombre | `cluster-password` |
    | Nombre de usuario | `admin` |
    | Contraseña | `SECURE_PASSWORD` |
    | Confirmar contraseña | `SECURE_PASSWORD` |

1. Seleccione **Crear**.
1. Repita el mismo proceso con una nueva credencial `ssh-password` con el nombre de usuario `sshuser` y una contraseña de su elección. Seleccione **Crear**. Esta credencial es para almacenar la contraseña SSH del clúster.

    :::image type="content" source="./media/manage-clusters-runbooks/create-credentials.png" alt-text="Crear una credencial" border="true":::

## <a name="create-a-runbook-to-create-a-cluster"></a>Creación de un runbook para crear un clúster

1. Seleccione **Runbooks** en **Automatización de procesos**.
1. Seleccione **Crear un runbook**.
1. En el panel **Crear un Runbook**, escriba un nombre para el runbook, como `hdinsight-cluster-create`. Seleccione **PowerShell** en la lista desplegable **Tipo de Runbook**.
1. Seleccione **Crear**.

    :::image type="content" source="./media/manage-clusters-runbooks/create-runbook.png" alt-text="crear un runbook" border="true":::

1. Escriba el código siguiente en la pantalla **Editar Runbook de PowerShell** y seleccione **Publicar**:

    :::image type="content" source="./media/manage-clusters-runbooks/publish-runbook.png" alt-text="publicar runbook" border="true":::

    ```powershell
    Param
    (
      [Parameter (Mandatory= $true)]
      [String] $subscriptionID,
    
      [Parameter (Mandatory= $true)]
      [String] $resourceGroup,
    
      [Parameter (Mandatory= $true)]
      [String] $storageAccount,
    
      [Parameter (Mandatory= $true)]
      [String] $containerName,
    
      [Parameter (Mandatory= $true)]
      [String] $clusterName
    )
    ### Authenticate to Azure 
    $Conn = Get-AutomationConnection -Name 'AzureRunAsConnection'
    Add-AzureRMAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
    
    # Set cluster variables
    $storageAccountKey = (Get-AzureRmStorageAccountKey –Name $storageAccount –ResourceGroupName $resourceGroup)[0].value 
    
    # Setting cluster credentials
    
    #Automation credential for Cluster Admin
    $clusterCreds = Get-AutomationPSCredential –Name 'cluster-password'
    
    #Automation credential for user to SSH into cluster
    $sshCreds = Get-AutomationPSCredential –Name 'ssh-password' 
    
    $clusterType = "Hadoop" #Use any supported cluster type (Hadoop, HBase, Storm, etc.)
    $clusterOS = "Linux"
    $clusterWorkerNodes = 3
    $clusterNodeSize = "Standard_D3_v2"
    $location = Get-AzureRmStorageAccount –StorageAccountName $storageAccount –ResourceGroupName $resourceGroup | %{$_.Location}
    
    ### Provision HDInsight cluster
    New-AzureRmHDInsightCluster –ClusterName $clusterName –ResourceGroupName $resourceGroup –Location $location –DefaultStorageAccountName "$storageAccount.blob.core.windows.net" –DefaultStorageAccountKey $storageAccountKey -DefaultStorageContainer $containerName –ClusterType $clusterType –OSType $clusterOS –Version “3.6” –HttpCredential $clusterCreds –SshCredential $sshCreds –ClusterSizeInNodes $clusterWorkerNodes –HeadNodeSize $clusterNodeSize –WorkerNodeSize $clusterNodeSize
    ```

## <a name="create-a-runbook-to-delete-a-cluster"></a>Creación de un runbook para eliminar un clúster

1. Seleccione **Runbooks** en **Automatización de procesos**.
1. Seleccione **Crear un runbook**.
1. En el panel **Crear un Runbook**, escriba un nombre para el runbook, como `hdinsight-cluster-delete`. Seleccione **PowerShell** en la lista desplegable **Tipo de Runbook**.
1. Seleccione **Crear**.
1. Escriba el código siguiente en la pantalla **Editar Runbook de PowerShell** y seleccione **Publicar**:

    ```powershell
    Param
    (
      [Parameter (Mandatory= $true)]
      [String] $clusterName
    )
    
    ### Authenticate to Azure 
    $Conn = Get-AutomationConnection -Name 'AzureRunAsConnection'
    Add-AzureRMAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
    
    Remove-AzureRmHDInsightCluster -ClusterName $clusterName
    ```

## <a name="execute-runbooks"></a>Ejecución de runbooks

### <a name="create-a-cluster"></a>Crear un clúster

1. Para ver la lista de runbooks de la cuenta de Automation, seleccione **Runbooks** en **Automatización de procesos**.
1. Seleccione `hdinsight-cluster-create` o el nombre que usó al crear el runbook de creación del clúster.
1. Seleccione **Iniciar** para ejecutar el runbook inmediatamente. También puede programar los runbooks para que se ejecuten periódicamente. Consulte [Programación de un runbook en Azure Automation](../automation/shared-resources/schedules.md).
1. Escriba los parámetros necesarios para el script y seleccione **Aceptar**. Se crea un clúster de HDInsight con el nombre especificado en el parámetro **CLUSTERNAME**.

    :::image type="content" source="./media/manage-clusters-runbooks/execute-create-runbook.png" alt-text="ejecutar runbook de creación de clúster" border="true":::

### <a name="delete-a-cluster"></a>Eliminación de un clúster

Para eliminar el clúster, seleccione el runbook `hdinsight-cluster-delete` que creó. Seleccione **Iniciar**, escriba el parámetro **CLUSTERNAME** y seleccione **Aceptar**.

## <a name="clean-up-resources"></a>Limpieza de recursos

Cuando ya no la necesite, elimine la cuenta de Azure Automation que se creó para evitar cargos imprevistos. Para ello, vaya a Azure Portal, seleccione el grupo de recursos en el que creó la cuenta de Azure Automation, seleccione la cuenta de Automation y, luego, seleccione **Eliminar**.

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Administración de clústeres de Apache Hadoop en HDInsight con Azure PowerShell](hdinsight-administer-use-powershell.md)