---
title: Configuración de Azure Key Vault mediante la CLI
description: Procedimiento para configurar Key Vault para máquinas virtuales mediante la CLI de Azure.
author: mimckitt
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 02/24/2017
ms.author: mimckitt
ms.custom: devx-track-azurecli
ms.openlocfilehash: af975db3c0737d9fb075b50b6087cc987198d7fd
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/23/2021
ms.locfileid: "122691717"
---
# <a name="how-to-set-up-key-vault-for-virtual-machines-with-the-azure-cli"></a>Configuración de Key Vault para máquinas virtuales con la CLI de Azure

**Se aplica a:** :heavy_check_mark: Máquinas virtuales Linux :heavy_check_mark: Conjuntos de escalado flexibles 

En la pila de Azure Resource Manager, los certificados o secretos se modelan como recursos que se proporcionan mediante Key Vault. Para más información sobre Azure Key Vault, consulte [¿Qué es Azure Key Vault?](../../key-vault/general/overview.md) Para que Key Vault se utilice con VM de Azure Resource Manager, la propiedad *EnabledForDeployment* de Key Vault se debe establecer en true. En este artículo se muestra cómo configurar Key Vault para su uso con máquinas virtuales (VM) de Azure mediante la CLI de Azure. 

Para realizar estos pasos, es preciso tener instalada la [CLI de Azure](/cli/azure/install-az-cli2) más reciente y haber iniciado sesión en una cuenta de Azure mediante [az login](/cli/azure/reference-index).

## <a name="create-a-key-vault"></a>Creación de un almacén de claves
Cree un almacén de claves y asigne la directiva de implementación con [az keyvault create](/cli/azure/keyvault). En el ejemplo siguiente se crea un almacén de claves denominado `myKeyVault` en el grupo de recursos `myResourceGroup`:

```azurecli
az keyvault create -l westus -n myKeyVault -g myResourceGroup --enabled-for-deployment true
```

## <a name="update-a-key-vault-for-use-with-vms"></a>Actualización de un almacén de claves para su uso con VM
Establezca la directiva de implementación en un almacén de claves existente con [az keyvault update](/cli/azure/keyvault). Lo siguiente actualiza el almacén de claves denominado `myKeyVault` en el grupo de recursos `myResourceGroup`:

```azurecli
az keyvault update -n myKeyVault -g myResourceGroup --set properties.enabledForDeployment=true
```

## <a name="use-templates-to-set-up-key-vault"></a>Uso de plantillas para configurar Key Vault
Al utilizar plantillas, debe configurar la propiedad `enabledForDeployment` como `true` para el recurso Key Vault tal y como se indica a continuación:

```json
{
    "type": "Microsoft.KeyVault/vaults",
    "name": "ContosoKeyVault",
    "apiVersion": "2015-06-01",
    "location": "<location-of-key-vault>",
    "properties": {
    "enabledForDeployment": "true",
    ....
    ....
    }
}
```

## <a name="next-steps"></a>Pasos siguientes
Para otras opciones que puede configurar al crear un almacén de claves mediante plantillas, consulte [Create a key vault](https://azure.microsoft.com/resources/templates/key-vault-create/)(Creación de un almacén de claves).
