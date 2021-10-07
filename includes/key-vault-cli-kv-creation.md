---
author: msmbaldwin
ms.service: key-vault
ms.topic: include
ms.date: 07/20/2020
ms.author: msmbaldwin
ms.openlocfilehash: 2d3c7cdcebc244aefb6ef5b2ceb7b931344a8960
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128593726"
---
Use el comando [az keyvault create](/cli/azure/keyvault#az_keyvault_create) de la CLI de Azure para crear un almacén de claves en el grupo de recursos del paso anterior. Tendrá que proporcionar algo de información:

- Nombre del almacén de claves: cadena de entre 3 y 24 caracteres que puede contener solo números (0-9), letras (a-z, A-Z) y guiones (-).

  > [!Important]
  > Cada almacén de claves debe tener un nombre único. Reemplace \<your-unique-keyvault-name\> por el nombre del almacén de claves en los ejemplos siguientes.

- Nombre del grupo de recursos: **myResourceGroup**.
- Ubicación: **EastUS**.

```azurecli
az keyvault create --name "<your-unique-keyvault-name>" --resource-group "myResourceGroup" --location "EastUS"
```

La salida de este comando muestra las propiedades del almacén de claves que acaba de crear. Tome nota de las dos propiedades siguientes:

- **Nombre del almacén**: nombre que proporcionó al parámetro --name anterior.
- **URI de almacén**: en el ejemplo, es https://&lt;su-nombre-de-almacén-de-claves-único&gt;.vault.azure.net/. Las aplicaciones que utilizan el almacén a través de su API de REST deben usar este identificador URI.

En este momento, su cuenta de Azure es la única autorizada para realizar operaciones en este nuevo almacén.
