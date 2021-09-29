---
title: Configuración de puntos de conexión de nodo en un grupo de Azure Batch
description: Cómo configurar o deshabilitar el acceso a los puertos SSH o RDP en nodos de proceso en un grupo de Azure Batch.
ms.topic: how-to
ms.date: 09/10/2021
ms.openlocfilehash: a3c7d3d8f4aeaea17334b4dc3c65ee47ab982462
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124779965"
---
# <a name="configure-or-disable-remote-access-to-compute-nodes-in-an-azure-batch-pool"></a>Configurar o deshabilitar el acceso remoto a nodos de proceso en un grupo de Azure Batch

De forma predeterminada, Batch permite un [usuario de nodo](/rest/api/batchservice/computenode/adduser) con conectividad de red para conectarse externamente a un nodo de proceso en un grupo de Batch. Por ejemplo, un usuario puede conectarse mediante Escritorio remoto (RDP) en el puerto 3389 a un nodo de proceso en un grupo de Windows. Asimismo, de forma predeterminada, un usuario puede conectarse mediante Shell seguro (SSH) en el puerto 22 a un nodo de proceso en un grupo de Linux. 

En su entorno, tal vez tenga que restringir o deshabilitar esta configuración de acceso externo predeterminada. Puede modificar esta configuración mediante las Batch API para establecer la propiedad [PoolEndpointConfiguration](/rest/api/batchservice/pool/add#poolendpointconfiguration). 

## <a name="about-the-pool-endpoint-configuration"></a>Acerca de la configuración de puntos de conexión de grupo
La configuración de punto de conexión consta de uno o varios [grupos de traducción de direcciones de red (NAT) ](/rest/api/batchservice/pool/add#inboundnatpool) de puertos front-end. (No confundir un grupo NAT con el grupo de nodos de proceso de Batch). Configure cada grupo NAT para invalidar la configuración de conexión predeterminada en los nodos de proceso del grupo. 

Cada configuración de grupo NAT incluye una o varias [reglas de grupo de seguridad de red (NSG)](/rest/api/batchservice/pool/add#networksecuritygrouprule). Cada regla NSG permite o deniega cierto tráfico de red al punto de conexión. Puede elegir permitir o denegar todo el tráfico, el tráfico identificado por una [etiqueta de servicio](../virtual-network/network-security-groups-overview.md#service-tags) (por ejemplo, "Internet") o el tráfico procedente de subredes o direcciones IP específicas.

### <a name="considerations"></a>Consideraciones
* La configuración de punto de conexión del grupo forma parte de la [configuración de red](/rest/api/batchservice/pool/add#networkconfiguration) del grupo. La configuración de red puede incluir, opcionalmente, una configuración para unir el grupo a una [red virtual de Azure](batch-virtual-network.md). Si configura el grupo en una red virtual, puede crear reglas NSG que usen la configuración de dirección de la red virtual.
* Puede configurar varias reglas NSG al configurar un grupo NAT. Las reglas se comprueban por orden de prioridad. Una vez que se aplica una regla, no se comprueba si las demás coinciden.


## <a name="example-deny-all-rdp-traffic"></a>Ejemplo: denegación de todo el tráfico RDP

El siguiente fragmento de código C# muestra cómo configurar el punto de conexión RDP en nodos de proceso de un grupo de Windows para denegar todo el tráfico de red. El punto de conexión utiliza un grupo de servidores front-end de puertos en el intervalo *60000 - 60099*. 

```csharp
pool.NetworkConfiguration = new NetworkConfiguration
{
    EndpointConfiguration = new PoolEndpointConfiguration(new InboundNatPool[]
    {
      new InboundNatPool("RDP", InboundEndpointProtocol.Tcp, 3389, 60000, 60099, new NetworkSecurityGroupRule[]
        {
            new NetworkSecurityGroupRule(162, NetworkSecurityGroupRuleAccess.Deny, "*"),
        })
    })    
};
```

## <a name="example-deny-all-ssh-traffic-from-the-internet"></a>Ejemplo: denegación de todo el tráfico SSH procedente de Internet

El siguiente fragmento de código Python muestra cómo configurar el punto de conexión SSH en nodos de proceso en un grupo de Linux para denegar todo el tráfico de Internet. El punto de conexión utiliza un grupo de servidores front-end de puertos en el intervalo *4000 - 4100*. 

```python
pool.network_configuration = batchmodels.NetworkConfiguration(
    endpoint_configuration=batchmodels.PoolEndpointConfiguration(
        inbound_nat_pools=[batchmodels.InboundNATPool(
            name='SSH',
            protocol='tcp',
            backend_port=22,
            frontend_port_range_start=4000,
            frontend_port_range_end=4100,
            network_security_group_rules=[
                batchmodels.NetworkSecurityGroupRule(
                    priority=170,
                    access=batchmodels.NetworkSecurityGroupRuleAccess.deny,
                    source_address_prefix='Internet'
                )
            ]
        )
        ]
    )
)
```

## <a name="example-allow-rdp-traffic-from-a-specific-ip-address"></a>Ejemplo: autorización del tráfico RDP procedente de una dirección IP específica

El siguiente fragmento de código C# muestra cómo configurar el punto de conexión RDP en nodos de proceso en un grupo de Windows para solo permitir el acceso RDP desde la dirección IP *198.51.100.7*. La segunda regla NSG deniega el tráfico que no coincide con la dirección IP.

```csharp
pool.NetworkConfiguration = new NetworkConfiguration
{
    EndpointConfiguration = new PoolEndpointConfiguration(new InboundNatPool[]
    {
        new InboundNatPool("RDP", InboundEndpointProtocol.Tcp, 3389, 7500, 8000, new NetworkSecurityGroupRule[]
        {   
            new NetworkSecurityGroupRule(179,NetworkSecurityGroupRuleAccess.Allow, "198.51.100.7"),
            new NetworkSecurityGroupRule(180,NetworkSecurityGroupRuleAccess.Deny, "*")
        })
    })    
};
```

## <a name="example-allow-ssh-traffic-from-a-specific-subnet"></a>Ejemplo: autorización del tráfico SSH procedente de una subred específica

El siguiente fragmento de código Python muestra cómo configurar el punto de conexión SSH en nodos de proceso en un grupo de Linux para solo permitir el acceso de la subred *192.168.1.0/24*. La segunda regla NSG deniega el tráfico que no coincide con la subred.

```python
pool.network_configuration = batchmodels.NetworkConfiguration(
    endpoint_configuration=batchmodels.PoolEndpointConfiguration(
        inbound_nat_pools=[batchmodels.InboundNATPool(
            name='SSH',
            protocol='tcp',
            backend_port=22,
            frontend_port_range_start=4000,
            frontend_port_range_end=4100,
            network_security_group_rules=[
                batchmodels.NetworkSecurityGroupRule(
                    priority=170,
                    access='allow',
                    source_address_prefix='192.168.1.0/24'
                ),
                batchmodels.NetworkSecurityGroupRule(
                    priority=175,
                    access='deny',
                    source_address_prefix='*'
                )
            ]
        )
        ]
    )
)
```

## <a name="next-steps"></a>Pasos siguientes

- Obtenga información sobre el [flujo de trabajo y recursos principales del servicio Batch](batch-service-workflow-features.md), como grupos, nodos, trabajos y tareas.
- Para obtener más información sobre reglas NSG en Azure, vea [Filtrado del tráfico de red con grupos de seguridad de red](../virtual-network/network-security-groups-overview.md).