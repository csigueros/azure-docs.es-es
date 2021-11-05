---
author: vicancy
ms.service: azure-web-pubsub
ms.topic: include
ms.date: 08/06/2021
ms.author: lianwei
ms.openlocfilehash: a34e4d77d77ad34a8a0487a9efcdeb48ed510dda
ms.sourcegitcommit: 96deccc7988fca3218378a92b3ab685a5123fb73
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/04/2021
ms.locfileid: "131663674"
---
### <a name="publish-messages-and-manage-the-clients"></a>Publicación de mensajes y administración de los clientes

La CLI de Azure también proporciona comandos [az webpubsub service](/cli/azure/webpubsub/service) para administrar las conexiones de cliente.

Abra **otro** comando de la CLI y podrá difundir mensajes a los clientes.

- Nombre del centro: **myHub1**.
- Nombre del grupo de recursos: **myResourceGroup**.

```azurecli-interactive
az webpubsub service broadcast --name "<your-unique-resource-name>" --resource-group "myResourceGroup" --hub-name "myHub1" --payload "Hello World"
```

Vuelva al comando anterior de la CLI y verá que el cliente recibió el mensaje:
```JSON
{"type":"message","from":"server","dataType":"text","data":"Hello World"}
```

También puede mostrar todos los comandos disponibles mediante la opción `--help` y probarlos.

```azurecli-interactive
az webpubsub service --help
```
