---
title: Adquisición de un token de usuario en Azure Cloud Shell
description: Aquí se explica cómo adquirir un token para el usuario autenticado en Azure Cloud Shell
services: azure
author: maertendMSFT
ms.author: damaerte
tags: azure-resource-manager
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 09/29/2021
ms.openlocfilehash: 117fa3672c78de29cd88797add83fa6e3bf2bf79
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/01/2021
ms.locfileid: "129362742"
---
# <a name="acquire-a-token-in-azure-cloud-shell"></a>Adquisición de un token en Azure Cloud Shell

Azure Cloud Shell proporciona un punto de conexión que autentica automáticamente al usuario que ha iniciado sesión en Azure Portal. Use este punto de conexión para adquirir tokens de acceso para interactuar con los servicios de Azure.

## <a name="authenticating-in-the-cloud-shell"></a>Autenticación en Cloud Shell
Azure Cloud Shell tiene su propio punto de conexión que interactúa con el explorador para iniciar la sesión del usuario automáticamente. Cuando este punto de conexión recibe una solicitud, la devuelve al explorador, que la reenvía al marco principal del portal. La ventana del portal realiza una solicitud a Azure Active Directory, y se devuelve el token resultante.

Si quiere autenticarse con otras credenciales, puede hacerlo mediante `az login` o `Connect-AzAccount`.

## <a name="acquire-and-use-access-token-in-cloud-shell"></a>Adquisición y uso de un token de acceso en Cloud Shell

### <a name="acquire-token"></a>Adquisición de un token

Ejecute los comandos siguientes para establecer el token de acceso de usuario como variable de entorno, `access_token`.
```
response=$(curl http://localhost:50342/oauth2/token --data "resource=https://management.azure.com/" -H Metadata:true -s)
access_token=$(echo $response | python -c 'import sys, json; print (json.load(sys.stdin)["access_token"])')
echo The access token is $access_token
```

### <a name="use-token"></a>Uso del token

Ejecute el siguiente comando para obtener una lista de todas las máquinas virtuales de la cuenta con el token adquirido en el paso anterior.

```
curl https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Compute/virtualMachines?api-version=2021-07-01 -H "Authorization: Bearer $access_token" -H "x-ms-version: 2019-02-02"
```

## <a name="handling-token-expiration"></a>Control de la expiración de un token

El punto de conexión de autenticación local almacena los tokens en caché. Puede llamarse con la frecuencia que se quiera, y solo se realiza una llamada de autenticación a Azure Active Directory si no hay ningún token almacenado en la caché o el token ha expirado.

## <a name="limitations"></a>Limitaciones
- Hay una lista de recursos permitidos a los que se pueden proporcionar tokens de Cloud Shell. Si ejecuta un comando y recibe un mensaje similar a `"error":{"code":"AudienceNotSupported","message":"Audience https://newservice.azure.com/ is not a supported MSI token audience...."}`, se ha encontrado con esta limitación. Puede presentar una incidencia en [GitHub](https://github.com/Azure/CloudShell/issues) para solicitar que este servicio se agregue a la lista de permitidos.
- Si inicia sesión de forma explícita con el comando `az login`, las reglas de acceso condicional que pueda tener la empresa se evalúan en función del contenedor de Cloud Shell en lugar de la máquina donde se ejecuta el explorador. El contenedor de Cloud Shell no cuenta como dispositivo administrado para estas directivas, por lo que los derechos pueden estar limitados por la directiva.
- Las identidades administradas de Azure no están disponibles en Azure Cloud Shell. [Más información sobre las identidades administradas de Azure](../active-directory/managed-identities-azure-resources/overview.md).
