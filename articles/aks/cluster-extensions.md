---
title: Extensiones de clúster para Azure Kubernetes Service (AKS) (versión preliminar)
description: Aprenda a implementar y administrar el ciclo de vida de las extensiones de Azure Kubernetes Service (AKS)
ms.service: container-service
ms.date: 10/13/2021
ms.topic: article
author: nickomang
ms.author: nickoman
ms.openlocfilehash: d9ef2efbd4b77f70bb43830e59b7dee0ae8ad26a
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131093189"
---
# <a name="deploy-and-manage-cluster-extensions-for-azure-kubernetes-service-aks-preview"></a>Implementación y administración de extensiones de clúster para Azure Kubernetes Service (AKS) (versión preliminar)

Las extensiones de clúster brindan una experiencia impulsada por Azure Resource Manager para la instalación y la administración del ciclo de vida de servicios como Azure Machine Learning (ML) en un clúster de AKS. Esta característica permite:

* Implementación de extensiones basada en Azure Resource Manager, incluidas implementaciones a escala en clústeres de AKS.
* Administración del ciclo de vida de la extensión (Actualización, eliminación) desde Azure Resource Manager.

En este artículo, aprenderá lo siguiente:
> [!div class="checklist"]

> * Cómo crear una instancia de extensión.
> * Extensiones de clúster disponibles en AKS.
> * Cómo ver, enumerar, actualizar y eliminar instancias de extensiones.

Puede encontrar información general y algunos conceptos sobre esta característica en el artículo [Extensiones de clúster: Kubernetes habilitado para Azure Arc][arc-k8s-extensions].

[!INCLUDE [preview features note](./includes/preview/preview-callout.md)]

## <a name="prerequisites"></a>Prerrequisitos

* Suscripción a Azure. Si no tiene una suscripción a Azure, puede crear una [cuenta gratuita](https://azure.microsoft.com/free).
* [CLI de Azure](/cli/azure/install-azure-cli) versión >= 2.16.0 instalado.

### <a name="register-provider-for-cluster-extensions"></a>Registro del proveedor para extensiones de clúster

#### <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)

1. Escriba los siguientes comandos:

    ```azurecli-interactive
    az provider register --namespace Microsoft.KubernetesConfiguration
    az provider register --namespace Microsoft.ContainerService
    ```

2. Supervise el proceso de registro. El registro puede tardar un máximo de 10 minutos.

    ```azurecli-interactive
    az provider show -n Microsoft.KubernetesConfiguration -o table
    az provider show -n Microsoft.ContainerService -o table
    ```

    Una vez registrado, debería ver que el estado `RegistrationState` de estos espacios de nombres cambia a `Registered`.

#### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Escriba los siguientes comandos:

    ```azurepowershell
    Register-AzResourceProvider -ProviderNamespace Microsoft.KubernetesConfiguration
    Register-AzResourceProvider -ProviderNamespace Microsoft.ContainerService
    ```

1. Supervise el proceso de registro. El registro puede tardar un máximo de 10 minutos.

    ```azurepowershell
    Get-AzResourceProvider -ProviderNamespace Microsoft.KubernetesConfiguration
    Get-AzResourceProvider -ProviderNamespace Microsoft.ContainerService
    ```

    Una vez registrado, debería ver que el estado `RegistrationState` de estos espacios de nombres cambia a `Registered`.

---

### <a name="register-the-aks-extensionmanager-preview-features"></a>Registro de las características en vista previa (GB) `AKS-ExtensionManager`

Para crear un clúster de AKS que pueda usar extensiones de clúster, debe habilitar la marca `AKS-ExtensionManager` de características en la suscripción.

Registre la marca de la característica `AKS-ExtensionManager` con el comando [az feature register][az-feature-register], como se muestra en el siguiente ejemplo:

```azurecli-interactive
az feature register --namespace "Microsoft.ContainerService" --name "AKS-ExtensionManager"
```

Tarda unos minutos en que el estado muestre *Registrado*. Puede comprobar el estado de registro con el comando [az feature list][az-feature-list]:

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AKS-ExtensionManager')].{Name:name,State:properties.state}"
```

Cuando esté listo, actualice el registro de los proveedores de recursos *Microsoft.KubernetesConfiguration* y *Microsoft.ContainerService* mediante el comando [az provider register][az-provider-register]:

```azurecli-interactive
az provider register --namespace Microsoft.KubernetesConfiguration
az provider register --namespace Microsoft.ContainerService
```

### <a name="setup-the-azure-cli-extension-for-cluster-extensions"></a>Configuración de la CLI de Azure para extensiones de clúster

> [!NOTE]
> La versión mínima admitida para la extensión CLI de Azure `k8s-extension` es `1.0.0`. Si no está seguro de qué versión ha instalado, ejecute `az extension show --name k8s-extension` y busque el campo `version`.

También necesitará la extensión CLI de Azure `k8s-extension`. Para instalarlo, ejecute los siguientes comandos:
  
```azurecli-interactive
az extension add --name k8s-extension
```

Si la extensión `k8s-extension` ya está instalada, puede actualizarla a su versión más reciente con el siguiente comando:

```azurecli-interactive
az extension update --name k8s-extension
```

## <a name="currently-available-extensions"></a>Extensiones disponibles actualmente

>[!NOTE]
> Las extensiones de clúster proporcionan una plataforma para que las distintas extensiones se instalen y se administran en un clúster de AKS. Si tiene problemas al usar cualquiera de estas extensiones, abra una incidencia de soporte técnico con el servicio correspondiente.

<!--
| Extension | Description |
| --------- | ----------- |
-->

Actualmente, no hay extensiones disponibles.

## <a name="supported-regions-and-kubernetes-versions"></a>Regiones admitidas y versiones de Kubernetes

Las extensiones de clúster se pueden usar en clústeres de AKS en las regiones enumeradas en la [compatibilidad con la región de Kubernetes habilitada para Azure Arc][arc-k8s-regions].

Para ver las versiones de Kubernetes admitidas, consulte la documentación correspondiente para cada extensión.

## <a name="usage-of-cluster-extensions"></a>Uso de las extensiones de clúster

> [!NOTE]
> Los ejemplos proporcionados en este artículo no están completos y solo están diseñados para presentar la funcionalidad. Para obtener una lista completa de los comandos y sus parámetros, consulte la [referencia de la CLI az k8s-extension][k8s-extension-reference].

### <a name="create-extensions-instance"></a>Creación de una instancia de extensión

Cree una instancia de la extensión con `k8s-extension create`; para ello, pase los valores de los parámetros obligatorios. El siguiente comando crea una instancia de extensión de Azure Machine Learning en su clúster de AKS:

```azurecli
az k8s-extension create --name aml-compute --extension-type Microsoft.AzureML.Kubernetes --scope cluster --cluster-name <clusterName> --resource-group <resourceGroupName> --cluster-type managedClusters --configuration-settings enableInference=True allowInsecureConnections=True
```

> [!NOTE]
> El servicio extensiones de clúster no puede conservar información confidencial durante más de 48 horas. Si los agentes de extensión de clúster no tienen conectividad de red durante más de 48 horas y no pueden determinar si se debe crear una extensión en el clúster, la extensión pasa al estado `Failed`. Una vez en el estado `Failed`, tendrá que volver a ejecutar `k8s-extension create` para crear una nueva instancia de extensión.

#### <a name="required-parameters"></a>Parámetros obligatorios

| Nombre de parámetro | Descripción |
|----------------|------------|
| `--name` | Nombre de la instancia de extensión. |
| `--extension-type` | Tipo de extensión que quiere instalar en el clúster. For example: Microsoft.AzureML.Kubernetes | 
| `--cluster-name` | Nombre del clúster de AKS en el que se debe crear la instancia de extensión |
| `--resource-group` | El grupo de recursos que incluya el clúster de AKS. |
| `--cluster-type` | Tipo de clúster en el que se debe crear la instancia de extensión. Especificar `managedClusters` a medida que se asigna a clústeres de AKS|

#### <a name="optional-parameters"></a>Parámetros opcionales

| Nombre de parámetro | Descripción |
|--------------|------------|
| `--auto-upgrade-minor-version` | Propiedad booleana que especifica si la versión secundaria de la extensión se actualizará automáticamente o no. Predeterminado: `true`.  Si este parámetro se establece en true, no se puede establecer el valor del parámetro`version`, ya que la versión se actualizará dinámicamente. Si se establece en `false`, la extensión no se actualizará automáticamente, ni siquiera para las versiones de revisión. |
| `--version` | Versión de la extensión que se va a instalar (versión específica en la que se fijará la instancia de extensión). No se debe proporcionar si auto-upgrade-minor-version está establecido en `true`. |
| `--configuration-settings` | Configuración que se puede pasar a la extensión para controlar su funcionalidad. Se deben pasar como pares `key=value` separados por espacios después del nombre del parámetro. Si se usa este parámetro, no se podrá emplear `--configuration-settings-file` en el mismo comando. |
| `--configuration-settings-file` | Ruta de acceso al archivo JSON que contiene los pares clave-valor que se usarán para pasar los valores de configuración a la extensión. Si se usa este parámetro, no se podrá emplear `--configuration-settings` en el mismo comando. |
| `--configuration-protected-settings` | Estos valores de configuración no se pueden recuperar mediante llamadas API `GET` o comandos `az k8s-extension show` y, por tanto, se usan para pasar valores de configuración confidenciales. Se deben pasar como pares `key=value` separados por espacios después del nombre del parámetro. Si se usa este parámetro, no se podrá emplear `--configuration-protected-settings-file` en el mismo comando. |
| `--configuration-protected-settings-file` | Ruta de acceso al archivo JSON que contiene los pares clave-valor que se usarán para pasar configuraciones confidenciales a la extensión. Si se usa este parámetro, no se podrá emplear `--configuration-protected-settings` en el mismo comando. |
| `--scope` | Ámbito de instalación de la extensión: `cluster` o `namespace`. |
| `--release-namespace` | Este parámetro indica el espacio de nombres en el que se debe crear la versión. Este parámetro solo es pertinente si el parámetro `scope` se establece en `cluster`. |
| `--release-train` |  Los autores de las extensiones pueden publicar las versiones en series de versiones diferentes, como `Stable`, `Preview`, etc. Si este parámetro no se establece explícitamente, `Stable` se usa como valor predeterminado. Este parámetro no se puede usar cuando el parámetro `autoUpgradeMinorVersion` está establecido en `false`. |
| `--target-namespace` | Este parámetro indica el espacio de nombres en el que se creará la versión. El permiso de la cuenta del sistema creada para esta instancia de extensión está restringido a este espacio de nombres. Este parámetro solo es pertinente si el parámetro `scope` se establece en `namespace`. |

### <a name="show-details-of-an-extension-instance"></a>Visualización de los detalles de una instancia de extensión

Vea los detalles de una instancia de extensión instalada actualmente con `k8s-extension show`; para ello, pase valores para los parámetros obligatorios:

```azurecli
az k8s-extension show --name azureml --cluster-name <clusterName> --resource-group <resourceGroupName> --cluster-type managedClusters
```

### <a name="list-all-extensions-installed-on-the-cluster"></a>Lista de todas las extensiones instaladas en el clúster

Muestre una lista de todas las extensiones instaladas en un clúster con `k8s-extension list`; para ello, pase los valores de los parámetros obligatorios.

```azurecli
az k8s-extension list --cluster-name <clusterName> --resource-group <resourceGroupName> --cluster-type managedClusters
```

### <a name="update-extension-instance"></a>Actualización de la instancia de extensión

> [!NOTE]
> Consulte la documentación del tipo de extensión (por ejemplo, Azure ML) para obtener información sobre la configuración específica en ConfigurationSetting y ConfigurationProtectedSettings que se pueden actualizar. Para ConfigurationProtectedSettings, se espera que todos los valores se proporcionen durante una actualización de un solo valor. Si se omiten algunos valores, estos se considerarían obsoletos y se eliminarían.

Actualice una instancia de extensión existente con `k8s-extension update`, al pasar los valores para los parámetros obligatorios. El comando siguiente actualiza la configuración de actualización automática para una instancia de extensión de Azure Machine Learning:

```azurecli
az k8s-extension update --name azureml --extension-type Microsoft.AzureML.Kubernetes --scope cluster --cluster-name <clusterName> --resource-group <resourceGroupName> --cluster-type managedClusters
```

**Parámetros obligatorios**

| Nombre de parámetro | Descripción |
|----------------|------------|
| `--name` | Nombre de la instancia de extensión. |
| `--extension-type` | Tipo de extensión que quiere instalar en el clúster. For example: Microsoft.AzureML.Kubernetes | 
| `--cluster-name` | Nombre del clúster de AKS en el que se debe crear la instancia de extensión |
| `--resource-group` | El grupo de recursos que incluya el clúster de AKS. |
| `--cluster-type` | Tipo de clúster en el que se debe crear la instancia de extensión. Especificar `managedClusters` a medida que se asigna a clústeres de AKS|

**Parámetros opcionales**

| Nombre de parámetro | Descripción |
|--------------|------------|
| `--auto-upgrade-minor-version` | Propiedad booleana que especifica si la versión secundaria de la extensión se actualizará automáticamente o no. Predeterminado: `true`.  Si este parámetro se establece en true, no se puede establecer el valor del parámetro`version`, ya que la versión se actualizará dinámicamente. Si se establece en `false`, la extensión no se actualizará automáticamente, ni siquiera para las versiones de revisión. |
| `--version` | Versión de la extensión que se va a instalar (versión específica en la que se fijará la instancia de extensión). No se debe proporcionar si auto-upgrade-minor-version está establecido en `true`. |
| `--configuration-settings` | Configuración que se puede pasar a la extensión para controlar su funcionalidad. Solo se deben proporcionar los valores que requieren una actualización. La configuración proporcionada se reemplazaría por los valores proporcionados. Se deben pasar como pares `key=value` separados por espacios después del nombre del parámetro. Si se usa este parámetro, no se podrá emplear `--configuration-settings-file` en el mismo comando. |
| `--configuration-settings-file` | Ruta de acceso al archivo JSON que contiene los pares clave-valor que se usarán para pasar los valores de configuración a la extensión. Si se usa este parámetro, no se podrá emplear `--configuration-settings` en el mismo comando. |
| `--configuration-protected-settings` | Estos valores de configuración no se pueden recuperar mediante llamadas API `GET` o comandos `az k8s-extension show` y, por tanto, se usan para pasar valores de configuración confidenciales. Al actualizar un valor, se espera que se proporcionan todas los valores. Si se omiten algunos valores, estos se considerarían obsoletos y se eliminarían. Se deben pasar como pares `key=value` separados por espacios después del nombre del parámetro. Si se usa este parámetro, no se podrá emplear `--configuration-protected-settings-file` en el mismo comando. |
| `--configuration-protected-settings-file` | Ruta de acceso al archivo JSON que contiene los pares clave-valor que se usarán para pasar configuraciones confidenciales a la extensión. Si se usa este parámetro, no se podrá emplear `--configuration-protected-settings` en el mismo comando. |
| `--scope` | Ámbito de instalación de la extensión: `cluster` o `namespace`. |
| `--release-train` |  Los autores de las extensiones pueden publicar las versiones en series de versiones diferentes, como `Stable`, `Preview`, etc. Si este parámetro no se establece explícitamente, `Stable` se usa como valor predeterminado. Este parámetro no se puede usar cuando el parámetro `autoUpgradeMinorVersion` está establecido en `false`. |

### <a name="delete-extension-instance"></a>Eliminación de una instancia de extensión

Elimine una instancia de extensión en un clúster con `k8s-extension delete`; para ello, pase los valores de los parámetros obligatorios.

```azurecli
az k8s-extension delete --name azureml --cluster-name <clusterName> --resource-group <resourceGroupName> --cluster-type managedClusters
```

>[!NOTE]
> El recurso de Azure que representa esta extensión se elimina inmediatamente. La versión de Helm del clúster asociado a esta extensión solo se eliminará cuando los agentes que se ejecutan en el clúster de Kubernetes tengan conectividad de red y puedan ponerse en contacto con los servicios de Azure de nuevo para recuperar el estado deseado.

<!-- when extensions are available, add this section
## Next steps

Learn more about the cluster extensions currently available for AKS:

> [!div class="nextstepaction"]

-->

<!-- LINKS -->
<!-- INTERNAL -->
[arc-k8s-extensions]: ../azure-arc/kubernetes/conceptual-extensions.md
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-provider-register]: /cli/azure/provider#az_provider_register
[azure-ml-overview]:  <!-- need link -->
[dapr-overview]: <!-- Not yet live -->
[k8s-extension-reference]: /cli/azure/k8s-extension

<!-- EXTERNAL -->
[arc-k8s-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=azure-arc&regions=all