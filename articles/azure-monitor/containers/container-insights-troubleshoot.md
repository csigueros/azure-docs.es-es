---
title: Procedimiento para solucionar problemas de Container Insights | Microsoft Docs
description: En este artículo se describe cómo se pueden detectar y solucionar problemas con Container Insights.
ms.topic: conceptual
ms.date: 03/25/2021
ms.openlocfilehash: 04fea3c36cbff4e2c8ecb315f6e3f93bc92aa2b3
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/14/2021
ms.locfileid: "130003257"
---
# <a name="troubleshooting-container-insights"></a>Solución de problemas de Container Insights

Al configurar la supervisión del clúster de Azure Kubernetes Service (AKS) con Container Insights, es posible que encuentre un problema que impida la recopilación de datos o el informe del estado. En este artículo se detallan algunos problemas comunes y los pasos para solucionarlos.

## <a name="authorization-error-during-onboarding-or-update-operation"></a>Error de autorización durante la operación de incorporación o actualización

Durante la habilitación de Container Insights o la actualización de un clúster para que admita la recopilación de métricas, puede recibir un error similar al siguiente: *El cliente "<identidad del usuario>" con el id. de objeto "<objectId del usuario >"no tiene autorización para realizar la acción "Microsoft.Authorization/roleAssignments/write" en el ámbito*.

Durante el proceso de incorporación o de actualización, se intenta la asignación del rol **Publicador de métricas de supervisión** en el recurso de clúster. El usuario que inicia el proceso para habilitar Container Insights o la actualización para admitir la recopilación de métricas debe tener acceso al permiso **Microsoft.Authorization/roleAssignments/write** en el ámbito de recurso de clúster de AKS. Solo a los miembros de los roles integrados **Propietario** y **Administrador de acceso de usuario** se les ha concedido acceso a este permiso. Si las directivas de seguridad requieren la asignación de permisos a un nivel específico, se recomienda consultar los [roles personalizados](../../role-based-access-control/custom-roles.md) y asignarlos a los usuarios que lo necesiten.

También puede conceder este rol de forma manual desde Azure Portal siguiendo estos pasos:

1. Inicie sesión en [Azure Portal](https://portal.azure.com).
2. En Azure Portal, haga clic en **Todos los servicios**, en la esquina superior izquierda. En la lista de recursos, escriba **Kubernetes**. Cuando comience a escribir, la lista se filtrará en función de la entrada. Seleccione **Azure Kubernetes**.
3. En la lista de clústeres de Kubernetes, seleccione uno.
2. En el menú de la izquierda, haga clic en **Control de acceso (IAM)** .
3. Seleccione **+ Agregar** para agregar una asignación de roles, seleccione el cuadro **Publicador de métricas de supervisión** y, en el cuadro **Seleccionar**, escriba **AKS** para filtrar los resultados en las entidades de servicio de clústeres definidas en la suscripción. Seleccione aquella en la lista que sea específica de ese clúster.
4. Haga clic en **Guardar** para finalizar la asignación del rol.

## <a name="container-insights-is-enabled-but-not-reporting-any-information"></a>Container Insights está habilitado, pero no notifica ninguna información

Si Container Insights está habilitado y configurado correctamente, pero no puede ver la información de estado o no se devuelve ningún resultado de una consulta de registros, siga estos pasos para diagnosticar el problema:

1. Ejecute este comando para comprobar el estado del agente:

    `kubectl get ds omsagent --namespace=kube-system`

    Si la salida se parece al ejemplo siguiente, la implementación se ha realizado correctamente:

    ```
    User@aksuser:~$ kubectl get ds omsagent --namespace=kube-system
    NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR                 AGE
    omsagent   2         2         2         2            2           beta.kubernetes.io/os=linux   1d
    ```
2. Si tiene nodos de Windows Server, compruebe el estado del agente mediante la ejecución del comando:

    `kubectl get ds omsagent-win --namespace=kube-system`

    Si la salida se parece al ejemplo siguiente, la implementación se ha realizado correctamente:

    ```
    User@aksuser:~$ kubectl get ds omsagent-win --namespace=kube-system
    NAME                   DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR                   AGE
    omsagent-win           2         2         2         2            2           beta.kubernetes.io/os=windows   1d
    ```
3. Compruebe el estado de implementación con la versión del agente *06072018* o posterior con el comando:

    `kubectl get deployment omsagent-rs -n=kube-system`

    Si la salida se parece al ejemplo siguiente, la implementación se ha realizado correctamente:

    ```
    User@aksuser:~$ kubectl get deployment omsagent-rs -n=kube-system
    NAME       DESIRED   CURRENT   UP-TO-DATE   AVAILABLE    AGE
    omsagent   1         1         1            1            3h
    ```

4. Con el comando siguiente, consulte el estado del pod para comprobar que se está ejecutando: `kubectl get pods --namespace=kube-system`

    La salida debe parecerse al ejemplo siguiente con un estado de *Running* (En ejecución) en omsagent:

    ```
    User@aksuser:~$ kubectl get pods --namespace=kube-system
    NAME                                READY     STATUS    RESTARTS   AGE
    aks-ssh-139866255-5n7k5             1/1       Running   0          8d
    azure-vote-back-4149398501-7skz0    1/1       Running   0          22d
    azure-vote-front-3826909965-30n62   1/1       Running   0          22d
    omsagent-484hw                      1/1       Running   0          1d
    omsagent-fkq7g                      1/1       Running   0          1d
    omsagent-win-6drwq                  1/1       Running   0          1d
    ```

## <a name="error-messages"></a>Mensajes de error

En la tabla siguiente se resumen los errores conocidos que pueden producirse al usar Container Insights.

| Mensajes de error  | Acción |
| ---- | --- |
| Mensaje de error `No data for selected filters`  | Puede tardar algún tiempo en establecer el flujo de datos de supervisión de clústeres recién creados. Espere de 10 a 15 minutos como mínimo para que aparezcan los datos del clúster. |
| Mensaje de error `Error retrieving data` | Mientras el clúster de Azure Kubernetes Service se configura para la supervisión de rendimiento y de mantenimiento, se establece una conexión entre el clúster y el área de trabajo de Azure Log Analytics. Se utiliza un área de trabajo de Log Analytics para almacenar todos los datos de supervisión del clúster. Este error puede producirse cuando el área de trabajo de Log Analytics se ha eliminado. Compruebe si se ha eliminado el área de trabajo y, en ese caso, tendrá que volver a habilitar la supervisión del clúster con Container Insights y especificar un área de trabajo existente o crear una. Para volver a habilitarla, tiene que [deshabilitar](container-insights-optout.md) la supervisión del clúster y [habilitar](container-insights-enable-new-cluster.md) de nuevo Container Insights. |
| `Error retrieving data` después de agregar Container Insights mediante az aks cli | Al habilitar la supervisión con `az aks cli`, es posible que Container Insights no se haya implementado correctamente. Compruebe que la solución se ha implementado. Para ello, vaya al área de trabajo de Log Analytics y vea si la solución está disponible seleccionado **Soluciones** en el panel de la izquierda. Para resolver este problema, tendrá que volver a implementar la solución siguiendo las instrucciones sobre [cómo implementar Container Insights](container-insights-onboard.md). |

Para ayudar a diagnosticar el problema, hemos incluido un [script de solución de problemas](https://github.com/microsoft/Docker-Provider/tree/ci_dev/scripts/troubleshoot).

## <a name="container-insights-agent-replicaset-pods-are-not-scheduled-on-non-azure-kubernetes-cluster"></a>Los pods de ReplicaSet del agente de Container Insights no están programados en un clúster de Kubernetes que no es de Azure

Los pods de ReplicaSet del agente de Container Insights tienen una dependencia de los siguientes selectores de nodo en los nodos de trabajo (o agente) para la programación:

```
nodeSelector:
  beta.kubernetes.io/os: Linux
  kubernetes.io/role: agent
```

Si los nodos de trabajo no tienen etiquetas de nodo adjuntas, los pods de ReplicaSet del agente no se programarán. Consulte [Selectores de etiqueta de asignación de Kubernetes](https://kubernetes.io/docs/concepts/configuration/assign-pod-node/) para obtener instrucciones sobre cómo adjuntar la etiqueta.

## <a name="performance-charts-dont-show-cpu-or-memory-of-nodes-and-containers-on-a-non-azure-cluster"></a>Los gráficos de rendimiento no muestran la CPU o la memoria de los nodos y contenedores de un clúster que no es de Azure.

Los pods del agente de Container Insights usan el punto de conexión cAdvisor en el agente de nodo para recopilar las métricas de rendimiento. Compruebe que el agente en contenedor del nodo está configurado para permitir que se abra `cAdvisor port: 10255` en todos los nodos del clúster para recopilar las métricas de rendimiento.

## <a name="non-azure-kubernetes-cluster-are-not-showing-in-container-insights"></a>No se muestran los clústeres de Kubernetes que no son de Azure en Container Insights

Para ver el clúster de Kubernetes que no es de Azure en Container Insights, se necesita acceso de lectura en el área de trabajo de Log Analytics que admite esta información y en el recurso de la solución de Container Insights **ContainerInsights (*área de trabajo*)** .

## <a name="metrics-arent-being-collected"></a>No se están recopilando las métricas

1. Compruebe que el clúster se encuentra en una [región admitida para las métricas personalizadas](../essentials/metrics-custom-overview.md#supported-regions).

2. Compruebe que la asignación de roles de **publicador de métricas de supervisión** existe con el siguiente comando de la CLI:

    ``` azurecli
    az role assignment list --assignee "SP/UserassignedMSI for omsagent" --scope "/subscriptions/<subid>/resourcegroups/<RG>/providers/Microsoft.ContainerService/managedClusters/<clustername>" --role "Monitoring Metrics Publisher"
    ```
    En el caso de los clústeres con MSI, el id. de cliente asignado por el usuario para omsagent cambia cada vez que la supervisión se habilita y deshabilita, por lo que la asignación de roles debe existir en el id. de cliente de MSI actual. 

3. En el caso de los clústeres con identidad de pod de Azure Active Directory habilitada y con MSI:

   - Compruebe que la etiqueta necesaria **kubernetes.azure.com/managedby: aks** está presente en los pods de omsagent con el siguiente comando:

        `kubectl get pods --show-labels -n kube-system | grep omsagent`

    - Compruebe que las excepciones están habilitadas cuando la identidad de pod está habilitada mediante uno de los métodos admitidos en https://github.com/Azure/aad-pod-identity#1-deploy-aad-pod-identity.

        Para comprobarlo, ejecute el comando siguiente:

        `kubectl get AzurePodIdentityException -A -o yaml`

        Debe recibir un resultado similar al siguiente:

        ```
        apiVersion: "aadpodidentity.k8s.io/v1"
        kind: AzurePodIdentityException
        metadata:
        name: mic-exception
        namespace: default
        spec:
        podLabels:
        app: mic
        component: mic
        ---
        apiVersion: "aadpodidentity.k8s.io/v1"
        kind: AzurePodIdentityException
        metadata:
        name: aks-addon-exception
        namespace: kube-system
        spec:
        podLabels:
        kubernetes.azure.com/managedby: aks
        ```

## <a name="installation-of-azure-monitor-containers-extension-fail-with-an-error-containing-manifests-contain-a-resource-that-already-exists-on-azure-arc-enabled-kubernetes-cluster"></a>La instalación de la extensión de Containers de Azure Monitor produce el error "los manifiestos contiene un recurso que ya existe" en el clúster de Kubernetes habilitado para Azure Arc
El error _los manifiestos contienen un recurso que ya existe_ indica que los recursos del agente de Container Insights ya existen en el clúster de Kubernetes habilitado para Azure Arc. Esto indica que el agente de Container Insights ya está instalado a través del gráfico de Helm azuremonitor-containers o el complemento de supervisión si es el clúster de AKS que está conectado a Azure Arc. La solución a este problema consiste en limpiar los recursos existentes del agente de Container Insights si existe y, a continuación, habilitar la extensión de Containers de Azure Monitor.

### <a name="for-non-aks-clusters"></a>Para clústeres que no son de AKS 
1.  En el clúster K8s que está conectado a Azure Arc, ejecute el siguiente comando para comprobar si la versión del gráfico de Helm azmon-containers-release-1 existe o no:

    `helm list  -A`

2.  Si la salida del comando anterior indica que azmon-containers-release-1 existe, elimine la versión del gráfico de Helm:

    `helm del azmon-containers-release-1`

### <a name="for-aks-clusters"></a>Para clústeres de AKS
1.  Ejecute los comandos siguientes y busque el perfil del complemento omsagent para comprobar si el complemento de supervisión de AKS está habilitado o no:

    ```
    az  account set -s <clusterSubscriptionId>
    az aks show -g <clusterResourceGroup> -n <clusterName>
    ```

2.  Si hay una configuración de perfil de complemento omsagent con el identificador de recurso del área de trabajo de Log Analytics en la salida del comando anterior, indica que el complemento supervisión de AKS está habilitado y que debe deshabilitarse:

    `az aks disable-addons -a monitoring -g <clusterResourceGroup> -n <clusterName>`

Si con los pasos anteriores no resolvió la instalación de la extensión de Containers de Azure Monitor, cree una incidencia a Microsoft para una investigación más exhaustiva.


## <a name="next-steps"></a>Pasos siguientes

Con la supervisión habilitada para capturar métricas de estado para los pods y nodos del clúster de AKS, estas métricas de estado están disponibles en Azure Portal. Para obtener información sobre cómo usar Container Insights, vea [Ver el estado de Azure Kubernetes Service](container-insights-analyze.md).
