---
title: Creación de un clúster privado de Azure Kubernetes Service
description: Aprenda a crear un clúster privado de Azure Kubernetes Service (AKS).
services: container-service
ms.topic: article
ms.date: 8/30/2021
ms.openlocfilehash: dcf969745fcc3c98b5bd0a9ba3681be602b73eb1
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2021
ms.locfileid: "129210219"
---
# <a name="create-a-private-azure-kubernetes-service-cluster"></a>Creación de un clúster privado de Azure Kubernetes Service

En un clúster privado, el servidor de la API o el plano de control tienen direcciones IP internas que se definen en el documento [RFC1918 sobre la asignación de direcciones para conexiones privadas de Internet](https://tools.ietf.org/html/rfc1918). Mediante el uso de un clúster privado, puede asegurarse de que el tráfico entre el servidor de API y los grupos de nodos permanece solo en la red privada.

El plano de control o el servidor de la API están en una suscripción de Azure administrada mediante Azure Kubernetes Service (AKS). El grupo de clústeres o nodos de un cliente está en la suscripción del cliente. El servidor y el grupo de clústeres o nodos pueden comunicarse entre sí a través del [servicio de Azure Private Link][private-link-service] en la red virtual del servidor de la API y de un punto de conexión privado expuesto en la subred del clúster de AKS del cliente.

## <a name="region-availability"></a>Disponibilidad en regiones

Un clúster privado está disponible en las regiones públicas, en Azure Government y en las regiones de Azure China 21Vianet en las que [se admite AKS](https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service).

> [!NOTE]
> Se admiten sitios de Azure Government, aunque en este momento no se admite US Gov Texas debido a la falta de compatibilidad con Private Link.

## <a name="prerequisites"></a>Requisitos previos

* CLI de Azure, versión 2.2.0 o cualquier versión posterior
* El servicio Azure Private Link solo se admite en Standard Azure Load Balancer. No se admite en Basic Azure Load Balancer.  
* Para usar un servidor DNS personalizado, agregue la IP 168.63.129.16 de Azure DNS como servidor DNS ascendente en el servidor DNS personalizado.

## <a name="create-a-private-aks-cluster"></a>Creación de un clúster privado de AKS

### <a name="create-a-resource-group"></a>Crear un grupo de recursos

Cree un grupo de recursos o seleccione uno existente en el clúster de AKS.

```azurecli-interactive
az group create -l westus -n MyResourceGroup
```

### <a name="default-basic-networking"></a>Redes básicas predeterminadas 

```azurecli-interactive
az aks create -n <private-cluster-name> -g <private-cluster-resource-group> --load-balancer-sku standard --enable-private-cluster  
```
Donde `--enable-private-cluster` es una marca obligatoria para un clúster privado. 

### <a name="advanced-networking"></a>Redes avanzadas  

```azurecli-interactive
az aks create \
    --resource-group <private-cluster-resource-group> \
    --name <private-cluster-name> \
    --load-balancer-sku standard \
    --enable-private-cluster \
    --network-plugin azure \
    --vnet-subnet-id <subnet-id> \
    --docker-bridge-address 172.17.0.1/16 \
    --dns-service-ip 10.2.0.10 \
    --service-cidr 10.2.0.0/24 
```
Donde `--enable-private-cluster` es una marca obligatoria para un clúster privado. 

> [!NOTE]
> Si la dirección CIDR del puente de Docker (172.17.0.1/16) entra en conflicto con el CIDR de la subred, cambie la dirección del puente de Docker.

## <a name="configure-private-dns-zone"></a>Configuración de zona de DNS privado 

Se pueden aprovechar los siguientes parámetros para configurar la zona DNS privada.

- "System", que es también el valor predeterminado. Si se omite el argumento --private-dns-zone, AKS creará una zona DNS privada en el grupo de recursos del nodo.
- "None" tiene como valor predeterminado un DNS público, lo que significa que AKS no creará una zona de DNS privado.  
- "CUSTOM_PRIVATE_DNS_ZONE_RESOURCE_ID", para lo que es necesario crear una zona DNS privada en este formato para la nube global de Azure: `privatelink.<region>.azmk8s.io`. A partir de ahora, necesitará el identificador de recurso de esa zona DNS privada.  También necesitará una identidad asignada por el usuario o una entidad de servicio que tenga como mínimo los roles `private dns zone contributor` y `vnet contributor`.
  - Si la zona de DNS privado está en una suscripción diferente a la del clúster de AKS, debe registrar Microsoft.ContainerServices en ambas suscripciones.
  - "fqdn-subdomain" solo se puede usar con "CUSTOM_PRIVATE_DNS_ZONE_RESOURCE_ID" para proporcionar capacidades de subdominio a `privatelink.<region>.azmk8s.io`.

### <a name="create-a-private-aks-cluster-with-private-dns-zone"></a>Creación de un clúster de AKS privado con zona de DNS privado

```azurecli-interactive
az aks create -n <private-cluster-name> -g <private-cluster-resource-group> --load-balancer-sku standard --enable-private-cluster --enable-managed-identity --assign-identity <ResourceId> --private-dns-zone [system|none]
```

### <a name="create-a-private-aks-cluster-with-a-custom-private-dns-zone"></a>Creación de un clúster de AKS privado con una zona DNS privada

```azurecli-interactive
az aks create -n <private-cluster-name> -g <private-cluster-resource-group> --load-balancer-sku standard --enable-private-cluster --enable-managed-identity --assign-identity <ResourceId> --private-dns-zone <custom private dns zone ResourceId> --fqdn-subdomain <subdomain-name>
```

## <a name="create-a-private-aks-cluster-with-a-public-fqdn"></a>Creación de un clúster de AKS privado con un nombre de dominio completo público

Requisitos previos:

* CLI de Azure >= 2.28.0 o CLI de Azure con la extensión aks-preview 0.5.29, o posterior.
* Si usa ARM o la API REST, la versión de la API de AKS debe ser 2021-05-01 o posterior.

La opción de DNS público se puede aprovechar para simplificar las opciones de enrutamiento de su clúster privado.  

![DNS público](https://user-images.githubusercontent.com/50749048/124776520-82629600-df0d-11eb-8f6b-71c473b6bd01.png)

1. Al aprovisionar un clúster de AKS privado, AKS crea de forma predeterminada un nombre de dominio completo público adicional y el registro D correspondiente en el DNS público de Azure. Los nodos del agente siguen usando el registro A en la zona de DNS privado, con el fin de resolver la dirección IP privada del punto de conexión privado para la comunicación con el servidor de la API.

2. Si usa `--private-dns-zone none`, el clúster solo tendrá un nombre de dominio completo público. Cuando se usa esta opción, no se crea ni se usa ninguna zona de DNS privado para la resolución de nombres del FQDN del servidor de la API. La dirección IP de la API sigue siendo privada y no es enrutable públicamente.

3. Si no se desea el nombre de dominio completo público, puede usar `--disable-public-fqdn` para deshabilitarlo ("ninguna" zona DNS privada puede deshabilitar el nombre de dominio completo público).

```azurecli-interactive
az aks create -n <private-cluster-name> -g <private-cluster-resource-group> --load-balancer-sku standard --enable-private-cluster --enable-managed-identity --assign-identity <ResourceId> --private-dns-zone <private-dns-zone-mode> --disable-public-fqdn
az aks update -n <private-cluster-name> -g <private-cluster-resource-group> --disable-public-fqdn
```

## <a name="options-for-connecting-to-the-private-cluster"></a>Opciones para conectarse al clúster privado

El punto de conexión del servidor de la API no tiene ninguna dirección IP pública. Para administrar el servidor de API, necesitará una máquina virtual que tenga acceso a la red virtual de Azure (VNet) del clúster de AKS. Hay varias opciones para establecer la conectividad de red con el clúster privado.

* Crear una máquina virtual en la misma red virtual de Azure (VNet) que el clúster de AKS.
* Usar una máquina virtual de una red diferente y configurar el [emparejamiento de red virtual][virtual-network-peering].  Consulte la sección siguiente para más información sobre esta opción.
* Usar una conexión de [ExpressRoute o VPN][express-route-or-VPN].
* Use la [característica Ejecutar comando de AKS](#aks-run-command).

La opción más sencilla es crear una máquina virtual en la misma red virtual que el clúster de AKS.  ExpressRoute y las VPN incrementan los costos y requieren redes más complejas.  Para utilizar el emparejamiento de red virtual, debe planear los intervalos CIDR de la red para asegurarse de que no haya intervalos superpuestos.

### <a name="aks-run-command"></a>Ejecutar comando de AKS

En la actualidad, cuando necesite tener acceso a un clúster privado, debe hacerlo dentro de la red virtual del clúster o en una red o una máquina cliente emparejadas. Normalmente, para ello es necesario que la máquina se conecte a través de VPN o ExpressRoute a la red virtual del clúster o que se cree un JumpBox en dicha red. Ejecutar comando de AKS le permite invocar comandos de forma remota en un clúster de AKS mediante la API de AKS. Esta característica proporciona una API que le permite, por ejemplo, ejecutar comandos Just-in-Time desde un equipo portátil remoto de un clúster privado. Esta API le puede ayudar considerablemente con el acceso rápido Just-In-Time a un clúster privado cuando la máquina cliente no está en la red privada del clúster y, al mismo tiempo, conserva y aplica los mismos controles de RBAC y el servidor de API privado.

### <a name="prerequisites"></a>Requisitos previos

* La versión 2.24.0 de la CLI de Azure, o cualquier versión posterior.

### <a name="use-aks-run-command"></a>Uso de Ejecutar comando de AKS

Comando sencillo

```azurecli-interactive
az aks command invoke -g <resourceGroup> -n <clusterName> -c "kubectl get pods -n kube-system"
```

Implementación de un manifiesto adjuntando el archivo específico

```azurecli-interactive
az aks command invoke -g <resourceGroup> -n <clusterName> -c "kubectl apply -f deployment.yaml -n default" -f deployment.yaml
```

Implementación de un manifiesto adjuntando una carpeta completa

```azurecli-interactive
az aks command invoke -g <resourceGroup> -n <clusterName> -c "kubectl apply -f deployment.yaml -n default" -f .
```

Realización de una instalación de Helm y paso del manifiesto de valores específico

```azurecli-interactive
az aks command invoke -g <resourceGroup> -n <clusterName> -c "helm repo add bitnami https://charts.bitnami.com/bitnami && helm repo update && helm install my-release -f values.yaml bitnami/nginx" -f values.yaml
```
> [!NOTE]
> Proteja el acceso a Ejecutar comando de AKS mediante la creación de un rol personalizado con los permisos "Microsoft.ContainerService/managedClusters/runcommand/action" y asígnelo a usuarios o grupos específicos, en combinación con directivas de acceso Just-In-Time o acceso condicional. 

## <a name="virtual-network-peering"></a>Emparejamiento de redes virtuales de Azure

Tal y como se ha dicho, el emparejamiento de red virtual es un mecanismo para acceder a un clúster privado. Si desea usar el emparejamiento de red virtual, tiene que configurar un vínculo entre la red virtual y la zona DNS privada.
    
1. Vaya al grupo de recursos del nodo en Azure Portal.  
2. Seleccione la zona DNS privada.   
3. En el panel izquierdo, seleccione el vínculo **red virtual**.  
4. Cree un nuevo vínculo para agregar la red virtual de la máquina virtual a la zona DNS privada. El vínculo de la zona DNS puede tardar unos minutos en estar disponible.  
5. En Azure Portal, vaya al grupo de recursos que contiene la red virtual del clúster.  
6. En el panel derecho, seleccione la red virtual. El nombre de la red virtual tiene el formato *aks-vnet-\** .  
7. En el panel izquierdo, seleccione **Emparejamientos**.  
8. Seleccione **Agregar**, agregue la red virtual de la máquina virtual y, después, cree el emparejamiento.  
9. Vaya a la red virtual en la que tiene la máquina virtual, seleccione **Emparejamientos**, seleccione la red virtual de AKS y, después, cree el emparejamiento. Si los intervalos de direcciones de la red virtual de AKS y de la red virtual de la máquina virtual entran en conflicto, se produce un error de emparejamiento. Para más información, vea el artículo [Emparejamiento de redes virtuales][virtual-network-peering].

## <a name="hub-and-spoke-with-custom-dns"></a>Concentrador y radio con DNS personalizado

[Las arquitecturas de concentrador y radio](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke) suelen usarse para implementar redes en Azure. En muchas de estas implementaciones, los valores de DNS en las redes virtuales de radios están configurados para hacer referencia a un reenviador de DNS central con el fin de permitir la resolución de DNS local y basada en Azure. Al implementar un clúster de AKS en un entorno de red de este tipo, hay algunas consideraciones especiales que se deben tener en cuenta.

![Concentrador y radio de clúster privado](media/private-clusters/aks-private-hub-spoke.png)

1. De forma predeterminada, cuando se aprovisiona un clúster privado, se crean un punto de conexión privado (1) y una zona DNS privada (2) en el grupo de recursos administrados del clúster. El clúster usa un registro A en la zona privada a fin de resolver la dirección IP del punto de conexión privado para la comunicación con el servidor de la API.

2. La zona DNS privada solo está vinculada a la red virtual a la que están adjuntados los nodos del clúster (3). Esto significa que el punto de conexión privado solo lo pueden resolver los hosts de esa red virtual vinculada. En escenarios en los que no haya ningún DNS personalizado configurado en la red virtual (valor predeterminado), esto funciona sin incidencias, ya que los hosts apuntan a 168.63.129.16 para DNS, que puede resolver registros en la zona DNS privada debido al vínculo.

3. En escenarios en los que la red virtual que contiene el clúster tenga una configuración de DNS personalizada (4), se produce un error en la implementación del clúster a menos que la zona DNS privada esté vinculada a la red virtual que contiene las resoluciones de DNS personalizadas (5). Este vínculo se puede crear manualmente después de crear la zona privada, durante el aprovisionamiento del clúster, o a través de la automatización si se detecta la creación de la zona usando mecanismos de implementación basados en eventos (por ejemplo, Azure Event Grid y Azure Functions).

> [!NOTE]
> Si usa [Traiga su propia tabla de rutas con kubenet](./configure-kubenet.md#bring-your-own-subnet-and-route-table-with-kubenet) y Traiga su propio DNS con un clúster privado, se producirá un error en la creación del clúster. Tendrá que asociar [RouteTable](./configure-kubenet.md#bring-your-own-subnet-and-route-table-with-kubenet) en el grupo de recursos del nodo a la subred después de que se haya producido un error en la creación del clúster, con el fin de que la creación se realice correctamente.

## <a name="limitations"></a>Limitaciones 
* Los intervalos autorizados de direcciones IP no se pueden aplicar al punto de conexión del servidor de API privada. Solo se aplican al servidor de API pública.
* Las [limitaciones del servicio Azure Private Link][private-link-service] aplican a los clústeres privados.
* No se admiten agentes hospedados por Microsoft en Azure DevOps con clústeres privados. Considere la posibilidad de usar [agentes autohospedados](/azure/devops/pipelines/agents/agents?tabs=browser). 
* En el caso de los clientes que necesitan habilitar Azure Container Registry para trabajar con instancias privadas de AKS, la red virtual de Container Registry debe estar emparejada con la red virtual del clúster del agente.
* No se admite la conversión de clústeres de AKS existentes en clústeres privados.
* La eliminación o modificación del punto de conexión privado en la subred del cliente hará que el clúster deje de funcionar. 

<!-- LINKS - internal -->
[az-provider-register]: /cli/azure/provider#az_provider_register
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
[private-link-service]: ../private-link/private-link-service-overview.md#limitations
[virtual-network-peering]: ../virtual-network/virtual-network-peering-overview.md
[azure-bastion]: ../bastion/tutorial-create-host-portal.md
[express-route-or-vpn]: ../expressroute/expressroute-about-virtual-network-gateways.md
[devops-agents]: /azure/devops/pipelines/agents/agents
[availability-zones]: availability-zones.md
