---
title: Solución de problemas de las implementaciones de plantillas de Resource Manager
description: Aprenda a supervisar y solucionar los problemas asociados a las implementaciones de las plantillas de Azure Resource Manager (ARM). Muestra registros de actividad y el historial de implementación.
ms.date: 11/01/2021
ms.topic: quickstart
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 2b2dae6d4f90410b8ce1f8fc455aa95ede0ab0d3
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131091409"
---
# <a name="quickstart-troubleshoot-arm-template-deployments"></a>Inicio rápido: Solución de problemas de las implementaciones de plantillas de ARM

En este inicio rápido se explica cómo solucionar errores de implementación de las plantillas de Azure Resource Manager (ARM). Se configura una plantilla con dos errores y se aprende a usar los registros de actividad y el historial de implementaciones para corregir los errores.

Hay dos tipos de errores relacionados con la implementación de plantillas:

- Los **errores de validación** se producen antes de que comience una implementación y son debidos a errores de sintaxis en el archivo.
- Los **errores de implementación** se producen durante el proceso de implementación y pueden deberse a un valor incorrecto, como una versión de API.

Los dos tipos de errores devuelven un código de error que utiliza para solucionar problemas de implementación. Ambos aparecen en el registro de actividad. Los errores de validación no aparecen en el historial de implementaciones porque la implementación nunca se ha iniciado.

## <a name="prerequisites"></a>Prerrequisitos

Para completar este inicio rápido, necesita los siguientes elementos:

- Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/) antes de empezar.
- [Visual Studio Code](https://code.visualstudio.com/) con la [extensión Herramientas de Azure Resource Manager](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools) más reciente.
- Revise cómo implementar una plantilla local con [Azure Cloud Shell](../templates/deploy-cloud-shell.md#deploy-local-template).

## <a name="create-a-template-with-errors"></a>Creación de una plantilla con errores

La plantilla siguiente contiene un error de validación y un error de implementación. Va a solucionar y corregir los errores de la plantilla para que se pueda implementar la cuenta de almacenamiento.

Ambos errores de la plantilla de ejemplo se encuentran en la línea siguiente:

```json
"apiVersion1": "2018-07-02",
```

1. Abra Visual Studio Code y seleccione **Archivo** > **Nuevo archivo**.
1. Para copiar la plantilla, seleccione **Copiar** y péguela en el nuevo archivo.
1. Seleccione **Archivo** > **Guardar como** para asignar al archivo el nombre _azuredeploy.json_ y guárdelo en el equipo local.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageAccountType": {
      "type": "string",
      "defaultValue": "Standard_LRS",
      "allowedValues": [
        "Standard_LRS",
        "Standard_GRS",
        "Standard_ZRS",
        "Premium_LRS"
      ],
      "metadata": {
        "description": "Storage Account type"
      }
    },
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]",
      "metadata": {
        "description": "Location for all resources."
      }
    }
  },
  "variables": {
    "storageAccountName": "[concat('store', uniquestring(resourceGroup().id))]"
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion1": "2018-07-02",
      "name": "[variables('storageAccountName')]",
      "location": "[parameters('location')]",
      "sku": {
        "name": "[parameters('storageAccountType')]"
      },
      "kind": "StorageV2",
      "properties": {}
    }
  ],
  "outputs": {
    "storageAccountName": {
      "type": "string",
      "value": "[variables('storageAccountName')]"
    }
  }
}
```

## <a name="deploy-the-template"></a>Implementación de la plantilla

Cargue la plantilla en Cloud Shell e implemente con la CLI de Azure o Azure PowerShell.

1. Inicie sesión en [Cloud Shell](https://shell.azure.com).
1. Seleccione **Bash** o **PowerShell** en la esquina superior izquierda.

    :::image type="content" source="media/quickstart-troubleshoot-arm-deployment/cloud-shell-upload.png" alt-text="Captura de pantalla de Azure Cloud Shell para seleccionar un shell y cargar un archivo.":::

1. Seleccione **Cargar/descargar archivos** y cargue el archivo _azuredeploy.json_.
1. Para implementar la plantilla, copie y pegue los siguientes comandos en la ventana del shell.

    # <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

    ```azurepowershell
    $resourceGroupName = Read-Host -Prompt "Enter a resource group name"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
    New-AzResourceGroup -Name "$resourceGroupName" -Location "$location"
    New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateFile "$HOME/azuredeploy.json"
    Write-Host "Press [ENTER] to continue ..."
    ```

    # <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)

    ```azurecli
    echo "Enter a resource group name:" &&
    read resourceGroupName &&
    echo "Enter the location (i.e. centralus):" &&
    read location &&
    az group create --name $resourceGroupName --location $location &&
    az deployment group create --resource-group $resourceGroupName --template-file $HOME/azuredeploy.json &&
    echo "Press [ENTER] to continue ..."
    ```

    ---

    > [!NOTE]
    > Use el mismo nombre y ubicación del grupo de recursos al ejecutar las implementaciones. Cuando se le pida que actualice el grupo de recursos, seleccione **Sí**.

## <a name="troubleshoot-the-validation-error"></a>Solución de errores de validación

El error de validación muestra un mensaje de error en el shell con el código de error `InvalidRequestContent`.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```Output
New-AzResourceGroupDeployment: 6:49:10 PM - Error: Code=InvalidRequestContent; Message=The request content
was invalid and could not be deserialized: 'Could not find member 'apiVersion1' on object of type
'TemplateResource'. Path 'properties.template.resources[0].apiVersion1', line 34, position 24.'.

New-AzResourceGroupDeployment: The deployment validation failed
```

# <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)

```Output
{"error":{"code":"InvalidRequestContent","message":"The request content was invalid and could not be
deserialized: 'Could not find member 'apiVersion1' on object of type 'TemplateResource'.
Path 'properties.template.resources[0].apiVersion1', line 32, position 20.'."}}
```

---

### <a name="fix-the-validation-error"></a>Corrección del error de validación

El error se debe a que `apiVersion1` es incorrecto. Use Visual Studio Code para cambiar `apiVersion1` a `apiVersion` y guarde la plantilla. Siga los pasos de [Implementación de la plantilla](#deploy-the-template) para cargar la plantilla y volver a ejecutar la implementación.

## <a name="troubleshoot-the-deployment-error"></a>Solución de errores de implementación

El error de implementación muestra un mensaje de error en el shell con el código de error `NoRegisteredProviderFound`. También puede ver los errores en **Implementaciones** y **Registro de actividad** del grupo de recursos.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```Output
New-AzResourceGroupDeployment: 6:51:48 PM - The deployment 'azuredeploy' failed with error(s).
Showing 1 out of 1 error(s).
Status Message: No registered resource provider found for location 'centralus' and API version '2018-07-02'
for type 'storageAccounts'. The supported api-versions are '2021-06-01, 2021-05-01, 2021-04-01, 2021-02-01,
2021-01-01, 2020-08-01-preview, 2019-06-01, 2019-04-01, 2018-11-01, 2018-07-01, 2018-03-01-preview,
2018-02-01, 2017-10-01, 2017-06-01, 2016-12-01, 2016-05-01, 2016-01-01, 2015-06-15, 2015-05-01-preview'.
The supported locations are 'eastus, eastus2, westus, westeurope, eastasia, southeastasia, japaneast,
japanwest, northcentralus, southcentralus, centralus, northeurope, brazilsouth, australiaeast,
australiasoutheast, southindia, centralindia, westindia, canadaeast, canadacentral, westus2, westcentralus,
uksouth, ukwest, koreacentral, koreasouth, francecentral, australiacentral, southafricanorth, uaenorth,
switzerlandnorth, germanywestcentral, norwayeast, westus3, jioindiawest'.
(Code:NoRegisteredProviderFound)
CorrelationId: 11111111-1111-1111-1111-111111111111
```

# <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)

```Output
{"status":"Failed","error":{"code":"DeploymentFailed","message":"At least one resource deployment operation
failed. Please list deployment operations for details. Please see https://aka.ms/DeployOperations for usage
details.", "details":[{"code":"BadRequest","message":"{\r\n  \"error\": {\r\n
\"code\": \"NoRegisteredProviderFound\", \r\n    \"message\": \"No registered resource provider found for
location 'centralus' and API version '2018-07-02' for type 'storageAccounts'. The supported api-versions
are '2021-06-01, 2021-05-01, 2021-04-01, 2021-02-01, 2021-01-01, 2020-08-01-preview, 2019-06-01,
2019-04-01, 2018-11-01, 2018-07-01, 2018-03-01-preview, 2018-02-01, 2017-10-01, 2017-06-01, 2016-12-01,
2016-05-01, 2016-01-01, 2015-06-15, 2015-05-01-preview'.
The supported locations are 'eastus, eastus2, westus, westeurope, eastasia, southeastasia, japaneast,
japanwest, northcentralus, southcentralus, centralus, northeurope, brazilsouth, australiaeast,
australiasoutheast, southindia, centralindia, westindia, canadaeast, canadacentral, westus2, westcentralus,
uksouth, ukwest, koreacentral, koreasouth, francecentral, australiacentral, southafricanorth, uaenorth,
switzerlandnorth, germanywestcentral, norwayeast, westus3, jioindiawest'.\"\r\n  }\r\n}"}]}}
```

---

### <a name="deployments"></a>Implementaciones

El error de implementación se puede ver en Azure Portal con estos pasos:

1. Inicie sesión en [Azure Portal](https://portal.azure.com).
1. Escriba _grupos de recursos_ en el cuadro de búsqueda y seleccione **Grupos de recursos**.

    :::image type="content" source="./media/quickstart-troubleshoot-arm-deployment/search-box.png" alt-text="Captura de pantalla del cuadro de búsqueda de Azure Portal.":::

1. Seleccione el nombre del grupo de recursos de la implementación.
1. Vaya a **Información general** y seleccione el vínculo **1 Error**.

    :::image type="content" source="./media/quickstart-troubleshoot-arm-deployment/arm-template-deployment-error.png" alt-text="Captura de pantalla donde se resalta el vínculo a la implementación con errores.":::

1. En **Implementaciones**, seleccione **Detalles del error**.

    :::image type="content" source="./media/quickstart-troubleshoot-arm-deployment/arm-template-deployment-error-details.png" alt-text="Captura de pantalla del vínculo de implementaciones con errores a los detalles del error.":::

    El mensaje de error es el mismo que la salida del comando de implementación:

    :::image type="content" source="./media/quickstart-troubleshoot-arm-deployment/arm-template-deployment-error-summary.png" alt-text="Captura de pantalla de los detalles del error de implementación.":::

### <a name="activity-log"></a>Registro de actividades

También puede encontrar el error en los registros de actividad del grupo de recursos. El registro de actividad tarda unos minutos en mostrar la información de implementación más reciente.

1. En el grupo de recursos, seleccione **Registro de actividad**.
1. Use los filtros para buscar el registro y seleccionar el que quiere ver.

    :::image type="content" source="./media/quickstart-troubleshoot-arm-deployment/arm-template-deployment-activity-log.png" alt-text="Captura de pantalla del registro de actividad del grupo de recursos donde se resalta una implementación con errores.":::

1. Después de seleccionar el registro, se muestran los detalles.

    :::image type="content" source="./media/quickstart-troubleshoot-arm-deployment/arm-template-deployment-activity-log-details.png" alt-text="Captura de pantalla de los detalles del registro de actividad donde se muestra el mensaje de error de una implementación con errores.":::

### <a name="fix-the-deployment-error"></a>Corrección del error de implementación

El error de implementación **No se encontró ningún proveedor de recursos registrado** se debe a una versión de API incorrecta. Use Visual Studio Code para cambiar la versión de API a un valor válido como `2021-04-01` y guarde la plantilla. Siga los pasos de [Implementación de la plantilla](#deploy-the-template) para cargar la plantilla y volver a ejecutar la implementación.

Una vez corregidos los errores de validación e implementación, se crea la cuenta de almacenamiento. Vaya a la página **Información general** del grupo de recursos para ver el recurso. **Implementaciones** y **Registro de actividad** muestran una implementación correcta.

## <a name="clean-up-resources"></a>Limpieza de recursos

Cuando los recursos de Azure ya no sean necesarios, elimine el grupo de recursos. Puede eliminar el grupo de recursos desde Cloud Shell o el portal.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Reemplace `<rgname>`, incluidos los corchetes angulares, por el nombre del grupo de recursos.

```azurepowershell
Remove-AzResourceGroup -Name <rgname>
```

# <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)

Reemplace `<rgname>`, incluidos los corchetes angulares, por el nombre del grupo de recursos.

```azurecli
az group delete --name <rgname>
```

---

Para eliminar el grupo de recursos del portal, siga estos pasos:

1. En Azure Portal, escriba **Grupo de recursos** en el cuadro de búsqueda.
1. Escriba el nombre del grupo de recursos en el campo **Filtrar por nombre**.
1. Seleccione el nombre del grupo de recursos.
1. Seleccione **Eliminar grupo de recursos**.
1. Para confirmar la eliminación, escriba el nombre del grupo de recursos y seleccione **Eliminar**.

## <a name="next-steps"></a>Pasos siguientes

En este inicio rápido ha aprendido a solucionar errores de implementación de plantillas de ARM.

> [!div class="nextstepaction"]
> [Solución de errores comunes de implementación de Azure](common-deployment-errors.md)
