---
title: Aprendizaje automático habilitado para Azure Arc (versión preliminar)
description: Configuración de Azure Kubernetes Service y clústeres de Kubernetes habilitados para Azure Arc para el entrenamiento y la inferencia de modelos de Machine Learning en Azure Machine Learning
titleSuffix: Azure Machine Learning
author: luisquintanilla
ms.author: luquinta
ms.service: machine-learning
ms.subservice: core
ms.date: 11/17/2021
ms.topic: how-to
ms.custom: ignite-fall-2021
ms.openlocfilehash: 5ae6b6c636e1713d9f423b00301759ffb469b5f0
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/17/2021
ms.locfileid: "132723442"
---
# <a name="configure-kubernetes-clusters-for-machine-learning-preview"></a>Configuración de clústeres de Kubernetes para el aprendizaje automático (versión preliminar)

Aprenda a configurar Azure Kubernetes Service (AKS) y clústeres de Kubernetes habilitados para Azure Arc para el entrenamiento y la inferencia de cargas de trabajo de aprendizaje automático.

## <a name="what-is-azure-arc-enabled-machine-learning"></a>¿Qué es el aprendizaje automático habilitado para Azure Arc?

Azure Arc permite ejecutar servicios de Azure en cualquier entorno de Kubernetes, ya sea local, en varias nubes o en el perímetro.

El aprendizaje automático habilitado para Azure Arc permite configurar y usar Azure Kubernetes Service o clústeres de Kubernetes habilitados para Azure Arc para el entrenamiento, la inferencia y la administración de modelos de Machine Learning en Azure Machine Learning.

## <a name="machine-learning-on-azure-kubernetes-service"></a>Machine Learning en Azure Kubernetes Service

Para usar clústeres de Azure Kubernetes Service para cargas de trabajo de entrenamiento e inferencia de Azure Machine Learning, no tiene que conectarlos a Azure Arc.

Tendrá que configurar el tráfico de red entrante y saliente. Para más información, vea [Configuración del tráfico de red de entrada y salida (AKS)](how-to-access-azureml-behind-firewall.md#azure-kubernetes-services-1).

Para implementar la extensión Azure Machine Learning en clústeres de Azure Kubernetes Service, vea la sección [Implementación de la extensión Azure Machine Learning](#deploy-azure-machine-learning-extension).

## <a name="prerequisites"></a>Prerrequisitos

* Suscripción a Azure. Si no tiene una suscripción a Azure, [cree una cuenta gratuita](https://azure.microsoft.com/free) antes de empezar.
* Clúster de Kubernetes habilitado para Azure Arc. Para más información, consulte [Inicio rápido: conexión de un clúster de Kubernetes existente a Azure Arc](../azure-arc/kubernetes/quickstart-connect-cluster.md).

    > [!NOTE]
    > Para los clústeres de AKS, la conexión a Azure Arc es **opcional**.

* Debe cumplir los [requisitos de red de Azure Arc](../azure-arc/kubernetes/quickstart-connect-cluster.md?tabs=azure-cli#meet-network-requirements).

    > [!IMPORTANT]
    > Los clústeres que se ejecutan detrás de un servidor proxy o firewall de salida necesitan configuraciones de red adicionales.
    >
    > Para más información, vea [Configuración del tráfico de red de entrada y salida (Kubernetes habilitado para Azure Arc)](how-to-access-azureml-behind-firewall.md#arc-kubernetes).

* Cumpla los [requisitos previos de las extensiones de clústeres de Kubernetes habilitados para Azure Arc](../azure-arc/kubernetes/extensions.md#prerequisites).
  * CLI de Azure versión 2.24.0 o posterior
  * Versión de la extensión k8s-extension de la CLI de Azure >= 1.0.0

* Un área de trabajo de Azure Machine Learning. [Cree un área de trabajo](how-to-manage-workspace.md?tabs=python) antes de empezar si no la tiene todavía.
  * SDK de Azure Machine Learning para Python 1.30 o posterior
* Debe iniciar sesión en Azure mediante la CLI de Azure.

    ```azurecli
    az login
    az account set --subscription <your-subscription-id>
    ```  

* **Azure RedHat OpenShift Service (ARO) y OpenShift Container Platform (OCP) solo**

    * Un clúster de Kubernetes de ARO o OCP está en funcionamiento. Para más información, consulte [Creación de un clúster de Kubernetes de ARO](../openshift/tutorial-create-cluster.md) y [Creación de un clúster de Kubernetes de OCP](https://docs.openshift.com/container-platform/4.6/installing/installing_platform_agnostic/installing-platform-agnostic.html).
    * Conceda acceso con privilegios a las cuentas de servicio de AzureML.

        Ejecute `oc edit scc privileged` y agregue lo siguiente 

        * ```system:serviceaccount:azure-arc:azure-arc-kube-aad-proxy-sa```
        * ```system:serviceaccount:azureml:{EXTENSION NAME}-kube-state-metrics``` **(Nota:** ```{EXTENSION NAME}``` **aquí debe coincidir con el nombre de extensión usado en el paso** ```az k8s-extension create --name``` **)** .
        * ```system:serviceaccount:azureml:cluster-status-reporter```
        * ```system:serviceaccount:azureml:prom-admission```
        * ```system:serviceaccount:azureml:default```
        * ```system:serviceaccount:azureml:prom-operator```
        * ```system:serviceaccount:azureml:csi-blob-node-sa```
        * ```system:serviceaccount:azureml:csi-blob-controller-sa```
        * ```system:serviceaccount:azureml:load-amlarc-selinux-policy-sa```
        * ```system:serviceaccount:azureml:azureml-fe```
        * ```system:serviceaccount:azureml:prom-prometheus```

## <a name="deploy-azure-machine-learning-extension"></a>Implementación de la extensión de Azure Machine Learning

Kubernetes habilitado para Azure Arc tiene una funcionalidad de extensión de clúster que le permite instalar varios agentes, como definiciones de Azure Policy, supervisión o aprendizaje automático, entre muchos otros. Azure Machine Learning requiere el uso de la extensión de clúster *Microsoft.AzureML.Kubernetes* para implementar el agente de Azure Machine Learning en el clúster de Kubernetes. Cuando ya está instalada la extensión de Azure Machine Learning, puede asociarse el clúster a un área de trabajo de Azure Machine Learning y usarlo en los siguientes escenarios:

* [Cursos](#training)
* [Solo inferencia en tiempo real](#inferencing)
* [Entrenamiento e inferencia](#training-inferencing)

> [!TIP]
> Al entrenar solo los clústeres, también se admite la inferencia por lotes como parte de las canalizaciones de Azure Machine Learning.

Use el comando [`create`](/cli/azure/k8s-extension?view=azure-cli-latest&preserve-view=true) de la extensión `k8s-extension` de la CLI de Azure para implementar la extensión de Azure Machine Learning en el clúster de Kubernetes habilitado para Azure Arc.

> [!IMPORTANT]
> Establezca el parámetro `--cluster-type` en `managedClusters` para implementar la extensión de Azure Machine Learning en clústeres de AKS.

A continuación, se muestra la lista de opciones de configuración disponibles para su uso en diferentes escenarios de implementación de extensiones de Azure Machine Learning.

Puede usar ```--config``` o ```--config-protected``` para especificar la lista de pares clave-valor para las opciones de configuración de implementaciones de Azure Machine Learning.

> [!TIP]
> Establezca el parámetro `openshift` en `True` para implementar la extensión de Azure Machine Learning en clústeres de Kubernetes de ARO y OCP.

| Nombre de clave de opción de configuración  | Descripción  | Cursos | Inferencia | Entrenamiento e inferencia |
|---|---|---|---|---|
| ```enableTraining``` | El valor predeterminado es `False`. Establezca esta opción en `True` para crear una instancia de extensión para entrenar modelos de aprendizaje automático. |  **&check;** | N/D |  **&check;** |
|```logAnalyticsWS```  | El valor predeterminado es `False`. La extensión de Azure Machine Learning se integra con el área de trabajo de Azure LogAnalytics. Establezca esta opción en `True` para proporcionar la funcionalidad de visualización y análisis de registros mediante el área de trabajo de LogAnalytics. Se pueden aplicar costos por el área de trabajo de LogAnalytics. | Opcional | Opcional | Opcional |
|```installNvidiaDevicePlugin```  | El valor predeterminado es `True`. El complemento de dispositivo Nvidia es necesario para el entrenamiento y la inferencia en hardware de GPU de Nvidia. La extensión de Azure Machine Learning instala Nvidia Device Plugin de forma predeterminada durante la creación de la instancia de Azure Machine Learning, con independencia de si el clúster de Kubernetes tiene hardware de GPU o no. Establezca esta opción en `False` si no tiene previsto usar una GPU o si el complemento de dispositivo Nvidia ya está instalado.  | Opcional |Opcional | Opcional |
| ```enableInference``` | El valor predeterminado es `False`.  Establezca esta opción en `True` para crear una instancia de extensión para la inferencia de modelos de aprendizaje automático. | N/D | **&check;** |  **&check;** |
| ```allowInsecureConnections``` | El valor predeterminado es `False`. Establézcalo en `True` para la implementación de extensiones de Azure Machine Learning con compatibilidad con puntos de conexión HTTP para la inferencia cuando ```sslCertPemFile``` y no se ```sslKeyPemFile``` proporcionen. | N/D | Opcional |  Opcional |
| ```sslCertPemFile``` & ```ssKeyPMFile``` | Ruta de acceso al certificado SSL y al archivo de clave (con codificación PEM). Necesario para la implementación de extensiones de AzureML con compatibilidad con puntos de conexión HTTPS para la inferencia. | N/D | Opcional |  Opcional |
| ```privateEndpointNodeport``` | El valor predeterminado es `False`.  Establézcalo en `True` para la implementación de extensiones de Azure Machine Learning con compatibilidad con puntos de conexión privados de inferencia de aprendizaje automático mediante NodePort. | N/D | Opcional |  Opcional |
| ```privateEndpointILB``` | El valor predeterminado es `False`.  Establézcalo en `True` para la implementación de extensiones de Azure Machine Learning con compatibilidad con puntos de conexión privados de inferencia de aprendizaje automático mediante el equilibrador de carga interno de serviceType. | N/D| Opcional |  Opcional |
| ```inferenceLoadBalancerHA``` | El valor predeterminado es `True`. De manera predeterminada, la extensión de Azure Machine Learning implementa varias réplicas de controlador de entrada para alta disponibilidad. Establezca el valor en `False` si tiene recursos de clúster limitados o desea implementar una extensión de Azure Machine Learning solo para desarrollo y pruebas. Si no se usa un equilibrador de carga de alta disponibilidad, solo se implementa una réplica del controlador de entrada. | N/D | Opcional |  Opcional |
|```openshift``` | El valor predeterminado es `False`. Establézcalo en `True` para la implementación de extensiones de Azure Machine Learning en un clúster de ARO u OCP. El proceso de implementación compila automáticamente un paquete de directivas y lo carga en cada nodo para que la operación de servicios de Azure Machine Learning funcione correctamente. | Opcional | Opcional |  Opcional |

> [!WARNING]
> Si el complemento de dispositivo Nvidia ya está instalado en el clúster, volver a instalarlo puede producir un error de instalación de la extensión. Establezca `installNvidiaDevicePlugin` en `False` para evitar errores de implementación.

### <a name="deploy-extension-for-training-workloads"></a>Implementación de la extensión para cargas de trabajo de entrenamiento <a id="training"></a>

Use el siguiente comando de la CLI de Azure para implementar la extensión de Azure Machine Learning y habilitar las cargas de trabajo de entrenamiento en el clúster de Kubernetes:

```azurecli
az k8s-extension create --name arcml-extension --extension-type Microsoft.AzureML.Kubernetes --config enableTraining=True --cluster-type connectedClusters --cluster-name <your-connected-cluster-name> --resource-group <resource-group> --scope cluster
```

### <a name="deploy-extension-for-real-time-inferencing-workloads"></a>Implementación de la extensión para cargas de trabajo de inferencia en tiempo real <a id="inferencing"></a>

Según la configuración de red, la variante de distribución de Kubernetes y dónde se hospeda el clúster de Kubernetes (en el entorno local o en la nube), elija una de las siguientes opciones para implementar la extensión de Azure Machine Learning y habilitar las cargas de trabajo de inferencia en el clúster de Kubernetes.

#### <a name="public-endpoints-support-with-public-load-balancer"></a>Compatibilidad de los puntos de conexión públicos con el equilibrador de carga público

* **HTTPS**

    ```azurecli
    az k8s-extension create --name arcml-extension --extension-type Microsoft.AzureML.Kubernetes --cluster-type connectedClusters --cluster-name <your-connected-cluster-name> --config enableInference=True --config-protected sslCertPemFile=<path-to-the-SSL-cert-PEM-ile> sslKeyPemFile=<path-to-the-SSL-key-PEM-file> --resource-group <resource-group> --scope cluster
    ```

* **HTTP**

    > [!WARNING]
    > La compatibilidad de los puntos de conexión HTTP públicos con el equilibrador de carga público es la manera menos segura de implementar la extensión de Azure Machine Learning para escenarios de inferencia en tiempo real y, por tanto, **NO** se recomienda.

    ```azurecli
    az k8s-extension create --name arcml-extension --extension-type Microsoft.AzureML.Kubernetes --cluster-type connectedClusters --cluster-name <your-connected-cluster-name>  --configuration-settings enableInference=True allowInsecureConnections=True --resource-group <resource-group> --scope cluster
    ```

#### <a name="private-endpoints-support-with-internal-load-balancer"></a>Compatibilidad de puntos de conexión privados con el equilibrador de carga interno

* **HTTPS**

    ```azurecli
    az k8s-extension create --name amlarc-compute --extension-type Microsoft.AzureML.Kubernetes --cluster-type connectedClusters --cluster-name <your-connected-cluster-name> --config enableInference=True privateEndpointILB=True --config-protected sslCertPemFile=<path-to-the-SSL-cert-PEM-ile> sslKeyPemFile=<path-to-the-SSL-key-PEM-file> --resource-group <resource-group> --scope cluster
    ```

* **HTTP**

   ```azurecli
   az k8s-extension create --name arcml-extension --extension-type Microsoft.AzureML.Kubernetes --cluster-type connectedClusters --cluster-name <your-connected-cluster-name> --config enableInference=True privateEndpointILB=True allowInsecureConnections=True --resource-group <resource-group> --scope cluster
   ```

#### <a name="endpoints-support-with-nodeport"></a>Compatibilidad de puntos de conexión con NodePort

El uso de NodePort le ofrece la libertad de configurar su propia solución de equilibrio de carga para configurar entornos que no son totalmente compatibles con Kubernetes o incluso exponer directamente una o varias direcciones IP de nodos.

Cuando se implementa con el servicio de NodePort, la dirección URL de puntuación (o dirección URL de Swagger) se reemplazará por una de las direcciones IP del nodo (por ejemplo, ```http://<NodeIP><NodePort>/<scoring_path>```) y permanecerá sin cambios incluso si el nodo no está disponible. Sin embargo, puede reemplazarlo por cualquier otra dirección IP del nodo.

* **HTTPS**

    ```azurecli
    az k8s-extension create --name arcml-extension --extension-type Microsoft.AzureML.Kubernetes --cluster-type connectedClusters --cluster-name <your-connected-cluster-name> --resource-group <resource-group> --scope cluster --config enableInference=True privateEndpointNodeport=True --config-protected sslCertPemFile=<path-to-the-SSL-cert-PEM-ile> sslKeyPemFile=<path-to-the-SSL-key-PEM-file>
    ```

* **HTTP**

   ```azurecli
   az k8s-extension create --name arcml-extension --extension-type Microsoft.AzureML.Kubernetes --cluster-type connectedClusters --cluster-name <your-connected-cluster-name> --config enableInference=True privateEndpointNodeport=True allowInsecureConnections=Ture --resource-group <resource-group> --scope cluster
   ```

### <a name="deploy-extension-for-training-and-inferencing-workloads"></a>Implementación de la extensión para cargas de trabajo de entrenamiento e inferencia <a id="training-inferencing"></a>

Use el siguiente comando de la CLI de Azure para implementar la extensión de Azure Machine Learning y habilitar las cargas de trabajo de inferencia en tiempo real, inferencia por lotes y entrenamiento del clúster de Kubernetes.

```azurecli
az k8s-extension create --name arcml-extension --extension-type Microsoft.AzureML.Kubernetes --cluster-type connectedClusters --cluster-name <your-connected-cluster-name> --config enableTraining=True enableInference=True --config-protected sslCertPemFile=<path-to-the-SSL-cert-PEM-ile> sslKeyPemFile=<path-to-the-SSL-key-PEM-file>--resource-group <resource-group> --scope cluster
```

## <a name="resources-created-during-deployment"></a>Recursos creados durante la implementación

Una vez implementada la extensión de Azure Machine Learning, se crean los siguientes recursos en Azure, así como en el clúster de Kubernetes, en función de las cargas de trabajo que se ejecuten en el clúster.

|Nombre del recurso  |Tipo de recurso |Cursos |Inferencia |Entrenamiento e inferencia|
|---|---|---|---|---|
|Azure ServiceBus|Recurso de Azure|**&check;**|**&check;**|**&check;**|
|Azure Relay|Recurso de Azure|**&check;**|**&check;**|**&check;**|
|{EXTENSION-NAME}|Recurso de Azure|**&check;**|**&check;**|**&check;**|
|aml-operator|Implementación de Kubernetes|**&check;**|N/D|**&check;**|
|{EXTENSION-NAME}-kube-state-metrics|Implementación de Kubernetes|**&check;**|**&check;**|**&check;**|
|{EXTENSION-NAME}-prometheus-operator|Implementación de Kubernetes|**&check;**|**&check;**|**&check;**|
|amlarc-identity-controller|Implementación de Kubernetes|N/D|**&check;**|**&check;**|
|amlarc-identity-proxy|Implementación de Kubernetes|N/D|**&check;**|**&check;**|
|azureml-fe|Implementación de Kubernetes|N/D|**&check;**|**&check;**|
|inference-operator-controller-manager|Implementación de Kubernetes|N/D|**&check;**|**&check;**|
|metrics-controller-manager|Implementación de Kubernetes|**&check;**|**&check;**|**&check;**|
|relayserver|Implementación de Kubernetes|**&check;**|**&check;**|**&check;**|
|cluster-status-reporter|Implementación de Kubernetes|**&check;**|**&check;**|**&check;**|
|nfd-master|Implementación de Kubernetes|**&check;**|N/D|**&check;**|
|gateway|Implementación de Kubernetes|**&check;**|**&check;**|**&check;**|
|csi-blob-controller|Implementación de Kubernetes|**&check;**|N/D|**&check;**|
|csi-blob-node|DaemonSet de Kubernetes|**&check;**|N/D|**&check;**|
|fluent-bit|DaemonSet de Kubernetes|**&check;**|**&check;**|**&check;**|
|k8s-host-device-plugin-daemonset|DaemonSet de Kubernetes|**&check;**|**&check;**|**&check;**|
|nfd-worker|DaemonSet de Kubernetes|**&check;**|N/D|**&check;**|
|prometheus-prom-prometheus|StatefulSet de Kubernetes|**&check;**|**&check;**|**&check;**|
|frameworkcontroller|StatefulSet de Kubernetes|**&check;**|N/D|**&check;**|

> [!IMPORTANT]
> Los recursos de Azure ServiceBus y Azure Relay están en el mismo grupo de recursos que el recurso de clúster de Arc. Estos recursos se usan para comunicarse con el clúster de Kubernetes y modificarlos interrumpirá los destinos de proceso asociados.

> [!NOTE]
> **{EXTENSION-NAME}** es el nombre de extensión especificado por el comando ```az k8s-extension create --name``` de la CLI de Azure.

## <a name="verify-your-azureml-extension-deployment"></a>Comprobación de la implementación de la extensión AzureML

```azurecli
az k8s-extension show --name arcml-extension --cluster-type connectedClusters --cluster-name <your-connected-cluster-name> --resource-group <resource-group>
```

En la respuesta, busque `"extensionType": "arcml-extension"` y `"installState": "Installed"`. Tenga en cuenta que puede aparecer `"installState": "Pending"` los primeros minutos.

Si `installState` aparece como **Installed** (Instalado), ejecute el siguiente comando en la máquina con el archivo kubeconfig que apunta al clúster para comprobar que todos los pods del espacio de nombres *azureml* se encuentra en estado *Running* (En ejecución):

```bash
kubectl get pods -n azureml
```

## <a name="attach-arc-cluster"></a>Asociación de un clúster de Arc

### <a name="studio"></a>[Estudio](#tab/studio)

La asociación de un clúster de Kubernetes habilitado para Azure Arc hace que esté disponible en el área de trabajo para el entrenamiento.

1. Vaya a [Azure Machine Learning Studio](https://ml.azure.com).
1. En **Administrar**, seleccione **Proceso**.
1. Seleccione la pestaña **Attached computes** (Procesos asociados).
1. Seleccione **+New > Kubernetes (preview)** (Nuevo > Kubernetes [versión preliminar]).

   ![Asociación del clúster de Kubernetes](./media/how-to-attach-arc-kubernetes/attach-kubernetes-cluster.png)

1. Escriba un nombre de proceso y seleccione el clúster de Kubernetes habilitado para Azure Arc en la lista desplegable.

    * **(Opcional)** Escriba el espacio de nombres de Kubernetes, que tiene como valor predeterminado `default`. Todas las cargas de trabajo de aprendizaje automático se enviarán al espacio de nombres de Kubernetes especificado en el clúster.

    * **(Opcional)** Asigne la identidad administrada asignada por el usuario o el sistema. Las identidades administradas eliminan la necesidad de que los desarrolladores administren las credenciales. Para obtener más información, consulte [Introducción a las identidades administradas](../active-directory/managed-identities-azure-resources/overview.md).

    ![Configuración del clúster de Kubernetes](./media/how-to-attach-arc-kubernetes/configure-kubernetes-cluster-2.png)

1. Seleccione **Attach** (Asociar).

    En la pestaña Attached computes (Procesos asociados), el estado inicial del clúster es *Creating* (Creación en curso). Cuando el clúster se ha asociado correctamente, el estado cambia a *Succeeded* (Realizado con éxito). De lo contrario, el estado cambia a *Failed* (Error).

    ![Aprovisionamiento de recursos](./media/how-to-attach-arc-kubernetes/provision-resources.png)

### <a name="python-sdk"></a>[SDK de Python](#tab/sdk)

Puede usar el SDK de Python de Azure Machine Learning para asociar clústeres de Kubernetes habilitados para Azure Arc para la ejecución como destinos de proceso mediante el método [`attach_configuration`](/python/api/azureml-core/azureml.core.compute.kubernetescompute.kubernetescompute?view=azure-ml-py&preserve-view=true).

El siguiente código de Python muestra cómo asociar un clúster de Kubernetes habilitado para Azure Arc y cómo usarlo como destino de proceso con la identidad administrada habilitada.

Las identidades administradas eliminan la necesidad de que los desarrolladores administren las credenciales. Para obtener más información, consulte [Introducción a las identidades administradas](../active-directory/managed-identities-azure-resources/overview.md).

```python
from azureml.core.compute import KubernetesCompute
from azureml.core.compute import ComputeTarget
from azureml.core.workspace import Workspace
import os

ws = Workspace.from_config()

# Specify a name for your Kubernetes compute
amlarc_compute_name = "<COMPUTE_CLUSTER_NAME>"

# resource ID for the Kubernetes cluster and user-managed identity
resource_id = "/subscriptions/<sub ID>/resourceGroups/<RG>/providers/Microsoft.Kubernetes/connectedClusters/<cluster name>"

user_assigned_identity_resouce_id = ['subscriptions/<sub ID>/resourceGroups/<RG>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<identity name>']

ns = "default" 

if amlarc_compute_name in ws.compute_targets:
    amlarc_compute = ws.compute_targets[amlarc_compute_name]
    if amlarc_compute and type(amlarc_compute) is KubernetesCompute:
        print("found compute target: " + amlarc_compute_name)
else:
   print("creating new compute target...")


# assign user-assigned managed identity
amlarc_attach_configuration = KubernetesCompute.attach_configuration(resource_id = resource_id, namespace = ns,  identity_type ='UserAssigned',identity_ids = user_assigned_identity_resouce_id) 

# assign system-assigned managed identity
# amlarc_attach_configuration = KubernetesCompute.attach_configuration(resource_id = resource_id, namespace = ns,  identity_type ='SystemAssigned') 

amlarc_compute = ComputeTarget.attach(ws, amlarc_compute_name, amlarc_attach_configuration)
amlarc_compute.wait_for_completion(show_output=True)

# get detailed compute description containing managed identity principle ID, used for permission access. 
print(amlarc_compute.get_status().serialize())
```

Use el parámetro `identity_type` para habilitar las identidades administradas `SystemAssigned` o `UserAssigned`.

### <a name="cli"></a>[CLI](#tab/cli)

Puede asociar un clúster de Kubernetes habilitado para Azure Arc o AKS mediante la CLI de Azure Machine Learning 2.0 (versión preliminar).

Use el comando [`attach`](/cli/azure/ml/compute?view=azure-cli-latest&preserve-view=true) de la CLI de Azure Machine Learning y establezca el argumento `--type` en `kubernetes` para adjuntar el clúster de Kubernetes mediante la CLI de Azure Machine Learning 2.0.

> [!NOTE]
> La compatibilidad con la asociación de proceso para clústeres de Kubernetes habilitados para AKS o Azure Arc requiere una versión de la extensión `ml` de la CLI de Azure >= 2.0.1a4. Para más información, consulte [Instalación y configuración de la CLI (v2)](how-to-configure-cli.md).

Los siguientes comandos muestran cómo asociar un clúster de Kubernetes habilitado para Azure Arc y cómo usarlo como destino de proceso con la identidad administrada habilitada.

**AKS**

```azurecli
az ml compute attach --resource-group <resource-group-name> --workspace-name <workspace-name> --name amlarc-compute --resource-id "/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Kubernetes/managedclusters/<cluster-name>" --type kubernetes --identity-type UserAssigned --user-assigned-identities "subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<identity-name>" --no-wait
```

**Kubernetes habilitado para Azure Arc**

```azurecli
az ml compute attach --resource-group <resource-group-name> --workspace-name <workspace-name> --name amlarc-compute --resource-id "/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Kubernetes/connectedClusters/<cluster-name>" --type kubernetes --user-assigned-identities "subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<identity-name>" --no-wait
```

Use el argumento `identity_type` para habilitar las identidades administradas `SystemAssigned` o `UserAssigned`.

> [!IMPORTANT]
> `--user-assigned-identities` solo es necesario para las identidades administradas `UserAssigned`. Aunque puede proporcionar una lista de identidades administradas por el usuario separadas por comas, solo se usa la primera al adjuntar el clúster.

---

## <a name="next-steps"></a>Pasos siguientes

- [Creación y selección de diferentes tipos de instancia para cargas de trabajo de entrenamiento e inferencia](how-to-kubernetes-instance-type.md)
- [Entrenamiento de modelos con la CLI (versión 2)](how-to-train-cli.md)
- [Envío de una ejecución de entrenamiento a un destino de proceso](how-to-set-up-training-targets.md)
- [Ajustar los hiperparámetros](how-to-tune-hyperparameters.md)
- [Entrenamiento de modelos de scikit-learn a gran escala con Azure Machine Learning](how-to-train-scikit-learn.md)
- [Entrenamiento de un modelo de TensorFlow](how-to-train-tensorflow.md)
- [Entrenamiento de modelos de PyTorch a gran escala con Azure Machine Learning](how-to-train-pytorch.md)
- [Creación y ejecución de canalizaciones de Machine Learning con el SDK de Azure Machine Learning](how-to-create-machine-learning-pipelines.md)
- [Inicio rápido: conexión de un clúster de Kubernetes existente a Azure Arc](../azure-arc/kubernetes/quickstart-connect-cluster.md#4a-connect-using-an-outbound-proxy-server)