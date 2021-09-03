---
title: Creación de un controlador de entrada con una instancia de Application Gateway existente
description: Este artículo ofrece información sobre cómo implementar un controlador de entrada de Application Gateway con una instancia de Application Gateway existente.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: how-to
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: ac59d16fc25b8f7d47d3422687a390013df38ebf
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121752399"
---
# <a name="install-an-application-gateway-ingress-controller-agic-using-an-existing-application-gateway"></a>Instalación de un controlador de entrada de Application Gateway (AGIC) con una instancia de Application Gateway existente

El controlador de entrada de Application Gateway (AGIC) es un pod dentro del clúster de Kubernetes.
AGIC supervisa los recursos de [entrada](https://kubernetes.io/docs/concepts/services-networking/ingress/) de Kubernetes. Además, crea y aplica la configuración de Application Gateway en función del estado del clúster de Kubernetes.

## <a name="outline"></a>Esquema:
- [Requisitos previos](#prerequisites)
- [Autenticación de Azure Resource Manager (ARM)](#azure-resource-manager-authentication)
    - Opción 1: [Configuración de aad-pod-identity](#set-up-aad-pod-identity) y creación de una identidad de Azure en ARM
    - Opción 2: [Uso de una entidad de servicio](#using-a-service-principal)
- [Instalación del controlador de entrada mediante Helm](#install-ingress-controller-as-a-helm-chart)
- [Instancia de Application Gateway compartida o de varios clústeres](#multi-cluster--shared-application-gateway): Instale AGIC en un entorno en el que se comparta la instancia de Application Gateway entre uno o varios clústeres de AKS y otros componentes de Azure.

## <a name="prerequisites"></a>Prerrequisitos
Este documento asume que ya tiene instaladas las siguientes herramientas e infraestructura:
- [AKS](https://azure.microsoft.com/services/kubernetes-service/) con [Azure Container Networking Interface (CNI)](../aks/configure-azure-cni.md)
- [Application Gateway v2](./tutorial-autoscale-ps.md) en la misma red virtual que AKS
- [AAD Pod Identity](https://github.com/Azure/aad-pod-identity) instalada en el clúster de AKS
- [Cloud Shell](https://shell.azure.com/) es el entorno de Azure Shell que tiene `az` CLI, `kubectl` y `helm` instalados. Estas herramientas son necesarias para los siguientes comandos.

__Realice una copia de seguridad de su configuración de Application Gateway__ antes de instalar AGIC:
  1. mediante [Azure Portal](https://portal.azure.com/) vaya a su instancia de `Application Gateway`
  2. en `Export template` haga clic en `Download`

El archivo zip que descargó tendrá plantillas JSON, bash y scripts de PowerShell que puede usar para restaurar la instancia de Application Gateway en caso de que sea necesario

## <a name="install-helm"></a>Instalación de Helm
[Helm](../aks/kubernetes-helm.md) es un administrador de paquetes para Kubernetes. Lo usaremos para instalar el paquete de `application-gateway-kubernetes-ingress`.
Use [Cloud Shell](https://shell.azure.com/) para instalar Helm:

1. Instale [Helm](../aks/kubernetes-helm.md) y ejecute lo siguiente para agregar el paquete de Helm `application-gateway-kubernetes-ingress`:

    - Clúster de AKS *habilitado para RBAC de Kubernetes*

    ```bash
    kubectl create serviceaccount --namespace kube-system tiller-sa
    kubectl create clusterrolebinding tiller-cluster-rule --clusterrole=cluster-admin --serviceaccount=kube-system:tiller-sa
    helm init --tiller-namespace kube-system --service-account tiller-sa
    ```

    - Clúster de AKS *deshabilitado para RBAC de Kubernetes*

    ```bash
    helm init
    ```

1. Agregue el repositorio de Helm de AGIC:
    ```bash
    helm repo add application-gateway-kubernetes-ingress https://appgwingress.blob.core.windows.net/ingress-azure-helm-package/
    helm repo update
    ```

## <a name="azure-resource-manager-authentication"></a>Autenticación de Azure Resource Manager

AGIC se comunica con el servidor de la API de Kubernetes y Azure Resource Manager. Una identidad es necesaria para acceder a estas API.

## <a name="set-up-aad-pod-identity"></a>Configuración de AAD Pod Identity

[AAD Pod Identity](https://github.com/Azure/aad-pod-identity) es un controlador, similar a AGIC, que también se ejecuta en su instancia de AKS. Enlaza las identidades de Azure Active Directory a los pods de Kubernetes. Una identidad es necesaria para que una aplicación de un pod de Kubernetes pueda comunicarse con otros componentes de Azure. En este caso en particular, se necesita autorización para que el pod de AGIC realice solicitudes HTTP a [ARM](../azure-resource-manager/management/overview.md).

Siga las [instrucciones de instalación de AAD Pod Identity](https://github.com/Azure/aad-pod-identity#deploy-the-azure-aad-identity-infra) para agregar este componente a su instancia de AKS.

A continuación, es necesario crear una identidad de Azure y concederle permisos de ARM.
Use [Cloud Shell](https://shell.azure.com/) para ejecutar todos los comandos siguientes y crear una identidad:

1. Cree una identidad de Azure **en el mismo grupo de recursos que los nodos de AKS**. Es importante elegir el grupo de recursos correcto. El grupo de recursos requerido en el siguiente comando *no* es al que se hace referencia en el panel del portal de AKS. Este es el grupo de recursos de las máquinas virtuales de `aks-agentpool`. Normalmente, el grupo de recursos comienza con `MC_` y contiene el nombre de su instancia de AKS. Por ejemplo: `MC_resourceGroup_aksABCD_westus`

    ```azurecli
    az identity create -g <agent-pool-resource-group> -n <identity-name>
    ```

1. Para los comandos de asignación de roles que aparecen a continuación, necesitamos obtener `principalId` para la identidad recién creada:

    ```azurecli
    az identity show -g <resourcegroup> -n <identity-name>
    ```

1. Conceda a la identidad acceso de tipo `Contributor` a la instancia de Application Gateway. Para ello, necesitará el identificador de la instancia de Application Gateway, que tendrá un aspecto similar al siguiente: `/subscriptions/A/resourceGroups/B/providers/Microsoft.Network/applicationGateways/C`

    Obtenga la lista de identificadores de la instancia de Application Gateway de su suscripción con: `az network application-gateway list --query '[].id'`

    ```azurecli
    az role assignment create \
        --role Contributor \
        --assignee <principalId> \
        --scope <App-Gateway-ID>
    ```

1. Conceda a la identidad acceso de `Reader` al grupo de recursos de Application Gateway. El identificador del grupo de recursos tendrá el siguiente aspecto: `/subscriptions/A/resourceGroups/B`. Puede obtener todos los grupos de recursos con: `az group list --query '[].id'`

    ```azurecli
    az role assignment create \
        --role Reader \
        --assignee <principalId> \
        --scope <App-Gateway-Resource-Group-ID>
    ```

## <a name="using-a-service-principal"></a>Uso de una entidad de servicio
También es posible proporcionar a AGIC acceso a ARM a través de un secreto de Kubernetes.

1. Cree una entidad de servicio de Active Directory y codifíquela con base64. La codificación base64 es necesaria para que el blob JSON se guarde en Kubernetes.

```azurecli
az ad sp create-for-rbac --sdk-auth | base64 -w0
```

2. Agregue el blob JSON codificado con base64 al archivo `helm-config.yaml`. Para obtener más información sobre `helm-config.yaml`, consulte la siguiente sección.
```yaml
armAuth:
    type: servicePrincipal
    secretJSON: <Base64-Encoded-Credentials>
```

## <a name="install-ingress-controller-as-a-helm-chart"></a>Instalación del controlador de entrada como un gráfico de Helm
En los primeros pasos instalamos Tiller de Helm en el clúster de Kubernetes. Use [Cloud Shell](https://shell.azure.com/) para instalar el paquete de Helm de AGIC:

1. Agregue el repositorio de Helm `application-gateway-kubernetes-ingress` y realice una actualización de Helm

    ```bash
    helm repo add application-gateway-kubernetes-ingress https://appgwingress.blob.core.windows.net/ingress-azure-helm-package/
    helm repo update
    ```

1. Descargue helm-config.yaml, que configurará AGIC:
    ```bash
    wget https://raw.githubusercontent.com/Azure/application-gateway-kubernetes-ingress/master/docs/examples/sample-helm-config.yaml -O helm-config.yaml
    ```
    O copie el siguiente archivo YAML: 
    
    ```yaml
    # This file contains the essential configs for the ingress controller helm chart

    # Verbosity level of the App Gateway Ingress Controller
    verbosityLevel: 3
    
    ################################################################################
    # Specify which application gateway the ingress controller will manage
    #
    appgw:
        subscriptionId: <subscriptionId>
        resourceGroup: <resourceGroupName>
        name: <applicationGatewayName>
    
        # Setting appgw.shared to "true" will create an AzureIngressProhibitedTarget CRD.
        # This prohibits AGIC from applying config for any host/path.
        # Use "kubectl get AzureIngressProhibitedTargets" to view and change this.
        shared: false
    
    ################################################################################
    # Specify which kubernetes namespace the ingress controller will watch
    # Default value is "default"
    # Leaving this variable out or setting it to blank or empty string would
    # result in Ingress Controller observing all acessible namespaces.
    #
    # kubernetes:
    #   watchNamespace: <namespace>
    
    ################################################################################
    # Specify the authentication with Azure Resource Manager
    #
    # Two authentication methods are available:
    # - Option 1: AAD-Pod-Identity (https://github.com/Azure/aad-pod-identity)
    armAuth:
        type: aadPodIdentity
        identityResourceID: <identityResourceId>
        identityClientID:  <identityClientId>
    
    ## Alternatively you can use Service Principal credentials
    # armAuth:
    #    type: servicePrincipal
    #    secretJSON: <<Generate this value with: "az ad sp create-for-rbac --sdk-auth | base64 -w0" >>
    
    ################################################################################
    # Specify if the cluster is Kubernetes RBAC enabled or not
    rbac:
        enabled: false # true/false
    
    # Specify aks cluster related information. THIS IS BEING DEPRECATED.
    aksClusterConfiguration:
        apiServerAddress: <aks-api-server-address>
    ```

1. Edite helm-config.yaml y rellene los valores de `appgw` y `armAuth`.
    ```bash
    nano helm-config.yaml
    ```

    > [!NOTE] 
    > `<identity-resource-id>` y `<identity-client-id>` son las propiedades de la identidad de Azure AD que configuró en la sección anterior. Para recuperar esta información, ejecute el comando siguiente: `az identity show -g <resourcegroup> -n <identity-name>`, donde `<resourcegroup>` es el grupo de recursos en el que se implementan el objeto de clúster AKS de nivel superior, la instancia de Application Gateway y la identidad administrada.

1. Instale el gráfico de Helm `application-gateway-kubernetes-ingress` con la configuración de `helm-config.yaml` del paso anterior

    ```bash
    helm install -f <helm-config.yaml> application-gateway-kubernetes-ingress/ingress-azure
    ```

    También puede combinar el `helm-config.yaml` y el comando de Helm en un solo paso:
    ```bash
    helm install ./helm/ingress-azure \
         --name ingress-azure \
         --namespace default \
         --debug \
         --set appgw.name=applicationgatewayABCD \
         --set appgw.resourceGroup=your-resource-group \
         --set appgw.subscriptionId=subscription-uuid \
         --set appgw.shared=false \
         --set armAuth.type=servicePrincipal \
         --set armAuth.secretJSON=$(az ad sp create-for-rbac --sdk-auth | base64 -w0) \
         --set rbac.enabled=true \
         --set verbosityLevel=3 \
         --set kubernetes.watchNamespace=default \
         --set aksClusterConfiguration.apiServerAddress=aks-abcdefg.hcp.westus2.azmk8s.io
    ```

1. Compruebe el registro del pod recién creado para comprobar si se inició correctamente

Consulte [esta guía paso a paso](ingress-controller-expose-service-over-http-https.md) para comprender cómo puede exponer un servicio de AKS a través de HTTP o HTTPS a Internet mediante Azure Application Gateway.



## <a name="multi-cluster--shared-application-gateway"></a>Instancia de Application Gateway compartida o de varios clústeres
De forma predeterminada, AGIC asume la propiedad total de la instancia de Application Gateway a la que está vinculado. AGIC versión 0.8.0 y versiones posteriores pueden compartir una única instancia de Application Gateway con otros componentes de Azure. Por ejemplo, podríamos usar la misma instancia de Application Gateway para una aplicación hospedada en un conjunto de escalado de máquinas virtuales, así como para un clúster de AKS.

__Realice una copia de seguridad de su configuración de Application Gateway__ antes de habilitar esta opción:
  1. mediante [Azure Portal](https://portal.azure.com/) vaya a su instancia de `Application Gateway`
  2. en `Export template` haga clic en `Download`

El archivo zip que descargó tendrá plantillas JSON, bash y scripts de PowerShell que puede usar para restaurar la instancia de Application Gateway

### <a name="example-scenario"></a>Escenario de ejemplo
Echemos un vistazo a una instancia de Application Gateway imaginaria, que administra el tráfico para dos sitios web:
  - `dev.contoso.com`: hospedado en una nueva instancia de AKS, mediante Application Gateway y AGIC
  - `prod.contoso.com`: hospedado en un [conjunto de escalado de máquinas virtuales de Azure](https://azure.microsoft.com/services/virtual-machine-scale-sets/)

Con la configuración predeterminada, AGIC asume la propiedad completa de la instancia de Application Gateway a la que se apunta. AGIC sobrescribe toda la configuración de la instancia de App Gateway. Si creamos manualmente un cliente de escucha para `prod.contoso.com` (en Application Gateway), sin definirlo en la entrada de Kubernetes, AGIC eliminará la configuración de `prod.contoso.com` en cuestión de segundos.

Para instalar AGIC y también servir `prod.contoso.com` desde las máquinas de un conjunto de escalado de máquinas virtuales, debemos restringir AGIC a configurar solo `dev.contoso.com`. Esto se facilita mediante la creación de instancias del siguiente [CRD](https://kubernetes.io/docs/concepts/extend-kubernetes/api-extension/custom-resources/):

```bash
cat <<EOF | kubectl apply -f -
apiVersion: "appgw.ingress.k8s.io/v1"
kind: AzureIngressProhibitedTarget
metadata:
  name: prod-contoso-com
spec:
  hostname: prod.contoso.com
EOF
```

El comando anterior crea un objeto de `AzureIngressProhibitedTarget`. Esto hace que AGIC (versión 0.8.0 y posteriores) tenga en cuenta la existencia de la configuración de Application Gateway para `prod.contoso.com` y le indica explícitamente que evite cambiar cualquier configuración relacionada con ese nombre de host.


### <a name="enable-with-new-agic-installation"></a>Habilitar con la nueva instalación de AGIC
Para limitar AGIC (versión 0.8.0 y posteriores) a un subconjunto de la configuración de Application Gateway, modifique la plantilla `helm-config.yaml`.
En la sección `appgw:`, agregue la clave `shared` y establézcala como `true`.

```yaml
appgw:
    subscriptionId: <subscriptionId>    # existing field
    resourceGroup: <resourceGroupName>  # existing field
    name: <applicationGatewayName>      # existing field
    shared: true                        # <<<<< Add this field to enable shared Application Gateway >>>>>
```

Aplique los cambios de Helm:
  1. Asegúrese de que el CRD de `AzureIngressProhibitedTarget` esté instalado con:
      ```bash
      kubectl apply -f https://raw.githubusercontent.com/Azure/application-gateway-kubernetes-ingress/ae695ef9bd05c8b708cedf6ff545595d0b7022dc/crds/AzureIngressProhibitedTarget.yaml
      ```
  2. Actualice Helm:
      ```bash
      helm upgrade \
          --recreate-pods \
          -f helm-config.yaml \
          ingress-azure application-gateway-kubernetes-ingress/ingress-azure
      ```

Como resultado, AKS tendrá una nueva instancia de `AzureIngressProhibitedTarget` denominada `prohibit-all-targets`:
```bash
kubectl get AzureIngressProhibitedTargets prohibit-all-targets -o yaml
```

El objeto `prohibit-all-targets`, como su nombre implica, prohíbe que AGIC cambie la configuración de *cualquier* host y ruta de acceso.
La instalación de Helm con `appgw.shared=true` implementará AGIC, pero no realizará ningún cambio en la instancia de Application Gateway.


### <a name="broaden-permissions"></a>Ampliar permisos
Dado que Helm con `appgw.shared=true` y el valor predeterminado `prohibit-all-targets` impide que AGIC aplique cualquier configuración.

Amplíe los permisos de AGIC con:
1. Cree una nueva `AzureIngressProhibitedTarget` con su configuración específica:
    ```bash
    cat <<EOF | kubectl apply -f -
    apiVersion: "appgw.ingress.k8s.io/v1"
    kind: AzureIngressProhibitedTarget
    metadata:
      name: your-custom-prohibitions
    spec:
      hostname: your.own-hostname.com
    EOF
    ```

2. Solo después de haber creado su propia prohibición personalizada, podrá eliminar la predeterminada, que es demasiado amplia:

    ```bash
    kubectl delete AzureIngressProhibitedTarget prohibit-all-targets
    ```

### <a name="enable-for-an-existing-agic-installation"></a>Habilitar para una instalación de AGIC existente
Supongamos que ya tenemos una instancia de AKS y Application Gateway en funcionamiento, además de AGIC configurado en nuestro clúster. Tenemos una entrada para `prod.contoso.com` y están atendiendo correctamente el tráfico de AKS. Queremos agregar `staging.contoso.com` a nuestra instancia de Application Gateway existente, pero es necesario hospedarlo en una [máquina virtual](https://azure.microsoft.com/services/virtual-machines/). Vamos a volver a usar la instancia de Application Gateway existente y a configurar manualmente un cliente de escucha y los grupos de back-end para `staging.contoso.com`. Sin embargo, el ajuste manual de la configuración de Application Gateway (a través del [portal](https://portal.azure.com), las [API de ARM](/rest/api/resources/) o [Terraform](https://www.terraform.io/)) puede entrar en conflicto con las suposiciones de AGIC sobre la propiedad total. Poco después de aplicar los cambios, AGIC los sobrescribirá o eliminará.

Podemos prohibir que AGIC realice cambios en un subconjunto de la configuración.

1. Cree un objeto de `AzureIngressProhibitedTarget`:
    ```bash
    cat <<EOF | kubectl apply -f -
    apiVersion: "appgw.ingress.k8s.io/v1"
    kind: AzureIngressProhibitedTarget
    metadata:
      name: manually-configured-staging-environment
    spec:
      hostname: staging.contoso.com
    EOF
    ```

2. Revise el objeto recién creado:
    ```bash
    kubectl get AzureIngressProhibitedTargets
    ```

3. Modifique la configuración de la instancia de Application Gateway a través del portal: agregue clientes de escucha, reglas de enrutamiento, back-ends, etc. El nuevo objeto que creamos (`manually-configured-staging-environment`) prohibirá que AGIC sobrescriba la configuración de Application Gateway relacionada con `staging.contoso.com`.
