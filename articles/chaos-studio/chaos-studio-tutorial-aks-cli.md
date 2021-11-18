---
title: Creación de un experimento que use un error de Chaos Mesh de AKS mediante Azure Chaos Studio con la CLI de Azure
description: Cree un experimento que use un error de Chaos Mesh de AKS con la CLI de Azure
author: johnkemnetz
ms.topic: how-to
ms.date: 11/11/2021
ms.author: johnkem
ms.service: chaos-studio
ms.custom: template-how-to, ignite-fall-2021
ms.openlocfilehash: c47a6fa58a9361dc9ab26e119346951e1eef9764
ms.sourcegitcommit: 901ea2c2e12c5ed009f642ae8021e27d64d6741e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/12/2021
ms.locfileid: "132373453"
---
# <a name="create-a-chaos-experiment-that-uses-a-chaos-mesh-fault-with-the-azure-cli"></a>Creación de un experimento de caos que use un error de Chaos Mesh con la CLI de Azure

Puede usar un experimento de caos para comprobar que la aplicación es resistente a los errores provocando esos errores en un entorno controlado. En esta guía, provocará errores de pod de Azure Kubernetes Service periódicos en un espacio de nombres mediante un experimento de caos y Azure Chaos Studio. La ejecución de este experimento puede ayudarle a defenderse contra la falta de disponibilidad del servicio cuando hay errores esporádicos.

Azure Chaos Studio usa [Chaos Mesh](https://chaos-mesh.org/), una plataforma gratuita de ingeniería de caos de código abierto para Kubernetes para insertar errores en un clúster de AKS. Los errores de Chaos Mesh son errores [directos de servicio](chaos-studio-tutorial-aks-portal.md) que requieren que Chaos Mesh esté instalado en el clúster de AKS. Estos mismos pasos se pueden usar para configurar y ejecutar un experimento para cualquier error de Chaos Mesh de AKS.

## <a name="prerequisites"></a>Prerrequisitos

- Suscripción a Azure. [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)] 
- Un clúster de AKS. Si no tiene un clúster de AKS, puede [seguir estos pasos para crear uno](../aks/kubernetes-walkthrough-portal.md).

## <a name="launch-azure-cloud-shell"></a>Inicio de Azure Cloud Shell

Azure Cloud Shell es un shell interactivo gratuito que puede usar para ejecutar los pasos de este artículo. Tiene las herramientas comunes de Azure preinstaladas y configuradas para usarlas en la cuenta. 

Para abrir Cloud Shell, seleccione **Pruébelo** en la esquina superior derecha de un bloque de código. También puede abrir Cloud Shell en una pestaña independiente acudiendo a [https://shell.azure.com/bash](https://shell.azure.com/bash). Seleccione **Copiar** para copiar los bloques de código, péguelos en Cloud Shell y, después, seleccione **Entrar** para ejecutarlos.

Si prefiere instalar y usar la CLI en un entorno local, para este tutorial se requiere la versión 2.0.30 de la CLI de Azure o una versión posterior. Ejecute `az --version` para encontrar la versión. Si necesita instalarla o actualizarla, vea [Instalación de la CLI de Azure]( /cli/azure/install-azure-cli).

## <a name="set-up-chaos-mesh-on-your-aks-cluster"></a>Configuración de Chaos Mesh en el clúster de AKS

Para poder ejecutar errores de Chaos Mesh en Chaos Studio, debe instalar Chaos Mesh en el clúster de AKS.

1. Ejecute los siguientes comandos en una ventana [Azure Cloud Shell](../cloud-shell/overview.md) donde tenga la suscripción activa establecida para que sea la suscripción donde se implementa el clúster de AKS. Reemplace `$RESOURCE_GROUP` y `$CLUSTER_NAME` por el grupo de recursos y asigne un nombre al recurso del clúster.

   ```azurecli-interactive
   az aks get-credentials -g $RESOURCE_GROUP -n $CLUSTER_NAME
   helm repo add chaos-mesh https://charts.chaos-mesh.org
   helm repo update
   kubectl create ns chaos-testing
   helm install chaos-mesh chaos-mesh/chaos-mesh --namespace=chaos-testing --version 2.0.3 --set chaosDaemon.runtime=containerd --set chaosDaemon.socketPath=/run/containerd/containerd.sock
   ```

2. Ejecute el siguiente comando para comprobar que los pods de Chaos Mesh están instalados:

   ```azurecli-interactive
   kubectl get po -n chaos-testing
   ```

Debe ver una salida similar a la siguiente (un chaos-controller-manager y uno o varios chaos-daemons):

```bash
NAME                                        READY   STATUS    RESTARTS   AGE
chaos-controller-manager-69fd5c46c8-xlqpc   1/1     Running   0          2d5h
chaos-daemon-jb8xh                          1/1     Running   0          2d5h
chaos-dashboard-98c4c5f97-tx5ds             1/1     Running   0          2d5h
```

También puede [usar las instrucciones de instalación en el sitio web de Chaos Mesh](https://chaos-mesh.org/docs/production-installation-using-helm/).


## <a name="enable-chaos-studio-on-your-aks-cluster"></a>Habilitación de Chaos Studio en el clúster de AKS

Chaos Studio no puede insertar errores en un recurso a menos que ese recurso se haya incorporado primero a Chaos Studio. Incorpore un recurso a Chaos Studio mediante la creación de un [destino y funcionalidades](chaos-studio-targets-capabilities.md) en el recurso. Los clústeres de AKS solo tienen un tipo de destino (directo de servicio), pero otros recursos pueden tener hasta dos tipos de destino: uno para los errores directos de servicio y otro para los errores basados en agente. Cada tipo de error de Chaos Mesh se representa como una funcionalidad (PodChaos, NetworkChaos, IOChaos, etc.).

1. Cree un destino al reemplazar `$RESOURCE_ID` por el identificador de recurso del clúster de AKS que va a incorporar:

    ```azurecli-interactive
    az rest --method put --url "https://management.azure.com/$RESOURCE_ID/providers/Microsoft.Chaos/targets/Microsoft-AzureKubernetesServiceChaosMesh?api-version=2021-09-15-preview" --body "{\"properties\":{}}"
    ```

2. Cree las capacidades en el destino al reemplazar `$RESOURCE_ID` por el identificador de recurso del clúster de AKS que va a incorporar y `$CAPABILITY` por el [nombre de la capacidad de error que está habilitando](chaos-studio-fault-library.md).
    
    ```azurecli-interactive
    az rest --method put --url "https://management.azure.com/$RESOURCE_ID/providers/Microsoft.Chaos/targets/Microsoft-AzureKubernetesServiceChaosMesh/capabilities/$CAPABILITY?api-version=2021-09-15-preview"  --body "{\"properties\":{}}"
    ```

    Por ejemplo, si está habilitando la capacidad PodChaos:

    ```azurecli-interactive
    az rest --method put --url "https://management.azure.com/subscriptions/b65f2fec-d6b2-4edd-817e-9339d8c01dc4/resourceGroups/myRG/providers/Microsoft.ContainerService/managedClusters/myCluster/providers/Microsoft.Chaos/targets/Microsoft-AzureKubernetesServiceChaosMesh/capabilities/PodChaos-2.1?api-version=2021-09-15-preview"  --body "{\"properties\":{}}"
    ```

    Esto debe hacerse con cada capacidad que quiera habilitar en el clúster.

Ya ha incorporado correctamente el clúster de AKS a Chaos Studio.

## <a name="create-an-experiment"></a>Creación de un experimento
Con el clúster de AKS ya incorporado, puede crear el experimento. Un experimento de caos define las acciones que desea realizar en los recursos de destino, organizados en pasos, que se ejecutan secuencialmente, y ramas, que se ejecutan en paralelo.

1. Cree un jsonSpec de Chaos Mesh:
    1. Visite la documentación de Chaos Mesh para ver un tipo de error, [por ejemplo, el tipo PodChaos](https://chaos-mesh.org/docs/simulate-pod-chaos-on-kubernetes/#create-experiments-using-yaml-configuration-files).
    2. Formule la configuración de YAML para ese tipo de error mediante la documentación de Chaos Mesh.

        ```yaml
        apiVersion: chaos-mesh.org/v1alpha1
        kind: PodChaos
        metadata:
          name: pod-failure-example
          namespace: chaos-testing
        spec:
          action: pod-failure
          mode: all
          duration: '600s'
          selector:
            namespaces:
              - default
        ```
    3. Quite cualquier YAML fuera de `spec` (incluido el nombre de la propiedad de especificación) y quite la sangría de los detalles de la especificación.

        ```yaml
        action: pod-failure
        mode: all
        duration: '600s'
        selector:
          namespaces:
            - default
        ```
    4. Use un [convertidor de YAML a JSON como este](https://www.convertjson.com/yaml-to-json.htm) para convertir el YAML de Chaos Mesh a JSON y minimizarlo.

        ```json
        {"action":"pod-failure","mode":"all","duration":"600s","selector":{"namespaces":["default"]}}
        ```
    5. Use una [herramienta de escape de cadena JSON como esta](https://www.freeformatter.com/json-escape.html) para aplicar escape a la especificación JSON.
    
        ```json
        {\"action\":\"pod-failure\",\"mode\":\"all\",\"duration\":\"600s\",\"selector\":{\"namespaces\":[\"default\"]}}
        ```

2. Cree el JSON del experimento a partir del ejemplo de JSON siguiente. Modifique el JSON para que corresponda con el experimento que quiere ejecutar mediante la [API de creación de experimento](/rest/api/chaosstudio/experiments/create-or-update), la [biblioteca de errores](chaos-studio-fault-library.md) y el jsonSpec creado en el paso anterior.

    ```json
    {
      "location": "centralus",
      "identity": {
        "type": "SystemAssigned"
      },
      "properties": {
        "steps": [
          {
            "name": "AKS pod kill",
            "branches": [
              {
                "name": "AKS pod kill",
                "actions": [
                  {
                    "type": "continuous",
                    "selectorId": "Selector1",
                    "duration": "PT10M",
                    "parameters": [
                      {
                          "key": "jsonSpec",
                          "value": "{\"action\":\"pod-failure\",\"mode\":\"all\",\"duration\":\"600s\",\"selector\":{\"namespaces\":[\"default\"]}}"
                      }
                    ],
                    "name": "urn:csci:microsoft:azureKubernetesServiceChaosMesh:podChaos/2.1"
                  }
                ]
              }
            ]
          }
        ],
        "selectors": [
          {
            "id": "Selector1",
            "type": "List",
            "targets": [
              {
                "type": "ChaosTarget",
                "id": "/subscriptions/b65f2fec-d6b2-4edd-817e-9339d8c01dc4/resourceGroups/myRG/providers/Microsoft.ContainerService/managedClusters/myCluster/providers/Microsoft.Chaos/targets/Microsoft-AzureKubernetesServiceChaosMesh"
              }
            ]
          }
        ]
      }
    }
    ```
    
2. Cree el experimento mediante la CLI de Azure, reemplazando `$SUBSCRIPTION_ID`, `$RESOURCE_GROUP` y `$EXPERIMENT_NAME` por las propiedades del experimento. Asegúrese de que ha guardado y cargado el JSON del experimento y actualice `experiment.json` con el nombre de archivo JSON.

    ```azurecli-interactive
    az rest --method put --uri https://management.azure.com/subscriptions/$SUBSCRIPTION_ID/resourceGroups/$RESOURCE_GROUP/providers/Microsoft.Chaos/experiments/$EXPERIMENT_NAME?api-version=2021-09-15-preview --body @experiment.json
    ```

    Cada experimento crea una identidad administrada asignada por el sistema correspondiente. Observe el valor `principalId` de esta identidad en la respuesta del paso siguiente.

## <a name="give-experiment-permission-to-your-aks-cluster"></a>Conceder permiso de experimento al clúster de AKS
Al crear un experimento de caos, Chaos Studio crea una identidad administrada asignada por el sistema que ejecuta errores en los recursos de destino. Esta identidad debe tener los [permisos adecuados](chaos-studio-fault-providers.md) en el recurso de destino para que el experimento se ejecute correctamente.

Dé al experimento acceso a los recursos mediante el comando siguiente, reemplazando `$EXPERIMENT_PRINCIPAL_ID` por el valor principalId del paso anterior y `$RESOURCE_ID` por el identificador de recurso del recurso de destino (en este caso, el identificador de recurso del clúster de AKS). Ejecute este comando para cada recurso de destino del experimento. 

```azurecli-interactive
az role assignment create --role "Azure Kubernetes Cluster User Role" --assignee-object-id $EXPERIMENT_PRINCIPAL_ID --scope $RESOURCE_ID
```

## <a name="run-your-experiment"></a>Ejecutar el experimento
Ahora ya puede ejecutar el experimento. Para ver el impacto, se recomienda abrir la información general del clúster de AKS e ir a **Información** en una pestaña del explorador independiente. Los datos en directo del **número de pods activos** mostrarán el impacto tras la ejecución el experimento.

1. Inicie el experimento mediante la CLI de Azure, reemplazando `$SUBSCRIPTION_ID`, `$RESOURCE_GROUP` y `$EXPERIMENT_NAME` por las propiedades del experimento.

    ```azurecli-interactive
    az rest --method post --uri https://management.azure.com/subscriptions/$SUBSCRIPTION_ID/resourceGroups/$RESOURCE_GROUP/providers/Microsoft.Chaos/experiments/$EXPERIMENT_NAME/start?api-version=2021-09-15-preview
    ```

2. La respuesta incluye una dirección URL de estado que puede usar para consultar el estado del experimento mientras se ejecuta.

## <a name="next-steps"></a>Pasos siguientes
Ahora que ha ejecutado un experimento directo del servicio de Chaos Mesh de AKS, está listo para:
- [Crear un experimento que usa errores basados en agente](chaos-studio-tutorial-agent-based-portal.md)
- [Administrar el experimento](chaos-studio-run-experiment.md)
