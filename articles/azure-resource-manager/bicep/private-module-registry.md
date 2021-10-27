---
title: Creación de un registro privado para el módulo de Bicep
description: Aprenda a configurar un registro de contenedor de Azure para módulos privados de Bicep.
ms.topic: conceptual
ms.date: 10/14/2021
ms.openlocfilehash: ccdb8685fe230f19e31ce2802f7c9fc54a33a8a0
ms.sourcegitcommit: 5361d9fe40d5c00f19409649e5e8fed660ba4800
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/18/2021
ms.locfileid: "130138711"
---
# <a name="create-private-registry-for-bicep-modules-preview"></a>Creación de un registro privado para módulos de Bicep (versión preliminar)

Para compartir [módulos](modules.md) en la organización, puede crear un registro de módulo privado. Los módulos se publican en ese registro y se proporciona acceso de lectura a los usuarios que necesitan implementarlos. Una vez que los módulos se comparten en los registros, puede hacer referencia a ellos desde los archivos de Bicep.

Para trabajar con registros de módulo, debe tener la versión **0.4.1008 o posterior** de la CLI de Bicep.

## <a name="configure-private-registry"></a>Configuración de un registro privado

Un registro de Bicep se hospeda en [Azure Container Registry (ACR)](../../container-registry/container-registry-intro.md). Siga los pasos que se indican a continuación para configurar el registro para los módulos.

1. Si ya tiene un registro de contenedor, puede usarlo. Si necesita crear un registro de contenedor, consulte [Inicio rápido: Creación de un registro de contenedor mediante un archivo de Bicep](../../container-registry/container-registry-get-started-bicep.md). 

   Puede usar cualquiera de las SKU de registro disponibles para el registro del módulo. La [replicación geográfica](../../container-registry/container-registry-geo-replication.md) del registro proporciona a los usuarios presencia local o una copia de seguridad activa.

1. Obtenga el nombre del servidor de inicio de sesión. Necesita este nombre al vincular con el registro desde los archivos de Bicep. 

   Para obtener el nombre del servidor de inicio de sesión, use [Get-AzContainerRegistry](/powershell/module/az.containerregistry/get-azcontainerregistry).

   ```azurepowershell
   Get-AzContainerRegistry -ResourceGroupName "<resource-group-name>" -Name "<registry-name>"
   ```

   También puede usar [az acr show](/cli/azure/acr#az_acr_show).

   ```azurecli
   az acr show --resource-group <resource-group-name> --name <registry-name>
   ```

   El formato del nombre del servidor de inicio de sesión es: `<registry-name>.azurecr.io`.

- Para publicar módulos en un registro, debe tener permiso para **insertar** una imagen. Para implementar un módulo desde un registro, debe tener permiso para **extraer** la imagen. Para obtener más información sobre los roles que conceden el acceso adecuado, consulte [Roles y permisos de Azure Container Registry](../../container-registry/container-registry-roles.md)

- Según el tipo de cuenta que use para implementar el módulo, es posible que tenga que personalizar las credenciales que se usan. Estas credenciales son necesarias para obtener los módulos del registro. De forma predeterminada, las credenciales se obtienen de la CLI de Azure o de Azure PowerShell. Puede personalizar la precedencia para obtener las credenciales en el archivo **bicepconfig.json**. Para obtener más información, consulte [Credenciales para restaurar módulos](bicep-config.md#credentials-for-restoring-modules).

- Para mayor seguridad, puede requerir acceso mediante un punto de conexión privado. Para más información, consulte [Conexión privada a una instancia de Azure Container Registry mediante Azure Private Link](../../container-registry/container-registry-private-link.md).

## <a name="publish-files-to-registry"></a>Publicación de archivos en el registro

Después de configurar el registro de contenedor, puede publicar archivos en este. Use el comando [publish](bicep-cli.md#publish) y proporcione los archivos de Bicep que tenga previsto usar como módulos. Especifique la ubicación de destino del módulo en el registro.

```azurecli
az bicep publish storage.bicep --target br:exampleregistry.azurecr.io/bicep/modules/storage:v1
```

## <a name="view-files-in-registry"></a>Visualización de archivos en el registro

Para ver el módulo publicado en el portal:

1. Inicie sesión en [Azure Portal](https://portal.azure.com).
1. Busque los **registros de contenedor**.
1. Seleccione el registro.
1. Seleccione **Repositorios** en el menú situado a la izquierda.
1. Seleccione la ruta de acceso del módulo (repositorio).  En el ejemplo anterior, el nombre de la ruta de acceso del módulo es **bicep/modules/storage**.
1. Seleccione la etiqueta. En el ejemplo anterior, la etiqueta es **v1**.
1. La **referencia de artefacto** coincide con la referencia que usará en el archivo de Bicep.

   ![Referencia de artefacto del registro del módulo de Bicep](./media/private-module-registry/bicep-module-registry-artifact-reference.png)

Ya está listo para hacer referencia al archivo del registro desde un archivo de Bicep. Para obtener ejemplos de la sintaxis que se va a usar para hacer referencia a un módulo externo, consulte los [Módulos de Bicep](modules.md).

## <a name="next-steps"></a>Pasos siguientes

* Para obtener más información sobre los módulos, consulte [Módulos de Bicep](modules.md).
* Para configurar alias para un registro de módulo, consulte [Adición de una configuración personalizada en el archivo de configuración de Bicep](private-module-registry.md).
* Para obtener más información sobre la publicación y la restauración de módulos, consulte los [comandos de la CLI de Bicep](bicep-cli.md).
