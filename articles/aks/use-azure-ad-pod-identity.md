---
title: Uso de identidades administradas del pod de Azure Active Directory en Azure Kubernetes Service (versión preliminar)
description: Aprenda a utilizar identidades administradas del pod de AAD en Azure Kubernetes Service (AKS)
services: container-service
ms.topic: article
ms.date: 3/12/2021
ms.openlocfilehash: 44f4415e09ca9e2942eb1da4c69cf98759f737ce
ms.sourcegitcommit: 05dd6452632e00645ec0716a5943c7ac6c9bec7c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/17/2021
ms.locfileid: "122253879"
---
# <a name="use-azure-active-directory-pod-managed-identities-in-azure-kubernetes-service-preview"></a>Uso de identidades administradas del pod de Azure Active Directory en Azure Kubernetes Service (versión preliminar)

Las identidades administradas del pod de Azure Active Directory usan primitivas de Kubernetes para asociar [identidades administradas para recursos de Azure][az-managed-identities] e identidades en Azure Active Directory (AAD) con pods. Los administradores crean identidades y enlaces como primitivas de Kubernetes que permiten a los pods acceder a los recursos de Azure que dependen de AAD como proveedor de identidades.

> [!NOTE]
>La característica descrita en este documento, identidades administradas por pods (versión preliminar), se reemplazará por identidades administradas por pods v2 (versión preliminar).
> Si tiene una instalación existente de AADPODIDENTITY, habrá una opción de migración a la versión V2. A medida que nos acerquemos a la versión preliminar pública programada para el segundo trimestre de 2022, se mostrarán más detalles sobre la migración. La habilitación de esta característica significa que el componente MIC no es necesario.

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

## <a name="before-you-begin"></a>Antes de empezar

Debe tener instalado el siguiente recurso:

* CLI de Azure, versión 2.20.0 o posterior
* Extensión `aks-preview`, versión 0.5.5 o posterior

### <a name="limitations"></a>Limitaciones

* Se permite un máximo de 200 identidades de pod para un clúster.
* Se permite un máximo de 200 excepciones de identidades de pod para un clúster.
* Las identidades administradas del pod solo están disponibles en grupos de nodos de Linux.

### <a name="register-the-enablepodidentitypreview"></a>Registro de `EnablePodIdentityPreview`

Registre la característica `EnablePodIdentityPreview`:

```azurecli
az feature register --name EnablePodIdentityPreview --namespace Microsoft.ContainerService
```

### <a name="install-the-aks-preview-azure-cli"></a>Instalación de la CLI de Azure `aks-preview`

También se necesita la versión 0.5.5 o posterior de la extensión *aks-preview* de la CLI de Azure. Instale la extensión de la CLI de Azure *aks-preview* mediante el comando [az extension add][az-extension-add]. También puede instalar las actualizaciones disponibles mediante el comando [az extension update][az-extension-update].

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

## <a name="create-an-aks-cluster-with-azure-container-networking-interface-cni"></a>Creación de un clúster de AKS con Azure Container Networking Interface (CNI)

> [!NOTE]
> Esta es la configuración recomendada predeterminada.

Cree un clúster de AKS con Azure CNI y una identidad administrada del pod habilitada. Los siguientes comandos usan [az group create][az-group-create] para crear un grupo de recursos denominado *myResourceGroup* y el comando [az aks create][az-aks-create] para crear un clúster de AKS denominado *myAKSCluster* en el grupo de recursos *myResourceGroup*.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
az aks create -g myResourceGroup -n myAKSCluster --enable-pod-identity --network-plugin azure
```
> [!NOTE]
> Azure Active Directory Pod Identity admite dos modos de funcionamiento:
> 
> 1. Modo estándar: en este modo, se implementan los dos componentes siguientes en el clúster de AKS: 
>     * [Controlador de identidades administradas (MIC)](https://azure.github.io/aad-pod-identity/docs/concepts/mic/): un controlador de Kubernetes que busca cambios en los pods, [AzureIdentity](https://azure.github.io/aad-pod-identity/docs/concepts/azureidentity/) y [AzureIdentityBinding](https://azure.github.io/aad-pod-identity/docs/concepts/azureidentitybinding/) mediante el servidor de API de Kubernetes. Cuando se detectan cambios pertinentes, MIC agrega o elimina [AzureAssignedIdentity](https://azure.github.io/aad-pod-identity/docs/concepts/azureassignedidentity/) según sea necesario. En concreto, cuando se programa un pod, MIC asigna la identidad administrada en Azure al conjunto de escalado de máquinas virtuales subyacente utilizado por el grupo de nodos durante la fase de creación. Cuando se eliminan todos los pods que usan la identidad, se quita la identidad del conjunto de escalado de máquinas virtuales del grupo de nodos, a menos que otros pods usen la misma identidad administrada. MIC realiza acciones similares cuando se crean o eliminan AzureIdentity o AzureIdentityBinding.
>     * [Identidad administrada del nodo (NMI)](https://azure.github.io/aad-pod-identity/docs/concepts/nmi/): es un pod que se ejecuta como DaemonSet en cada nodo del clúster de AKS. NMI intercepta las solicitudes de token de seguridad a [Azure Instance Metadata Service](../virtual-machines/linux/instance-metadata-service.md?tabs=linux) en cada nodo, las redirige a sí mismo y valida si el pod tiene acceso a la identidad para la que se solicita un token; luego, captura el token del inquilino de Azure Active Directory en nombre de la aplicación.
> 2. Modo administrado: en este modo, solo hay NMI. El usuario debe asignar y administrar manualmente la identidad. Para más información, consulte [Pod Identity en modo administrado](https://azure.github.io/aad-pod-identity/docs/configure/pod_identity_in_managed_mode/).
>
>Cuando se realiza la instalación de Azure Active Directory Pod Identity mediante un gráfico de Helm o un manifiesto YAML como se muestra en la [Guía de instalación](https://azure.github.io/aad-pod-identity/docs/getting-started/installation/), puede elegir entre el modo `standard` y `managed`. Si en cambio decide instalar Azure Active Directory Pod Identity mediante el [complemento de clúster de AKS](/azure/aks/use-azure-ad-pod-identity) como se muestra en este artículo, la configuración usará el modo `managed` (administrado).

Use [az aks get-credentials][az-aks-get-credentials] para iniciar sesión en el clúster de AKS. Este comando también descarga y configura el certificado de cliente `kubectl` en el equipo de desarrollo.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

## <a name="update-an-existing-aks-cluster-with-azure-cni"></a>Actualización de un clúster de AKS existente con Azure CNI

Actualice un clúster de AKS existente con Azure CNI para incluir la identidad administrada del pod.

```azurecli-interactive
az aks update -g $MY_RESOURCE_GROUP -n $MY_CLUSTER --enable-pod-identity
```
## <a name="using-kubenet-network-plugin-with-azure-active-directory-pod-managed-identities"></a>Uso del complemento de red Kubenet con identidades administradas del pod de Azure Active Directory 

> [!IMPORTANT]
> La ejecución de aad-pod-identity en un clúster con Kubenet no es una configuración recomendada debido a la implicación que conlleva en materia de seguridad. Siga los pasos de mitigación y configure las directivas antes de habilitar aad-pod-identity en un clúster con Kubenet.

## <a name="mitigation"></a>Mitigación

Para mitigar la vulnerabilidad en el nivel de clúster, puede usar la directiva integrada de Azure "Los contenedores de clúster de Kubernetes solo deben usar funcionalidades permitidas" para limitar el ataque CAP_NET_RAW.  

Adición de NET_RAW a "Funcionalidades de eliminación necesarias"

![image](https://user-images.githubusercontent.com/50749048/118558790-206b8880-b735-11eb-9e48-236b81116812.png)

Si no usa Azure Policy, puede usar el controlador de admisión OpenPolicyAgent junto con el webhook de validación de Gatekeeper. Si ya tiene instalado Gatekeeper en el clúster, agregue un elemento ConstraintTemplate del tipo K8sPSPCapabilities:

```
kubectl apply -f https://raw.githubusercontent.com/open-policy-agent/gatekeeper-library/master/library/pod-security-policy/capabilities/template.yaml
```
Agregue una plantilla para limitar la generación de pods con la funcionalidad NET_RAW:

```
apiVersion: constraints.gatekeeper.sh/v1beta1
kind: K8sPSPCapabilities
metadata:
  name: prevent-net-raw
spec:
  match:
    kinds:
      - apiGroups: [""]
        kinds: ["Pod"]
    excludedNamespaces:
      - "kube-system"
  parameters:
    requiredDropCapabilities: ["NET_RAW"]
```

## <a name="create-an-aks-cluster-with-kubenet-network-plugin"></a>Creación de un clúster de AKS con el complemento de red Kubenet

Cree un clúster de AKS con complemento de red Kubenet y una identidad administrada del pod habilitada.

```azurecli-interactive
az aks create -g $MY_RESOURCE_GROUP -n $MY_CLUSTER --enable-pod-identity --enable-pod-identity-with-kubenet
```

## <a name="update-an-existing-aks-cluster-with-kubenet-network-plugin"></a>Actualización de un clúster de AKS existente con el complemento de red Kubenet

Actualice un clúster de AKS existente con el complemento de red Kubnet para incluir la identidad administrada por pods.

```azurecli-interactive
az aks update -g $MY_RESOURCE_GROUP -n $MY_CLUSTER --enable-pod-identity --enable-pod-identity-with-kubenet
```

## <a name="create-an-identity"></a>Creación de una identidad

Cree una identidad mediante [az identity create][az-identity-create] y establezca las variables *IDENTITY_CLIENT_ID* e *IDENTITY_RESOURCE_ID*.

```azurecli-interactive
az group create --name myIdentityResourceGroup --location eastus
export IDENTITY_RESOURCE_GROUP="myIdentityResourceGroup"
export IDENTITY_NAME="application-identity"
az identity create --resource-group ${IDENTITY_RESOURCE_GROUP} --name ${IDENTITY_NAME}
export IDENTITY_CLIENT_ID="$(az identity show -g ${IDENTITY_RESOURCE_GROUP} -n ${IDENTITY_NAME} --query clientId -otsv)"
export IDENTITY_RESOURCE_ID="$(az identity show -g ${IDENTITY_RESOURCE_GROUP} -n ${IDENTITY_NAME} --query id -otsv)"
```

## <a name="assign-permissions-for-the-managed-identity"></a>Asignación de permisos a la identidad administrada

Para ejecutar la demostración, la identidad administrada *IDENTITY_CLIENT_ID* debe tener permisos de colaborador de máquina virtual en el grupo de recursos que contiene el conjunto de escalado de máquinas virtuales del clúster de AKS.

```azurecli-interactive
NODE_GROUP=$(az aks show -g myResourceGroup -n myAKSCluster --query nodeResourceGroup -o tsv)
NODES_RESOURCE_ID=$(az group show -n $NODE_GROUP -o tsv --query "id")
az role assignment create --role "Virtual Machine Contributor" --assignee "$IDENTITY_CLIENT_ID" --scope $NODES_RESOURCE_ID
```

## <a name="create-a-pod-identity"></a>Creación de una identidad de pod

Cree una identidad de pod para el clúster mediante `az aks pod-identity add`.

> [!IMPORTANT]
> Debe tener los permisos pertinentes (por ejemplo, Propietario) en la suscripción para crear la identidad y asignar el enlace de roles a la identidad del clúster.
> 
> La identidad del clúster debe tener permisos de operador de identidad administrada para la identidad que se va a asignar.

```azurecli-interactive
export POD_IDENTITY_NAME="my-pod-identity"
export POD_IDENTITY_NAMESPACE="my-app"
az aks pod-identity add --resource-group myResourceGroup --cluster-name myAKSCluster --namespace ${POD_IDENTITY_NAMESPACE}  --name ${POD_IDENTITY_NAME} --identity-resource-id ${IDENTITY_RESOURCE_ID}
```

> [!NOTE]
> Al habilitar la identidad administrada del pod en el clúster de AKS, se agrega una excepción AzurePodIdentityException denominada *aks-addon-exception* al espacio de nombres *kube-system*. Una excepción AzurePodIdentityException permite que los pods con determinadas etiquetas tengan acceso al punto de conexión de Azure Instance Metadata Service (IMDS) sin que el servidor de identidad administrada del nodo (NMI) lo intercepte. *aks-addon-exception* permite que los complementos propios de AKS, como la identidad administrada del pod de AAD, funcionen sin tener que configurar manualmente una excepción AzurePodIdentityException. También puede agregar, quitar y actualizar una excepción AzurePodIdentityException con `az aks pod-identity exception add`, `az aks pod-identity exception delete`, `az aks pod-identity exception update` o `kubectl`.

> [!NOTE]
> Al asignar la identidad del pod mediante `pod-identity add`, la CLI de Azure intenta conceder el rol de operador de identidad administrada para la identidad del pod (*IDENTITY_RESOURCE_I* ) a la identidad del clúster.

## <a name="run-a-sample-application"></a>Ejecución de una aplicación de ejemplo

Para que un pod use la identidad administrada del pod de AAD, el pod necesita una etiqueta *aadpodidbinding* con un valor que coincida con un selector de *AzureIdentityBinding*. Para ejecutar una aplicación de ejemplo mediante la identidad administrada del pod de AAD, cree un archivo de `demo.yaml` con el siguiente contenido. Reemplace *POD_IDENTITY_NAME*, *IDENTITY_CLIENT_ID* e *IDENTITY_RESOURCE_GROUP* por los valores de los pasos anteriores. Reemplace *SUBSCRIPTION_ID* por su identificador de suscripción.

> [!NOTE]
> En los pasos anteriores, creó las variables *POD_IDENTITY_NAME*, *IDENTITY_CLIENT_ID* e *IDENTITY_RESOURCE_GROUP*. Puede usar un comando como `echo` para mostrar el valor establecido para las variables; por ejemplo, `echo $IDENTITY_NAME`.

```yml
apiVersion: v1
kind: Pod
metadata:
  name: demo
  labels:
    aadpodidbinding: $POD_IDENTITY_NAME
spec:
  containers:
  - name: demo
    image: mcr.microsoft.com/oss/azure/aad-pod-identity/demo:v1.6.3
    args:
      - --subscriptionid=$SUBSCRIPTION_ID
      - --clientid=$IDENTITY_CLIENT_ID
      - --resourcegroup=$IDENTITY_RESOURCE_GROUP
    env:
      - name: MY_POD_NAME
        valueFrom:
          fieldRef:
            fieldPath: metadata.name
      - name: MY_POD_NAMESPACE
        valueFrom:
          fieldRef:
            fieldPath: metadata.namespace
      - name: MY_POD_IP
        valueFrom:
          fieldRef:
            fieldPath: status.podIP
  nodeSelector:
    kubernetes.io/os: linux
```

Observe que la definición del pod tiene una etiqueta *aadpodidbinding* con un valor que coincide con el nombre de la identidad de pod que ejecutó `az aks pod-identity add` en el paso anterior.

Implemente `demo.yaml` en el mismo espacio de nombres que la identidad de pod mediante `kubectl apply`:

```azurecli-interactive
kubectl apply -f demo.yaml --namespace $POD_IDENTITY_NAMESPACE
```

Compruebe que la aplicación de ejemplo se ejecuta correctamente con `kubectl logs`.

```azurecli-interactive
kubectl logs demo --follow --namespace $POD_IDENTITY_NAMESPACE
```

Compruebe que los registros muestran que un token se ha obtenido correctamente y que la operación *GET* es correcta.
 
```output
...
successfully doARMOperations vm count 0
successfully acquired a token using the MSI, msiEndpoint(http://169.254.169.254/metadata/identity/oauth2/token)
successfully acquired a token, userAssignedID MSI, msiEndpoint(http://169.254.169.254/metadata/identity/oauth2/token) clientID(xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx)
successfully made GET on instance metadata
...
```
## <a name="run-an-application-with-multiple-identities"></a>Ejecución de una aplicación con varias identidades

## <a name="create-multiple-identities"></a>Creación de varias identidades

Cree identidades mediante [az identity create][az-identity-create] y establezca las variables *IDENTITY_CLIENT_ID* e *IDENTITY_RESOURCE_ID*.

```azurecli-interactive
az group create --name myIdentityResourceGroup --location eastus
export IDENTITY_RESOURCE_GROUP="myIdentityResourceGroup"
export IDENTITY_NAME_1="application-identity_1"
az identity create --resource-group ${IDENTITY_RESOURCE_GROUP} --name ${IDENTITY_NAME_1}
export IDENTITY_NAME_2="application-identity_2"
az identity create --resource-group ${IDENTITY_RESOURCE_GROUP} --name ${IDENTITY_NAME_2}
export IDENTITY_CLIENT_ID="$(az identity show -g ${IDENTITY_RESOURCE_GROUP} -n ${IDENTITY_NAME_1} --query clientId -otsv)"
export IDENTITY_RESOURCE_ID="$(az identity show -g ${IDENTITY_RESOURCE_GROUP} -n ${IDENTITY_NAME_1} --query id -otsv)"
export IDENTITY_CLIENT_ID="$(az identity show -g ${IDENTITY_RESOURCE_GROUP} -n ${IDENTITY_NAME_2} --query clientId -otsv)"
export IDENTITY_RESOURCE_ID="$(az identity show -g ${IDENTITY_RESOURCE_GROUP} -n ${IDENTITY_NAME_2} --query id -otsv)"
```

## <a name="assign-permissions-for-the-managed-identities"></a>Asignación de permisos a las identidades administradas

La identidad administrada *IDENTITY_CLIENT_ID* debe tener permisos de lectura en el grupo de recursos que contiene el conjunto de escalado de máquinas virtuales del clúster de AKS.

```azurecli-interactive
NODE_GROUP=$(az aks show -g myResourceGroup -n myAKSCluster --query nodeResourceGroup -o tsv)
NODES_RESOURCE_ID=$(az group show -n $NODE_GROUP -o tsv --query "id")
az role assignment create --role "Reader" --assignee "$IDENTITY_CLIENT_ID_1" --scope $NODES_RESOURCE_ID
az role assignment create --role "Reader" --assignee "$IDENTITY_CLIENT_ID_2" --scope $NODES_RESOURCE_ID
```

## <a name="create-pod-identities"></a>Creación de identidades de pod

Cree identidades de pod para el clúster mediante `az aks pod-identity add`.

> [!IMPORTANT]
> Debe tener los permisos adecuados, como `Owner`, en su suscripción para crear la identidad y el enlace de rol.

```azurecli-interactive
export POD_IDENTITY_NAME="my-pod-identity"
export POD_IDENTITY_NAMESPACE="my-app"
az aks pod-identity add --resource-group myResourceGroup --cluster-name myAKSCluster --namespace ${POD_IDENTITY_NAMESPACE}  --name ${POD_IDENTITY_NAME} --identity-resource-id ${IDENTITY_RESOURCE_ID_1} --binding-selector foo
az aks pod-identity add --resource-group myResourceGroup --cluster-name myAKSCluster --namespace ${POD_IDENTITY_NAMESPACE}  --name ${POD_IDENTITY_NAME} --identity-resource-id ${IDENTITY_RESOURCE_ID_2} --binding-selector foo
```

> [!NOTE]
> Al habilitar la identidad administrada del pod en el clúster de AKS, se agrega una excepción AzurePodIdentityException denominada *aks-addon-exception* al espacio de nombres *kube-system*. Una excepción AzurePodIdentityException permite que los pods con determinadas etiquetas tengan acceso al punto de conexión de Azure Instance Metadata Service (IMDS) sin que el servidor de identidad administrada del nodo (NMI) lo intercepte. *aks-addon-exception* permite que los complementos propios de AKS, como la identidad administrada del pod de AAD, funcionen sin tener que configurar manualmente una excepción AzurePodIdentityException. También puede agregar, quitar y actualizar una excepción AzurePodIdentityException con `az aks pod-identity exception add`, `az aks pod-identity exception delete`, `az aks pod-identity exception update` o `kubectl`.

## <a name="run-a-sample-application-with-multiple-identities"></a>Ejecución de una aplicación de ejemplo con varias identidades

Para que un pod use la identidad administrada del pod de AAD, el pod necesita una etiqueta *aadpodidbinding* con un valor que coincida con un selector de *AzureIdentityBinding*. Para ejecutar una aplicación de ejemplo mediante la identidad administrada del pod de AAD, cree un archivo de `demo.yaml` con el siguiente contenido. Reemplace *POD_IDENTITY_NAME*, *IDENTITY_CLIENT_ID* e *IDENTITY_RESOURCE_GROUP* por los valores de los pasos anteriores. Reemplace *SUBSCRIPTION_ID* por su identificador de suscripción.

> [!NOTE]
> En los pasos anteriores, creó las variables *POD_IDENTITY_NAME*, *IDENTITY_CLIENT_ID* e *IDENTITY_RESOURCE_GROUP*. Puede usar un comando como `echo` para mostrar el valor establecido para las variables; por ejemplo, `echo $IDENTITY_NAME`.

```yml
apiVersion: v1
kind: Pod
metadata:
  name: demo
  labels:
    aadpodidbinding: foo
spec:
  containers:
  - name: demo
    image: mcr.microsoft.com/oss/azure/aad-pod-identity/demo:v1.6.3
    args:
      - --subscriptionid=$SUBSCRIPTION_ID
      - --clientid=$IDENTITY_CLIENT_ID
      - --resourcegroup=$IDENTITY_RESOURCE_GROUP
    env:
      - name: MY_POD_NAME
        valueFrom:
          fieldRef:
            fieldPath: metadata.name
      - name: MY_POD_NAMESPACE
        valueFrom:
          fieldRef:
            fieldPath: metadata.namespace
      - name: MY_POD_IP
        valueFrom:
          fieldRef:
            fieldPath: status.podIP
  nodeSelector:
    kubernetes.io/os: linux
```

Observe que la definición del pod tiene una etiqueta *aadpodidbinding* con un valor que coincide con el nombre de la identidad de pod que ejecutó `az aks pod-identity add` en el paso anterior.

Implemente `demo.yaml` en el mismo espacio de nombres que la identidad de pod mediante `kubectl apply`:

```azurecli-interactive
kubectl apply -f demo.yaml --namespace $POD_IDENTITY_NAMESPACE
```

Compruebe que la aplicación de ejemplo se ejecuta correctamente con `kubectl logs`.

```azurecli-interactive
kubectl logs demo --follow --namespace $POD_IDENTITY_NAMESPACE
```

Compruebe que los registros muestran que un token se ha obtenido correctamente y que la operación *GET* es correcta.
 
```output
...
successfully doARMOperations vm count 0
successfully acquired a token using the MSI, msiEndpoint(http://169.254.169.254/metadata/identity/oauth2/token)
successfully acquired a token, userAssignedID MSI, msiEndpoint(http://169.254.169.254/metadata/identity/oauth2/token) clientID(xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx)
successfully made GET on instance metadata
...
```
export IDENTITY_CLIENT_ID="$(az identity show -g ${IDENTITY_RESOURCE_GROUP} -n ${IDENTITY_NAME} --query clientId -otsv)" export IDENTITY_RESOURCE_ID="$(az identity show -g ${IDENTITY_RESOURCE_GROUP} -n ${IDENTITY_NAME} --query id -otsv)"
```

## Clean up

To remove AAD pod-managed identity from your cluster, remove the sample application and the pod identity from the cluster. Then remove the identity.

```azurecli-interactive
kubectl delete pod demo --namespace $POD_IDENTITY_NAMESPACE
az aks pod-identity delete --name ${POD_IDENTITY_NAME} --namespace ${POD_IDENTITY_NAMESPACE} --resource-group myResourceGroup --cluster-name myAKSCluster
az identity delete -g ${IDENTITY_RESOURCE_GROUP} -n ${IDENTITY_NAME}
```

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre las identidades administradas, consulte [Identidades administradas para recursos de Azure][az-managed-identities].

<!-- LINKS - external -->
[az-aks-create]: /cli/azure/aks#az_aks_create
[az-aks-get-credentials]: /cli/azure/aks#az_aks_get_credentials
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
[az-group-create]: /cli/azure/group#az_group_create
[az-identity-create]: /cli/azure/identity#az_identity_create
[az-managed-identities]: ../active-directory/managed-identities-azure-resources/overview.md
[az-role-assignment-create]: /cli/azure/role/assignment#az_role_assignment_create