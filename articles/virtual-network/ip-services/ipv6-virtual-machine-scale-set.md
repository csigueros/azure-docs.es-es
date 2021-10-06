---
title: Implementación de conjuntos de escalado de máquinas virtuales con IPv6 en Azure
titlesuffix: Azure Virtual Network
description: En este artículo se muestra cómo implementar conjuntos de escalado de máquinas virtuales con IPv6 en una red virtual de Azure.
services: virtual-network
documentationcenter: na
author: asudbring
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/31/2020
ms.author: allensu
ms.openlocfilehash: 0f8bce99f26f175f3103ccc4824c094b2e1c4654
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/01/2021
ms.locfileid: "129367828"
---
# <a name="deploy-virtual-machine-scale-sets-with-ipv6-in-azure"></a>Implementación de conjuntos de escalado de máquinas virtuales con IPv6 en Azure

En este artículo se muestra cómo implementar un conjunto de escalado de máquinas virtuales de pila doble (IPv4 + IPv6) con un equilibrador de carga externo de pila doble en una red virtual de Azure. El proceso para crear un conjunto de escalado de máquinas virtuales compatible con IPv6 es casi idéntico al de crear máquinas virtuales individuales que se describe [aquí](../../load-balancer/ipv6-configure-standard-load-balancer-template-json.md). Empezará con los pasos que son similares a los descritos para las máquinas virtuales individuales:
1.    Crear direcciones IP públicas IPv4 e IPv6
2.    Crear un equilibrador de carga de pila doble  
3.    Crear reglas del grupo de seguridad de red (NSG)  

El único paso que no es igual en las máquinas virtuales individuales es la creación de la configuración de interfaz de red (NIC) que usa el recurso del conjunto de escalado de máquinas virtuales networkProfile/networkInterfaceConfigurations. La estructura JSON es similar a la del objeto Microsoft.Network/networkInterfaces que se usa para las máquinas virtuales individuales, pero se agrega la NIC y la configuración de IpConfiguration de IPv4 como interfaz principal mediante el atributo **"primary": true**, tal como se aprecia en el ejemplo siguiente:

```json
          "networkProfile": {
            "networkInterfaceConfigurations": [
              {
                "name": "[variables('nicName')]",
                "properties": {
                  "primary": true,
          "networkSecurityGroup": {
            "id": "[resourceId('Microsoft.Network/networkSecurityGroups','VmssNsg')]"
          },                  
                  "ipConfigurations": [
                    {
                      "name": "[variables('ipConfigName')]",
                      "properties": {
                        "primary": true,
                        "subnet": {
                          "id": "[resourceId('Microsoft.Network/virtualNetworks/subnets', 'MyvirtualNetwork','Mysubnet')]"
                        },
                        "privateIPAddressVersion":"IPv4",                       
                        "publicipaddressconfiguration": {
                          "name": "pub1",
                          "properties": {
                            "idleTimeoutInMinutes": 15
                          }
                        },
                        "loadBalancerBackendAddressPools": [
                          {
                            "id": "[resourceId('Microsoft.Network/loadBalancers/backendAddressPools', 'loadBalancer', 'bePool'))]"
                          }
                        ],
                        "loadBalancerInboundNatPools": [
                          {
                            "id": "[resourceId('Microsoft.Network/loadBalancers/inboundNatPools', 'loadBalancer', 'natPool')]"
                          }
                        ]
                      }
                    },
                    {
                      "name": "[variables('ipConfigNameV6')]",
                      "properties": {
                        "subnet": {
                          "id": "[resourceId('Microsoft.Network/virtualNetworks/subnets','MyvirtualNetwork','Mysubnet')]"
                        },
                        "privateIPAddressVersion":"IPv6",
                        "loadBalancerBackendAddressPools": [
                          {
                            "id": "[resourceId('Microsoft.Network/loadBalancers/backendAddressPools', 'loadBalancer','bePoolv6')]"
                          }
                        ],                        
                      }
                    }
                  ]
                }
              }
            ]
          }

```


## <a name="sample-virtual-machine-scale-set-template-json"></a>Estructura JSON de ejemplo de la plantilla de conjunto de escalado de máquinas virtuales

Para implementar un conjunto de escalado de máquinas virtuales de pila doble (IPv4 + IPv6) con un equilibrador de carga externo de pila doble y la plantilla de ejemplo de vista de red virtual, consulte [aquí](https://azure.microsoft.com/resources/templates/ipv6-in-vnet-vmss/).
## <a name="next-steps"></a>Pasos siguientes

Para más información sobre la compatibilidad con IPv6 en las redes virtuales de Azure, consulte [¿Qué es IPv6 para Azure Virtual Network?](ipv6-overview.md)
