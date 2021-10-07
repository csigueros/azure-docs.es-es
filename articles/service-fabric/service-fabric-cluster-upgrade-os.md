---
title: Actualización del sistema operativo Linux para Azure Service Fabric
description: Obtenga información sobre las opciones para migrar el clúster de Azure Service Fabric a otro sistema operativo Linux.
manager: tassb
ms.topic: conceptual
ms.date: 09/14/2021
ms.openlocfilehash: 4a5b1a97fde977fa4ba64a4a23d0c57725595c37
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128699540"
---
# <a name="upgrading-linux-os-for-azure-service-fabric"></a>Actualización del sistema operativo Linux para Azure Service Fabric

En este documento, se describe la guía para migrar el clúster de Azure Service Fabric para Linux de la versión 16.04 LTS de Ubuntu a la 18.04 LTS. Cada versión del sistema operativo requiere un paquete del entorno de ejecución de SF distinto, lo que requiere los pasos descritos en este documento para facilitar una migración sin problemas.

## <a name="overview"></a>Información general

Este es el enfoque general:

1. Cambie el recurso de ARM (Azure Resource Manager) del clúster de Service Fabric de "vmImage" a "Ubuntu18_04" para extraer futuras actualizaciones de código para esta versión del sistema operativo. Este error de coincidencia temporal del sistema operativo con los tipos de nodo existentes bloqueará los lanzamientos automáticos de actualización de código para garantizar una sustitución segura.

    * Evite emitir actualizaciones manuales del código del clúster de SF durante la migración del sistema operativo. Si lo hace, los nodos del tipo del nodo antiguo pueden entrar en un estado que requerirá intervención humana.

2. Para cada tipo de nodo del clúster, cree otro tipo de nodo destinado a la imagen del sistema operativo Ubuntu 18.04 para el conjunto de escalado de máquinas virtuales subyacente. Cada nuevo tipo de nodo asumirá el rol de su homólogo anterior.

    * Tendrá que crear un nuevo tipo de nodo principal para reemplazar el tipo de nodo anterior marcado como "isPrimary": true.
    
    * Para cada tipo de nodo no principal adicional, estos tipos de nodos se marcarán de forma similar como "isPrimary": false.

    * Asegúrese de que, una vez creado el nuevo tipo de nodo del sistema operativo de destino, las cargas de trabajo existentes sigan funcionando correctamente. Si se observan problemas, realice los cambios necesarios en la aplicación o en los paquetes de máquina preinstalados antes de continuar con la eliminación del tipo de nodo anterior.
3. Marque el tipo de nodo principal anterior como "isPrimary": false. Esto dará como resultado un conjunto de actualizaciones de larga duración para realizar la transición de todos los nodos de inicialización.
4. (Solo para tipos de nodos de durabilidad Bronze): Conéctese al clúster mediante [sfctl](https://docs.microsoft.com/azure/service-fabric/service-fabric-sfctl) / [PowerShell](https://docs.microsoft.com/powershell/module/ServiceFabric/?view=azureservicefabricps) / [FabricClient](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient?view=azure-dotnet) y deshabilite todos los nodos del tipo de nodo anterior.
5. Quite los tipos de nodo anteriores.

> [!NOTE]
> Az PowerShell genera un nuevo nombre DNS para el tipo de nodo agregado, por lo que el tráfico externo tendrá que redirigirse a este punto de conexión.


## <a name="ease-of-use-steps-for-non-production-clusters"></a>Pasos de facilidad de uso para clústeres que no son de producción

> [!NOTE]
> En los pasos siguientes, se muestra cómo crear rápidamente un prototipo de la migración del tipo de nodo mediante cmdlets de Az PowerShell en un clúster de solo PRUEBA. En el caso de los clústeres de producción que se enfrentan al tráfico empresarial real, se espera que se den los mismos pasos mediante la emisión de actualizaciones de ARM para conservar la capacidad de reproducción y un origen de verdad declarativo coherente.

1. Actualice la configuración de vmImage en el recurso del clúster de Service Fabric mediante [Update-AzServiceFabricVmImage](https://docs.microsoft.com/powershell/module/az.servicefabric/update-azservicefabricvmimage):

    [Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps):
    ```powershell
    # Replace subscriptionId, resourceGroup, clusterName with ones corresponding to your cluster.
    $subscriptionId="cea219db-0593-4b27-8bfa-a703332bf433"
    Login-AzAccount; Select-AzSubscription -SubscriptionId $subscriptionId

    $resourceGroup="Group1"
    $clusterName="Contoso01SFCluster"
    # Update cluster vmImage to target OS. This registers the SF runtime package type that is supplied for upgrades.
    Update-AzServiceFabricVmImage -ResourceGroupName $resourceGroup -ClusterName $clusterName -VmImage Ubuntu18_04
    ```

2. Agregue un nuevo de tipo de nodo homólogo para cada uno de los tipos de nodo existentes:

    ```powershell
    $nodeTypeName="nt1u18"
    # You can customize this to fetch a password from a secure store.
    $securePassword = ConvertTo-SecureString -String 'Yourpassword123!@#' -AsPlainText -Force

    # Ensure last upgrade is done - Ready means the next command can be issued.
    (Get-AzServiceFabricCluster -ResourceGroupName $resourceGroup).ClusterState

    # Add new primary node type. Omit the IsPrimaryNodeType parameter for non-primary node types.
    Add-AzServiceFabricNodeType -ResourceGroupName $resourceGroup  -ClusterName $clusterName -NodeType $nodeTypeName -Capacity 5 -VmUserName testuser -VmPassword $securePassword -DurabilityLevel Silver -Verbose -VMImageSku 18.04-LTS -IsPrimaryNodeType $true

    # Redirect traffic to new node type dns
    # dns-Contoso01SFCluster-nt1u18.westus2.cloudapp.azure.com
    ```

3. Actualice el tipo de nodo principal anterior a no principal para sustituir los nodos de inicialización y los servicios del sistema al nuevo tipo de nodo:

    ```powershell
    # Query to ensure background upgrades are done.
    (Get-AzServiceFabricCluster -ResourceGroupName $resourceGroup).ClusterState

    # Update old nodetype to isPrimary: false
    $oldNodeTypeName="nt1"
    Update-AzServiceFabricNodeType -ResourceGroupName $resourceGroup -ClusterName $clusterName -IsPrimaryNodeType $false -NodeType $oldNodeTypeName -Verbose

    # Ensure node type is showing isPrimary: False before proceeding.
    Get-AzServiceFabricCluster -ResourceGroupName $resourceGroup
    ```

    Salida de ejemplo:
    ```
    NodeTypes :
              NodeTypeDescription :
                  Name : nt1
                  PlacementProperties :
                  Capacities :
                  ClientConnectionEndpointPort : 19000
                  HttpGatewayEndpointPort : 19080
                  DurabilityLevel : Bronze
                  ApplicationPorts :
                      StartPort : 20000
                      EndPort : 30000
                  EphemeralPorts :
                      StartPort : 49152
                      EndPort : 65534
                  IsPrimary : False
                  VmInstanceCount : 5
                  ReverseProxyEndpointPort :
              NodeTypeDescription :
                  Name : nt1u18
                  PlacementProperties :
                  Capacities :
                  ClientConnectionEndpointPort : 19000
                  HttpGatewayEndpointPort : 19080
                  DurabilityLevel : Silver
                  ApplicationPorts :
                      StartPort : 20000
                      EndPort : 30000
                  EphemeralPorts :
                      StartPort : 49152
                      EndPort : 65534
                  IsPrimary : True
                  VmInstanceCount : 5
                  ReverseProxyEndpointPort :
    ```

4. Para quitar los tipos de nodos de durabilidad Bronze, deshabilite primero los nodos antes de continuar para quitar el tipo de nodo anterior. Conéctese mediante *ssh* a un nodo del clúster y ejecute los siguientes comandos:

    ```bash
    # as root user:

    # install jq tool to automatically parse JSON responses
    apt-get install jq -fy

    # retrieve the thumbprint to be used for establishing a fabric client
    dataroot=$(cat /etc/servicefabric/FabricDataRoot)
    nodename=_nt1_0
    cat $dataroot/$nodename/Fabric/ClusterManifest.current.xml | grep ClientCertThumbprints
    # 0777FE1A43E306F332D96DA339EF6834D0E4A453

    # verify node count
    sfctl cluster select --endpoint https://Contoso01SFCluster.westus2.cloudapp.azure.com:19080 --pem /var/lib/waagent/0777FE1A43E306F332D96DA339EF6834D0E4A453.pem --no-verify

    # sample command to list all nodes
    sfctl node list

    # for each node part of the node type to be removed, disable the node:
    nodeTypeBeingDisabled=nt1
    nodes=$(sfctl node list | jq --arg nodeTypeBeingDisabled "$nodeTypeBeingDisabled" '.items[] | select(.type==$nodeTypeBeingDisabled) | .name' | sed s/\"//g)
    echo $nodes
    for n in $nodes; do echo "Disabling $n"; sfctl node disable --node-name $n --deactivation-intent RemoveNode --timeout 300; done
    ```

5. Quite el tipo de nodo anterior quitando el atributo de tipo de nodo del recurso de clúster de SF y retirando el conjunto de escalado de máquinas virtuales asociado y los recursos de redes.

    ```powershell
    $resourceGroup="Group1"
    $clusterName="Contoso01SFCluster"
    $oldNodeTypeName="nt1"

    # Remove the Service Fabric node type, associated virtual machine scale set resource, and any trailing networking resources that are no longer used. 
    Remove-AzServiceFabricNodeType -ResourceGroupName $resourceGroup -ClusterName $clusterName -NodeType $oldNodeTypeName
    ```

    > [!NOTE]
    > En algunos casos, esto puede producir el siguiente error. En tal caso, puede ver mediante Service Fabric Explorer (SFX) que el elemento InfrastructureService para el tipo de nodo que se ha quitado está en estado de error. Para resolverlo, vuelva a intentar la eliminación.
    ```
    Remove-AzServiceFabricNodeType : Code: ClusterUpgradeFailed, Message: Long running operation failed with status 'Failed'
    ```

Una vez que se ha confirmado que las cargas de trabajo se han migrado correctamente a los nuevos tipos de nodo y se han purgado los tipos de nodo anteriores, el clúster está listo para continuar con las posteriores actualizaciones de la versión de código del entorno de ejecución de Service Fabric y la configuración.

## <a name="next-steps"></a>Pasos siguientes

* [Administración de actualizaciones de Service Fabric](service-fabric-cluster-upgrade-version-azure.md)
* [Personalización de la configuración de un clúster de Service Fabric](service-fabric-cluster-fabric-settings.md)
* Obtenga más información sobre las [características de durabilidad](./service-fabric-cluster-capacity.md#durability-characteristics-of-the-cluster) del clúster.
* Obtenga más información sobre los [tipos de nodo y los conjuntos de escalado de máquinas virtuales](service-fabric-cluster-nodetypes.md).
* Obtenga más información sobre el [escalado de clústeres de Service Fabric](service-fabric-cluster-scaling.md).
* [Escalar o reducir horizontalmente el clúster](service-fabric-cluster-scale-in-out.md)
* [Eliminación de un tipo de nodo de Azure Service Fabric](service-fabric-how-to-remove-node-type.md)

