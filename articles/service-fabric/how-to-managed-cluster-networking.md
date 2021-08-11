---
title: Configuración de la red para clústeres administrados de Service Fabric
description: Aprenda a configurar el clúster administrado de Service Fabric para las reglas de grupos de seguridad de red, el acceso a puertos RDP, las reglas de equilibrio de carga, etc.
ms.topic: how-to
ms.date: 5/10/2021
ms.openlocfilehash: 5164a7e3aeb1e82700bd5c5bc4d44e55de64421b
ms.sourcegitcommit: 34feb2a5bdba1351d9fc375c46e62aa40bbd5a1f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/10/2021
ms.locfileid: "111895620"
---
# <a name="configure-network-settings-for-service-fabric-managed-clusters"></a>Configuración de la red para clústeres administrados de Service Fabric

Los clústeres administrados de Service Fabric se crean con una configuración de red predeterminada. Esta configuración consta de reglas obligatorias para la funcionalidad esencial del clúster y algunas reglas opcionales, como permitir de manera predeterminada todo el tráfico de salida, que están diseñadas para facilitar la configuración del cliente.

Además de la configuración de red predeterminada, puede modificar las reglas de red para que se adapten a las necesidades de su escenario.

## <a name="nsg-rules-guidance"></a>Orientaciones sobre las reglas del grupo de seguridad de red

Tenga en cuenta estas consideraciones al crear nuevas reglas del grupo de seguridad de red para el clúster administrado.

* Los clústeres administrados de Service Fabric reservan el intervalo de prioridad de la regla de grupo de seguridad de red del 0 al 999 para la funcionalidad esencial. No se pueden crear reglas de grupos de seguridad de red personalizadas con un valor de prioridad inferior a 1000.
* Los clústeres administrados de Service Fabric reservan el intervalo de prioridad de 3001 a 4000 para crear reglas de grupo de seguridad de red opcionales. Estas reglas se agregan automáticamente para que las configuraciones sean rápidas y sencillas. Para invalidar estas reglas puede agregar reglas de grupo de seguridad de red personalizadas en el intervalo de prioridad de 1000 a 3000.
* Las reglas de grupo de seguridad de red personalizadas deben tener una prioridad en el intervalo de 1000 a 3000.

## <a name="apply-nsg-rules"></a>Aplicación de las reglas de grupo de seguridad de red

Con los clústeres clásicos (no administrados) de Service Fabric, debe declarar y administrar un recurso *Microsoft.Network/networkSecurityGroups* independiente para [aplicar las reglas del grupo de seguridad de red al clúster](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.servicefabric/service-fabric-secure-nsg-cluster-65-node-3-nodetype). Los clústeres administrados de Service Fabric le permiten asignar reglas de grupo de seguridad de red directamente en el recurso de clúster de la plantilla de implementación.

Use la propiedad [networkSecurityRules](/azure/templates/microsoft.servicefabric/managedclusters#managedclusterproperties-object) del recurso *Microsoft.ServiceFabric/managedclusters* (versión `2021-05-01` o posterior) para asignar reglas de grupo de seguridad de red. Por ejemplo:

```json
            "apiVersion": "2021-05-01",
            "type": "Microsoft.ServiceFabric/managedclusters",
            ...
            "properties": {
                ...
                "networkSecurityRules" : [
                    {
                        "name": "AllowCustomers",
                        "protocol": "*",
                        "sourcePortRange": "*",
                        "sourceAddressPrefix": "Internet",
                        "destinationAddressPrefix": "*",
                        "destinationPortRange": "33000-33499",
                        "access": "Allow",
                        "priority": 2001,
                        "direction": "Inbound"
                    },
                    {
                        "name": "AllowARM",
                        "protocol": "*",
                        "sourcePortRange": "*",
                        "sourceAddressPrefix": "AzureResourceManager",
                        "destinationAddressPrefix": "*",
                        "destinationPortRange": "33500-33699",
                        "access": "Allow",
                        "priority": 2002,
                        "direction": "Inbound"
                    },
                    {
                        "name": "DenyCustomers",
                        "protocol": "*",
                        "sourcePortRange": "*",
                        "sourceAddressPrefix": "Internet",
                        "destinationAddressPrefix": "*",
                        "destinationPortRange": "33700-33799",
                        "access": "Deny",
                        "priority": 2003,
                        "direction": "Outbound"
                    },
                    {
                        "name": "DenyRDP",
                        "protocol": "*",
                        "sourcePortRange": "*",
                        "sourceAddressPrefix": "*",
                        "destinationAddressPrefix": "VirtualNetwork",
                        "destinationPortRange": "3389",
                        "access": "Deny",
                        "priority": 2004,
                        "direction": "Inbound",
                        "description": "Override for optional SFMC_AllowRdpPort rule. This is required in tests to avoid Sev2 incident for security policy violation."
                    }
                ],
                "fabricSettings": [
                ...
                ]
            }
```

## <a name="rdp-ports"></a>Puertos RDP

Los clústeres administrados de Service Fabric no permiten el acceso a los puertos RDP de forma predeterminada. Puede abrir puertos RDP en Internet estableciendo la siguiente propiedad en un recurso de clúster administrado de Service Fabric.

```json
"allowRDPAccess": true
```

Si la propiedad allowRDPAccess está establecida en true, se agregará la siguiente regla de grupo de seguridad de red a la implementación del clúster.

```json
{
    "name": "SFMC_AllowRdpPort",
    "type": "Microsoft.Network/networkSecurityGroups/securityRules",
    "properties": {
        "description": "Optional rule to open RDP ports.",
        "protocol": "tcp",
        "sourcePortRange": "*",
        "sourceAddressPrefix": "*",
        "destinationAddressPrefix": "VirtualNetwork",
        "access": "Allow",
        "priority": 3002,
        "direction": "Inbound",
        "sourcePortRanges": [],
        "destinationPortRange": "3389"
    }
}
```

Los clústeres administrados de Service Fabric crean automáticamente reglas NAT de entrada para cada instancia de un tipo de nodo. Para buscar las asignaciones de puertos para llegar a instancias específicas (nodos de clúster), siga estos pasos:

Use Azure Portal para buscar el clúster administrado creado por las reglas NAT de entrada para Protocolo de Escritorio remoto (RDP).

1. Vaya al grupo de recursos de clúster administrado dentro de la suscripción cuyo nombre tiene el siguiente formato: SFC_{cluster-id}

2. Seleccione el equilibrador de carga para el clúster con el siguiente formato: LB-{cluster-name}

3. En la página de su equilibrador de carga, seleccione Reglas NAT de entrada. Revise las reglas NAT de entrada para confirmar la asignación de puerto front-end de entrada a puerto de destino para un nodo. 

   La siguiente captura de pantalla muestra las reglas NAT de entrada para tres tipos de nodos:

   ![Reglas NAT de entrada][Inbound-NAT-Rules]

   De forma predeterminada, para los clústeres de Windows, el puerto de front-end está en el intervalo de 50000 y superiores, y el puerto de destino es el puerto 3389, que se asigna al servicio RDP en el nodo de destino.

4. Conéctese de forma remota al nodo específico (instancia del conjunto de escalado). Puede usar el nombre de usuario y la contraseña que estableció cuando creó el clúster u otras credenciales que haya configurado.

La siguiente captura de pantalla muestra cómo usar Conexión a Escritorio remoto para conectarse al nodo de aplicaciones (instancia 0) en un clúster Windows:

![Conexión del Escritorio remoto][sfmc-rdp-connect]

## <a name="clientconnection-and-httpgatewayconnection-ports"></a>Puertos ClientConnection y HttpGatewayConnection

### <a name="nsg-rule-sfmc_allowservicefabricgatewaytosfrp"></a>Regla de grupo de seguridad de red: SFMC_AllowServiceFabricGatewayToSFRP

Se agrega una regla de grupo de seguridad de red predeterminada para permitir que el proveedor de recursos de Service Fabric tenga acceso a los puertos clientConnectionPort y httpGatewayConnectionPort del clúster. Esta regla permite el acceso a los puertos a través de la etiqueta de servicio "ServiceFabric".

>[!NOTE]
>Esta regla se agrega siempre y no se puede invalidar.

```json
{
    "name": "SFMC_AllowServiceFabricGatewayToSFRP",
    "type": "Microsoft.Network/networkSecurityGroups/securityRules",
    "properties": {
        "description": "This is required rule to allow SFRP to connect to the cluster. This rule cannot be overridden.",
        "protocol": "TCP",
        "sourcePortRange": "*",
        "sourceAddressPrefix": "ServiceFabric",
        "destinationAddressPrefix": "VirtualNetwork",
        "access": "Allow",
        "priority": 500,
        "direction": "Inbound",
        "sourcePortRanges": [],
        "destinationPortRanges": [
            "19000",
            "19080"
        ]
    }
}
```

### <a name="nsg-rule-sfmc_allowservicefabricgatewayports"></a>Regla de grupo de seguridad de red: SFMC_AllowServiceFabricGatewayPorts

Esta regla opcional permite a los clientes acceder a SFX, conectarse al clúster mediante PowerShell y usar puntos de conexión de API de clúster de Service Fabric desde Internet abriendo los puertos LB para clientConnectionPort y httpGatewayPort.

>[!NOTE]
>Esta regla no se agregará si hay una regla personalizada con los mismos valores de acceso, dirección y protocolo para el mismo puerto. Puede invalidar esta regla con reglas de grupo de seguridad de red personalizadas.

```json
{
    "name": "SFMC_AllowServiceFabricGatewayPorts",
    "type": "Microsoft.Network/networkSecurityGroups/securityRules",
    "properties": {
        "description": "Optional rule to open SF cluster gateway ports. To override add a custom NSG rule for gateway ports in priority range 1000-3000.",
        "protocol": "tcp",
        "sourcePortRange": "*",
        "sourceAddressPrefix": "*",
        "destinationAddressPrefix": "VirtualNetwork",
        "access": "Allow",
        "priority": 3001,
        "direction": "Inbound",
        "sourcePortRanges": [],
        "destinationPortRanges": [
            "19000",
            "19080"
        ]
    }
}
```

## <a name="load-balancer-ports"></a>Puertos del equilibrador de carga

Los clústeres administrados de Service Fabric crean una regla de grupo de seguridad de red en el intervalo de prioridad predeterminado de todos los puertos del equilibrador de carga (LB) configurados en la sección "loadBalancingRules" en las propiedades de *ManagedCluster*. Esta regla abre los puertos del equilibrador de carga para el tráfico entrante desde Internet.  

>[!NOTE]
>Esta regla se agrega en el intervalo de prioridad opcional y se puede invalidar mediante la adición de reglas de grupo de seguridad de red personalizadas.

```json
{
    "name": "SFMC_AllowLoadBalancedPorts",
    "type": "Microsoft.Network/networkSecurityGroups/securityRules",
    "properties": {
        "description": "Optional rule to open LB ports",
        "protocol": "*",
        "sourcePortRange": "*",
        "sourceAddressPrefix": "*",
        "destinationAddressPrefix": "VirtualNetwork",
        "access": "Allow",
        "priority": 3003,
        "direction": "Inbound",
        "sourcePortRanges": [],
        "destinationPortRanges": [
        "80", "8080", "4343"
        ]
    }
}
```

## <a name="load-balancer-probes"></a>Sondeos del equilibrador de carga

Los clústeres administrados de Service Fabric crean automáticamente sondeos del equilibrador de carga para los puertos de Fabric Gateway, así como para todos los puertos configurados en la sección "loadBalancingRules" de las propiedades del clúster administrado.

```json
{
  "value": [
    {
        "name": "FabricTcpGateway",
        "properties": {
            "provisioningState": "Succeeded",
            "protocol": "Tcp",
            "port": 19000,
            "intervalInSeconds": 5,
            "numberOfProbes": 2,
            "loadBalancingRules": [
                {
                    "id": "<>"
                }
            ]
        },
        "type": "Microsoft.Network/loadBalancers/probes"
    },
    {
        "name": "FabricHttpGateway",
        "properties": {
            "provisioningState": "Succeeded",
            "protocol": "Tcp",
            "port": 19080,
            "intervalInSeconds": 5,
            "numberOfProbes": 2,
            "loadBalancingRules": [
                {
                    "id": "<>"
                }
            ]
        },
        "type": "Microsoft.Network/loadBalancers/probes"
    },
    {
        "name": "probe1_tcp_8080",
        "properties": {
            "provisioningState": "Succeeded",
            "protocol": "Tcp",
            "port": 8080,
            "intervalInSeconds": 5,
            "numberOfProbes": 2,
            "loadBalancingRules": [
            {
                "id": "<>"
            }
        ]
      },
      "type": "Microsoft.Network/loadBalancers/probes"
    }
  ]
}
```

## <a name="next-steps"></a>Pasos siguientes

[Opciones de configuración del clúster administrado de Service Fabric](how-to-managed-cluster-configuration.md)

[Información general de los clústeres administrados de Service Fabric](overview-managed-cluster.md)

<!--Image references-->
[Inbound-NAT-Rules]: ./media/how-to-managed-cluster-networking/inbound-nat-rules.png
[sfmc-rdp-connect]: ./media/how-to-managed-cluster-networking/sfmc-rdp-connect.png

