---
title: 'Inicio rápido: Creación de una cuenta de Purview con PowerShell o la CLI de Azure'
description: En este inicio rápido se describe cómo crear una cuenta de Azure Purview mediante Azure PowerShell o la CLI de Azure.
author: hophanms
ms.author: hophan
ms.date: 09/27/2021
ms.topic: quickstart
ms.service: purview
ms.custom:
- mode-api
ms.openlocfilehash: 0e3cb8399e42dc651a48ada6018c58cb4f48e5d8
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2021
ms.locfileid: "129217360"
---
# <a name="quickstart-create-an-azure-purview-account-using-azure-powershellazure-cli"></a>Inicio rápido: Creación de una cuenta de Azure Purview mediante Azure PowerShell o la CLI de Azure

En este inicio rápido se creará una cuenta de Azure Purview mediante Azure PowerShell o la CLI de Azure. La [referencia de PowerShell para Purview](/powershell/module/az.purview/) está disponible, pero este artículo le llevará por todos los pasos necesarios para crear una cuenta con PowerShell.

Azure Purview es un servicio de gobernanza de datos que le ayuda a administrar y controlar el panorama de sus datos. Al conectarse a los datos de sus orígenes locales, multinube y de software como servicio (SaaS), Purview crea un mapa actualizado de la información. Identifica y clasifica datos confidenciales y proporciona linaje de un extremo a otro. Los consumidores de datos pueden descubrir datos en la organización y los administradores de datos pueden auditar, proteger y garantizar el uso correcto de los datos.

Para obtener más información sobre Purview, [consulte nuestra página de información general](overview.md). Para más información sobre la implementación de Purview en la organización, [consulte nuestros procedimientos recomendados sobre implementación](deployment-best-practices.md).

[!INCLUDE [purview-quickstart-prerequisites](includes/purview-quickstart-prerequisites.md)]

### <a name="install-powershell"></a>Instale PowerShell.

 Instale Azure PowerShell o la CLI de Azure en la máquina cliente para implementar la plantilla: [Implementación desde la línea de comandos](../azure-resource-manager/templates/template-tutorial-create-first-template.md?tabs=azure-cli#command-line-deployment)

## <a name="create-an-azure-purview-account"></a>Creación de una cuenta de Azure Purview

1. Inicie sesión con su credencial de Azure

    # <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
    
    ```azurepowershell
    Connect-AzAccount
    ```
    
    # <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)
    
    ```azurecli
    az login
    ```
    
    ---

1. Si tiene varias suscripciones de Azure, seleccione la suscripción que desee usar:

    # <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
    
    ```azurepowershell
    Set-AzContext [SubscriptionID/SubscriptionName]
    ```
    
    # <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)
    
    ```azurecli
    az account set --subscription [SubscriptionID/SubscriptionName]
    ```
    
    ---

1. Cree un grupo de recursos para su cuenta de Purview. Si ya tiene una, puede omitir este paso:

    # <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
    
    ```azurepowershell
    New-AzResourceGroup `
      -Name myResourceGroup `
      -Location "East US"
    ```
    
    # <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)
    
    ```azurecli
    az group create \
      --name myResourceGroup \
      --location "East US"
    ```
    
    ---

1. Cree un archivo de plantilla de Purview como `purviewtemplate.json`. Puede actualizar `name`, `location` y `capacity` (`4` o `16`):

    ```json
    {
      "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "resources": [
        {
          "name": "<yourPurviewAccountName>",
          "type": "Microsoft.Purview/accounts",
          "apiVersion": "2020-12-01-preview",
          "location": "EastUs",
          "identity": {
            "type": "SystemAssigned"
          },
          "properties": {
            "networkAcls": {
              "defaultAction": "Allow"
            }
          },
          "dependsOn": [],
          "sku": {
            "name": "Standard",
            "capacity": "4"
          },
          "tags": {}
        }
      ],
      "outputs": {}
    }
    ```

1. Implementación de una plantilla de Purview

    # <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
    
    ```azurepowershell
    New-AzResourceGroupDeployment -ResourceGroupName "<myResourceGroup>" -TemplateFile "<PATH TO purviewtemplate.json>"
    ```
    
    # <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)
    
    Para ejecutar este comando de implementación, debe tener la [última versión](/cli/azure/install-azure-cli) de la CLI de Azure.
    
    ```azurecli
    az deployment group create --resource-group "<myResourceGroup>" --template-file "<PATH TO purviewtemplate.json>"
    ```
    
    ---

1. El comando de implementación devuelve los resultados. Busque `ProvisioningState` para ver si la implementación se realizó correctamente.
    
## <a name="next-steps"></a>Pasos siguientes

En este inicio rápido, ha aprendido a crear una cuenta de Azure Purview.

Consulte los artículos siguientes para aprender a navegar por Purview Studio, crear una colección y conceder acceso a Purview.

* [Uso de Purview Studio](use-purview-studio.md)
* [Crear una colección](quickstart-create-collection.md)
* [Adición de usuarios a una cuenta de Azure Purview](catalog-permissions.md)
