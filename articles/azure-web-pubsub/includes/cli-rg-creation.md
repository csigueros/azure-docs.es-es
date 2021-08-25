---
author: vicancy
ms.service: azure-web-pubsub
ms.topic: include
ms.date: 08/06/2021
ms.author: lianwei
ms.openlocfilehash: c2f05a81e52edc858bc13bc016e030236241a6f0
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121785570"
---
Un grupo de recursos es un contenedor lógico en el que se implementan y se administran los recursos de Azure. Utilice el comando [az group create](/cli/azure/group#az_group_create) para crear un grupo de recursos denominado *myResourceGroup* en la ubicación *eastus*.

```azurecli-interactive
az group create --name "myResourceGroup" -l "EastUS"
```
