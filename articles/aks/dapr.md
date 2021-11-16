---
title: Extensión de Dapr para Azure Kubernetes Service (AKS) (versión preliminar)
description: Instale y configure Dapr en el clúster de Azure Kubernetes Service (AKS) mediante la extensión de clúster de Dapr.
author: greenie-msft
ms.author: nigreenf
ms.service: container-service
ms.topic: article
ms.date: 10/15/2021
ms.custom: devx-track-azurecli, ignite-fall-2021
ms.openlocfilehash: a8ba281a1061643cc582e6de4fc75a8c43fae71c
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2021
ms.locfileid: "131447441"
---
# <a name="dapr-extension-for-azure-kubernetes-service-aks-preview"></a>Extensión de Dapr para Azure Kubernetes Service (AKS) (versión preliminar)

[Dapr](https://dapr.io/) es un entorno de ejecución portátil controlado por eventos que facilita a cualquier desarrollador la creación de aplicaciones resistentes, sin estado y con estado que se ejecutan en la nube y en el perímetro, y abarca la diversidad de lenguajes y marcos de desarrollador. Al aprovechar las ventajas de una arquitectura de tipo sidecar, Dapr le ayuda a abordar los desafíos que conlleva la creación de microservicios y mantiene independiente la plataforma de código. En concreto, ayuda a resolver problemas relacionados con servicios que llaman a otros servicios de forma confiable y segura, a compilar aplicaciones controladas por eventos con pub-sub y a compilar aplicaciones que son portátiles en varios servicios en la nube y hosts (por ejemplo, Kubernetes en comparación con una máquina virtual).

Al usar la extensión de Dapr de AKS para aprovisionar Dapr en el clúster de AKS, se elimina la sobrecarga de descargar las herramientas de Dapr e instalar y administrar manualmente el entorno de ejecución en el clúster de AKS. Además, la extensión ofrece compatibilidad con todas las [funcionalidades de configuración nativas de Dapr][dapr-configuration-options] a través de argumentos de línea de comandos sencillos.

> [!NOTE]
> Si tiene previsto instalar Dapr en un entorno de producción de Kubernetes, consulte la página de documentación [Directrices de Dapr para el uso de producción][kubernetes-production].

## <a name="how-it-works"></a>Cómo funciona

La extensión de Dapr de AKS usa la CLI de Azure para aprovisionar el plano de control de Dapr en el clúster de AKS. Esto creará lo siguiente:

- **dapr-operator:** administra las actualizaciones de componentes y los puntos de conexión de servicios de Kubernetes para Dapr (almacenes de estado, pub/subs, etc.).
- **dapr-sidecar-injector:** inserta Dapr en pods de implementación anotados y agrega las variables de entorno `DAPR_HTTP_PORT` y `DAPR_GRPC_PORT` para permitir que las aplicaciones definidas por el usuario se comuniquen fácilmente con Dapr sin codificar de forma rígida los valores de puerto de Dapr.
- **dapr-placement:** solo se usa para actores. Crea tablas de asignación que asignan instancias de actor a pods.
- **dapr-sentry:** administra mTLS entre servicios y actúa como entidad de certificación. Para obtener más información, lea la [Introducción a la seguridad][dapr-security].

Una vez que Dapr está instalado en el clúster de AKS, los servicios de aplicación tienen el sidecar de Dapr ejecutándose junto con ellos. Esto le permite empezar a usar inmediatamente las API del bloque de compilación de Dapr. Para obtener información general más detallada de las API de bloques de compilación y la mejor forma de usarlas, consulte la [Introducción a los bloques de compilación de Dapr][building-blocks-concepts].

> [!WARNING]
> Si instala Dapr a través de la extensión de AKS, nuestra recomendación es seguir usando la extensión para la administración futura de Dapr en lugar de la CLI de Dapr. La combinación de las dos herramientas puede provocar conflictos y generar un comportamiento no deseado.

## <a name="supported-kubernetes-versions"></a>Versiones de Kubernetes compatibles en Azure Kubernetes Service (AKS).

La extensión de Dapr usa la misma ventana de soporte técnico que AKS. Para más información, consulte la [directiva de compatibilidad de versiones de Kubernetes][k8s-version-support-policy].

## <a name="prerequisites"></a>Prerrequisitos 

- Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar.
- Instale la versión más reciente de la [CLI de Azure](/cli/azure/install-azure-cli-windows) y la extensión *aks-preview*.
- Si todavía no tiene uno, debe crear un [clúster de AKS][deploy-cluster].


### <a name="register-the-aks-extensionmanager-and-aks-dapr-preview-features"></a>Registro de las características en versión preliminar `AKS-ExtensionManager` y `AKS-Dapr`

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

Para crear un clúster de AKS que pueda usar la extensión de Dapr, debe habilitar las marcas de características `AKS-ExtensionManager` y `AKS-Dapr` en la suscripción.

Registre las marcas de características `AKS-ExtensionManager` y `AKS-Dapr` con el comando [az feature register][az-feature-register], como se muestra en el siguiente ejemplo:

```azurecli-interactive
az feature register --namespace "Microsoft.ContainerService" --name "AKS-ExtensionManager"
az feature register --namespace "Microsoft.ContainerService" --name "AKS-Dapr"
```

Tarda unos minutos en que el estado muestre *Registrado*. Puede comprobar el estado de registro con el comando [az feature list][az-feature-list]:

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AKS-ExtensionManager')].{Name:name,State:properties.state}"
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AKS-Dapr')].{Name:name,State:properties.state}"
```

Cuando esté listo, actualice el registro de los proveedores de recursos *Microsoft.KubernetesConfiguration* y *Microsoft.ContainerService* mediante el comando [az provider register][az-provider-register]:

```azurecli-interactive
az provider register --namespace Microsoft.KubernetesConfiguration
az provider register --namespace Microsoft.ContainerService
```

### <a name="setup-the-azure-cli-extension-for-cluster-extensions"></a>Configuración de la extensión de la CLI de Azure para extensiones de clúster

También necesitará la extensión de la CLI de Azure `k8s-extension`. Para instalarla, ejecute los siguientes comandos:
  
```azurecli-interactive
az extension add --name k8s-extension
```

Si la extensión `k8s-extension` ya está instalada, puede actualizarla a su versión más reciente con el siguiente comando:

```azurecli-interactive
az extension update --name k8s-extension
```

## <a name="create-the-extension-and-install-dapr-on-your-aks-cluster"></a>Creación de la extensión e instalación de Dapr en el clúster de AKS

Una vez que ha registrado la suscripción para usar extensiones de Kubernetes, puede crear la extensión de Dapr, que instala Dapr en el clúster de AKS. Por ejemplo:

```azure-cli-interactive
az k8s-extension create --cluster-type managedClusters \
--cluster-name myAKSCluster \
--resource-group myResourceGroup \
--name myDaprExtension \
--extension-type Microsoft.Dapr \
```

Tiene la opción de permitir que Dapr actualice automáticamente su versión menor especificando el parámetro `--auto-upgrade-minor-version` y estableciendo el valor en `true`:

```azure-cli-interactive
--auto-upgrade-minor-version true
```

## <a name="configuration-settings"></a>Parámetros de configuración

La extensión le permite establecer las opciones de configuración de Dapr mediante el parámetro `--configuration-settings`. Por ejemplo, para aprovisionar Dapr con alta disponibilidad (HA) habilitada, establezca el parámetro `global.ha.enabled` en `true`:

```azure-cli-interactive
az k8s-extension create --cluster-type managedClusters \
--cluster-name myAKSCluster \
--resource-group myResourceGroup \
--name myDaprExtension \
--extension-type Microsoft.Dapr \
--auto-upgrade-minor-version true \  
--configuration-settings "global.ha.enabled=true" \
--configuration-settings "dapr_operator.replicaCount=2" \
```

> [!NOTE]
> Si los valores de configuración son confidenciales y deben protegerse, como, por ejemplo, la información relacionada con certificados, pase el parámetro `--configuration-protected-settings` y el valor se protegerá para que no se lea.

Si no se pasa ninguna configuración, los valores predeterminados de la configuración de Dapr son los siguientes:

```yaml
  ha:
    enabled: true
    replicaCount: 3
    disruption:
      minimumAvailable: ""
      maximumUnavailable: "25%"
  prometheus:
    enabled: true
    port: 9090
  mtls:
    enabled: true
    workloadCertTTL: 24h
    allowedClockSkew: 15m
```

Para obtener una lista de las opciones disponibles, consulte [Configuración de Dapr][dapr-configuration-options].

## <a name="targeting-a-specific-dapr-version"></a>Obtención como destino una versión específica de Dapr

> [!NOTE]
> Dapr se admite con una ventana gradual, que incluye solo las versiones actuales y anteriores. Es su responsabilidad operativa mantenerse actualizado a estas versiones admitidas. Si tiene una versión más antigua de Dapr, es posible que deba realizar actualizaciones intermedias para llegar a una versión admitida.

Para instalar una versión específica de Dapr o revertir a una versión anterior, se usa el mismo argumento de línea de comandos. Establezca `--auto-upgrade-minor-version` en `false` y `--version` en la versión de Dapr que quiere instalar. Si se omite el parámetro `version`, la extensión instalará la versión más reciente de Dapr. Por ejemplo, para usar Dapr X.X.X: 

```azure-cli-interactive
az k8s-extension create --cluster-type managedClusters \
--cluster-name myAKSCluster \
--resource-group myResourceGroup 
--name myDaprExtension \
--extension-type Microsoft.Dapr \
--auto-upgrade-minor-version false \
--version X.X.X \
```

## <a name="show-current-configuration-settings"></a>Visualización de las opciones de configuración actuales

Use el comando `az k8s-extension show` para mostrar los valores de configuración actuales de Dapr:  

```azure-cli-interactive
az k8s-extension show --cluster-type managedClusters \
--cluster-name myAKSCluster \
--resource-group myResourceGroup \
--name myDaprExtension
```

## <a name="update-configuration-settings"></a>Actualizar valores de configuración

> [!IMPORTANT]
> Algunas opciones de configuración no se pueden modificar después de la creación. Los ajustes de estas opciones requieren la eliminación y la nueva creación de la extensión. Esto es aplicable a las siguientes opciones de configuración:
> * `global.ha.*`
> * `dapr_placement.*`

> [!NOTE]
> La alta disponibilidad (HA) se puede habilitar en cualquier momento. Sin embargo, una vez habilitada, para volver a deshabilitarla es necesario eliminar y volver a crear la extensión. Si no está seguro de si es necesaria una alta disponibilidad para su caso de uso, se recomienda empezar con esta opción deshabilitada para minimizar las interrupciones.

Para actualizar los valores de configuración de Dapr, simplemente vuelva a crear la extensión con el estado deseado. Por ejemplo, suponga que hemos creado e instalado previamente la extensión con la siguiente configuración:

```azurecli-interactive
az k8s-extension create --cluster-type managedClusters \
--cluster-name myAKSCluster \
--resource-group myResourceGroup \
--name myDaprExtension \
--extension-type Microsoft.Dapr \
--auto-upgrade-minor-version true \  
--configuration-settings "global.ha.enabled=true" \
--configuration-settings "dapr_operator.replicaCount=2" 
```

Para actualizar `dapr_operator.replicaCount` de 2 a 3, use lo siguiente:

```azurecli-interactive
az k8s-extension create --cluster-type managedClusters \
--cluster-name myAKSCluster \
--resource-group myResourceGroup \
--name myDaprExtension \
--extension-type Microsoft.Dapr \
--auto-upgrade-minor-version true \  
--configuration-settings "global.ha.enabled=true" \
--configuration-settings "dapr_operator.replicaCount=3" 
```

## <a name="troubleshooting-extension-errors"></a>Solución de errores de la extensión

Si la extensión no se puede crear o actualizar, puede inspeccionar dónde se ha producido el error ejecutando el comando `az k8s-extension list`. Por ejemplo, si se usa una clave incorrecta en los valores de configuración, como, por ejemplo `global.ha=false` en lugar de `global.ha.enabled=false`: 

```azure-cli-interactive
az k8s-extension list --cluster-type managedClusters --cluster-name myAKSCluster --resource-group myResourceGroup
```

Se devuelve el JSON siguiente y el mensaje de error se captura en la propiedad `message`.

```json
"statuses": [
      {
        "code": "InstallationFailed",
        "displayStatus": null,
        "level": null,
        "message": "Error: {failed to install chart from path [] for release [dapr-1]: err [template: dapr/charts/dapr_sidecar_injector/templates/dapr_sidecar_injector_poddisruptionbudget.yaml:1:17: executing \"dapr/charts/dapr_sidecar_injector/templates/dapr_sidecar_injector_poddisruptionbudget.yaml\" at <.Values.global.ha.enabled>: can't evaluate field enabled in type interface {}]} occurred while doing the operation : {Installing the extension} on the config",
        "time": null
      }
],
```

## <a name="delete-the-extension"></a>Eliminación de la extensión

Si necesita eliminar la extensión y quitar Dapr del clúster de AKS, puede usar el siguiente comando: 

```azure-cli-interactive
az k8s-extension delete --resource-group myResourceGroup --cluster-name myAKSCluster --cluster-type managedClusters --name myDaprExtension
```

## <a name="next-steps"></a>Pasos siguientes

- Una vez que haya aprovisionado correctamente Dapr en el clúster de AKS, intente implementar una [aplicación de ejemplo][sample-application].

<!-- LINKS INTERNAL -->
[deploy-cluster]: ./tutorial-kubernetes-deploy-cluster.md
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-provider-register]: /cli/azure/provider#az_provider_register
[sample-application]: ./quickstart-dapr.md
[k8s-version-support-policy]: ./supported-kubernetes-versions.md?tabs=azure-cli#kubernetes-version-support-policy

<!-- LINKS EXTERNAL -->
[kubernetes-production]: https://docs.dapr.io/operations/hosting/kubernetes/kubernetes-production
[building-blocks-concepts]: https://docs.dapr.io/developing-applications/building-blocks/
[dapr-configuration-options]: https://github.com/dapr/dapr/blob/master/charts/dapr/README.md#configuration
[sample-application]: https://github.com/dapr/quickstarts/tree/master/hello-kubernetes#step-2---create-and-configure-a-state-store
[dapr-security]: https://docs.dapr.io/concepts/security-concept/
