---
title: Implementación de recursos con la CLI de Azure y archivos Bicep
description: Use Azure Resource Manager y la CLI de Azure para implementar recursos en Azure. Los recursos se definen en un archivo Bicep.
author: mumian
ms.author: jgao
ms.topic: conceptual
ms.date: 07/15/2021
ms.openlocfilehash: 38d1762faf0d9cbab70b57a79cf9d7811c9fab6b
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/03/2021
ms.locfileid: "123427073"
---
# <a name="deploy-resources-with-bicep-and-azure-cli"></a>Implementación de recursos con Bicep y la CLI de Azure

En este artículo, se explica el uso de la CLI de Azure con archivos Bicep para implementar recursos en Azure. Si no conoce los conceptos de implementación y administración de las soluciones de Azure, consulte [Introducción a Bicep](./overview.md).

Para implementar archivos Bicep, necesita la [CLI de Azure versión 2.20.0 o posterior](/cli/azure/install-azure-cli).

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

Si no tiene instalada la CLI de Azure, puede usar Azure Cloud Shell. Para más información, consulte [Implementación de archivos Bicep desde Azure Cloud Shell](./deploy-cloud-shell.md).

## <a name="deployment-scope"></a>Ámbito de la implementación

La implementación puede tener como destino un grupo de recursos, una suscripción, un grupo de administración o un inquilino. Según el ámbito de la implementación, usará comandos diferentes.

* Para implementar en un **grupo de recursos**, use [az deployment group create](/cli/azure/deployment/group#az_deployment_group_create):

  ```azurecli-interactive
  az deployment group create --resource-group <resource-group-name> --template-file <path-to-bicep>
  ```

* Para implementar en una **suscripción**, use [az deployment sub create](/cli/azure/deployment/sub#az_deployment_sub_create):

  ```azurecli-interactive
  az deployment sub create --location <location> --template-file <path-to-bicep>
  ```

  Para más información sobre las implementaciones en el nivel de suscripción, consulte [Creación de grupos de recursos y otros recursos en el nivel de suscripción](deploy-to-subscription.md).

* Para implementar en un **grupo de administración**, use [az deployment mg create](/cli/azure/deployment/mg#az_deployment_mg_create):

  ```azurecli-interactive
  az deployment mg create --location <location> --template-file <path-to-bicep>
  ```

  Para obtener más información sobre las implementaciones de nivel de grupo de administración, consulte [Creación de recursos en el nivel de grupo de administración](deploy-to-management-group.md).

* Para implementar en un **inquilino**, use [az deployment tenant create](/cli/azure/deployment/tenant#az_deployment_tenant_create):

  ```azurecli-interactive
  az deployment tenant create --location <location> --template-file <path-to-bicep>
  ```

  Para obtener más información sobre las implementaciones a nivel de inquilino, consulte [Creación de recursos en el nivel de inquilino](deploy-to-tenant.md).

Para cada ámbito, el usuario que implementa el archivo Bicep debe tener los permisos necesarios para crear recursos.

## <a name="deploy-local-bicep-file"></a>Implementación de un archivo Bicep local

Puede implementar un archivo Bicep desde la máquina local o una que esté almacenada externamente. En esta sección se describe la implementación de un archivo Bicep local.

Si va a realizar la implementación en un grupo de recursos que no existe, cree el grupo de recursos. El nombre del grupo de recursos solo puede incluir caracteres alfanuméricos, puntos, guiones bajos, guiones y paréntesis. Puede tener hasta 90 caracteres. El nombre no puede terminar con un punto.

```azurecli-interactive
az group create --name ExampleGroup --location "Central US"
```

Para implementar un archivo Bicep, use el parámetro `--template-file` en el comando de implementación. En el ejemplo siguiente también se muestra cómo establecer un valor de parámetro.

```azurecli-interactive
az deployment group create \
  --name ExampleDeployment \
  --resource-group ExampleGroup \
  --template-file <path-to-bicep> \
  --parameters storageAccountType=Standard_GRS
```

La implementación puede demorar unos minutos en completarse. Cuando termine, verá un mensaje que incluye el resultado:

```output
"provisioningState": "Succeeded",
```

## <a name="deploy-remote-bicep-file"></a>Implementación de un archivo Bicep remoto

Actualmente, la CLI de Azure no admite la implementación de archivos Bicep remotos. Use la [CLI de Bicep](./install.md#development-environment) para compilar el archivo de Bicep en una plantilla JSON y, luego, cargue el archivo JSON en la ubicación remota.

## <a name="parameters"></a>Parámetros

Para pasar valores de parámetros, puede usar parámetros en línea o un archivo de parámetros.

### <a name="inline-parameters"></a>Parámetros en línea

Para pasar parámetros en línea, proporcione los valores en `parameters`. Por ejemplo, para pasar una cadena y una matriz a un archivo Bicep en un shell de Bash, use:

```azurecli-interactive
az deployment group create \
  --resource-group testgroup \
  --template-file <path-to-bicep> \
  --parameters exampleString='inline string' exampleArray='("value1", "value2")'
```

Si usa la CLI de Azure con el símbolo del sistema de Windows (CMD) o PowerShell, pase la matriz con el formato `exampleArray="['value1','value2']"`.

También puede obtener el contenido del archivo y proporcionar ese contenido como un parámetro en línea.

```azurecli-interactive
az deployment group create \
  --resource-group testgroup \
  --template-file <path-to-bicep> \
  --parameters exampleString=@stringContent.txt exampleArray=@arrayContent.json
```

Obtener un valor de parámetro de un archivo es útil cuando se necesita proporcionar valores de configuración. Por ejemplo, puede proporcionar [valores de cloud-init para una máquina virtual Linux](../../virtual-machines/linux/using-cloud-init.md).

El formato de _arrayContent.json_ es:

```json
[
    "value1",
    "value2"
]
```

Para pasar un objeto, por ejemplo, para establecer etiquetas, use JSON. Por ejemplo, el archivo Bicep podría incluir un parámetro como este:

```json
    "resourceTags": {
      "type": "object",
      "defaultValue": {
        "Cost Center": "IT Department"
      }
    }
```

En este caso, puede pasar una cadena JSON para establecer el parámetro como se muestra en el siguiente script de Bash:

```bash
tags='{"Owner":"Contoso","Cost Center":"2345-324"}'
az deployment group create --name addstorage  --resource-group myResourceGroup \
--template-file $bicepFile \
--parameters resourceName=abcdef4556 resourceTags="$tags"
```

Use comillas dobles alrededor del código JSON que desee pasar al objeto.

Puede usar una variable para contener los valores de parámetro. En Bash, establezca la variable en todos los valores de parámetro y agréguela al comando de implementación.

```azurecli-interactive
params="prefix=start suffix=end"

az deployment group create \
  --resource-group testgroup \
  --template-file <path-to-bicep> \
  --parameters $params
```

Sin embargo, si usa CLI de Azure con el símbolo del sistema (CMD) de Windows o PowerShell, establezca la variable en una cadena JSON. Incluya las comillas: `$params = '{ \"prefix\": {\"value\":\"start\"}, \"suffix\": {\"value\":\"end\"} }'`.

### <a name="parameter-files"></a>Archivos de parámetros

En lugar de pasar parámetros como valores en línea en el script, quizá le resulte más fácil usar un archivo JSON que contiene los valores de parámetro. El archivo de parámetros debe ser un archivo local. No se admiten los archivos de parámetros externos con la CLI de Azure. El archivo Bicep usa archivos de parámetros JSON.

Para más información sobre el archivo de parámetro, consulte [Creación de un archivo de parámetros de Resource Manager](./parameter-files.md).

Para pasar un archivo de parámetros local, use `@` para especificar un archivo local denominado _storage.parameters.json_.

```azurecli-interactive
az deployment group create \
  --name ExampleDeployment \
  --resource-group ExampleGroup \
  --template-file storage.bicep \
  --parameters @storage.parameters.json
```

## <a name="preview-changes"></a>Vista previa de los cambios

Antes de implementar el archivo Bicep, puede obtener una vista previa de los cambios que el archivo Bicep realizará en su entorno. Use la [operación "what-if"](./deploy-what-if.md) para comprobar que la plantilla realiza los cambios esperados. La operación "what-if" también valida que el archivo Bicep no tenga errores.

## <a name="deploy-template-specs"></a>Especificaciones de la implementación de la plantilla

Actualmente, la CLI de Azure no admite la creación de especificaciones de plantilla mediante archivos Bicep. Pero puede crear un archivo de Bicep con el recurso [Microsoft.Resources/templateSpecs](/azure/templates/microsoft.resources/templatespecs) para implementar una especificación de plantilla. En el [ejemplo Creación de una especificación de plantilla](https://github.com/Azure/azure-docs-bicep-samples/blob/main/samples/create-template-spec/azuredeploy.bicep) se muestra cómo crear una especificación de plantilla en un archivo de Bicep. También puede compilar el archivo Bicep en una plantilla ARM JSON mediante la CLI de Bicep y, a continuación, crear una especificación de plantilla con la plantilla JSON.

## <a name="deployment-name"></a>Nombre de implementación

Al implementar un archivo Bicep, puede asignarle un nombre a la implementación. Este nombre puede ayudarle a recuperar la implementación del historial de implementaciones. Si no especifica un nombre para la implementación, se utilizará el nombre del archivo Bicep. Por ejemplo, si implementa un archivo Bicep llamado `azuredeploy.bicep` y no especifica ningún nombre para la implementación, se le asignará el nombre `azuredeploy`.

Cada vez que se ejecuta una implementación, se agrega una entrada al historial de implementación del grupo de recursos con el nombre de la implementación. Si ejecuta otra implementación y le asigna el mismo nombre, la entrada anterior se reemplazará por la implementación actual. Si desea que todas las entradas del historial de implementaciones sean diferentes, asigne un nombre único a cada implementación.

Para crear un nombre único, puede asignar un número aleatorio.

```azurecli-interactive
deploymentName='ExampleDeployment'$RANDOM
```

También puede agregar un valor de fecha.

```azurecli-interactive
deploymentName='ExampleDeployment'$(date +"%d-%b-%Y")
```

Si ejecuta implementaciones simultáneas en el mismo grupo de recursos utilizando el mismo nombre de implementación, solo se completará la última implementación. Aquellas implementaciones que tengan el mismo nombre y no hayan finalizado se sustituirán por la última implementación. Por ejemplo, si ejecuta una implementación llamada `newStorage` que implementa la cuenta de almacenamiento `storage1` y, al mismo tiempo, ejecuta otra implementación llamada `newStorage` que implementa la cuenta de almacenamiento `storage2`, solo se implementará una única cuenta de almacenamiento. La cuenta de almacenamiento resultante será `storage2`.

Sin embargo, si ejecuta una implementación llamada `newStorage` que implementa la cuenta de almacenamiento `storage1` e inmediatamente después ejecuta otra implementación llamada `newStorage` que implementa la cuenta de almacenamiento `storage2`, tendrá dos cuentas de almacenamiento. Una se llamará `storage1` y la otra, `storage2`. Sin embargo, solo tendrá una entrada en el historial de implementaciones.

Si especifica un nombre único para cada implementación, podrá ejecutarlas simultáneamente sin conflictos. Si ejecuta una implementación llamada `newStorage1` que implementa la cuenta de almacenamiento `storage1` y, al mismo tiempo, ejecuta otra implementación llamada `newStorage2` que implementa la cuenta de almacenamiento `storage2`, tendrá dos cuentas de almacenamiento y dos entradas en el historial de implementación.

Para evitar conflictos con las implementaciones simultáneas y garantizar que las entradas del historial de implementaciones sean únicas, asigne un nombre diferente a cada implementación.

## <a name="next-steps"></a>Pasos siguientes

* Para revertir a una implementación correcta cuando se produce un error, consulte [Revertir en caso de error a una implementación correcta](../templates/rollback-on-error.md).
- Para entender cómo definir parámetros en el archivo, consulte [Nociones sobre la estructura y la sintaxis de los archivos Bicep](file.md).
* Para obtener sugerencias para resolver los errores de implementación más comunes, consulte [Solución de errores comunes de implementación de Azure con Azure Resource Manager](../templates/common-deployment-errors.md).
