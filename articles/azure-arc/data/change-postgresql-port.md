---
title: Cambio del puerto de PostgreSQL
description: Cambie el puerto donde escucha el grupo de servidores de Hiperescala de PostgreSQL habilitada para Azure Arc.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 11/03/2021
ms.topic: how-to
ms.openlocfilehash: 516c296bc18a3a38b0a6bbe303313119fe2bce1d
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/04/2021
ms.locfileid: "131561809"
---
# <a name="change-the-port-on-which-the-server-group-is-listening"></a>Cambio del puerto donde escucha el grupo de servidores 

Para cambiar el puerto, edite el grupo de servidores. Por ejemplo, ejecute el siguiente comando:

```azurecli
 az postgres arc-server edit -n <server group name> --port <desired port number> --k8s-namespace <namespace> --use-k8s
```

Si el nombre del grupo de servidores es _postgres01_ y quiere que escuche en el puerto _866_. Ejecute el siguiente comando:

```azurecli
 az postgres arc-server edit -n postgres01 --port 866 --k8s-namespace arc --use-k8s
```

## <a name="verify-that-the-port-was-changed"></a>Comprobación del que el puerto ha cambiado

Para comprobar que el puerto ha cambiado, ejecute el siguiente comando para mostrar la configuración del grupo de servidores:

```azurecli
az postgres arc-server show -n <server group name> --k8s-namespace <namespace> --use-k8s
```

En el resultado de ese comando, examine el número de puerto que se muestra para el elemento "port" en la sección "service" de las especificaciones del grupo de servidores.

Como alternativa, en el elemento `externalEndpoint` de la sección "status" de las especificaciones del grupo de servidores puede comprobar que la dirección IP esté seguida del número de puerto que ha configurado.

Como ejemplo, para continuar con el ejemplo anterior, ejecute el comando:

```azurecli
az postgres arc-server show -n postgres01 --k8s-namespace arc --use-k8s
```

El comando devuelve el puerto 866:

```output
"services": {
      "primary": {
        "port": 866,
        "type": "LoadBalancer"
      }
    }
```

Además, observe el valor de `primaryEndpoint`.

```output
"primaryEndpoint": "12.345.67.890:866",
```

## <a name="next-steps"></a>Pasos siguientes
- Lea acerca de [cómo conectarse a su grupo de servidores](get-connection-endpoints-and-connection-strings-postgres-hyperscale.md).
- Obtenga información sobre cómo puede configurar otros aspectos del grupo de servidores en la sección Instrucciones\Administrar\Configuración y escalado de la documentación.
