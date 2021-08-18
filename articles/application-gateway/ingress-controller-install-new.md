---
title: Creación de un controlador de entrada con una nueva instancia de Application Gateway
description: Este artículo ofrece información sobre cómo implementar un controlador de entrada de Application Gateway con una nueva instancia de Application Gateway.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: how-to
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: 95a8f3090fd1599cbf30f783df1c501fa797cdff
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/17/2021
ms.locfileid: "112287954"
---
# <a name="how-to-install-an-application-gateway-ingress-controller-agic-using-a-new-application-gateway"></a>Cómo instalar un controlador de entrada de Application Gateway (AGIC) con una nueva instancia de Application Gateway

Las instrucciones que se muestran a continuación asumen que el controlador de entrada de Application Gateway (AGIC) se instalará en un entorno sin componentes ya existentes.

## <a name="required-command-line-tools"></a>Herramientas de línea de comandos necesarias

Se recomienda el uso de [Azure Cloud Shell](https://shell.azure.com/) para todas las operaciones siguientes de línea de comandos. Inicie Shell desde shell.azure.com o haga clic en el vínculo:

[![Insertar inicio](https://shell.azure.com/images/launchcloudshell.png "Inicio de Azure Cloud Shell")](https://shell.azure.com)

Como alternativa, inicie Cloud Shell desde Azure Portal mediante el siguiente icono:

![Inicio en Azure Portal](./media/application-gateway-ingress-controller-install-new/portal-launch-icon.png)

Su instancia de [Azure Cloud Shell](https://shell.azure.com/) ya tiene todas las herramientas necesarias. Si decide usar otro entorno, asegúrese de que estén instaladas las herramientas siguientes de línea de comandos:

* `az` - CLI de Azure: [instrucciones de instalación](/cli/azure/install-azure-cli)
* `kubectl` - Herramienta de línea de comandos de Kubernetes: [instrucciones de instalación](https://kubernetes.io/docs/tasks/tools/install-kubectl)
* `helm` - Administrador de paquetes de Kubernetes: [instrucciones de instalación](https://github.com/helm/helm/releases/latest)
* `jq` - Procesador JSON de línea de comandos: [instrucciones de instalación](https://stedolan.github.io/jq/download/)


## <a name="create-an-identity"></a>Creación de una identidad

Siga los pasos que se indican a continuación para crear un [objeto de entidad de servicio](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) de Azure Active Directory (AAD). Registre los valores de `appId`, `password` y `objectId`: se usarán en los pasos siguientes.

1. Cree una entidad de servicio de AD ([lea más sobre RBAC](../role-based-access-control/overview.md)):
    ```azurecli
    az ad sp create-for-rbac --skip-assignment -o json > auth.json
    appId=$(jq -r ".appId" auth.json)
    password=$(jq -r ".password" auth.json)
    ```
    Los valores de `appId` y `password` del resultado de JSON se usarán en los pasos siguientes


1. Use el `appId` del resultado del comando anterior para obtener el `objectId` de la nueva entidad de servicio:
    ```azurecli
    objectId=$(az ad sp show --id $appId --query "objectId" -o tsv)
    ```
    El resultado de este comando es `objectId`, que se usará en la plantilla de Azure Resource Manager siguiente

1. Cree el archivo de parámetros que se usará en la implementación de la plantilla de Azure Resource Manager más adelante.
    ```bash
    cat <<EOF > parameters.json
    {
      "aksServicePrincipalAppId": { "value": "$appId" },
      "aksServicePrincipalClientSecret": { "value": "$password" },
      "aksServicePrincipalObjectId": { "value": "$objectId" },
      "aksEnableRBAC": { "value": false }
    }
    EOF
    ```
    Para implementar un clúster con **RBAC de Kubernetes RBAC** habilitado, establezca el campo `aksEnableRBAC` en `true`.

## <a name="deploy-components"></a>Implementación de componentes
En este paso se agregarán los siguientes componentes a la suscripción:

- [Azure Kubernetes Service](../aks/intro-kubernetes.md)
- [Application Gateway](./overview.md) v2
- [Red virtual](../virtual-network/virtual-networks-overview.md) con 2 [subredes](../virtual-network/virtual-networks-overview.md)
- [Dirección IP pública](../virtual-network/virtual-network-public-ip-address.md)
- [Identidad administrada](../active-directory/managed-identities-azure-resources/overview.md), que [AAD Pod Identity](https://github.com/Azure/aad-pod-identity/blob/master/README.md) usará

1. Descargue la plantilla de Azure Resource Manager y modifíquela según sea necesario.
    ```bash
    wget https://raw.githubusercontent.com/Azure/application-gateway-kubernetes-ingress/master/deploy/azuredeploy.json -O template.json
    ```

1. Implemente la plantilla de Azure Resource Manager mediante `az cli`. Esta operación puede tardar hasta cinco minutos.
    ```azurecli
    resourceGroupName="MyResourceGroup"
    location="westus2"
    deploymentName="ingress-appgw"

    # create a resource group
    az group create -n $resourceGroupName -l $location

    # modify the template as needed
    az deployment group create \
            -g $resourceGroupName \
            -n $deploymentName \
            --template-file template.json \
            --parameters parameters.json
    ```

1. Una vez finalizada la implementación, descargue el resultado de la implementación en un archivo llamado `deployment-outputs.json`.
    ```azurecli
    az deployment group show -g $resourceGroupName -n $deploymentName --query "properties.outputs" -o json > deployment-outputs.json
    ```

## <a name="set-up-application-gateway-ingress-controller"></a>Configuración del controlador de entrada de Application Gateway

Con las instrucciones de la sección anterior, hemos creado y configurado un nuevo clúster de AKS y una instancia de Application Gateway. Ahora estamos listos para implementar una aplicación de ejemplo y un controlador de entrada en nuestra nueva infraestructura de Kubernetes.

### <a name="setup-kubernetes-credentials"></a>Configuración de las credenciales de Kubernetes
En los pasos siguientes, es necesario configurar el comando [kubectl](https://kubectl.docs.kubernetes.io/), que usaremos para conectarnos al nuevo clúster de Kubernetes. [Cloud Shell](https://shell.azure.com/) ya tiene `kubectl` instalado. Usaremos la CLI de `az` para obtener las credenciales de Kubernetes.

Obtenga las credenciales de la instancia de AKS recién implementada ([lea más](../aks/kubernetes-walkthrough.md#connect-to-the-cluster)):
```azurecli
# use the deployment-outputs.json created after deployment to get the cluster name and resource group name
aksClusterName=$(jq -r ".aksClusterName.value" deployment-outputs.json)
resourceGroupName=$(jq -r ".resourceGroupName.value" deployment-outputs.json)

az aks get-credentials --resource-group $resourceGroupName --name $aksClusterName
```

### <a name="install-aad-pod-identity"></a>Instalación de AAD Pod Identity
  Azure Active Directory Pod Identity proporciona acceso basado en token a [Azure Resource Manager (ARM)](../azure-resource-manager/management/overview.md).

  [AAD Pod Identity](https://github.com/Azure/aad-pod-identity) agregará los siguientes componentes al clúster de Kubernetes:
   * [CRD](https://kubernetes.io/docs/tasks/access-kubernetes-api/custom-resources/custom-resource-definitions/) de Kubernetes: `AzureIdentity`, `AzureAssignedIdentity`, `AzureIdentityBinding`
   * Componente [Controlador de identidades administradas (MIC)](https://github.com/Azure/aad-pod-identity#managed-identity-controllermic)
   * Componente [Identidad administrada del nodo (NMI)](https://github.com/Azure/aad-pod-identity#node-managed-identitynmi)


Para instalar AAD Pod Identity en el clúster:

   - Clúster de AKS *habilitado para RBAC de Kubernetes*

     ```bash
     kubectl create -f https://raw.githubusercontent.com/Azure/aad-pod-identity/master/deploy/infra/deployment-rbac.yaml
     ```

   - Clúster de AKS *deshabilitado para RBAC de Kubernetes*

     ```bash
     kubectl create -f https://raw.githubusercontent.com/Azure/aad-pod-identity/master/deploy/infra/deployment.yaml
     ```

### <a name="install-helm"></a>Instalación de Helm
[Helm](../aks/kubernetes-helm.md) es un administrador de paquetes para Kubernetes. Lo usaremos para instalar el paquete de `application-gateway-kubernetes-ingress`:

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

### <a name="install-ingress-controller-helm-chart"></a>Instalación del gráfico de Helm del controlador de entrada

1. Use el archivo `deployment-outputs.json` creado anteriormente y cree las siguientes variables.
    ```bash
    applicationGatewayName=$(jq -r ".applicationGatewayName.value" deployment-outputs.json)
    resourceGroupName=$(jq -r ".resourceGroupName.value" deployment-outputs.json)
    subscriptionId=$(jq -r ".subscriptionId.value" deployment-outputs.json)
    identityClientId=$(jq -r ".identityClientId.value" deployment-outputs.json)
    identityResourceId=$(jq -r ".identityResourceId.value" deployment-outputs.json)
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
    #    secretJSON: <<Generate this value with: "az ad sp create-for-rbac --subscription <subscription-uuid> --sdk-auth | base64 -w0" >>
    
    ################################################################################
    # Specify if the cluster is Kubernetes RBAC enabled or not
    rbac:
        enabled: false # true/false
    
    # Specify aks cluster related information. THIS IS BEING DEPRECATED.
    aksClusterConfiguration:
        apiServerAddress: <aks-api-server-address>
    ```

1. Edite el archivo helm-config.yaml recién descargado y rellene las secciones `appgw` y `armAuth`.
    ```bash
    sed -i "s|<subscriptionId>|${subscriptionId}|g" helm-config.yaml
    sed -i "s|<resourceGroupName>|${resourceGroupName}|g" helm-config.yaml
    sed -i "s|<applicationGatewayName>|${applicationGatewayName}|g" helm-config.yaml
    sed -i "s|<identityResourceId>|${identityResourceId}|g" helm-config.yaml
    sed -i "s|<identityClientId>|${identityClientId}|g" helm-config.yaml

    # You can further modify the helm config to enable/disable features
    nano helm-config.yaml
    ```

   Valores:
     - `verbosityLevel`: establece el nivel de detalle de la infraestructura de registro de AGIC. Consulte [Niveles de registro](https://github.com/Azure/application-gateway-kubernetes-ingress/blob/463a87213bbc3106af6fce0f4023477216d2ad78/docs/troubleshooting.yml#logging-levels) para los valores posibles.
     - `appgw.subscriptionId`: El identificador de suscripción de Azure en el que reside la instancia de Application Gateway. Ejemplo: `a123b234-a3b4-557d-b2df-a0bc12de1234`
     - `appgw.resourceGroup`: El nombre del grupo de recursos de Azure donde se creó la instancia de Application Gateway. Ejemplo: `app-gw-resource-group`
     - `appgw.name`: nombre de la instancia de Application Gateway. Ejemplo: `applicationgatewayd0f0`
     - `appgw.shared`: se debe establecer el valor predeterminado de esta marca booleana en `false`. Establézcalo como `true` si necesita una [instancia de Application Gateway compartida](https://github.com/Azure/application-gateway-kubernetes-ingress/blob/072626cb4e37f7b7a1b0c4578c38d1eadc3e8701/docs/setup/install-existing.md#multi-cluster--shared-app-gateway).
     - `kubernetes.watchNamespace`: especifica el espacio de nombres que debe ver AGIC. Este puede ser un valor de cadena único o una lista de espacios de nombres separada por comas.
    - `armAuth.type`: podría ser `aadPodIdentity` o `servicePrincipal`
    - `armAuth.identityResourceID`: Identificador de recurso de la identidad administrada de Azure
    - `armAuth.identityClientId`: identificador de cliente de la identidad. Para obtener más información sobre las identidades, siga leyendo
    - `armAuth.secretJSON`: Solo es necesario cuando se elige el tipo de secreto de entidad de servicio (cuando se establece `armAuth.type` en `servicePrincipal`) 


   > [!NOTE]
   > `identityResourceID` y `identityClientID` son valores que se crearon durante los pasos de [Implementación de componentes](ingress-controller-install-new.md#deploy-components) y que se pueden obtener de nuevo mediante el siguiente comando:
   > ```azurecli
   > az identity show -g <resource-group> -n <identity-name>
   > ```
   > `<resource-group>` en el comando anterior se encuentra el grupo de recursos de la instancia de Application Gateway. `<identity-name>` es el nombre de la identidad creada. Todas las identidades de una suscripción determinada se pueden enumerar mediante: `az identity list`


1. Instale el paquete del controlador de entrada de Application Gateway:

    ```bash
    helm install -f helm-config.yaml application-gateway-kubernetes-ingress/ingress-azure
    ```

## <a name="install-a-sample-app"></a>Instalación de una aplicación de ejemplo
Ahora que hemos instalado Application Gateway, AKS y AGIC, podemos instalar una aplicación de ejemplo mediante [Azure Cloud Shell](https://shell.azure.com/):

```yaml
cat <<EOF | kubectl apply -f -
apiVersion: v1
kind: Pod
metadata:
  name: aspnetapp
  labels:
    app: aspnetapp
spec:
  containers:
  - image: "mcr.microsoft.com/dotnet/core/samples:aspnetapp"
    name: aspnetapp-image
    ports:
    - containerPort: 80
      protocol: TCP

---

apiVersion: v1
kind: Service
metadata:
  name: aspnetapp
spec:
  selector:
    app: aspnetapp
  ports:
  - protocol: TCP
    port: 80
    targetPort: 80

---

apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: aspnetapp
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
spec:
  rules:
  - http:
      paths:
      - path: /
        backend:
          serviceName: aspnetapp
          servicePort: 80
EOF
```

O también:

* Descargue el archivo YAML anterior:

```bash
curl https://raw.githubusercontent.com/Azure/application-gateway-kubernetes-ingress/master/docs/examples/aspnetapp.yaml -o aspnetapp.yaml
```

* Aplique el archivo YAML:

```bash
kubectl apply -f aspnetapp.yaml
```


## <a name="other-examples"></a>Otros ejemplos
Esta [guía paso a paso](ingress-controller-expose-service-over-http-https.md) contiene más ejemplos sobre cómo exponer un servicio de AKS a través de HTTP o HTTPS a Internet con Application Gateway.
