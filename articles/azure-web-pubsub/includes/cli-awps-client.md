---
author: vicancy
ms.service: azure-web-pubsub
ms.topic: include
ms.date: 08/06/2021
ms.author: lianwei
ms.openlocfilehash: 1c9875f6c3c6b69098c915d4257ed05e3ca0abc4
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121788246"
---
### <a name="connect-to-the-service"></a>Conexión al servicio

[!INCLUDE [az webpubsub client](cli-awps-client-connect.md)]

Jugar con él e intentar unirse a grupos mediante `joingroup <group-name>` y enviar mensajes a grupos mediante `sendtogroup <group-name>`:

```azurecli
joingroup group1
```

```azurecli
sendtogroup group1 hello
```

[!INCLUDE [publish messages](cli-awps-service.md)]
