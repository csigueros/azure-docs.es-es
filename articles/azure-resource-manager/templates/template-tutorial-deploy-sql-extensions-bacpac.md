---
title: Importación de archivos BACPAC de SQL con plantillas
description: Aprenda a usar las extensiones de Azure SQL Database para importar archivos BACPAC de SQL con plantillas de Azure Resource Manager (ARM).
author: mumian
ms.date: 09/30/2021
ms.topic: tutorial
ms.author: jgao
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 3310d3f33d7aa31a24a19bc7c43b59173ffd78d6
ms.sourcegitcommit: 557ed4e74f0629b6d2a543e1228f65a3e01bf3ac
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/05/2021
ms.locfileid: "129456051"
---
# <a name="tutorial-import-sql-bacpac-files-with-arm-templates"></a>Tutorial: Importación de archivos BACPAC de SQL con plantillas de Resource Manager

Aprenda a usar las extensiones de Azure SQL Database para importar un archivo [BACPAC](/sql/relational-databases/data-tier-applications/data-tier-applications#bacpac) con las plantillas de Azure Resource Manager (ARM). Los artefactos de implementación son cualquier archivo, además de los archivos de la plantilla principal, que se necesitan para completar una implementación. El archivo BACPAC es un artefacto.

En este tutorial creará una plantilla para implementar un [servidor SQL lógico](../../azure-sql/database/logical-servers.md) y una base de datos única, e importar un archivo BACPAC. Para más información sobre la implementación de extensiones de máquina virtual de Azure mediante plantillas de Azure Resource Manager, consulte [Tutorial: Implementación de extensiones de máquina virtual con plantillas de Resource Manager](./template-tutorial-deploy-vm-extensions.md).

En este tutorial se describen las tareas siguientes:

> [!div class="checklist"]
>
> - Preparación de un archivo BACPAC.
> - Apertura de una plantilla de inicio rápido.
> - Edición de la plantilla.
> - Implemente la plantilla.
> - Comprobación de la implementación.

Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/) antes de empezar.

## <a name="prerequisites"></a>Requisitos previos

Para completar este artículo, necesitará lo siguiente:

- Visual Studio Code con la extensión Herramientas de Resource Manager. Consulte [Quickstart: Creación de plantillas de ARM mediante Visual Studio Code](./quickstart-create-templates-use-visual-studio-code.md).
- Para aumentar la seguridad, utilice una contraseña generada para la cuenta de administrador del servidor. Puede usar [Azure Cloud Shell](../../cloud-shell/overview.md) ejecutar el siguiente comando en PowerShell o Bash:

    ```shell
    openssl rand -base64 32
    ```

    Para más información, ejecute `man openssl rand` para abrir la página manual.

    Azure Key Vault está diseñado para proteger las claves criptográficas y otros secretos. Para más información, consulte el [Tutorial: Integración de Azure Key Vault en la implementación de la plantilla de Resource Manager](./template-tutorial-use-key-vault.md). También se recomienda actualizar la contraseña cada tres meses.

## <a name="prepare-a-bacpac-file"></a>Preparación de un archivo BACPAC

En [GitHub](https://github.com/Azure/azure-docs-json-samples/raw/master/tutorial-sql-extension/SQLDatabaseExtension.bacpac) se comparte un archivo BACPAC. Para crear el suyo propio, consulte [Exportación de una base de datos de Azure SQL Database a un archivo BACPAC](../../azure-sql/database/database-export.md). Si elige publicar el archivo en su propia ubicación, tendrá que actualizar la plantilla más adelante en el tutorial.

El archivo BACPAC se debe almacenar en una cuenta de Azure Storage para poder importarlo con una plantilla de Resource Manager. El siguiente script de PowerShell prepara el archivo BACPAC con estos pasos:

- Descargue el archivo BACPAC.
- Cree una cuenta de Azure Storage.
- Cree un contenedor de blobs en una cuenta de almacenamiento.
- Cargue el archivo BACPAC en el contenedor.
- Muestre la clave de la cuenta de almacenamiento, la dirección URL del blob, el nombre del grupo de recursos y la ubicación.

1. Seleccione **Probar** para abrir Cloud Shell. Luego, copie y pegue el siguiente script de PowerShell en la ventana del shell.

    ```azurepowershell-interactive
    $projectName = Read-Host -Prompt "Enter a project name that is used to generate Azure resource names"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"

    $resourceGroupName = "${projectName}rg"
    $storageAccountName = "${projectName}store"
    $containerName = "bacpacfiles"
    $bacpacFileName = "SQLDatabaseExtension.bacpac"
    $bacpacUrl = "https://github.com/Azure/azure-docs-json-samples/raw/master/tutorial-sql-extension/SQLDatabaseExtension.bacpac"

    # Download the bacpac file
    Invoke-WebRequest -Uri $bacpacUrl -OutFile "$HOME/$bacpacFileName"

    # Create a resource group
    New-AzResourceGroup -Name $resourceGroupName -Location $location

    # Create a storage account
    $storageAccount = New-AzStorageAccount -ResourceGroupName $resourceGroupName `
                                           -Name $storageAccountName `
                                           -SkuName Standard_LRS `
                                           -Location $location
    $storageAccountKey = (Get-AzStorageAccountKey -ResourceGroupName $resourceGroupName `
                                                  -Name $storageAccountName).Value[0]

    # Create a container
    New-AzStorageContainer -Name $containerName -Context $storageAccount.Context

    # Upload the BACPAC file to the container
    Set-AzStorageBlobContent -File $HOME/$bacpacFileName `
                             -Container $containerName `
                             -Blob $bacpacFileName `
                             -Context $storageAccount.Context

    Write-Host "The storage account key is $storageAccountKey"
    Write-Host "The BACPAC file URL is https://$storageAccountName.blob.core.windows.net/$containerName/$bacpacFileName"
    Write-Host "The project name and location are $projectName and $location"
    Write-Host "Press [ENTER] to continue ..."
    ```

1. Guarde la clave de la cuenta de almacenamiento, la dirección URL del archivo BACPAC, el nombre del proyecto y la ubicación. Estos valores los usará al implementar la plantilla más adelante en este tutorial.

## <a name="open-a-quickstart-template"></a>Apertura de una plantilla de inicio rápido

La plantilla que se usa en este tutorial se almacena en [GitHub](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorial-sql-extension/azuredeploy.json).

1. En Visual Studio Code, seleccione **Archivo** > **Abrir archivo**.
1. En **Nombre de archivo**, pegue el código URL siguiente:

    ```url
    https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorial-sql-extension/azuredeploy.json
    ```

1. Seleccione **Abrir** para abrir el archivo.

    En la plantilla hay dos recursos definidos:

   - `Microsoft.Sql/servers`. Consulte la [referencia de plantilla](/azure/templates/microsoft.sql/servers).
   - `Microsoft.SQL.servers/databases`. Consulte la [referencia de plantilla](/azure/templates/microsoft.sql/servers/databases).

     Resulta útil obtener cierta información básica de la plantilla antes de personalizarla.

1. Seleccione **Archivo** > **Guardar como** para guardar una copia del archivo en la máquina local con el nombre _azuredeploy.json_.

## <a name="edit-the-template"></a>Edición de la plantilla

1. Agregue dos parámetros más al final de la sección `parameters` para establecer la clave de la cuenta de almacenamiento y la dirección URL de BACPAC.

    ```json
        "storageAccountKey": {
          "type":"string",
          "metadata":{
            "description": "Specifies the key of the storage account where the BACPAC file is stored."
          }
        },
        "bacpacUrl": {
          "type":"string",
          "metadata":{
            "description": "Specifies the URL of the BACPAC file."
          }
        }
    ```

    Agregue una coma después de la llave de cierre (`}`) de la propiedad `adminPassword`. Para dar formato al archivo JSON desde Visual Studio Code, seleccione Mayús + Alt + F.

1. Agregue dos recursos a la plantilla.

    - Para permitir que la extensión de SQL Database importe archivos BACPAC, debe permitir el tráfico desde los servicios de Azure. Cuando se implementa SQL servidor, la regla de firewall activa la opción **Permitir que los servicios y recursos de Azure accedan a este servidor**.

      Agregue la siguiente regla de firewall en la definición del servidor:

        ```json
        "resources": [
          {
            "type": "firewallrules",
            "apiVersion": "2021-02-01-preview",
            "name": "AllowAllAzureIps",
            "location": "[parameters('location')]",
            "dependsOn": [
              "[parameters('databaseServerName')]"
            ],
            "properties": {
              "startIpAddress": "0.0.0.0",
              "endIpAddress": "0.0.0.0"
            }
          }
        ]
        ```

        En el ejemplo siguiente se muestra la plantilla actualizada:

        :::image type="content" source="media/template-tutorial-deploy-sql-extensions-bacpac/resource-manager-tutorial-deploy-sql-extensions-bacpac-firewall.png" alt-text="Plantilla con definición de firewall.":::

    - Agregue un recurso de extensión de SQL Database a la definición de la base de datos con el siguiente código JSON:

        ```json
        "resources": [
          {
            "type": "extensions",
            "apiVersion": "2014-04-01",
            "name": "Import",
            "dependsOn": [
              "[resourceId('Microsoft.Sql/servers/databases', parameters('databaseServerName'), parameters('databaseName'))]"
            ],
            "properties": {
              "storageKeyType": "StorageAccessKey",
              "storageKey": "[parameters('storageAccountKey')]",
              "storageUri": "[parameters('bacpacUrl')]",
              "administratorLogin": "[parameters('adminUser')]",
              "administratorLoginPassword": "[parameters('adminPassword')]",
              "operationMode": "Import"
            }
          }
        ]
        ```

        En el ejemplo siguiente se muestra la plantilla actualizada:

        :::image type="content" source="media/template-tutorial-deploy-sql-extensions-bacpac/resource-manager-tutorial-deploy-sql-extensions-bacpac.png" alt-text="Plantilla con extensión de SQL Database.":::

        Para conocer la definición de recursos, consulte la [referencia de extensiones de SQL Database](/azure/templates/microsoft.sql/servers/databases/extensions) de la versión de la API. Estos son algunos elementos importantes:

        - `dependsOn`: el recurso de la extensión debe crearse después de que se haya creado la base de datos.
        - `storageKeyType`: especifique el tipo de clave de almacenamiento que se usa. El valor puede ser o `StorageAccessKey` o `SharedAccessKey`. Use `StorageAccessKey` en este tutorial.
        - `storageKey`: Especifique la clave de la cuenta de almacenamiento donde se almacena el archivo BACPAC. Si el tipo de clave de almacenamiento es `SharedAccessKey`, debe ir precedida de "?".
        - `storageUri`: Especifique la dirección URL del archivo BACPAC almacenado en una cuenta de almacenamiento.
        - `administratorLogin`: nombre de la cuenta del administrador de SQL.
        - `administratorLoginPassword`: contraseña del administrador de SQL. Para usar una contraseña generada, consulte los [requisitos previos](#prerequisites).

En el ejemplo siguiente se muestra la plantilla completada:

[!code-json[](~/resourcemanager-templates/tutorial-sql-extension/azuredeploy2.json?range=1-106&highlight=38-49,62-76,86-103)]

## <a name="deploy-the-template"></a>Implementación de la plantilla

Use el nombre del proyecto y la ubicación que se usaron al preparar el archivo BACPAC. Esto agrupa todos los recursos en el mismo grupo de recursos, lo que resulta útil cuando se eliminan recursos.

1. Inicie sesión en [Cloud Shell](https://shell.azure.com).
1. Seleccione **PowerShell** en la esquina superior izquierda.

    :::image type="content" source="media/template-tutorial-deploy-sql-extensions-bacpac/cloud-shell-select.png" alt-text="Abra Azure Cloud Shell en PowerShell y cargue un archivo.":::

1. Seleccione **Cargar/descargar archivos** y cargue el archivo _azuredeploy.json_.
1. Para implementar la plantilla, copie y pegue el siguiente script en la ventana del shell.

    ```azurepowershell
    $projectName = Read-Host -Prompt "Enter the same project name that is used earlier"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
    $adminUsername = Read-Host -Prompt "Enter the SQL admin username"
    $adminPassword = Read-Host -Prompt "Enter the admin password" -AsSecureString
    $storageAccountKey = Read-Host -Prompt "Enter the storage account key"
    $bacpacUrl = Read-Host -Prompt "Enter the URL of the BACPAC file"
    $resourceGroupName = "${projectName}rg"

    New-AzResourceGroupDeployment `
        -ResourceGroupName $resourceGroupName `
        -adminUser $adminUsername `
        -adminPassword $adminPassword `
        -TemplateFile "$HOME/azuredeploy.json" `
        -storageAccountKey $storageAccountKey `
        -bacpacUrl $bacpacUrl

    Write-Host "Press [ENTER] to continue ..."
    ```

## <a name="verify-the-deployment"></a>Comprobación de la implementación

Para acceder al servidor desde el equipo cliente, es preciso agregar una regla de firewall. La dirección IP del cliente y la dirección IP que se usa para conectarse al servidor pueden ser diferentes debido a la traducción de direcciones de red (NAT). Para más información, consulte [Creación y administración de reglas de firewall de IP](../../azure-sql/database/firewall-configure.md#create-and-manage-ip-firewall-rules).

Por ejemplo, al iniciar sesión en el **Editor de consultas** se muestra un mensaje que indica que no se permite la dirección IP. La dirección es diferente de la dirección IP del cliente debido a NAT. Seleccione el vínculo del mensaje para agregar una regla de firewall para la dirección IP. Cuando haya terminado, puede eliminar la dirección IP de la configuración de **Firewalls y redes virtuales** del servidor.

En Azure Portal, en el grupo de recursos, seleccione la base de datos. Seleccione **Editor de consultas (versión preliminar)** y escriba las credenciales de administrador. Verá que se han importado dos tablas en la base de datos.

![Editor de consultas (versión preliminar)](./media/template-tutorial-deploy-sql-extensions-bacpac/resource-manager-tutorial-deploy-sql-extensions-bacpac-query-editor.png)

## <a name="clean-up-resources"></a>Limpieza de recursos

Cuando los recursos de Azure que implementó dejen de ser necesarios, elimine el grupo de recursos. Se eliminan el grupo de recursos, la cuenta de almacenamiento, el servidor SQL Server y las bases de datos SQL.

1. En Azure Portal, escriba **Grupo de recursos** en el cuadro de búsqueda.
1. Escriba el nombre del grupo de recursos en el campo **Filtrar por nombre**.
1. Seleccione el nombre del grupo de recursos.
1. Seleccione **Eliminar grupo de recursos**.
1. Para confirmar la eliminación, escriba el nombre del grupo de recursos y seleccione **Eliminar**.

## <a name="next-steps"></a>Pasos siguientes

En este tutorial ha implementado un servidor y una base de datos, y ha importado un archivo BACPAC. Para obtener información sobre cómo solucionar problemas de implementación de plantillas, consulte:

> [!div class="nextstepaction"]
> [Solución de problemas de las implementaciones de plantillas de Resource Manager](./template-tutorial-troubleshoot.md)
