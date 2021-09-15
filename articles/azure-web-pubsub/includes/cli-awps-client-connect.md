---
author: vicancy
ms.service: azure-web-pubsub
ms.topic: include
ms.date: 08/06/2021
ms.author: lianwei
ms.openlocfilehash: 1f6356ceaa844433e6da3102b8db0fcbd5a61615
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121785569"
---
Use la CLI de Azure [az webpubsub client](/cli/azure/webpubsub/client) para iniciar una conexión de cliente webSocket con el servicio creado en el paso anterior, proporcionando la siguiente información:

- Nombre del centro: una cadena de 1 a 127 caracteres. Debe comenzar con los caracteres alfabéticos `(a-z, A-Z)` y solo puede contener caracteres `(0-9, a-z, A-Z)` alfanuméricos o un guion bajo `(_)`.

Un **centro** es un conjunto lógico de las conexiones de WebSocket conectadas. Para obtener más información sobre estos conceptos, consulte [Acerca de centros de conectividad, grupos y conexiones](../key-concepts.md) para obtener más información sobre los conceptos.

  > [!Important]
  > Reemplace &lt;your-unique-resource-name&gt; por el nombre del recurso de Web PubSub que creó en los pasos anteriores.

- Nombre del centro: **myHub1**.
- Nombre del grupo de recursos: **myResourceGroup**.

```azurecli-interactive
az webpubsub client start --name "<your-unique-resource-name>" --resource-group "myResourceGroup" --hub-name myHub1
```

Puede ver que el comando estableció una conexión de WebSocket con el servicio PubSub web y que recibió un mensaje JSON que indica que ahora está conectado correctamente y se le asigna un único `connectionId`:

```json
{"type":"system","event":"connected","userId":null,"connectionId":"<your_unique_connection_id>"}
```
