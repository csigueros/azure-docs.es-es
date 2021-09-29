---
title: Configuración de Azure Arc para App Service, Functions y Logic Apps
description: En el caso Azure Arc clústeres de Kubernetes habilitados, aprenda a habilitar aplicaciones App Service, aplicaciones de funciones y aplicaciones lógicas.
ms.topic: article
ms.date: 08/17/2021
ms.openlocfilehash: 7fbdae7b5e8b3bda94c1f1ddbb2a70720596d1fd
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128639040"
---
# <a name="set-up-an-azure-arc-enabled-kubernetes-cluster-to-run-app-service-functions-and-logic-apps-preview"></a>Configuración de un clúster de Kubernetes habilitado para Azure Arc para ejecutar App Service, Functions y Logic Apps (versión preliminar)

Si tiene un [clúster de Kubernetes habilitado para Azure Arc](../azure-arc/kubernetes/overview.md), puede usarlo para crear una [ubicación personalizada habilitada para App Service](overview-arc-integration.md) e implementar aplicaciones web, aplicaciones de funciones y aplicaciones lógicas en él.

Kubernetes habilitado para Azure Arc le permite hacer que el clúster de Kubernetes local o en la nube sea visible para App Service, Functions y Logic Apps en Azure. Puede crear una aplicación e implementarla como en otra región de Azure.

## <a name="prerequisites"></a>Requisitos previos

Si no tiene una cuenta de Azure, [regístrese hoy mismo](https://azure.microsoft.com/free/?utm_source=campaign&utm_campaign=vscode-tutorial-app-service-extension&mktingSource=vscode-tutorial-app-service-extension) para obtener una cuenta gratuita.

<!-- ## Prerequisites

- Create a Kubernetes cluster in a supported Kubernetes distribution and connect it to Azure Arc in a supported region. See [Public preview limitations](overview-arc-integration.md#public-preview-limitations).
- [Install Azure CLI](/cli/azure/install-azure-cli), or use the [Azure Cloud Shell](../cloud-shell/overview.md).
- [Install kubectl](https://kubernetes.io/docs/tasks/tools/). It's also preinstalled in the Azure Cloud Shell.

## Obtain cluster information

Set the following environment variables based on your Kubernetes cluster deployment:

```bash
staticIp="<public-ip-address-of-the-kubernetes-cluster>"
aksClusterGroupName="<name-resource-group-with-aks-cluster>"
groupName="<name-of-resource-group-with-the-arc-connected-cluster>"
clusterName="<name-of-arc-connected-cluster>"
geomasterLocation="TODO: Why so many different locations for different resources? Shouldn't we just say create everything in the connected cluster's resource group and location?"
``` -->

## <a name="add-azure-cli-extensions"></a>Adición de extensiones de la CLI de Azure

Inicie el entorno de Bash en [Azure Cloud Shell](../cloud-shell/quickstart.md).

[![Iniciar Cloud Shell en una nueva ventana](../../includes/media/cloud-shell-try-it/hdi-launch-cloud-shell.png)](https://shell.azure.com)

Dado que estos comandos de la CLI aún no forman parte del conjunto de la CLI principal, agréguelos con los siguientes comandos.

```azurecli-interactive
az extension add --upgrade --yes --name connectedk8s
az extension add --upgrade --yes --name k8s-extension
az extension add --upgrade --yes --name customlocation
az provider register --namespace Microsoft.ExtendedLocation --wait
az provider register --namespace Microsoft.Web --wait
az provider register --namespace Microsoft.KubernetesConfiguration --wait
az extension remove --name appservice-kube
az extension add --yes --source "https://aka.ms/appsvc/appservice_kube-latest-py2.py3-none-any.whl"
```

## <a name="create-a-connected-cluster"></a>Creación de un clúster conectado

> [!NOTE]
> En este tutorial se usa [Azure Kubernetes Service (AKS)](../aks/index.yml) para proporcionar instrucciones concretas para configurar un entorno desde cero. Sin embargo, para una carga de trabajo de producción, es probable que no quiera habilitar Azure Arc en un clúster de AKS, ya que ya está administrado en Azure. Los pasos siguientes le ayudarán a empezar a comprender el servicio, pero en el caso de las implementaciones de producción, deben considerarse ilustrativos, no prescriptivos. Vea [Inicio rápido: conexión de un clúster de Kubernetes existente a Azure Arc](../azure-arc/kubernetes/quickstart-connect-cluster.md) para obtener instrucciones generales sobre cómo crear un clúster de Kubernetes habilitado para Azure Arc.

1. Cree un clúster en Azure Kubernetes Service con una dirección IP pública. Sustituya `<group-name>` por el nombre del grupo de recursos que desee.

    # <a name="bash"></a>[bash](#tab/bash)

    ```azurecli-interactive
    aksClusterGroupName="<group-name>" # Name of resource group for the AKS cluster
    aksName="${aksClusterGroupName}-aks" # Name of the AKS cluster
    resourceLocation="eastus" # "eastus" or "westeurope"

    az group create -g $aksClusterGroupName -l $resourceLocation
    az aks create --resource-group $aksClusterGroupName --name $aksName --enable-aad --generate-ssh-keys
    infra_rg=$(az aks show --resource-group $aksClusterGroupName --name $aksName --output tsv --query nodeResourceGroup)
    az network public-ip create --resource-group $infra_rg --name MyPublicIP --sku STANDARD
    staticIp=$(az network public-ip show --resource-group $infra_rg --name MyPublicIP --output tsv --query ipAddress)
    ```

    # <a name="powershell"></a>[PowerShell](#tab/powershell)

    ```powershell
    $aksClusterGroupName="<group-name>" # Name of resource group for the AKS cluster
    $aksName="${aksClusterGroupName}-aks" # Name of the AKS cluster
    $resourceLocation="eastus" # "eastus" or "westeurope"

    az group create -g $aksClusterGroupName -l $resourceLocation
    az aks create --resource-group $aksClusterGroupName --name $aksName --enable-aad --generate-ssh-keys
    $infra_rg=$(az aks show --resource-group $aksClusterGroupName --name $aksName --output tsv --query nodeResourceGroup)
    az network public-ip create --resource-group $infra_rg --name MyPublicIP --sku STANDARD
    $staticIp=$(az network public-ip show --resource-group $infra_rg --name MyPublicIP --output tsv --query ipAddress)
    ```

    ---
    
2. Obtenga el archivo [kubeconfig](https://kubernetes.io/docs/concepts/configuration/organize-cluster-access-kubeconfig/) y pruebe la conexión al clúster. De manera predeterminada, el archivo kubeconfig se guarda en `~/.kube/config`.

    ```azurecli-interactive
    az aks get-credentials --resource-group $aksClusterGroupName --name $aksName --admin
    
    kubectl get ns
    ```
    
3. Cree un grupo de recursos que contenga los recursos de Azure Arc. Sustituya `<group-name>` por el nombre del grupo de recursos que desee.

    # <a name="bash"></a>[bash](#tab/bash)

    ```azurecli-interactive
    groupName="<group-name>" # Name of resource group for the connected cluster

    az group create -g $groupName -l $resourceLocation
    ```

    # <a name="powershell"></a>[PowerShell](#tab/powershell)

    ```powershell
    $groupName="<group-name>" # Name of resource group for the connected cluster

    az group create -g $groupName -l $resourceLocation
    ```

    ---
    
4. Conecte el clúster que creó a Azure Arc.

    # <a name="bash"></a>[bash](#tab/bash)

    ```azurecli-interactive
    clusterName="${groupName}-cluster" # Name of the connected cluster resource

    az connectedk8s connect --resource-group $groupName --name $clusterName
    ```
    
    # <a name="powershell"></a>[PowerShell](#tab/powershell)


    ```powershell
    $clusterName="${groupName}-cluster" # Name of the connected cluster resource

    az connectedk8s connect --resource-group $groupName --name $clusterName
    ```

    ---
    
5. Valide la conexión con el comando siguiente. Debe mostrar la propiedad `provisioningState` como `Succeeded`. Si no es así, vuelva a ejecutar el comando después de un minuto.

    ```azurecli-interactive
    az connectedk8s show --resource-group $groupName --name $clusterName
    ```
    
## <a name="create-a-log-analytics-workspace"></a>Creación de un área de trabajo de Log Analytics

Aunque no se necesita un [área de trabajo de Log Analytics](../azure-monitor/logs/quick-create-workspace.md) para ejecutar App Service en Azure Arc, es cómo los desarrolladores pueden obtener registros de aplicaciones para sus aplicaciones que se ejecutan en el clúster de Kubernetes habilitado para Azure Arc. 

1. Para simplificar, cree el área de trabajo ahora.

    # <a name="bash"></a>[bash](#tab/bash)

    ```azurecli-interactive
    workspaceName="$groupName-workspace" # Name of the Log Analytics workspace
    
    az monitor log-analytics workspace create \
        --resource-group $groupName \
        --workspace-name $workspaceName
    ```

    # <a name="powershell"></a>[PowerShell](#tab/powershell)

    ```powershell
    $workspaceName="$groupName-workspace"

    az monitor log-analytics workspace create `
        --resource-group $groupName `
        --workspace-name $workspaceName
    ```

    ---
    
2. Ejecute los siguientes comandos para obtener el identificador del área de trabajo codificada y la clave compartida para un área de trabajo de Log Analytics existente. Los necesitará en el paso siguiente.

    # <a name="bash"></a>[bash](#tab/bash)

    ```azurecli-interactive
    logAnalyticsWorkspaceId=$(az monitor log-analytics workspace show \
        --resource-group $groupName \
        --workspace-name $workspaceName \
        --query customerId \
        --output tsv)
    logAnalyticsWorkspaceIdEnc=$(printf %s $logAnalyticsWorkspaceId | base64 -w0) # Needed for the next step
    logAnalyticsKey=$(az monitor log-analytics workspace get-shared-keys \
        --resource-group $groupName \
        --workspace-name $workspaceName \
        --query primarySharedKey \
        --output tsv)
    logAnalyticsKeyEnc=$(printf %s $logAnalyticsKey | base64 -w0) # Needed for the next step
    ```

    # <a name="powershell"></a>[PowerShell](#tab/powershell)

    ```powershell
    $logAnalyticsWorkspaceId=$(az monitor log-analytics workspace show `
        --resource-group $groupName `
        --workspace-name $workspaceName `
        --query customerId `
        --output tsv)
    $logAnalyticsWorkspaceIdEnc=[Convert]::ToBase64String([System.Text.Encoding]::UTF8.GetBytes($logAnalyticsWorkspaceId))# Needed for the next step
    $logAnalyticsKey=$(az monitor log-analytics workspace get-shared-keys `
        --resource-group $groupName `
        --workspace-name $workspaceName `
        --query primarySharedKey `
        --output tsv)
    $logAnalyticsKeyEnc=[Convert]::ToBase64String([System.Text.Encoding]::UTF8.GetBytes($logAnalyticsKey))
    ```
    
    ---

## <a name="install-the-app-service-extension"></a>Instalación de la extensión de App Service

1. Establezca las siguientes variables de entorno para el nombre deseado de la [extensión de App Service](overview-arc-integration.md), el espacio de nombres del clúster en el que se deben aprovisionar los recursos y el nombre del entorno de Kubernetes de App Service. Elija un nombre único para `<kube-environment-name>`, ya que formará parte del nombre de dominio de la aplicación creada en el entorno de Kubernetes de App Service.

    # <a name="bash"></a>[bash](#tab/bash)

    ```bash
    extensionName="appservice-ext" # Name of the App Service extension
    namespace="appservice-ns" # Namespace in your cluster to install the extension and provision resources
    kubeEnvironmentName="<kube-environment-name>" # Name of the App Service Kubernetes environment resource
    ```

    # <a name="powershell"></a>[PowerShell](#tab/powershell)

    ```powershell
    $extensionName="appservice-ext" # Name of the App Service extension
    $namespace="appservice-ns" # Namespace in your cluster to install the extension and provision resources
    $kubeEnvironmentName="<kube-environment-name>" # Name of the App Service Kubernetes environment resource
    ```

    ---
    
2. Instale la extensión de App Service en el clúster conectado a Azure Arc, con Log Analytics habilitado. De nuevo, aunque Log Analytics no es necesario, no se puede agregar a la extensión más adelante, por lo que es más fácil hacerlo ahora.

    # <a name="bash"></a>[bash](#tab/bash)

    ```azurecli-interactive
    az k8s-extension create \
        --resource-group $groupName \
        --name $extensionName \
        --cluster-type connectedClusters \
        --cluster-name $clusterName \
        --extension-type 'Microsoft.Web.Appservice' \
        --release-train stable \
        --auto-upgrade-minor-version true \
        --scope cluster \
        --release-namespace $namespace \
        --configuration-settings "Microsoft.CustomLocation.ServiceAccount=default" \
        --configuration-settings "appsNamespace=${namespace}" \
        --configuration-settings "clusterName=${kubeEnvironmentName}" \
        --configuration-settings "loadBalancerIp=${staticIp}" \
        --configuration-settings "keda.enabled=true" \
        --configuration-settings "buildService.storageClassName=default" \
        --configuration-settings "buildService.storageAccessMode=ReadWriteOnce" \
        --configuration-settings "customConfigMap=${namespace}/kube-environment-config" \
        --configuration-settings "envoy.annotations.service.beta.kubernetes.io/azure-load-balancer-resource-group=${aksClusterGroupName}" \
        --configuration-settings "logProcessor.appLogs.destination=log-analytics" \
        --configuration-protected-settings "logProcessor.appLogs.logAnalyticsConfig.customerId=${logAnalyticsWorkspaceIdEnc}" \
        --configuration-protected-settings "logProcessor.appLogs.logAnalyticsConfig.sharedKey=${logAnalyticsKeyEnc}"
    ```

    # <a name="powershell"></a>[PowerShell](#tab/powershell)

    ```powershell
    az k8s-extension create `
        --resource-group $groupName `
        --name $extensionName `
        --cluster-type connectedClusters `
        --cluster-name $clusterName `
        --extension-type 'Microsoft.Web.Appservice' `
        --release-train stable `
        --auto-upgrade-minor-version true `
        --scope cluster `
        --release-namespace $namespace `
        --configuration-settings "Microsoft.CustomLocation.ServiceAccount=default" `
        --configuration-settings "appsNamespace=${namespace}" `
        --configuration-settings "clusterName=${kubeEnvironmentName}" `
        --configuration-settings "loadBalancerIp=${staticIp}" `
        --configuration-settings "keda.enabled=true" `
        --configuration-settings "buildService.storageClassName=default" `
        --configuration-settings "buildService.storageAccessMode=ReadWriteOnce" `
        --configuration-settings "customConfigMap=${namespace}/kube-environment-config" `
        --configuration-settings "envoy.annotations.service.beta.kubernetes.io/azure-load-balancer-resource-group=${aksClusterGroupName}" `
        --configuration-settings "logProcessor.appLogs.destination=log-analytics" `
        --configuration-protected-settings "logProcessor.appLogs.logAnalyticsConfig.customerId=${logAnalyticsWorkspaceIdEnc}" `
        --configuration-protected-settings "logProcessor.appLogs.logAnalyticsConfig.sharedKey=${logAnalyticsKeyEnc}"
    ```

    ---

    > [!NOTE]
    > Para instalar la extensión sin la integración de Log Analytics, quite los tres últimos parámetros `--configuration-settings` del comando.
    >

    En la siguiente tabla se describen los diferentes parámetros `--configuration-settings` al ejecutar el comando:
    
    | Parámetro | Descripción |
    | - | - |
    | `Microsoft.CustomLocation.ServiceAccount` | Cuenta de servicio que se debe crear para la ubicación personalizada que se creará. Se recomienda que se establezca en el valor `default`. |
    | `appsNamespace` | Espacio de nombres para aprovisionar las definiciones de la aplicación y los pods. Debe coincidir con el del espacio de nombres de la versión de la extensión. |
    | `clusterName` | Nombre del entorno de Kubernetes de App Service que se creará con esta extensión. |
    | `loadBalancerIp` | Dirección IP pública del clúster de Kubernetes. Las aplicaciones de App Service reciben tráfico en esta dirección IP. También informa de la asignación de DNS predeterminada. |
    | `keda.enabled` | Si [KEDA](https://keda.sh/) debe instalarse en el clúster de Kubernetes. Acepta `true` o `false`. |
    | `buildService.storageClassName` | [Nombre de la clase de almacenamiento](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#class) para que el servicio de compilación almacene artefactos de compilación. Un valor como `default` especifica una clase denominada `default`, y no [ninguna clase marcada como predeterminada](https://kubernetes.io/docs/tasks/administer-cluster/change-default-storage-class/). |
    | `buildService.storageAccessMode` | [Modo de acceso](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#access-modes) que se usará con la clase de almacenamiento con nombre anterior. Acepta `ReadWriteOnce` o `ReadWriteMany`. |
    | `customConfigMap` | Nombre del mapa de configuración que establecerá el entorno de Kubernetes de App Service. Actualmente, debe ser `<namespace>/kube-environment-config`, reemplazando `<namespace>` por el valor de `appsNamespace` anterior. |
    | `envoy.annotations.service.beta.kubernetes.io/azure-load-balancer-resource-group` | Nombre del grupo de recursos en el que reside el clúster de Azure Kubernetes Service. Válido y necesario solo cuando el clúster subyacente es Azure Kubernetes Service.  |
    | `logProcessor.appLogs.destination` | Opcional. Acepta `log-analytics`. |
    | `logProcessor.appLogs.logAnalyticsConfig.customerId` | Necesario solo cuando `logProcessor.appLogs.destination` se establece en `log-analytics`. Identificador del área de trabajo de Log Analytics codificada en base64. Este parámetro debe configurarse como una configuración protegida. |
    | `logProcessor.appLogs.logAnalyticsConfig.sharedKey` | Necesario solo cuando `logProcessor.appLogs.destination` se establece en `log-analytics`. Clave compartida del área de trabajo de Log Analytics codificada en base64. Este parámetro debe configurarse como una configuración protegida. |
    | | |
        
3. Guarde la propiedad `id` de la extensión de App Service para más adelante.

    # <a name="bash"></a>[bash](#tab/bash)

    ```azurecli-interactive
    extensionId=$(az k8s-extension show \
        --cluster-type connectedClusters \
        --cluster-name $clusterName \
        --resource-group $groupName \
        --name $extensionName \
        --query id \
        --output tsv)
    ```

    # <a name="powershell"></a>[PowerShell](#tab/powershell)

    ```powershell
    $extensionId=$(az k8s-extension show `
        --cluster-type connectedClusters `
        --cluster-name $clusterName `
        --resource-group $groupName `
        --name $extensionName `
        --query id `
        --output tsv)
    ```

    ---

4. Espere a que la extensión se instale por completo antes de continuar. Ejecute el siguiente comando para hacer que la sesión de terminal espere hasta que se complete:

    ```azurecli-interactive
    az resource wait --ids $extensionId --custom "properties.installState!='Pending'" --api-version "2020-07-01-preview"
    ```

Puede usar `kubectl` para ver los pods que se han creado en el clúster de Kubernetes:

```bash
kubectl get pods -n $namespace
```

Puede obtener más información sobre estos pods y su rol en el sistema de [Pods creados por la extensión de App Service](overview-arc-integration.md#pods-created-by-the-app-service-extension).

## <a name="create-a-custom-location"></a>Creación de una ubicación personalizada

La [ubicación personalizada](../azure-arc/kubernetes/custom-locations.md) en Azure se usa para asignar el entorno de Kubernetes de App Service.

<!-- https://github.com/MicrosoftDocs/azure-docs-pr/pull/156618 -->

1. Establezca las siguientes variables de entorno para el nombre deseado de la ubicación personalizada y para el identificador del clúster conectado de Azure Arc.

    # <a name="bash"></a>[bash](#tab/bash)

    ```bash
    customLocationName="my-custom-location" # Name of the custom location
    
    connectedClusterId=$(az connectedk8s show --resource-group $groupName --name $clusterName --query id --output tsv)
    ```

    # <a name="powershell"></a>[PowerShell](#tab/powershell)

    ```powershell
    $customLocationName="my-custom-location" # Name of the custom location
    
    $connectedClusterId=$(az connectedk8s show --resource-group $groupName --name $clusterName --query id --output tsv)
    ```

    ---
    
2. Cree la ubicación personalizada:

    # <a name="bash"></a>[bash](#tab/bash)

    ```azurecli-interactive
    az customlocation create \
        --resource-group $groupName \
        --name $customLocationName \
        --host-resource-id $connectedClusterId \
        --namespace $namespace \
        --cluster-extension-ids $extensionId
    ```

    # <a name="powershell"></a>[PowerShell](#tab/powershell)

    ```azurecli-interactive
    az customlocation create `
        --resource-group $groupName `
        --name $customLocationName `
        --host-resource-id $connectedClusterId `
        --namespace $namespace `
        --cluster-extension-ids $extensionId
    ```

    ---
    
    <!-- --kubeconfig ~/.kube/config # needed for non-Azure -->

3. Valide que la ubicación personalizada se ha creado correctamente con el siguiente comando. La salida debe mostrar la propiedad `provisioningState` como `Succeeded`. Si no es así, vuelva a ejecutarla después de un minuto.

    ```azurecli-interactive
    az customlocation show --resource-group $groupName --name $customLocationName
    ```
    
4. Guarde el identificador de la ubicación personalizada para el paso siguiente.

    # <a name="bash"></a>[bash](#tab/bash)

    ```azurecli-interactive
    customLocationId=$(az customlocation show \
        --resource-group $groupName \
        --name $customLocationName \
        --query id \
        --output tsv)
    ```

    # <a name="powershell"></a>[PowerShell](#tab/powershell)

    ```azurecli-interactive
    $customLocationId=$(az customlocation show `
        --resource-group $groupName `
        --name $customLocationName `
        --query id `
        --output tsv)
    ```

    ---
    
## <a name="create-the-app-service-kubernetes-environment"></a>Creación del entorno de Kubernetes de App Service

Para poder empezar a crear aplicaciones en la ubicación personalizada, necesita un [entorno de Kubernetes de App Service](overview-arc-integration.md#app-service-kubernetes-environment).

1. Creación del entorno de Kubernetes de App Service:
    
    # <a name="bash"></a>[bash](#tab/bash)

    ```azurecli-interactive
    az appservice kube create \
        --resource-group $groupName \
        --name $kubeEnvironmentName \
        --custom-location $customLocationId \
        --static-ip $staticIp
    ```

    # <a name="powershell"></a>[PowerShell](#tab/powershell)

    ```azurecli-interactive
    az appservice kube create `
        --resource-group $groupName `
        --name $kubeEnvironmentName `
        --custom-location $customLocationId `
        --static-ip $staticIp
    ```

    ---
    
2. Valide que el entorno de Kubernetes de App Service se ha creado correctamente con el siguiente comando. La salida debe mostrar la propiedad `provisioningState` como `Succeeded`. Si no es así, vuelva a ejecutarla después de un minuto.

    ```azurecli-interactive
    az appservice kube show --resource-group $groupName --name $kubeEnvironmentName
    ```
    

## <a name="next-steps"></a>Pasos siguientes

- [Inicio rápido: creación de una aplicación web en Azure Arc](quickstart-arc.md)
- [Creación de la primera función en Azure Arc](../azure-functions/create-first-function-arc-cli.md)
- [Creación de la primera aplicación lógica en Azure Arc](../logic-apps/azure-arc-enabled-logic-apps-create-deploy-workflows.md)
