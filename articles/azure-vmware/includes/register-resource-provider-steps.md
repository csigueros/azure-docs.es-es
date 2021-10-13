---
title: Registro del proveedor de recursos de Azure VMware Solution
description: Pasos para registrar el proveedor de recursos de Azure VMware Solution.
ms.topic: include
ms.date: 02/17/2021
author: suzizuber
ms.author: v-szuber
ms.service: azure-vmware
ms.openlocfilehash: a4f24e503c06f4f7650334f8c513466eb4dff546
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/06/2021
ms.locfileid: "129638604"
---
<!-- Used in deploy-azure-vmware-solution.md and tutorial-create-private-cloud.md -->

Para usar Azure VMware Solution, primero debe registrar el proveedor de recursos con la suscripción. Para más información sobre los proveedores de recursos, consulte [Tipos y proveedores de recursos de Azure](../../azure-resource-manager/management/resource-providers-and-types.md).


### <a name="portal"></a>[Portal](#tab/azure-portal)
 
1. Inicie sesión en [Azure Portal](https://portal.azure.com).

1. En el menú de Azure Portal, seleccione **Todos los servicios**.

1. En el cuadro **Todos los servicios**, escriba **suscripción** y, a continuación, seleccione **Suscripciones**.

1. Seleccione la suscripción en la lista de suscripción para verla.

1. Seleccione **Proveedores de recursos** y escriba **Microsoft.AVS** en la búsqueda. 
 
1. Si el proveedor de recursos no está registrado, seleccione **Registrar**.

### <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)

Para empezar a usar la CLI de Azure:

[!INCLUDE [azure-cli-prepare-your-environment-no-header](../../../includes/azure-cli-prepare-your-environment-no-header.md)]

Inicie sesión en la suscripción de Azure que usa para la implementación de Azure VMware Solution mediante la CLI de Azure. Registre el proveedor de recursos `Microsoft.AVS` con el comando [az provider register](/cli/azure/provider#az_provider_register):

```azurecli-interactive
az provider register -n Microsoft.AVS --subscription <your subscription ID>
```

Puede usar el comando [az provider list](/cli/azure/provider#az_provider_list) para ver todos los proveedores disponibles.

---


 
