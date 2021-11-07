---
title: API de selección de ubicación de circuitos CrossConnnections de Azure ExpressRoute
description: En este artículo se proporciona información general detallada para los asociados de ExpressRoute sobre la API de selección de ubicación de circuitos CrossConnections de ExpressRoute.
services: expressroute
author: mialdrid
ms.service: expressroute
ms.topic: conceptual
ms.date: 10/19/2021
ms.author: mialdrid
ms.openlocfilehash: 3b464e0d9bb5c770109899f7aba8c9e0934d2064
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131093549"
---
# <a name="expressroute-circuit-placement-api"></a>API de selección de ubicación de circuitos de ExpressRoute

La API de selección de ubicación de circuitos de los asociados de ExpressRoute permite a dichos asociados aprovisionar la conectividad de circuito en un par de puertos específicos. En concreto, si un asociado de ExpressRoute administra varios pares de puertos en una ubicación de emparejamiento, puede usar esta API para seleccionar qué par de puertos facilitará el circuito ExpressRoute.

Esta API usa el tipo de recurso expressRouteCrossConnection. Para más información, consulte [Desarrollo e integración de la API CrossConnnections de ExpressRoute](cross-connections-api-development.md).

## <a name="workflow"></a>Flujo de trabajo

1. Los clientes de ExpressRoute comparten la clave de servicio del circuito ExpressRoute de destino.

1. El asociado de ExpressRoute ejecuta una instrucción GET mediante la API expressRouteProviderPorts para identificar el recurso **portPairDescription** del par de puertos de destino. El asociado de ExpressRoute puede consultar una lista de recursos PortPairDescription en todos los pares de puertos de la suscripción o establecer el ámbito de la consulta para una ubicación de emparejamiento específica.

1. Una vez identificado el recurso PortPairDescription de destino, el asociado de ExpressRoute realiza la operación GET/PUT de expressRouteCrossConnection para mover el circuito ExpressRoute al par de puertos de destino.

Los asociados de ExpressRoute administran la configuración de nivel 2 y nivel 3 emitiendo operaciones REST en el recurso expressRouteCrossConnections.

## <a name="api-development-and-integration-steps"></a>Pasos de desarrollo e integración de API

### <a name="get-using-the-expressrouteproviderports-api-to-list-port-pairs"></a>Operación GET mediante la API del recurso expressRouteProviderPorts para enumerar pares de puertos

El asociado de ExpressRoute puede enumerar todos los pares de puertos dentro de la suscripción del proveedor de destino o enumerar los pares de puertos dentro de una ubicación de emparejamiento específica.

### <a name="to-get-a-list-of-all-port-pairs-for-a-provider"></a>Para obtener una lista de todos los pares de puertos de un proveedor

https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Network/expressRouteProviderPorts 

#### <a name="get-operation"></a>Operación GET

```rest
{
    "parameters": {
      "api-version": "2020-03-01",
      "subscriptionId": "subid"
    },
    "responses": {
        "200": {
          "body": {
            "value": [
              {
                "portPairDescriptor": "bvtazureixpportpair1",
                "id": "/subscriptions/subid/providers/Microsoft.Network/ExpressRouteProviderPort/bvtazureixpportpair1",
                "type": "Microsoft.Network/expressRouteProviderPort",
                "location": "uswest",
                "etag": "W/\"c0e6477e-8150-4d4f-9bf6-bb10e6acb63a\"",
                "properties": {
                    "portPairDescriptor": "bvtazureixpportpair",
                    "primaryAzurePort": "bvtazureixp01a",
                    "secondaryAzurePort": "bvtazureixp01b",
                    "peeringLocation": "SiliconValley",
                    "overprovisionFactor": 4,
                    "portBandwidthInMbps": 4000,
                    "usedBandwidthInMbps": 2500,
                    "remainingBandwidthInMbps": 1500
                }
              },
              {
                "portPairDescriptor": "bvtazureixpportpair2",
                "id": "/subscriptions/subid/providers/Microsoft.Network/ ExpressRouteProviderPort/bvtazureixpportpair2",
                "type": "Microsoft.Network/expressRouteProviderPort",
                "location": "uswest",
                "etag": "W/\"c0e6477e-8150-4d4f-9bf6-bb10e6acb63a\"",
                "properties": {
                    "portPairDescriptor": "bvtazureixpportpair2",
                    "primaryAzurePort": "bvtazureixp02a",
                    "secondaryAzurePort": "bvtazureixp02b",
                    "peeringLocation": "seattle",
                    "overprovisionFactor": 4,
                    "portBandwidthInMbps": 4000,
                    "usedBandwidthInMbps": 1200,
                    "remainingBandwidthInMbps": 1800
                }
              }
            ]
          }
        }
      }
    }
  }
}
```

**Código de estado de respuesta**

* 200 (Correcto) La solicitud es correcta.Capturará la lista de puertos.
* 4XX (solicitud no válida) Error en una de las validaciones; por ejemplo, el subid. del proveedor no es válido.

### <a name="list-of-all-port-for-a-provider-for-a-particular-peering-location"></a>Lista de todos los puertos de un proveedor para una ubicación de emparejamiento determinada

#### <a name="get"></a>GET

https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Network/expressRouteProviderPorts?location={locationName}

#### <a name="get-operation"></a>Operación GET

```rest
{
  "parameters": {
    "api-version": "2020-03-01",
    "locationName": "SiliconValley",
    "subscriptionId": "subid"
  },
  "responses": {
    "200": {
      "body": {
        "value": [
          {
            "portPairDescriptor": "bvtazureixpportpair1",
            "id": "/subscriptions/subid/providers/Microsoft.Network/ ExpressRouteProviderPort /bvtazureixpportpair1",
            "type": "Microsoft.Network/expressRouteProviderPort",
            "location": "uswest",
            "etag": "W/\"c0e6477e-8150-4d4f-9bf6-bb10e6acb63a\"",
            "properties": {
              "portPairDescriptor": "bvtazureixpportpair",
              "primaryAzurePort": "bvtazureixp01a",
              "secondaryAzurePort": "bvtazureixp01b",
              "peeringLocation": "SiliconValley",
              "overprovisionFactor": 4,
              "portBandwidthInMbps": 4000,
              "usedBandwidthInMbps": 2500,
              "remainingBandwidthInMbps": 1500
            }
          }
        ]
      }
    }
  }
}
```

**Código de estado de respuesta**

* 200 (Correcto) La solicitud es correcta. Capturará la lista de puertos.
* 4XX (solicitud no válida) Error en una de las validaciones; por ejemplo, el subid. del proveedor no es válido o la ubicación no es válida.

Para obtener los detalles de puerto de un puerto determinado mediante el identificador del descriptor de par de puertos.

#### <a name="get"></a>GET

https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Network/expressRouteProviderPorts/{portPairDescriptor}

#### <a name="get-operation"></a>Operación GET

```rest
{
  "parameters": {
    "api-version": "2020-03-01",
    "portPairDescriptor": " bvtazureixpportpair1",
    "subscriptionId": "subid"
  },
  "responses": {
    "200": {
      "body": {
        "value": [
          {
            "portPairDescriptor": "bvtazureixpportpair1",
            "id": "/subscriptions/subid/providers/Microsoft.Network/ExpressRouteProviderPort/bvtazureixpportpair1",
            "type": "Microsoft.Network/expressRouteProviderPort",
            "location": "uswest",
            "etag": "W/\"c0e6477e-8150-4d4f-9bf6-bb10e6acb63a\"",
            "properties": {
              "portPairDescriptor": "bvtazureixpportpair",
              "primaryAzurePort": "bvtazureixp01a",
              "secondaryAzurePort": "bvtazureixp01b",
              "peeringLocation": "SiliconValley",
              "overprovisionFactor": 4,
              "portBandwidthInMbps": 4000,
              "usedBandwidthInMbps": 2500,
              "remainingBandwidthInMbps": 15
            }
          }
        ]
      }
    }
  }
}
```

**Descripción del código de estado**

* 200 (Correcto) La solicitud es correcta. Capturará los detalles del puerto.
* 204 El par de puertos con el identificador de descriptor mencionado no está disponible.
* 4XX (solicitud no válida) Error en una de las validaciones; por ejemplo, el subid. del proveedor no es válido.

### <a name="put-expressroutecrossconnection-api-to-move-a-circuit-to-a-specific-port-pair"></a>Operación PUT de la API del recurso expressRouteCrossConnection para mover un circuito a un par de puertos específico

Una vez identificado el recurso portPairDescriptor del par de puertos de destino, el asociado de ExpressRoute puede usar la [API de ExpressRouteCrossConnection](/rest/api/expressroute/express-route-cross-connections/create-or-update) para mover el circuito ExpressRoute a un par de puertos específico.

Actualmente, los proveedores usan esta API para actualizar el estado de aprovisionamiento del circuito. Los proveedores usarán esta misma API para actualizar el par de puertos del circuito.

Actualmente, los recursos primaryAzurePort y secondaryAzurePort son propiedades de solo lectura. Ahora hemos deshabilitado las propiedades de solo lectura para estos puertos.

#### <a name="put"></a>PUT

https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/expressRouteCrossConnections/{crossConnectionName}?api-version=2021-02-01

#### <a name="put-operation"></a>Operación PUT

```rest
{
"parameters": {
    "api-version": "2020-03-01",
    "crossConnectionName": "The name of the cross connection",
    "subscriptionId": "subid"
  }
},
{
Request   "body": {
    " primaryAzurePort ": " bvtazureixp03a"
     "secondaryAzurePort": "bvtazureixp03b",
  }
}
Response:
{
  "name": "<circuitServiceKey>",
  "id": "/subscriptions/subid/resourceGroups/CrossConnectionSiliconValley/providers/Microsoft.Network/expressRouteCrossConnections/<circuitServiceKey>",
  "type": "Microsoft.Network/expressRouteCrossConnections",
  "location": "brazilsouth",
  "properties": {
    "provisioningState": "Enabled",
    "expressRouteCircuit": {
      "id": "/subscriptions/subid/resourceGroups/ertest/providers/Microsoft.Network/expressRouteCircuits/er1"
    },
    "peerings": [],
    "peeringLocation": "SiliconValley",
    "bandwidthInMbps": 1000,
    "primaryAzurePort": "bvtazureixp03a",
    "secondaryAzurePort": "bvtazureixp03b",
    "sTag": 2,
    "serviceProviderProvisioningState": "NotProvisioned"
  }
}
```

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre todas las API de REST de ExpressRoute, consulte [API de REST de ExpressRoute](/rest/api/expressroute/).