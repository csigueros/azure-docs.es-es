---
title: Traslado del registro contenedor a otra región
description: Traslade manualmente la configuración y los datos del registro de contenedor de Azure a otra región de Azure.
ms.topic: article
ms.date: 06/08/2021
ms.openlocfilehash: e2bc00287923a95e2e4d3698b22c4c2ca65bebc6
ms.sourcegitcommit: d858083348844b7cf854b1a0f01e3a2583809649
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/25/2021
ms.locfileid: "122835894"
---
# <a name="manually-move-a-container-registry-to-another-region"></a>Traslado manual de un registro de contenedor a otra región

Es posible que tenga que trasladar un registro de contenedor de Azure de una región de Azure a otra. Por ejemplo, puede ejecutar una canalización de desarrollo u hospedar un nuevo destino de implementación en una región diferente y desea proporcionar un registro cercano.

Aunque [Azure Resource Mover](../resource-mover/overview.md) actualmente no se puede automatizar un traslado para un registro de contenedor de Azure, puede trasladar manualmente un registro de contenedor a otra región:

* Exportación de la configuración del registro a una plantilla de Resource Manager
* Uso de la plantilla para implementar un registro en otra región de Azure
* Importación del contenido del registro a partir del registro de origen al de destino

[!INCLUDE [container-registry-geo-replication-include](../../includes/container-registry-geo-replication-include.md)]

## <a name="prerequisites"></a>Prerrequisitos

Azure CLI

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

## <a name="considerations"></a>Consideraciones

* Siga los pasos de este artículo para trasladar el registro a otra región de la misma suscripción. Es posible que se necesite más configuración para trasladar un registro a otra suscripción de Azure en el mismo inquilino de Active Directory.
* Exportar y usar una plantilla de Resource Manager puede ayudar a volver a crear muchas configuraciones del registro. Puede editar la plantilla para configurar más opciones o actualizar el registro de destino después de la creación.
* Actualmente, Azure Container Registry no admite un traslado del registro a otro inquilino de Active Directory. Esta limitación se aplica a ambos registros cifrados con una [clave administrada por el cliente](container-registry-customer-managed-keys.md) y registros sin cifrar.
* Si no puede trasladar un registro que se describa en este artículo, cree un nuevo registro, vuelva a crear manualmente la configuración e [importe el contenido del registro en el registro de destino](#import-registry-content-in-target-registry).

## <a name="export-template-from-source-registry"></a>Exportación de una plantilla desde el registro de origen 

Use Azure Portal, CLI de Azure, Azure PowerShell u otras herramientas de Azure para exportar una plantilla de Resource Manager. Para utilizar el Portal de Azure:

1. En [Azure Portal](https://portal.azure.com), vaya al registro de origen.
1. En el menú, en **Automatización**, seleccione **Exportar plantilla** > **Descargar**.

    :::image type="content" source="media/manual-regional-move/export-template.png" alt-text="Exportación de una plantilla para el registro de contenedor":::

## <a name="redeploy-target-registry-in-new-region"></a>Reimplementación del registro de destino en una nueva región

### <a name="modify-template"></a>Modificación de una plantilla

Inspeccione las propiedades del registro en el archivo JSON de la plantilla que ha descargado y realice los cambios necesarios. Como mínimo:

* Cambie el `defaultValue` del nombre del registro por el nombre deseado del registro de destino.
* Actualice la `location` a la región de Azure deseada para el registro de destino.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "registries_myregistry_name": {
            "defaultValue": "myregistry",
            "type": "String"
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.ContainerRegistry/registries",
            "apiVersion": "2020-11-01-preview",
            "name": "[parameters('myregistry_name')]",
            "location": "centralus",
[...]
```

Para más información, consulte [Usar plantilla exportada desde Azure Portal](../azure-resource-manager/templates/template-tutorial-export-template.md) y la [referencia de plantilla](/azure/templates/microsoft.containerregistry/registries).

> [!IMPORTANT]
> Si desea cifrar el registro de destino mediante una clave administrada por el cliente, asegúrese de actualizar la plantilla con la configuración de la identidad administrada, el almacén de claves y la clave necesarios. Solo puede habilitar la clave administrada por el cliente al implementar el registro.
> 
> Para más información, consulte [Cifrado del registro con una clave administrada por el cliente](./container-registry-customer-managed-keys.md#enable-customer-managed-key---template).

### <a name="create-resource-group"></a>Creación de un grupo de recursos 

Cree un grupo de recursos para el registro de destino con el comando [az group create](/cli/azure/group#az_group_create). En el ejemplo siguiente, se crea un grupo de recursos denominado *myResourceGroup* en la ubicación *eastus*. 

```azurecli
az group create --name myResourceGroup --location eastus
```

### <a name="deploy-target-registry-in-new-region"></a>Implementación del registro de destino en una nueva región

Use el comando [az deployment group create](/cli/azure/deployment/group#az_deployment_group_create) para implementar el registro de destino mediante la plantilla:

```azurecli
az deployment group --resource-group myResourceGroup \
   --template-file template.json --name mydeployment
```

> [!NOTE]
> Si ve errores durante la implementación, es posible que tenga que actualizar ciertas configuraciones en el archivo de plantilla y volver a intentar el comando.

## <a name="import-registry-content-in-target-registry"></a>Importación del contenido del registro en el registro de destino

Después de crear el registro en la región de destino, use el comando [az acr import](/cli/azure/acr#az_acr_import) o el comando de PowerShell equivalente `Import-AzContainerImage` para importar imágenes y otros artefactos que quiera conservar del registro de origen al registro de destino. Para ver ejemplos de comando, consulte [Importación de imágenes de contenedor en un registro de contenedor](container-registry-import-images.md).

* Use los comandos de la CLI de Azure [az acr repository list](/cli/azure/acr/repository#az_acr_repository_list) y [az acr repository show-tags](/cli/azure/acr/repository#az_acr_repository_show_tags), o equivalentes de Azure PowerShell, para ayudar a enumerar el contenido del registro de origen.
* Ejecute el comando de importación para artefactos individuales o cree un script para ejecutarlo en una lista de artefactos.

En el ejemplo siguiente, el script de la CLI de Azure de ejemplo enumera los repositorios y etiquetas de origen y, a continuación, importa los artefactos a un registro de destino de la misma suscripción a Azure. Modifique según sea necesario para importar etiquetas o repositorios específicos. Para importar desde un registro de otra suscripción o inquilino, consulte los ejemplos de [Importación de imágenes de contenedor a un registro de contenedor](container-registry-import-images.md).

```azurecli
#!/bin/bash
# Modify registry names for your environment
SOURCE_REG=myregistry
TARGET_REG=targetregistry

# Get list of source repositories
REPO_LIST=$(az acr repository list \
    --name $SOURCE_REG --output tsv)

# Enumerate tags and import to target registry
for repo in $REPO_LIST; do
    TAGS_LIST=$(az acr repository show-tags --name $SOURCE_REG --repository $repo --output tsv);
    for tag in $TAGS_LIST; do
        echo "Importing $repo:$tag";
        az acr import --name $TARGET_REG --source $SOURCE_REG.azurecr.io/$repo":"$tag;
    done
done
```



## <a name="verify-target-registry"></a>Comprobación del registro de destino

Confirme la siguiente información en el registro de destino:

* Configuración del registro, como el nombre del registro, el nivel de servicio, el acceso público y las replicaciones
* Repositorios y etiquetas para el contenido que desea conservar


### <a name="additional-configuration"></a>Configuración adicional

* Si es necesario, configure manualmente las opciones en el registro de destino, como los puntos de conexión privados, las reglas de acceso IP y las identidades administradas.

* Actualice los sistemas de desarrollo e implementación para usar el registro de destino en lugar del registro de origen.

* Actualice las reglas de firewall de cliente para permitir el acceso al registro de destino.

## <a name="delete-original-registry"></a>Eliminación del registro original

Después de implementar correctamente el registro de destino, el contenido migrado y la configuración del registro comprobado, puede eliminar el registro de origen.

## <a name="next-steps"></a>Pasos siguientes

* Aprenda más sobre la [importación de imágenes de contenedor](container-registry-import-images.md) en un registro de contenedor de Azure desde un registro público u otro registro privado. 
* Consulte la [referencia de la plantilla de Resource Manager](/azure/templates/microsoft.containerregistry/registries) para Azure Container Registry.