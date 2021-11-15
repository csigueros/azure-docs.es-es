---
author: msmbaldwin
ms.service: confidential-ledger
ms.topic: include
ms.date: 05/05/2021
ms.author: msmbaldwin
ms.openlocfilehash: fa07459629d832516fda5d3141f85b23afe3fbb8
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/05/2021
ms.locfileid: "131860571"
---
Un proveedor de recursos es un servicio que proporciona recursos de Azure. Use el comando [az provider register](/cli/azure/provider#az_provider_register) de la CLI de Azure o el cmdlet [Register-AzureRmResourceProvider](/powershell/module/azurerm.resources/register-azurermresourceprovider) de Azure PowerShell para registrar el proveedor de recursos de Azure Confidential Ledger, "microsoft.ConfidentialLedger".

# <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)
```azurecli
az provider register --namespace "microsoft.ConfidentialLedger"
```
# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azurepowershell)

```azurepowershell
Register-AzureRmResourceProvider -ProviderNamespace "microsoft.ConfidentialLedger"
```
---

Puede comprobar que el registro se ha completado con el comando de la CLI de Azure [az provider register](/cli/azure/provider#az_provider_show) o el cmdlet de Azure PowerShell [Get-AzureRmResourceProvider](/powershell/module/azurerm.resources/get-azurermresourceprovider).

# <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)
```azurecli
az provider show --namespace "microsoft.ConfidentialLedger"
```
# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azurepowershell)

```azurepowershell
Get-AzureRmResourceProvider -ProviderNamespace "microsoft.ConfidentialLedger"
```
---
