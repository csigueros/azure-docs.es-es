---
title: Inicio y detención de Azure Kubernetes Service (AKS)
description: Aprenda a iniciar y detener un clúster de Azure Kubernetes Service (AKS).
services: container-service
ms.topic: article
ms.date: 08/09/2021
author: palma21
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: fefdb4619c017d7c43e4dfa84c8099450310ca2f
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121743942"
---
# <a name="stop-and-start-an-azure-kubernetes-service-aks-cluster"></a>Inicio y detención de un clúster de Azure Kubernetes Service (AKS)

Es posible que las cargas de trabajo de AKS no tengan que ejecutarse continuamente, por ejemplo, en el caso de un clúster de desarrollo que se use solo durante el horario comercial. Esto se traduce en momentos en que el clúster de Azure Kubernetes Service (AKS) podría estar inactivo, sin ejecutar más que los componentes del sistema. Para reducir la superficie del clúster, [escale todos los grupos de nodos `User` a 0](scale-cluster.md#scale-user-node-pools-to-0), aunque sigue siendo necesario que el [grupo `System`](use-system-pools.md) ejecute los componentes del sistema mientras el clúster está en ejecución.
Para optimizar aún más los costos durante estos períodos, puede desactivar por completo (detener) el clúster. Esta acción detiene el plano de control y los nodos del agente, lo que permite ahorrar en todos los costos de proceso, a la vez que se mantienen todos los objetos y el estado del clúster almacenados para cuando se inicie de nuevo. Puede continuar justo donde se ha dejado después de un fin de semana o hacer que el clúster se ejecute solo mientras se ejecutan los trabajos por lotes.

## <a name="before-you-begin"></a>Antes de empezar

En este artículo se supone que ya tiene un clúster de AKS. Si necesita un clúster de AKS, consulte el inicio rápido de AKS [mediante la CLI de Azure][aks-quickstart-cli], [Azure PowerShell][kubernetes-walkthrough-powershell] o [Azure Portal][aks-quickstart-portal].

### <a name="limitations"></a>Limitaciones

Cuando se usa la característica de inicio o detención del clúster, se aplican las restricciones siguientes:

- Esta característica solo se admite para clústeres respaldados por Virtual Machine Scale Sets.
- El estado de clúster de un clúster de AKS detenido se conserva durante un máximo de 12 meses. Si el clúster se detiene durante más de 12 meses, no se puede recuperar su estado. Para obtener más información, vea [Directivas de soporte técnico para AKS](support-policies.md).
- Solo puede iniciar o eliminar un clúster de AKS detenido. Para realizar cualquier operación, como escalado o actualización, primero inicie el clúster.
- Los puntos de conexión privados aprovisionados por el cliente y vinculados al clúster privado deben eliminarse y volver a crearse al iniciar un clúster de AKS detenido.

## <a name="stop-an-aks-cluster"></a>Detención de un clúster de AKS

### <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)

Puede usar el comando `az aks stop` para detener los nodos y el plano de control de un clúster de AKS en ejecución. En el siguiente ejemplo se detiene un clúster denominado *myAKSCluster*:

```azurecli-interactive
az aks stop --name myAKSCluster --resource-group myResourceGroup
```

Para comprobar que el clúster se ha detenido, use el comando [az aks show][az-aks-show] y confirme que `powerState` aparece como `Stopped` en la siguiente salida:

```json
{
[...]
  "nodeResourceGroup": "MC_myResourceGroup_myAKSCluster_westus2",
  "powerState":{
    "code":"Stopped"
  },
  "privateFqdn": null,
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
[...]
}
```

Si `provisioningState` muestra `Stopping`, significa que el clúster aún no se ha detenido por completo.

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

Puede usar el cmdlet [Stop-AzAksCluster][stop-azakscluster] para detener el plano de control y los nodos del clúster de AKS en ejecución. En el siguiente ejemplo se detiene un clúster denominado *myAKSCluster*:

```azurepowershell-interactive
Stop-AzAksCluster -Name myAKSCluster -ResourceGroupName myResourceGroup
```

Puede comprobar que el clúster se ha detenido con el cmdlet [Get-AzAksCluster][get-azakscluster] y confirmar que `ProvisioningState` aparece como `Stopped`, como se muestra en la salida siguiente:

```Output
ProvisioningState       : Stopped
MaxAgentPools           : 100
KubernetesVersion       : 1.20.7
...
```

Si `ProvisioningState` muestra `Stopping`, significa que el clúster aún no se ha detenido por completo.

---

> [!IMPORTANT]
> Si usa [presupuestos de interrupciones de pods](https://kubernetes.io/docs/concepts/workloads/pods/disruptions/), la operación de detención puede tardar más tiempo, ya que el proceso de purga tarda más en finalizar.

## <a name="start-an-aks-cluster"></a>Inicio de un clúster de AKS

### <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)

Puede usar el comando `az aks start` para iniciar los nodos y el plano de control de un clúster de AKS detenido. El clúster se reinicia con el estado del plano de control y el número de nodos de agente anteriores.
En el siguiente ejemplo se inicia un clúster denominado *myAKSCluster*:

```azurecli-interactive
az aks start --name myAKSCluster --resource-group myResourceGroup
```

Para comprobar que el clúster se ha iniciado, use el comando [az aks show][az-aks-show] y confirme que `powerState` aparece como `Running` en la siguiente salida:

```json
{
[...]
  "nodeResourceGroup": "MC_myResourceGroup_myAKSCluster_westus2",
  "powerState":{
    "code":"Running"
  },
  "privateFqdn": null,
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
[...]
}
```

Si `provisioningState` muestra `Starting`, significa que el clúster aún no se ha iniciado por completo.

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

Puede usar el cmdlet [Start-AzAksCluster][start-azakscluster] para iniciar el plano de control y los nodos del clúster de AKS detenido. El clúster se reinicia con el estado del plano de control y el número de nodos de agente anteriores.
En el siguiente ejemplo se inicia un clúster denominado *myAKSCluster*:

```azurepowershell-interactive
Start-AzAksCluster -Name myAKSCluster -ResourceGroupName myResourceGroup
```

Puede comprobar si el clúster se ha iniciado con el cmdlet [Get-AzAksCluster][get-azakscluster] y confirmar que `ProvisioningState` aparece como `Succeeded`, como se muestra en la salida siguiente:

```Output
ProvisioningState       : Succeeded
MaxAgentPools           : 100
KubernetesVersion       : 1.20.7
...
```

Si `ProvisioningState` muestra `Starting`, significa que el clúster aún no se ha iniciado por completo.

---

> [!NOTE]
> Al iniciar la copia de seguridad del clúster, se espera el siguiente comportamiento:
>
> * La dirección IP del servidor de API puede cambiar.
> * Si usa el escalador automático de clústeres, al iniciar la copia de seguridad del clúster, es posible que el número de nodos actual no esté entre los valores de intervalo mínimo y máximo establecidos. El clúster comienza con el número de nodos que necesita para ejecutar sus cargas de trabajo, que no se verá afectados por la configuración del escalador automático. Cuando el clúster realiza operaciones de escalado, los valores mínimo y máximo afectarán al número de nodos actual y el clúster finalmente entrará y permanecerá en ese intervalo deseado hasta que detenga el clúster.

## <a name="next-steps"></a>Pasos siguientes

- Para obtener información sobre cómo escalar grupos `User` a 0, vea [Escalado de grupos `User` a 0](scale-cluster.md#scale-user-node-pools-to-0).
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
