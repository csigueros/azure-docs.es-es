---
title: Uso de Azure Spot Virtual Machines en un clúster de Red Hat OpenShift en Azure (ARO)
description: Descubra cómo usar Azure Spot Virtual Machines en Red Hat OpenShift en Azure (ARO)
author: nilsanderselde
ms.author: suvetriv
ms.service: azure-redhat-openshift
keywords: de acceso puntual, nodos, aro, implementar, openshift, red hat
ms.topic: how-to
ms.date: 10/21/2021
ms.custom: template-how-to, ignite-fall-2021
ms.openlocfilehash: 941db40b96d9e7a00c32b42817a3f2a93e17643a
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131084433"
---
# <a name="use-azure-spot-virtual-machines-in-an-azure-red-hat-openshift-aro-cluster"></a>Uso de Azure Spot Virtual Machines en un clúster de Red Hat OpenShift en Azure (ARO)

En este artículo se proporcionan los detalles necesarios que permiten configurar el clúster de Red Hat OpenShift en Azure (ARO) para que use Azure Spot Virtual Machines.

El uso de máquinas virtuales de acceso puntual de Azure permite aprovechar las ventajas de nuestra capacidad no utilizada con un importante ahorro en los costos. Siempre que Azure necesite recuperar la capacidad, la infraestructura de esta plataforma expulsará las máquinas virtuales de acceso puntual de Azure. Para más información sobre las instancias de Spot, consulte [Spot Virtual Machines](../virtual-machines/spot-vms.md).

## <a name="before-you-begin"></a>Antes de empezar

Antes de empezar, asegúrese de que tiene implementado un clúster de Red Hat Openshift en Azure. Si necesita un clúster de ARO, consulte el artículo de [inicio rápido de ARO](tutorial-create-cluster.md) para obtener un clúster público, o el [tutorial de clústeres privados](howto-create-private-cluster-4x.md) para un clúster privado. Los pasos para configurar el clúster para usar máquina virtual de acceso puntual son los mismos para los clústeres tanto privados como públicos.

## <a name="add-spot-vms"></a>Adición de máquinas virtuales de acceso puntual

La administración de máquinas en Red Hat Openshift en Azure se realiza mediante MachineSet. Los recursos MachineSet son grupos de máquinas. MachineSets son para máquinas como ReplicaSets para pods. Si necesita más máquinas o debe reducirlas verticalmente, cambie el campo *Réplicas* del conjunto de máquinas para satisfacer sus necesidades de proceso. Para más información, consulte nuestra documentación de OpenShift [MachineSet](https://docs.openshift.com/container-platform/4.8/machine_management/creating_machinesets/creating-machineset-azure.html)

El uso de máquinas virtuales de Spot se especifica agregando el campo `spotVMOptions` dentro de la especificación de plantilla de un MachineSet.
Para crear este MachineSet, haremos lo siguiente:

1. Obtenga una copia de un MachineSet que se ejecuta en el clúster.
2. Cree una configuración de MachineSet modificada.
3. Implementación de este MachineSet en el clúster

En primer lugar, [conéctese al clúster de OpenShift mediante la CLI](tutorial-connect-cluster.md).

```azurecli-interactive
oc login $apiServer -u kubeadmin -p <kubeadmin password>
```

A continuación, enumerará los MachineSets en el clúster. Un clúster predeterminado tendrá 3 MachineSets implementados: 
```azurecli-interactive
oc get machinesets -n openshift-machine-api
```

Este es un resultado de ejemplo de este comando: 
```
NAME                                    DESIRED   CURRENT   READY   AVAILABLE   AGE
aro-cluster-5t2dj-worker-eastus1   1         1         1       1           2d22h
aro-cluster-5t2dj-worker-eastus2   1         1         1       1           2d22h
aro-cluster-5t2dj-worker-eastus3   1         1         1       1           2d22h
```

A continuación, describirá la implementación de MachineSet. Reemplace \<machineset\> por uno de los MachineSets enumerados anteriormente y haga que esto se desplace a un archivo.

```azurecli-interactive
oc get machineset <machineset> -n openshift-machine-api -o yaml > spotmachineset.yaml
```

Deberá cambiar los parámetros siguientes en MachineSet:
- `metadata.name`
- `spec.selector.matchLabels.machine.openshift.io/cluster-api-machineset`
- `spec.template.metadata.labels.machine.openshift.io/cluster-api-machineset`
- `spec.template.spec.providerSpec.value.spotVMOptions` (Agregue este campo y esta establezca en `{}`).


A continuación se muestra un ejemplo abreviado de YAML de MachineSet de acceso puntual. Resalta los cambios clave que debe realizar al basar un nuevo MachineSet de acceso puntual en un MachineSet de trabajo existente, incluida información adicional para el contexto. (No representa un MachineSet completo y funcional; a continuación se omiten muchos campos).

```
apiVersion: machine.openshift.io/v1beta1
kind: MachineSet
metadata:
  name: aro-cluster-abcd1-spot-eastus
spec:
  replicas: 2
  selector:
    matchLabels:
      machine.openshift.io/cluster-api-cluster: aro-cluster-abcd1
      machine.openshift.io/cluster-api-machineset: aro-cluster-abcd1-spot-eastus
  template:
    metadata:
        machine.openshift.io/cluster-api-machineset: aro-cluster-abcd1-spot-eastus
    spec:
      providerSpec:
        value:
          spotVMOptions: {}
      taints:
        - effect: NoExecute
          key: spot
          value: 'true'
```

Una vez actualizado el archivo, hay que aplicarlo.

```azurecli-interactive
oc create -f spotmachineset.yaml
```

Para validar que MachineSet se ha creado correctamente, ejecute el siguiente comando:
```azurecli-interactive
oc get machinesets -n openshift-machine-api
```

Esta es una salida de ejemplo. El MachineSet está listo una vez que tenga las máquinas en el estado "Listo".
```
  NAME                                    DESIRED   CURRENT   READY   AVAILABLE   AGE
aro-cluster-5t2dj-worker-eastus1           1         1         1       1           3d1h
aro-cluster-5t2dj-worker-eastus2           1         1         1       1           3d1h
aro-cluster-5t2dj-worker-eastus3           1         1         1       1           3d1h
spot                                       1         1         1       1           2m47s
```

## <a name="schedule-interruptible-workloads"></a>Programación de cargas de trabajo interrumpibles

Se recomienda agregar una intolerancia a los nodos de acceso puntual para evitar que se programen nodos no ininterrumpidamente en ellos y agregar tolerancias de esta intolerancia a los pods que quiera programar en ellos. Puede contaminar los nodos a través de la especificación MachineSet.

Por ejemplo, puede agregar el siguiente código YAML a `spec.template.spec`:

```
     taints:
        - effect: NoExecute
          key: spot
          value: 'true'
```

Esto impediría que los pods se programaran en el nodo resultante a menos que tuvieran una tolerancia para la intolerancia `spot='true'` y expulsaría los pods que no tuvieran esa tolerancia.

Para obtener más información sobre la aplicación de intolerancias y tolerancias, lea [Control de la colocación de pods mediante intolerancias de nodo](https://docs.openshift.com/container-platform/4.7/nodes/scheduling/nodes-scheduler-taints-tolerations.html).

## <a name="quota"></a>Quota

Las máquinas pueden entrar en un estado de error debido a problemas de cuota si la cuota del tipo de máquina que está usando es demasiado baja durante un breve momento, incluso si finalmente debería ser suficiente (por ejemplo, un nodo sigue eliminando cuando se crea otro). Por este problema, se recomienda establecer la cuota del tipo de máquina que va a usar para que las instancias de acceso puntual sean ligeramente superiores a las necesarias (quizás en 2*n, donde n es el número de núcleos usados por una máquina). Esta sobrecarga evitaría tener que solucionar las máquinas con errores, lo que, aunque es relativamente simple, sigue siendo una intervención manual.

## <a name="node-readiness"></a>Preparación para el nodo

Como se explica en la documentación de máquina virtual de acceso puntual vinculada anteriormente, las máquinas virtuales se encuentran en estado de aprovisionamiento desasignado cuando ya no están disponibles o ya no están disponibles al precio máximo especificado.

Esto se manifestará en OpenShift como nodos **No listos.** Las máquinas seguirán en buen estado, en la fase **Aprovisionadas como nodo**.

Volverán a estar **Listos** una vez que las máquinas virtuales estén disponibles de nuevo.

## <a name="troubleshooting"></a>Solución de problemas

### <a name="node-stuck-in-not-ready-state-underlying-vm-deallocated"></a>Nodo bloqueado en estado No listo, máquina virtual subyacente desasignada

Si un nodo está bloqueado durante un largo período de tiempo en estado No listo después de desasignar su máquina virtual, puede intentar eliminarlo o eliminar su objeto de máquina de OpenShift correspondiente.

### <a name="spot-machine-stuck-in-failed-state"></a>Máquina de acceso puntual bloqueada en estado de Error

Si una máquina (objeto de OpenShift) que usa una máquina virtual de acceso puntual está bloqueada en un estado de Error, intente eliminarla manualmente. Si no se puede eliminar debido a un 403 porque la máquina virtual ya no existe, edite la máquina y quite los finalizadores.
