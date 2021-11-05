---
author: dlepow
ms.service: container-registry
ms.topic: include
ms.date: 10/07/2021
ms.author: danlep
ms.openlocfilehash: eb85c0df13a90afb08a3730ff58457f9897650ae
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131091096"
---
La salida del comando incluye la cadena de conexión del registro y la configuración relacionada. En la salida de ejemplo siguiente, se muestra la cadena de conexión del registro conectado llamado *myconnectedregistry* con el registro primario *contosoregistry*:

```json
{
  "ACR_REGISTRY_CONNECTION_STRING": "ConnectedRegistryName=myconnectedregistry;SyncTokenName=myconnectedregistry-sync-token;SyncTokenPassword=xxxxxxxxxxxxxxxx;ParentGatewayEndpoint=contosoregistry.eastus.data.azurecr.io;ParentEndpointProtocol=https"
}
```
