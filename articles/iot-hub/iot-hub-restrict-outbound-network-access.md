---
title: Restricción del acceso de red saliente de IoT Hub y prevención de pérdida de datos
description: 'Guía para desarrolladores: cómo configurar IoT Hub para el acceso saliente solo a ubicaciones de confianza.'
author: jlian
manager: briz
ms.author: jlian
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/24/2021
ms.custom:
- 'Role: Cloud Development'
ms.openlocfilehash: f4f8044237c82212723a54b677d77bc5aecb95a5
ms.sourcegitcommit: 7854045df93e28949e79765a638ec86f83d28ebc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/25/2021
ms.locfileid: "122868767"
---
# <a name="restrict-outbound-network-access-for-azure-iot-hub"></a>Restricción del acceso de red saliente de Azure IoT Hub

IoT Hub admite la salida de datos a otros servicios mediante el [enrutamiento a puntos de conexión personalizados](iot-hub-devguide-messages-d2c.md), la [carga de archivos](iot-hub-devguide-file-upload.md) y la [exportación de identidades de dispositivo](iot-hub-bulk-identity-mgmt.md). Para mayor seguridad en un entorno empresarial, use la API `restrictOutboundNetworkAccess` para restringir la salida de un centro de IoT a solo destinos aprobados explícitamente. Actualmente esta característica no está disponible en Azure Portal.

## <a name="enabling-the-restriction"></a>Habilitación de la restricción

Para habilitar la característica, use cualquier método para actualizar las propiedades del recurso IoT Hub (un elemento `PUT`) para establecer `restrictOutboundNetworkAccess` en `true` mientras se incluye un parámetro `allowedFqdnList` que contiene nombres de dominio completos (FQDN) como una matriz. 

Ejemplo que muestra la representación JSON que se usará con el [método create o update](/rest/api/iothub/iothubresource/createorupdate):

```json
{
...
            "properties": {
              ...
                "restrictOutboundNetworkAccess": true,
                "allowedFqdnList": [
                    "my-eventhub.servicebus.windows.net",
                    "iothub-ns-built-in-endpoint-2917414-51ea2487eb.servicebus.windows.net"
                ]
              ...
            },
            "sku": {
                "name": "S1",
                "capacity": 1
            }
        }
    }
}
```
Para realizar la misma actualización mediante la CLI de Azure, ejecute:

```azurecli-interactive
az resource update -n <iothubName> -g <resourceGroupName> --resource-type Microsoft.Devices/IotHubs --set properties.restrictOutboundNetworkAccess=true properties.allowedFqdnList="['my-eventhub.servicebus.windows.net','iothub-ns-built-in-endpoint-2917414-51ea2487eb.servicebus.windows.net']"
```

## <a name="restricting-outbound-network-access-with-existing-routes"></a>Restricción del acceso de red saliente con rutas existentes

Una vez que `restrictOutboundNetworkAccess` se ha establecido en `true`, los intentos de emitir datos a destinos fuera de los nombres de dominio completos generan un error. Incluso las rutas configuradas existentes dejan de funcionar si el punto de conexión personalizado no está incluido en la lista de nombres de dominio completos permitidos.

## <a name="built-in-endpoint"></a>Punto de conexión integrado

Si `restrictOutboundNetworkAccess` está establecido en `true`, el punto de conexión compatible con el centro de eventos integrado no está exento de la restricción. En otras palabras, debe incluir el nombre de dominio completo del punto de conexión integrado en la lista de nombres de dominio completos permitidos para que siga funcionando.

## <a name="next-steps"></a>Pasos siguientes

- Para usar identidades administradas en la salida de datos, consulte [Compatibilidad de IoT Hub con identidades administradas](iot-hub-managed-identity.md).
- Para restringir el acceso de red entrante, consulte [Administración del acceso a la red pública para su instancia de IoT Hub](iot-hub-public-network-access.md) y [Compatibilidad de IoT Hub con redes virtuales mediante Private Link e identidad administrada](virtual-network-support.md).