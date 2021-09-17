---
author: vicancy
ms.service: azure-web-pubsub
ms.topic: include
ms.date: 08/06/2021
ms.author: lianwei
ms.openlocfilehash: 2b12b25b973d9e56de0241c1e088e2409137b086
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121788255"
---
### <a name="publish-messages-and-manage-the-clients"></a>Publicación de mensajes y administración de los clientes

La CLI de Azure también proporciona comandos [az webpubsub service](/cli/azure/webpubsub/service) para administrar las conexiones de cliente.

Abra **otro** comando de la CLI y podrá difundir mensajes a los clientes.

- Nombre del centro: **myHub1**.
- Nombre del grupo de recursos: **myResourceGroup**.

```azurecli-interactive
az webpubsub service broadcast --name "<your-unique-resource-name>" --resource-group "myResourceGroup" --hub-name myHub1 --payload "Hello World"
```

Vuelva al comando anterior de la CLI y verá que el cliente recibió el mensaje:
```JSON
{"type":"message","from":"server","dataType":"text","data":"Hello World"}
```

También puede mostrar todos los comandos disponibles mediante la opción `--help` y probarlos.

```azurecli-interactive
az webpubsub service --help
```
