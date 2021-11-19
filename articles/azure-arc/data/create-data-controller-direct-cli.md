---
title: Creación del controlador de datos de Azure Arc | Modo de conexión directa
description: Explica cómo crear el controlador de datos en modo de conexión directa.
author: dnethi
ms.author: dinethi
ms.reviewer: mikeray
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.date: 11/12/2021
ms.topic: overview
ms.openlocfilehash: 53cec5c48c65352c50fe115b0dc9160e72229b9a
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/15/2021
ms.locfileid: "132486095"
---
#  <a name="create-azure-arc-data-controller-in-direct-connectivity-mode-using-cli"></a>Creación del controlador de datos de Azure Arc en modo de conexión directa mediante la CLI

En este artículo se describe cómo crear el controlador de datos de Azure Arc en modo de conectividad **directa** mediante la CLI, durante la versión preliminar actual de esta característica. 


## <a name="complete-prerequisites"></a>Realización de los requisitos previos

Antes de empezar, compruebe que ha completado los requisitos previos que se describen en [Implementación del controlador de datos: modo de conexión directa (requisitos previos)](create-data-controller-direct-prerequisites.md).

La creación del controlador de datos de Azure Arc en modo de conectividad **directa** involucra los pasos siguientes:

1. Crear una extensión de servicios de datos habilitados para Azure Arc. 
1. Crear una ubicación personalizada.
1. Cree el controlador de datos.

> [!NOTE]
> Actualmente, este paso solo se puede realizar desde el portal. Para obtener información detallada, consulte las [notas de la versión](release-notes.md). 

## <a name="step-1-create-an-azure-arc-enabled-data-services-extension"></a>Paso 1: Creación de una extensión de servicios de datos habilitados para Azure Arc

Use la extensión k8s de la CLI para crear una extensión de servicios de datos.

### <a name="set-environment-variables"></a>Establecimiento de variables de entorno

Establezca las siguientes variables de entorno que se usarán en el paso siguiente.

#### <a name="linux"></a>Linux

``` terminal
## variables for Azure location, extension and namespace
export subscription=<Your subscription ID>
export resourceGroup=<Your resource group>
export clusterName=<name of your connected kubernetes cluster>
export location=<Azure location>
export adsExtensionName="ads-ext" 
export namespace="arcds"

## variables for Metrics and Monitoring dashboard credentials
export AZDATA_LOGSUI_USERNAME=<username for Kibana dashboard>
export AZDATA_LOGSUI_PASSWORD=<password for Kibana dashboard>
export AZDATA_METRICSUI_USERNAME=<username for Grafana dashboard>
export AZDATA_METRICSUI_PASSWORD=<password for Grafana dashboard>
```

#### <a name="windows-powershell"></a>Windows PowerShell
``` PowerShell
## variables for Azure location, extension and namespace
$ENV:subscription="<Your subscription ID>"
$ENV:resourceGroup="<Your resource group>"
$ENV:clusterName="<name of your connected kubernetes cluster>"
$ENV:location="<Azure location>"
$ENV:adsExtensionName="<name of Data controller extension" 
$ENV:namespace="namespace where extension and data controller will be deployed"

## variables for Metrics and Monitoring dashboard credentials
$ENV:AZDATA_LOGSUI_USERNAME="<username for Kibana dashboard>"
$ENV:AZDATA_LOGSUI_PASSWORD="<password for Kibana dashboard>"
$ENV:AZDATA_METRICSUI_USERNAME="<username for Grafana dashboard>"
$ENV:AZDATA_METRICSUI_PASSWORD="<password for Grafana dashboard>"
```

### <a name="create-the-arc-data-services-extension"></a>Creación de la extensión de servicios de datos de Arc

#### <a name="linux"></a>Linux

```bash
az k8s-extension create --cluster-name ${clusterName} --resource-group ${resourceGroup} --name ${adsExtensionName} --cluster-type connectedClusters --extension-type microsoft.arcdataservices --auto-upgrade false --scope cluster --release-namespace ${namespace} --config Microsoft.CustomLocation.ServiceAccount=sa-arc-bootstrapper

az k8s-extension show --resource-group ${resourceGroup} --cluster-name ${resourceName} --name ${adsExtensionName} --cluster-type connectedclusters
```

#### <a name="windows-powershell"></a>Windows PowerShell
```PowerShell

az k8s-extension create --cluster-name $ENV:clusterName --resource-group $ENV:resourceGroup --name $ENV:adsExtensionName --cluster-type connectedClusters --extension-type microsoft.arcdataservices --auto-upgrade false --scope cluster --release-namespace $ENV:namespace --config Microsoft.CustomLocation.ServiceAccount=sa-arc-bootstrapper

az k8s-extension show --resource-group $ENV:resourceGroup --cluster-name $ENV:clusterName --name $ENV:adsExtensionName --cluster-type connectedclusters
```

#### <a name="deploy-azure-arc-data-services-extension-using-private-container-registry-and-credentials"></a>Implementación de la extensión de servicios de datos de Azure Arc mediante credenciales y registro de contenedor privado

Use el comando siguiente si va a realizar la implementación desde el repositorio privado:

```azurecli
az k8s-extension create --cluster-name "<connected cluster name>" --resource-group "<resource group>" --name "<extension name>" --cluster-type connectedClusters --extension-type microsoft.arcdataservices --scope cluster --release-namespace "<namespace>" --config Microsoft.CustomLocation.ServiceAccount=sa-arc-bootstrapper --config imageCredentials.registry=<registry info> --config imageCredentials.username=<username> --config systemDefaultValues.image=<registry/repo/arc-bootstrapper:<imagetag>> --config-protected imageCredentials.password=$ENV:DOCKER_PASSWORD --debug
```

 Por ejemplo
```azurecli
az k8s-extension create --cluster-name "my-connected-cluster" --resource-group "my-resource-group" --name "arc-data-services" --cluster-type connectedClusters --extension-type microsoft.arcdataservices --scope cluster --release-namespace "arc" --config Microsoft.CustomLocation.ServiceAccount=sa-bootstrapper --config imageCredentials.registry=mcr.microsoft.com --config imageCredentials.username=arcuser --config systemDefaultValues.image=mcr.microsoft.com/arcdata/arc-bootstrapper:latest --config-protected imageCredentials.password=$ENV:DOCKER_PASSWORD --debug
```


> [!NOTE]
> La instalación de la extensión de servicios de datos de Arc puede tardar unos minutos en completarse.

### <a name="verify-the-arc-data-services-extension-is-created"></a>Comprobación de la creación de la extensión de servicios de datos de Arc

El estado de la implementación de la extensión de servicios de datos habilitados para Azure Arc se puede comprobar desde el portal o mediante la conexión directa al clúster de Kubernetes habilitado para Azure Arc. 

#### <a name="check-status-from-azure-portal"></a>Comprobación del estado desde Azure Portal
1. Inicie sesión en Azure Portal y vaya al grupo de recursos donde se encuentra el recurso del clúster conectado de Kubernetes.
1. Seleccione el clúster de Kubernetes habilitado para Azure Arc (Tipo = "Kubernetes: Azure Arc") donde se implementó la extensión.
1. En el panel de navegación de la izquierda, en **Configuración**, seleccione **Extensiones**.
1. Debería ver la extensión que se creó anteriormente en un estado instalado.

:::image type="content" source="media/deploy-data-controller-direct-mode-prerequisites/dc-extensions-dashboard.png" alt-text="Panel Extensiones":::

#### <a name="check-status-using-kubectl-cli"></a>Comprobación del estado mediante la CLI de kubectl

1. Conéctese al clúster de Kubernetes mediante una ventana de terminal.
1. Ejecute el comando siguiente y asegúrese de que (1) se ha creado el espacio de nombres mencionado anteriormente y (2) el pod `bootstrapper` está en estado "En ejecución" antes de continuar con el paso siguiente.

``` console
kubectl get pods --name <name of namespace used in the json template file above>
```

Por ejemplo, el ejemplo siguiente obtiene los pods del espacio de nombres `arc`.

```console
#Example:
kubectl get pods --name arc
```

## <a name="retrieve-the-managed-identity-and-grant-roles"></a>Recuperación de la identidad administrada y concesión de roles

La identidad administrada que se crea durante la creación de la extensión de servicios de datos de Arc debe tener asignados determinados roles para que el uso o las métricas se carguen automáticamente.

### <a name="1-retrieve-managed-identity-of-the-arc-data-controller-extension"></a>(1) Recuperación de la identidad administrada de la extensión del controlador de datos de Arc

```powershell
$Env:MSI_OBJECT_ID = (az k8s-extension show --resource-group <resource group>  --cluster-name <connectedclustername> --cluster-type connectedClusters --name <name of extension> | convertFrom-json).identity.principalId
#Example
$Env:MSI_OBJECT_ID = (az k8s-extension show --resource-group myresourcegroup  --cluster-name myconnectedcluster --cluster-type connectedClusters --name ads-extension | convertFrom-json).identity.principalId
```

### <a name="2-assign-role-to-the-managed-identity"></a>(2) Asignación de un rol a la identidad administrada

Ejecute el siguiente comando para asignar los roles de **Colaborador** y **Publicador de métricas de supervisión**:
```powershell
az role assignment create --assignee $Env:MSI_OBJECT_ID --role "Contributor" --scope "/subscriptions/$ENV:subscription/resourceGroups/$ENV:resourceGroup"

az role assignment create --assignee $Env:MSI_OBJECT_ID --role "Monitoring Metrics Publisher" --scope "/subscriptions/$ENV:subscription/resourceGroups/$ENV:resourceGroup"

```

## <a name="step-2-create-a-custom-location-using-customlocation-cli-extension"></a>Paso 2: Creación de una ubicación personalizada mediante la extensión de la CLI ```customlocation```

Una ubicación personalizada es un recurso de Azure equivalente a un espacio de nombres en un clúster de Kubernetes.  Las ubicaciones personalizadas se usan como destino para implementar recursos en o desde Azure. Obtenga más información sobre las ubicaciones personalizadas en [Ubicaciones personalizadas basadas en Kubernetes habilitado para Azure Arc](../kubernetes/conceptual-custom-locations.md).

### <a name="set-environment-variables"></a>Establecimiento de variables de entorno

#### <a name="linux"></a>Linux

```bash
export clName=mycustomlocation

export hostClusterId=$(az connectedk8s show --resource-group ${resourceGroup} --name ${clusterName} --query id -o tsv)
export extensionId=$(az k8s-extension show --resource-group ${resourceGroup} --cluster-name ${clusterName} --cluster-type connectedClusters --name ${adsExtensionName} --query id -o tsv)

az customlocation create --resource-group ${resourceGroup} --name ${clName} --namespace ${namespace} --host-resource-id ${hostClusterId} --cluster-extension-ids ${extensionId} --location ${location}
```

#### <a name="windows-powershell"></a>Windows PowerShell
```PowerShell
$ENV:clName="mycustomlocation"


$ENV:hostClusterId=(az connectedk8s show --resource-group $ENV:resourceGroup --name $ENV:clusterName --query id -o tsv)
$ENV:extensionId=(az k8s-extension show --resource-group $ENV:resourceGroup --cluster-name $ENV:clusterName --cluster-type connectedClusters --name $ENV:adsExtensionName --query id -o tsv)

az customlocation create --resource-group $ENV:resourceGroup --name $ENV:clName --namespace $ENV:namespace --host-resource-id $ENV:hostClusterId --cluster-extension-ids $ENV:extensionId
```

## <a name="validate--the-custom-location-is-created"></a>Validación de la creación de la ubicación personalizada

Desde el terminal, ejecute el siguiente comando para enumerar las ubicaciones personalizadas y compruebe que el **estado de aprovisionamiento** se muestra como realizado correctamente:

```azurecli
az customlocation list -o table
```

## <a name="step-3-create-the-azure-arc-data-controller"></a>Paso 3: Creación del controlador de datos de Azure Arc

Una vez creada la extensión y la ubicación personalizada, continúe con la implementación del controlador de datos de Azure Arc como se muestra a continuación.

```
az arcdata dc create --name <name> --resource-group <resourcegroup> --location <location> --connectivity-mode direct --profile-name <profile name>  --auto-upload-logs true --auto-upload-metrics true --custom-location <name of custom location>
# Example
az arcdata dc create --name arc-dc1 --resource-group my-resource-group --location eastasia --connectivity-mode direct --profile-name azure-arc-aks-premium-storage  --auto-upload-logs true --auto-upload-metrics true --custom-location mycustomlocation
```

Si desea crear el controlador de datos de Azure Arc mediante una plantilla de configuración personalizada, siga los pasos descritos en [Creación de un perfil de configuración personalizado](create-custom-configuration-template.md) y proporcione la ruta de acceso al archivo de la siguiente manera:

```
az arcdata dc create --name <name> --resource-group <resourcegroup> --location <location> --connectivity-mode direct --path ./azure-arc-custom  --auto-upload-logs true --auto-upload-metrics true --custom-location <name of custom location>
# Example
az arcdata dc create --name arc-dc1 --resource-group my-resource-group --location eastasia --connectivity-mode direct --path ./azure-arc-custom  --auto-upload-logs true --auto-upload-metrics true --custom-location mycustomlocation
```

## <a name="monitor-the-status-of-azure-arc-data-controller-deployment"></a>Supervisión del estado de la implementación del controlador de datos de Azure Arc

El estado de implementación del controlador de datos de Arc en el clúster se puede supervisar de la siguiente manera:

```console
kubectl get datacontrollers --name arc
```

## <a name="next-steps"></a>Pasos siguientes

[Creación de un grupo de servidores de Hiperescala de PostgreSQL habilitada para Azure Arc](create-postgresql-hyperscale-server-group.md)

[Creación de una Instancia administrada de Azure SQL en Azure Arc](create-sql-managed-instance.md)
