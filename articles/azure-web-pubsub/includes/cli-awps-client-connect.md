---
author: vicancy
ms.service: azure-web-pubsub
ms.topic: include
ms.date: 08/06/2021
ms.author: lianwei
ms.openlocfilehash: be837e20579a9236cc634e9518c82f17e2273e6b
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/03/2021
ms.locfileid: "123453752"
---
Use la CLI de Azure [az webpubsub client](/cli/azure/webpubsub/client) para iniciar una conexión de cliente webSocket con el servicio creado en el paso anterior, proporcionando la siguiente información:

- Nombre del centro: una cadena de 1 a 127 caracteres. Debe comenzar con los caracteres alfabéticos `(a-z, A-Z)` y solo puede contener caracteres `(0-9, a-z, A-Z)` alfanuméricos o un guion bajo `(_)`.

Un **centro** es un conjunto lógico de las conexiones de WebSocket conectadas. Para obtener más información sobre estos conceptos, consulte [Acerca de centros de conectividad, grupos y conexiones](../key-concepts.md) para obtener más información sobre los conceptos.

  > [!Important]
  > Reemplace &lt;your-unique-resource-name&gt; por el nombre del recurso de Web PubSub que creó en los pasos anteriores.

- Nombre del centro: **myHub1**.
- Nombre del grupo de recursos: **myResourceGroup**.
- Id. de usuario: **user1**

```azurecli-interactive
az webpubsub client start --name "<your-unique-resource-name>" --resource-group "myResourceGroup" --hub-name "myHub1" --user-id "user1"
```

Puede ver que el comando estableció una conexión de WebSocket con el servicio PubSub web y que recibió un mensaje JSON que indica que ahora está conectado correctamente y se le asigna un único `connectionId`:

```json
{"type":"system","event":"connected","userId":"user1","connectionId":"<your_unique_connection_id>"}
```
