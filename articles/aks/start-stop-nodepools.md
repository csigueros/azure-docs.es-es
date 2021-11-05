---
title: Iniciar y detener un grupo de nodos en Azure Kubernetes Service (AKS) (versión preliminar)
description: Aprenda a iniciar o detener un grupo de nodos en Azure Kubernetes Service (AKS).
services: container-service
ms.topic: article
ms.date: 10/25/2021
author: qpetraroia
ms.author: qpetraroia
ms.openlocfilehash: 4bf2eeda7b13d520bca769e9698e4ea5abebd584
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131093150"
---
# <a name="start-and-stop-an-azure-kubernetes-service-aks-node-pool-preview"></a>Iniciar y detener un grupo de nodos de Azure Kubernetes Service (AKS) (versión preliminar)

Es posible que las cargas de trabajo de AKS no necesiten ejecutarse continuamente, por ejemplo, un clúster de desarrollo que tenga grupos de nodos que ejecutan cargas de trabajo específicas. Para optimizar los costos, puede desactivar (detener) completamente los grupos de nodos en el clúster de AKS, lo que le permite ahorrar en costos de proceso.

## <a name="before-you-begin"></a>Antes de empezar

En este artículo se supone que ya tiene un clúster de AKS. Si necesita un clúster de AKS, consulte el inicio rápido de AKS [mediante la CLI de Azure][aks-quickstart-cli], [Azure PowerShell][kubernetes-walkthrough-powershell] o [Azure Portal][aks-quickstart-portal].

### <a name="install-aks-preview-cli-extension"></a>Instalación de la extensión aks-preview de la CLI

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

También necesita la extensión de CLI de Azure *aks-preview*. Instale la extensión de la CLI de Azure *aks-preview* mediante el comando [az extension add][az-extension-add]. También puede instalar las actualizaciones disponibles mediante el comando [az extension update][az-extension-update].

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="register-the-previewstartstopagentpool-preview-feature"></a>Registro de la característica en vista previa (GB) `PreviewStartStopAgentPool`

Para usar la característica, también debe habilitar la marca de característica `PreviewStartStopAgentPool` en la suscripción.

Registre la marca de la característica `PreviewStartStopAgentPool` con el comando [az feature register][az-feature-register], como se muestra en el siguiente ejemplo:

```azurecli-interactive
az feature register --namespace "Microsoft.ContainerService" --name "PreviewStartStopAgentPool"
```

Tarda unos minutos en que el estado muestre *Registrado*. Puede comprobar el estado de registro con el comando [az feature list][az-feature-list]:

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/PreviewStartStopAgentPool')].{Name:name,State:properties.state}"
```

Cuando haya terminado, actualice el registro del proveedor de recursos *Microsoft.ContainerService* con el comando [az provider register][az-provider-register]:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="stop-an-aks-node-pool"></a>Detener un grupo de nodos de AKS

> [!IMPORTANT]
> Cuando se usa el inicio o la detención del grupo de nodos, se espera el siguiente comportamiento:
>
> * No se pueden detener los grupos del sistema.
> * Se admiten los grupos de nodos de acceso puntual.
> * Los grupos de nodos detenidos se pueden actualizar.
> * El clúster y el grupo de nodos deben estar en ejecución.

Use `az aks nodepool stop` para detener un grupo de nodos de AKS en ejecución. En el ejemplo siguiente se detiene el grupo de nodos *testnodepool*:

```azurecli-interactive
az aks nodepool stop --nodepool-name testnodepool --resource-group myResourceGroup --cluster-name myAKSCluster
```

Para comprobar cuándo se detiene el grupo de nodos, use el comando [az aks show][az-aks-show] y confirme los `powerState` como `Stopped` en la salida siguiente:

```json
{
[...]
 "osType": "Linux",
    "podSubnetId": null,
    "powerState": {
        "code&quot;: &quot;Stopped"
        },
    "provisioningState": "Succeeded",
    "proximityPlacementGroupId": null,
[...]
}
```

> [!NOTE]
> Si `provisioningState` muestra `Stopping`, el grupo de nodos aún no se ha detenido por completo.

## <a name="start-a-stopped-aks-node-pool"></a>Inicio de un grupo de nodos de AKS detenido

Use `az aks nodepool start` para iniciar un grupo de nodos de AKS detenido. En el ejemplo siguiente se inicia el grupo de nodos detenido denominado *testnodepool*:

```azurecli-interactive
az aks nodepool start --nodepool-name testnodepool --resource-group myResourceGroup --cluster-name myAKSCluster
```

Puede comprobar que el grupo de nodos ha empezado a usar [az aks show][az-aks-show] y confirmar que `powerState` muestra `Running`. Por ejemplo:

```json
{
[...]
 "osType": "Linux",
    "podSubnetId": null,
    "powerState": {
        "code&quot;: &quot;Running"
        },
    "provisioningState": "Succeeded",
    "proximityPlacementGroupId": null,
[...]
}
```

> [!NOTE]
> Si `provisioningState` muestra `Starting`, el grupo de nodos aún no se ha iniciado por completo.

---

## <a name="next-steps"></a>Pasos siguientes

- Para obtener información sobre cómo escalar grupos `User` a 0, vea [Escalado de grupos `User` a 0](scale-cluster.md#scale-user-node-pools-to-0).
- Para obtener información sobre cómo detener el clúster, consulte [Inicio y detención del clúster](start-stop-cluster.md).
- Para obtener información sobre cómo ahorrar costos mediante instancias de acceso puntual, vea [Incorporación de un grupo de nodos de acceso puntual a AKS](spot-node-pool.md).
- Para obtener más información sobre las directivas de soporte técnico de AKS, vea [Directivas de soporte técnico de AKS](support-policies.md).

<!-- LINKS - external -->

<!-- LINKS - internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-provider-register]: /cli/azure/provider#az_provider_register
[az-aks-show]: /cli/azure/aks#az_aks_show
[kubernetes-walkthrough-powershell]: kubernetes-walkthrough-powershell.md
[stop-azakscluster]: /powershell/module/az.aks/stop-azakscluster
[get-azakscluster]: /powershell/module/az.aks/get-azakscluster
[start-azakscluster]: /powershell/module/az.aks/start-azakscluster