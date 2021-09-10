---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 08/18/2021
ms.author: glenga
ms.openlocfilehash: 87f43ebd24cdc7b2cf4163c835e713eb39a52a85
ms.sourcegitcommit: 16e25fb3a5fa8fc054e16f30dc925a7276f2a4cb
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/25/2021
ms.locfileid: "122829722"
---
## <a name="create-supporting-azure-resources-for-your-function"></a>Creación de recursos auxiliares de Azure para la función

Antes de poder implementar el código de la función en Azure, debe crear tres recursos:

- Un [grupo de recursos](../articles/azure-resource-manager/management/overview.md), que es un contenedor lógico de recursos relacionados.
- Una [cuenta de almacenamiento](../articles/storage/common/storage-account-create.md), que se usa para mantener el estado y otra información sobre sus funciones.
- Una aplicación de funciones, que proporciona el entorno para ejecutar el código de función. Una aplicación de funciones se asigna al proyecto de funciones y le permite agrupar funciones como una unidad lógica para facilitar la administración, la implementación y el uso compartido de recursos.

Utilice los comandos siguientes para crear los elementos. Se admiten las CLI de Azure y de PowerShell.

1. Si todavía no lo ha hecho, inicie sesión en Azure:

    # <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)
    ```azurecli
    az login
    ```

    El comando [az login](/cli/azure/reference-index#az_login) inicia sesión en su cuenta de Azure.

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell) 
    ```azurepowershell
    Connect-AzAccount
    ```

    El cmdlet [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) inicia sesión en su cuenta de Azure.

    ---

1. Cree un grupo de recursos denominado `AzureFunctionsQuickstart-rg` en la región elegida:

    # <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)
    
    ```azurecli
    az group create --name AzureFunctionsQuickstart-rg --location <REGION>
    ```
 
    El comando [az group create](/cli/azure/group#az_group_create) crea un grupo de recursos. En el comando anterior, reemplace `<REGION>` por una región cercana mediante un código de región disponible devuelto por el comando [az account list-locations](/cli/azure/account#az_account_list_locations).

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

    ```azurepowershell
    New-AzResourceGroup -Name AzureFunctionsQuickstart-rg -Location <REGION>
    ```

    El comando [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) crea un grupo de recursos. Por lo general, los recursos y los grupos de recursos se crean en una región cerca de usted, y se utiliza alguna de las regiones devueltas por el cmdlet [Get-AzLocation](/powershell/module/az.resources/get-azlocation).

    ---

1. Cree una cuenta de almacenamiento de uso general en su grupo de recursos y región:

    # <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)

    ```azurecli
    az storage account create --name <STORAGE_NAME> --location <REGION> --resource-group AzureFunctionsQuickstart-rg --sku Standard_LRS
    ```

    El comando [az storage account create](/cli/azure/storage/account#az_storage_account_create) crea la cuenta de almacenamiento. 

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

    ```azurepowershell
    New-AzStorageAccount -ResourceGroupName AzureFunctionsQuickstart-rg -Name <STORAGE_NAME> -SkuName Standard_LRS -Location <REGION>
    ```

    El cmdlet [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) crea la cuenta de almacenamiento.

    ---

    En el ejemplo anterior, reemplace `<STORAGE_NAME>` por un nombre que sea apropiado para usted y único en Azure Storage. Los nombres deben contener entre 3 y 24 caracteres y solo letras minúsculas. `Standard_LRS` especifica una cuenta de uso general, que es [compatible con Functions](../articles/azure-functions/storage-considerations.md#storage-account-requirements).
