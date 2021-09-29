---
title: Creación de áreas de trabajo con la CLI de Azure
titleSuffix: Azure Machine Learning
description: Aprenda a usar la extensión de la CLI de Azure para aprendizaje automático para crear una nueva área de trabajo de Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: larryfr
author: Blackmist
ms.date: 09/23/2021
ms.topic: how-to
ms.custom: devx-track-azurecli
ms.openlocfilehash: 16def8c00db3702a77e261b71841aa7d2d3c3728
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128655512"
---
# <a name="manage-azure-machine-learning-workspaces-using-azure-cli"></a>Administración de áreas de trabajo de Azure Machine Learning usando la CLI de Azure

En este artículo obtendrá información sobre cómo crear y administrar áreas de trabajo de Azure Machine Learning usando la CLI de Azure. La CLI de Azure proporciona comandos para administrar recursos de Azure y está diseñada para ayudarle a trabajar rápidamente con la plataforma, con especial énfasis en la automatización. La extensión de aprendizaje automático de la CLI proporciona comandos para trabajar con recursos de Azure Machine Learning.

> [!NOTE]
> En los ejemplos de este artículo se hace referencia a las versiones 1.0 y (v2) de la CLI. La versión (v2) de la CLI de Machine Learning se encuentra actualmente en versión preliminar pública. Esta versión preliminar se ofrece sin Acuerdo de Nivel de Servicio y no se recomienda para cargas de trabajo de producción.

## <a name="prerequisites"></a>Requisitos previos

* Una **suscripción de Azure**. Si no tiene una ya, pruebe la [versión gratuita o de pago de Azure Machine Learning](https://azure.microsoft.com/free/).

* Para usar los comandos de la CLI de este documento desde su **entorno local**, necesita la [CLI de Azure](/cli/azure/install-azure-cli).

    Si usa el [Azure Cloud Shell](https://azure.microsoft.com//features/cloud-shell/), la CLI es accesible a través del explorador y reside en la nube.

## <a name="limitations"></a>Limitaciones

[!INCLUDE [register-namespace](../../includes/machine-learning-register-namespace.md)]

## <a name="connect-the-cli-to-your-azure-subscription"></a>Conexión de la CLI a una suscripción de Azure

> [!IMPORTANT]
> Puede omitir esta sección si usa Azure Cloud Shell. Cloud Shell se autentica automáticamente mediante la cuenta con la que inicia sesión en su suscripción a Azure.

Hay varias maneras de autenticarse en la suscripción a Azure desde la CLI. La manera más simple consiste en autenticarse interactivamente a través de un explorador. Para autenticarse de forma interactiva, abra una línea de comandos o un terminal y use el siguiente comando:

```azurecli-interactive
az login
```

Si la CLI puede abrir el explorador predeterminado, lo hará y cargará una página de inicio de sesión. De lo contrario, tendrá que abrir un explorador y seguir las instrucciones de la línea de comandos. Las instrucciones implican navegar a [https://aka.ms/devicelogin](https://aka.ms/devicelogin) y escribir un código de autorización.

[!INCLUDE [select-subscription](../../includes/machine-learning-cli-subscription.md)] 

Para obtener otros métodos de autenticación, consulte [Inicio de sesión con la CLI de Azure](/cli/azure/authenticate-azure-cli).

## <a name="create-a-resource-group"></a>Crear un grupo de recursos

El área de trabajo de Azure Machine Learning debe crearse dentro de un grupo de recursos. Puede usar un grupo de recursos existente o crear uno nuevo. Utilice el comando siguiente para __crear un nuevo grupo de recursos__. Reemplace `<resource-group-name>` por el nombre que va a usar para este grupo de recursos. Reemplace `<location>` por la región de Azure que va a usar para este grupo de recursos:

> [!NOTE]
> Debe seleccionar una región en la que Azure Machine Learning esté disponible. Para obtener más información, consulte los [Productos disponibles por región](https://azure.microsoft.com/global-infrastructure/services/?products=machine-learning-service).

```azurecli-interactive
az group create --name <resource-group-name> --location <location>
```

La respuesta de este comando será similar al siguiente código JSON. Puede usar los valores de salida para buscar los recursos creados o analizarlos como entrada de pasos posteriores de la CLI a efectos de automatización.

```json
{
  "id": "/subscriptions/<subscription-GUID>/resourceGroups/<resourcegroupname>",
  "location": "<location>",
  "managedBy": null,
  "name": "<resource-group-name>",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null,
  "type": null
}
```

Para obtener más información sobre cómo trabajar con grupos de recursos, consulte [az group](/cli/azure/group).

## <a name="create-a-workspace"></a>Crear un área de trabajo

Al implementar un área de trabajo de Azure Machine Learning, se requieren otros servicios como [recursos asociados dependientes](./concept-workspace.md#resources). Cuando se usa la CLI para crear el área de trabajo, esta puede crear recursos asociados en su nombre o adjuntar otros existentes.

> [!IMPORTANT]
> Al adjuntar su propia cuenta de almacenamiento, asegúrese de que cumple los siguientes criterios:
>
> * La cuenta de almacenamiento _no_ es una cuenta prémium (Premium_LRS y Premium_GRS)
> * Las funcionalidades Azure Blob y Azure File están habilitadas
> * El espacio de nombres jerárquico (ADLS Gen 2) está deshabilitado. Estos requisitos son solo para la cuenta de almacenamiento _predeterminada_ utilizada por el área de trabajo.
>
> Al adjuntar un registro de contenedor de Azure, debe tener habilitada la [cuenta de administrador](../container-registry/container-registry-authentication.md#admin-account) para que se pueda usar con un área de trabajo de Azure Machine Learning.

# <a name="create-with-new-resources"></a>[Creación de recursos](#tab/createnewresources)

Para crear una nueva área de trabajo en la que __los servicios se creen automáticamente__, use el siguiente comando:

```azurecli-interactive
az ml workspace create -w <workspace-name> -g <resource-group-name>
```

# <a name="bring-existing-resources-10-cli"></a>[Incorporación de recursos existentes (CLI 1.0)](#tab/bringexistingresources1)
Para crear un área de trabajo que use recursos existentes, debe proporcionar el Id. de cada recurso. Para obtener este Id., acceda a la pestaña "Propiedades" de cada recurso en Azure Portal o ejecute los siguientes comandos usando la CLI de Azure.

  * **Cuenta de Azure Storage:**     `az storage account show --name <storage-account-name> --query "id"`
  * **Azure Application Insights:**     `az monitor app-insights component show --app <application-insight-name> -g <resource-group-name> --query "id"`
  * **Azure Key Vault:**     `az keyvault show --name <key-vault-name> --query "ID"`
  * **Azure Container Registry:**     `az acr show --name <acr-name> -g <resource-group-name> --query "id"`

  El Id. de recurso devuelto tiene el formato siguiente: `"/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/<provider>/<subresource>/<resource-name>"`.

Una vez que tenga los id. de los recursos que desea usar con el área de trabajo, use el comando base `az workspace create -w <workspace-name> -g <resource-group-name>` y agregue los parámetros y los id. de los recursos existentes. Por ejemplo, el siguiente comando crea un área de trabajo que usa un registro de contenedor existente:

```azurecli-interactive
az ml workspace create -w <workspace-name>
                       -g <resource-group-name>
                       --container-registry "/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.ContainerRegistry/registries/<acr-name>"
```

# <a name="bring-existing-resources-cli-v2---preview"></a>[Incorporación de recursos existentes (CLI v2): (versión preliminar)](#tab/bringexistingresources2)

Para crear un área de trabajo incorporando recursos asociados existentes mediante la CLI, primero tendrá que definir cómo se debe configurar el área de trabajo en un archivo de configuración.

```yaml workspace.yml
name: azureml888
location: EastUS
description: Description of my workspace
storage_account: /subscriptions/<subscription-id>/resourceGroups/<resourcegroup-name>/providers/Microsoft.Storage/storageAccounts/<storage-account-name>
container_registry: /subscriptions/<subscription-id>/resourceGroups/<resourcegroup-name>/providers/Microsoft.ContainerRegistry/registries/<registry-name>
key_vault: /subscriptions/<subscription-id>/resourceGroups/<resourcegroup-name>/providers/Microsoft.KeyVault/vaults/<vault-name>
application_insights: /subscriptions/<subscription-id>/resourceGroups/<resourcegroup-name>/providers/microsoft.insights/components/<application-insights-name>
```

A continuación, puede hacer referencia a este archivo de configuración como parte del comando de creación de áreas de trabajo de la CLI.

```azurecli-interactive
az ml workspace create -w <workspace-name> -g <resource-group-name> --file workspace.yml
```

Si va a adjuntar recursos existentes, debe proporcionar su Id. Para obtener este Id., acceda a la pestaña "Propiedades" de cada recurso en Azure Portal o ejecute los siguientes comandos usando la CLI de Azure.

* **Cuenta de Azure Storage:**     `az storage account show --name <storage-account-name> --query "id"`
* **Azure Application Insights:**     `az monitor app-insights component show --app <application-insight-name> -g <resource-group-name> --query "id"`
* **Azure Key Vault:**     `az keyvault show --name <key-vault-name> --query "ID"`
* **Azure Container Registry:**     `az acr show --name <acr-name> -g <resource-group-name> --query "id"`

El valor del Id. de recurso tiene un aspecto similar al siguiente: `"/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/<provider>/<subresource>/<resource-name>"`.

---

> [!IMPORTANT]
> Al adjuntar recursos existentes, no es necesario que los especifique todos. Puede especificar uno o varios. Por ejemplo, puede especificar una cuenta de almacenamiento existente y el área de trabajo creará los demás recursos.

La salida de este comando de creación de áreas de trabajo es similar al siguiente código JSON. Puede usar los valores de salida para buscar los recursos creados o analizarlos como entrada de pasos posteriores de la CLI.

```json
{
  "applicationInsights": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<application-insight-name>",
  "containerRegistry": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.containerregistry/registries/<acr-name>",
  "creationTime": "2019-08-30T20:24:19.6984254+00:00",
  "description": "",
  "friendlyName": "<workspace-name>",
  "id": "/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.MachineLearningServices/workspaces/<workspace-name>",
  "identityPrincipalId": "<GUID>",
  "identityTenantId": "<GUID>",
  "identityType": "SystemAssigned",
  "keyVault": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.keyvault/vaults/<key-vault-name>",
  "location": "<location>",
  "name": "<workspace-name>",
  "resourceGroup": "<resource-group-name>",
  "storageAccount": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.storage/storageaccounts/<storage-account-name>",
  "type": "Microsoft.MachineLearningServices/workspaces",
  "workspaceid": "<GUID>"
}

```

## <a name="advanced-configurations"></a>Configuraciones avanzadas
### <a name="configure-workspace-for-private-network-connectivity"></a>Configuración de un área de trabajo para conectividad de red privada

En función de su caso de uso y de sus requisitos organizativos, puede optar por configurar Azure Machine Learning usando conectividad de red privada. Puede usar la CLI de Azure para implementar un área de trabajo y un punto de conexión de vínculo privado para el recurso del área de trabajo. Para obtener más información sobre el uso de un punto de conexión privado y una red virtual con el área de trabajo, vea [Información general sobre la privacidad y el aislamiento de la red virtual](how-to-network-security-overview.md). En el caso de configuraciones de recursos complejas, consulte también opciones de implementación basadas en plantillas, incluido [Azure Resource Manager](how-to-create-workspace-template.md).

# <a name="10-cli"></a>[CLI 1.0](#tab/vnetpleconfigurationsv1cli)

Si quiere restringir el acceso al área de trabajo a una red virtual, puede usar los siguientes parámetros como parte del comando `az ml workspace create` o usar los comandos `az ml workspace private-endpoint`.

```azurecli-interactive
az ml workspace create -w <workspace-name>
                       -g <resource-group-name>
                       --pe-name "<pe name>"
                       --pe-auto-approval "<pe-autoapproval>"
                       --pe-resource-group "<pe name>"
                       --pe-vnet-name "<pe name>"
                       --pe-subnet-name "<pe name>"
```

* `--pe-name`: nombre del punto de conexión privado creado.
* `--pe-auto-approval`: indica si las conexiones de punto de conexión privado al área de trabajo se deben aprobar automáticamente.
* `--pe-resource-group`: grupo de recursos en el que se va a crear el punto de conexión privado. Debe ser el mismo grupo que contiene la red virtual.
* `--pe-vnet-name`: red virtual existente en la que se va a crear el punto de conexión privado.
* `--pe-subnet-name`: nombre de la subred en la que se va a crear el punto de conexión privado. El valor predeterminado es `default`.

Para obtener más información sobre cómo usar estos comandos, consulte las [páginas de referencia de la CLI](/cli/azure/ml(v1)/workspace).

# <a name="cli-v2---preview"></a>[CLI (v2): versión preliminar](#tab/vnetpleconfigurationsv2cli)

A fin de configurar la conectividad de red privada para el área de trabajo mediante la CLI (v2), amplíe el archivo de configuración del área de trabajo para que incluya los detalles del recurso de punto de conexión de vínculo privado.

```yaml workspace.yml
name: azureml888
location: EastUS
description: Description of my workspace
storage_account: /subscriptions/<subscription-id>/resourceGroups/<resourcegroup-name>/providers/Microsoft.Storage/storageAccounts/<storage-account-name>
container_registry: /subscriptions/<subscription-id>/resourceGroups/<resourcegroup-name>/providers/Microsoft.ContainerRegistry/registries/<registry-name>
key_vault: /subscriptions/<subscription-id>/resourceGroups/<resourcegroup-name>/providers/Microsoft.KeyVault/vaults/<vault-name>
application_insights: /subscriptions/<subscription-id>/resourceGroups/<resourcegroup-name>/providers/microsoft.insights/components/<application-insights-name>

private_endpoints:
  approval_type: AutoApproval
  connections:
    my-endpt1:
      subscription_id: <subscription-id>
      resource_group: <resourcegroup>
      location: <location>
      vnet_name: <vnet-name>
      subnet_name: <subnet-name>
```

A continuación, puede hacer referencia a este archivo de configuración como parte del comando de creación de áreas de trabajo de la CLI.

```azurecli-interactive
az ml workspace create -w <workspace-name> -g <resource-group-name> --file workspace.yml
```

---

### <a name="customer-managed-key-and-high-business-impact-workspace"></a>Clave administrada por el cliente y área de trabajo de alto impacto de negocio

De manera predeterminada, los metadatos del área de trabajo se almacenan en una instancia de Azure Cosmos DB que Microsoft mantiene. Estos datos se cifran con claves administradas por Microsoft. En lugar de usar la clave administrada por Microsoft, puede proporcionar su propia clave. Al hacerlo, se crea un conjunto adicional de recursos en la suscripción de Azure para almacenar los datos.

Para obtener más información sobre los recursos que se crean al aportar su propia clave para el cifrado, consulte [Cifrado de datos con Azure Machine Learning](./concept-data-encryption.md#azure-cosmos-db).

En los siguientes comandos de la CLI se proporcionan ejemplos para crear un área de trabajo que usa claves administradas por el cliente para el cifrado, mediante las versiones 1.0 y (v2) de la CLI.

# <a name="10-cli"></a>[CLI 1.0](#tab/vnetpleconfigurationsv1cli)

Use el parámetro `--cmk-keyvault` para especificar el almacén de Azure Key Vault que contiene la clave y `--resource-cmk-uri` para especificar la dirección URL de la clave en el almacén.

Para [limitar los datos que Microsoft recopila](./concept-data-encryption.md#encryption-at-rest) sobre su área de trabajo, puede especificar además el parámetro `--hbi-workspace`. 

```azurecli-interactive
az ml workspace create -w <workspace-name>
                       -g <resource-group-name>
                       --cmk-keyvault "<cmk keyvault name>"
                       --resource-cmk-uri "<resource cmk uri>"
                       --hbi-workspace
```

# <a name="cli-v2---preview"></a>[CLI (v2): versión preliminar](#tab/vnetpleconfigurationsv2cli)

Use el parámetro `customer_managed_key` con los parámetros`key_vault` y `key_uri`, para especificar el Id. de recurso y el URI de la clave dentro del almacén.

Para [limitar los datos que Microsoft recopila](./concept-data-encryption.md#encryption-at-rest) sobre su área de trabajo, puede especificar además la propiedad `hbi_workspace`. 

```yaml workspace.yml
name: azureml888
location: EastUS
description: Description of my workspace
storage_account: /subscriptions/<subscription-id>/resourceGroups/<resourcegroup-name>/providers/Microsoft.Storage/storageAccounts/<storage-account-name>
container_registry: /subscriptions/<subscription-id>/resourceGroups/<resourcegroup-name>/providers/Microsoft.ContainerRegistry/registries/<registry-name>
key_vault: /subscriptions/<subscription-id>/resourceGroups/<resourcegroup-name>/providers/Microsoft.KeyVault/vaults/<vault-name>
application_insights: /subscriptions/<subscription-id>/resourceGroups/<resourcegroup-name>/providers/microsoft.insights/components/<application-insights-name>

hbi_workspace: true
customer_managed_key:
  key_vault: /subscriptions/<subscription-id>/resourceGroups/<resourcegroup-name>/providers//Microsoft.KeyVault/<vaulttype>/<vaultname>
  key_uri: https://<keyvaultid>.vault.azure.net/keys/<keyname>/<keyversion>

```

A continuación, puede hacer referencia a este archivo de configuración como parte del comando de creación de áreas de trabajo de la CLI.

```azurecli-interactive
az ml workspace create -w <workspace-name> -g <resource-group-name> --file workspace.yml
```
---

> [!NOTE]
> Autorice __Aplicación de Machine Learning__ (en "Administración de identidades y acceso") con permisos de colaborador en la suscripción, para administrar los recursos adicionales de cifrado de datos.

> [!NOTE]
> Azure Cosmos DB __no__ se usa para almacenar información como el rendimiento del modelo, la información registrada por los experimentos ni la información registrada desde las implementaciones del modelo. Para obtener más información sobre la supervisión de estos elementos, vea la sección [Supervisión y registro](concept-azure-machine-learning-architecture.md) del artículo de arquitectura y conceptos.

> [!IMPORTANT]
> La selección de un alto impacto de negocio solo puede realizarse al crear un área de trabajo. Este valor no se puede cambiar tras la creación del área de trabajo.

Para obtener más información sobre las claves administradas por el cliente y el área de trabajo de alto impacto de negocio, vea [Seguridad Enterprise para Azure Machine Learning](concept-data-encryption.md#encryption-at-rest).

## <a name="using-the-cli-to-manage-workspaces"></a>Uso de la CLI para administrar áreas de trabajo

### <a name="list-workspaces"></a>Enumeración de áreas de trabajo

Para enumerar todas las áreas de trabajo de su suscripción a Azure, use el siguiente comando:

```azurecli-interactive
az ml workspace list
```

Para obtener más información, consulte la documentación de [az ml workspace list](/cli/azure/ml/workspace#az_ml_workspace_list).

### <a name="get-workspace-information"></a>Obtención de la información del área de trabajo

Para obtener información sobre un área de trabajo, use el siguiente comando:

```azurecli-interactive
az ml workspace show -w <workspace-name> -g <resource-group-name>
```

Para obtener más información, consulte la documentación de [az ml workspace show](/cli/azure/ml/workspace#az_ml_workspace_show).

### <a name="update-a-workspace"></a>Actualización de un área de trabajo

Use el comando siguiente para actualizar un área de trabajo:

```azurecli-interactive
az ml workspace update -w <workspace-name> -g <resource-group-name>
```

Para obtener más información, consulte la documentación de [az ml workspace update](/cli/azure/ml/workspace#az_ml_workspace_update).

### <a name="sync-keys-for-dependent-resources"></a>Sincronización de claves para recursos dependientes

Si cambia las claves de acceso de uno de los recursos que usa el área de trabajo, esta tarda aproximadamente una hora en sincronizarse con las nuevas claves. Para forzar la sincronización inmediata del área de trabajo con las nuevas claves, use el siguiente comando:

```azurecli-interactive
az ml workspace sync-keys -w <workspace-name> -g <resource-group-name>
```

Para obtener más información sobre los cambios de clave, consulte [Regeneración de las claves de la cuenta de almacenamiento](how-to-change-storage-access-key.md).

Para obtener más información sobre el comando sync-keys, consulte [az ml workspace sync-keys\](/cli/azure/ml/workspace#az_ml_workspace_sync-keys).

### <a name="delete-a-workspace"></a>Eliminar un área de trabajo

Use el siguiente comando para eliminar un área de trabajo cuando ya no es necesaria:

```azurecli-interactive
az ml workspace delete -w <workspace-name> -g <resource-group-name>
```

> [!IMPORTANT]
> La eliminación de un área de trabajo no elimina la instancia de Application Insights, la cuenta de almacenamiento, el almacén de claves ni el registro de contenedor que usa el área de trabajo.

También puede eliminar el grupo de recursos; al hacerlo, se elimina el área de trabajo y todos los demás recursos de Azure en el grupo de recursos. Para eliminar el grupo de recursos, use el siguiente comando:

```azurecli-interactive
az group delete -g <resource-group-name>
```

Para obtener más información, consulte la documentación de [az ml workspace delete](/cli/azure/ml/workspace#az_ml_workspace_delete).

## <a name="troubleshooting"></a>Solución de problemas

### <a name="resource-provider-errors"></a>Errores del proveedor de recursos

[!INCLUDE [machine-learning-resource-provider](../../includes/machine-learning-resource-provider.md)]

### <a name="moving-the-workspace"></a>Movimiento del área de trabajo

> [!WARNING]
> No se admite mover el área de trabajo de Azure Machine Learning a otra suscripción ni mover la suscripción propietaria a un nuevo inquilino. Si lo hace, pueden producirse errores.

### <a name="deleting-the-azure-container-registry"></a>Eliminación de la instancia de Azure Container Registry

El área de trabajo de Azure Machine Learning usa Azure Container Registry (ACR) para algunas operaciones. La primera vez que se necesite una instancia de ACR, se creará automáticamente.

[!INCLUDE [machine-learning-delete-acr](../../includes/machine-learning-delete-acr.md)]

## <a name="next-steps"></a>Pasos siguientes

Para obtener más información sobre la extensión de la CLI de Azure para aprendizaje automático, consulte la documentación de [az ml](/cli/azure/ml).