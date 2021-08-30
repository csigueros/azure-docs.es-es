---
title: Obtención de información de configuración de la cuenta de almacenamiento
titleSuffix: Azure Storage
description: Use Azure Portal, PowerShell o la CLI de Azure para recuperar las propiedades de configuración de la cuenta de almacenamiento, incluido el identificador de recurso de Azure Resource Manager, la ubicación de la cuenta, el tipo de cuenta o la SKU de replicación.
services: storage
author: tamram
ms.author: tamram
ms.date: 06/23/2021
ms.service: storage
ms.subservice: common
ms.topic: how-to
ms.openlocfilehash: f3f94dda19f11b1a0aad9a84e7ae624e41c5015c
ms.sourcegitcommit: ca38027e8298c824e624e710e82f7b16f5885951
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/24/2021
ms.locfileid: "112573659"
---
# <a name="get-storage-account-configuration-information"></a>Obtención de información de configuración de la cuenta de almacenamiento

En este artículo se muestra cómo obtener información de configuración y las propiedades de una cuenta de Azure Storage mediante Azure Portal, PowerShell o la CLI de Azure.

## <a name="get-the-resource-id-for-a-storage-account"></a>Obtención del identificador de recurso de una cuenta de almacenamiento

Cada recurso de Azure Resource Manager tienen asociado un identificador de recurso que lo define de forma única. Ciertas operaciones requieren que proporcione dicho identificador. Para obtener el identificador de recurso de una cuenta de almacenamiento puede usar Azure Portal, PowerShell o la CLI de Azure.

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

Para mostrar el identificador de recurso de Azure Resource Manager de una cuenta de almacenamiento en Azure Portal, siga estos pasos:

1. Vaya a la cuenta de almacenamiento en Azure Portal.
1. En la página de **Información general**, en la sección **Información esencial**, seleccione el vínculo **Vista JSON**.
1. El identificador de recurso de la cuenta de almacenamiento se muestra en la parte superior de la página.

    :::image type="content" source="media/storage-account-get-info/resource-id-portal.png" alt-text="Captura de pantalla que muestra cómo copiar el identificador de recurso de la cuenta de almacenamiento desde el portal":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Para devolver el identificador de recurso de Azure Resource Manager de una cuenta de almacenamiento con PowerShell, asegúrese de que ha instalado el módulo [Az.Storage](https://www.powershellgallery.com/packages/Az.Storage). A continuación, llame al comando [Get-AzStorageAccount](/powershell/module/az.storage/get-azstorageaccount) para devolver la cuenta de almacenamiento y obtener su identificador de recurso:

```azurepowershell
(Get-AzStorageAccount -ResourceGroupName <resource-group> -Name <storage-account>).Id
```

# <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)

Para devolver el identificador de recurso de Azure Resource Manager de una cuenta de almacenamiento con la CLI de Azure, llame al comando [az storage account show](/cli/azure/storage/account#az_storage_account_show) y consulte el identificador de recurso:

```azurecli
az storage account show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --query id \
    --output tsv
```

---

También puede obtener el identificador de recurso de una cuenta de almacenamiento si llama a la operación [Storage Accounts - Get Properties](/rest/api/storagerp/storage-accounts/get-properties) en la API REST.

Para obtener más información sobre los tipos de recursos administrados por Azure Resource Manager, vea [Tipos y proveedores de recursos](../../azure-resource-manager/management/resource-providers-and-types.md).

## <a name="get-the-account-type-location-or-replication-sku-for-a-storage-account"></a>Obtención del tipo de cuenta, la ubicación o la SKU de replicación de una cuenta de almacenamiento

El tipo de cuenta, la ubicación y la SKU de replicación son algunas de las propiedades disponibles en una cuenta de almacenamiento. Para ver estos valores puede usar Azure Portal, PowerShell o la CLI de Azure.

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

Para ver el tipo de cuenta, la ubicación o la SKU de replicación de una cuenta de almacenamiento en Azure Portal, siga estos pasos:

1. Vaya a la cuenta de almacenamiento en Azure Portal.
1. Encuentre estas propiedades en la página de **Información general**, en la sección **Información esencial**.

    :::image type="content" source="media/storage-account-get-info/account-configuration-portal.png" alt-text="Captura de pantalla que muestra la configuración de la cuenta en el portal":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Para ver el tipo de cuenta, la ubicación o la SKU de replicación de una cuenta de almacenamiento con PowerShell, llame al comando [Get-AzStorageAccount](/powershell/module/az.storage/get-azstorageaccount) para devolver la cuenta de almacenamiento y, a continuación, compruebe las propiedades:

```azurepowershell
$account = Get-AzStorageAccount -ResourceGroupName <resource-group> -Name <storage-account>
$account.Location
$account.Sku
$account.Kind
```

# <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)

Para ver el tipo de cuenta, la ubicación o la SKU de replicación de una cuenta de almacenamiento con la CLI de Azure, llame al comando [az storage account show](/cli/azure/storage/account#az_storage_account_show) y consulte las propiedades:

```azurecli
az storage account show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --query '[location,sku,kind]' \
    --output tsv
```

---

## <a name="next-steps"></a>Pasos siguientes

- [Introducción a las cuentas de almacenamiento](storage-account-overview.md)
- [Cree una cuenta de almacenamiento](storage-account-create.md)
