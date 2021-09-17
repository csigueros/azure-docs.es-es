---
author: vicancy
ms.service: azure-web-pubsub
ms.topic: include
ms.date: 08/06/2021
ms.author: lianwei
ms.openlocfilehash: 785114c797fba5c84e97f5a0d0cc1a34dc14d540
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121785578"
---
Use el comando de la CLI de Azure [az webpubsub create](/cli/azure/webpubsub#az_webpubsub_create) para crear una instancia de Web PubSub en el grupo de recursos del paso anterior, proporcionando la siguiente información:

- Nombre del recurso: una cadena de entre 3 y 24 caracteres que puede contener solo números (0-9), letras (a-z, A-Z) y guiones (-).

  > [!Important]
  > Cada recurso de Web PubSub debe tener un nombre único. Reemplace &lt;your-unique-resource-name&gt; por el nombre de Web PubSub en los ejemplos siguientes.

- Nombre del grupo de recursos: **myResourceGroup**.
- Ubicación: **EastUS**.
- SKU: **Free_F1**

```azurecli-interactive
az webpubsub create --name "<your-unique-resource-name>" --resource-group "myResourceGroup" --location "EastUS" --sku Free_F1
```

> [!Note]
> Puesto que el servicio Web PubSub todavía está en versión preliminar, es posible que haya observado que hay una advertencia en la consola de la CLI con un mensaje similar al siguiente: "El comando requiere la extensión webpubsub. ¿Quiere instalarla ahora? El comando seguirá ejecutándose una vez instalada la extensión. (Y/n)". Presione Y para instalar la extensión.

La salida de este comando muestra las propiedades del recurso que acaba de crear. Tome nota de las dos propiedades siguientes:

- **Nombre del recurso**: nombre que proporcionó al parámetro `--name` anterior.
- **hostName**: en el ejemplo, el nombre de host es `<your-unique-resource-name>.webpubsub.azure.com/`.

En este momento, su cuenta de Azure es la única autorizada para realizar operaciones en este nuevo recurso.
