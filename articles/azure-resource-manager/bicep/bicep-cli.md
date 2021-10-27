---
title: Introducción y comandos de la CLI de Bicep
description: Describe los comandos que puede usar en la CLI de Bicep. Estos comandos incluyen la creación de plantillas de Azure Resource Manager desde Bicep.
ms.topic: conceptual
ms.date: 10/18/2021
ms.openlocfilehash: ff5eea15c5e8e3b4f92cdde73d1dfd25865488f0
ms.sourcegitcommit: 5361d9fe40d5c00f19409649e5e8fed660ba4800
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/18/2021
ms.locfileid: "130137626"
---
# <a name="bicep-cli-commands"></a>Comandos de la CLI de Bicep

En este artículo se describen los comandos que puede usar en la CLI de Bicep. Para ejecutar los comandos, debe tener [instalada la CLI de Bicep](./install.md).

En este artículo se muestra cómo ejecutar los comandos en la CLI de Azure. Si no usa la CLI de Azure, ejecute los comandos sin `az` al principio del comando. Por ejemplo, `az bicep version` se convierte en ``bicep version``.

## <a name="build"></a>build

El comando `build` convierte un archivo de Bicep en una plantilla de Azure Resource Manager (plantilla de ARM). Normalmente, no es necesario ejecutar este comando, ya que se ejecuta automáticamente al implementar un archivo de Bicep. Ejecútelo manualmente cuando quiera ver el JSON de la plantilla de ARM que se crea a partir del archivo de Bicep.

En el ejemplo siguiente se convierte un archivo de Bicep denominado _main.bicep_ en una plantilla de ARM denominada _main.json_. El nuevo archivo se crea en el mismo directorio que el archivo de Bicep.

```azurecli
az bicep build --file main.bicep
```

En el ejemplo siguiente se guarda _main.json_ en otro directorio.

```azurecli
az bicep build --file main.bicep --outdir c:\jsontemplates
```

En el ejemplo siguiente se especifica el nombre y la ubicación del archivo que se va a crear.

```azurecli
az bicep build --file main.bicep --outfile c:\jsontemplates\azuredeploy.json
```

Para imprimir el archivo en `stdout`, use:

```azurecli
az bicep build --file main.bicep --stdout
```

Si el archivo Bicep incluye un módulo que hace referencia a un registro externo, el comando de compilación llama automáticamente a [restore](#restore). El comando restore obtiene el archivo del registro y lo almacena en la memoria caché local.

Para no llamar a la restauración automáticamente, use el modificador `--no-restore`:

```azurecli
az bicep build --no-restore <bicep-file>
```

Se produce un error en el proceso de compilación con el modificador `--no-restore` si uno de los módulos externos aún no está almacenado en caché:

```error
The module with reference "br:exampleregistry.azurecr.io/bicep/modules/storage:v1" has not been restored.
```

Cuando reciba este error, ejecute el comando `build` sin el modificador `--no-restore` o ejecute `bicep restore` primero.

Para usar el modificador `--no-restore`, debe tener la CLI de Bicep versión **0.4.1008 o posterior**.

## <a name="decompile"></a>decompile

El comando `decompile` convierte el JSON de la plantilla de ARM en un archivo de Bicep.

```azurecli
az bicep decompile --file main.json
```

Para más información sobre el uso de este comando, consulte [Descompilación de JSON de plantilla de ARM en Bicep](decompile.md).

## <a name="install"></a>instalar

El comando `install` agrega la CLI de Bicep al entorno local. Para más información, consulte [Instalación de herramientas de Bicep](install.md).

Para instalar la versión más reciente, use:

```azurecli
az bicep install
```

Para instalar una versión específica:

```azurecli
az bicep install --version v0.3.255
```

## <a name="list-versions"></a>list-versions

El comando `list-versions` devuelve todas las versiones disponibles de la CLI de Bicep. Use este comando para ver si quiere [actualizar](#upgrade) o [instalar](#install) una nueva versión.

```azurecli
az bicep list-versions
```

El comando devuelve una matriz de versiones disponibles.

```azurecli
[
  "v0.4.1",
  "v0.3.539",
  "v0.3.255",
  "v0.3.126",
  "v0.3.1",
  "v0.2.328",
  "v0.2.317",
  "v0.2.212",
  "v0.2.59",
  "v0.2.14",
  "v0.2.3",
  "v0.1.226-alpha",
  "v0.1.223-alpha",
  "v0.1.37-alpha",
  "v0.1.1-alpha"
]
```

## <a name="publish"></a>Publicar

El comando `publish` agrega un módulo a un registro. El registro de contenedor de Azure debe existir y la cuenta que se publica en el registro debe tener los permisos correctos. Para obtener más información sobre cómo configurar un registro de módulo, consulte [Uso del registro privado para módulos de Bicep](private-module-registry.md).

Después de publicar el archivo en el registro, puede hacer [referencia a él en un módulo](modules.md#file-in-registry).

Para usar el comando publish, debe tener la CLI de versión **0.4.1008 o posterior**.

Para publicar un módulo en un registro, use:

```azurecli
az bicep publish <bicep-file> --target br:<registry-name>.azurecr.io/<module-path>:<tag>
```

Por ejemplo:

```azurecli
az bicep publish storage.bicep --target br:exampleregistry.azurecr.io/bicep/modules/storage:v1
```

El comando `publish` no reconoce los alias que ha definido en un archivo [bicepconfig.json](bicep-config.md). Proporcione la ruta de acceso completa del módulo.

> [!WARNING]
> La publicación en el mismo destino sobrescribe el módulo anterior. Se recomienda incrementar la versión al actualizar.

## <a name="restore"></a>Restauración

Cuando el archivo Bicep usa módulos publicados en un registro, el comando `restore` obtiene copias de todos los módulos necesarios del registro. Almacena esas copias en una caché local. Un archivo Bicep solo se puede crear cuando los archivos externos están disponibles en la memoria caché local. Normalmente, no es necesario ejecutar `restore` porque lo llama automáticamente `build`.

Para usar el comando restore, debe tener la CLI de Bicep versión **0.4.1008 o posterior**.

Para restaurar manualmente los módulos externos de un archivo, use:

```azurecli
az bicep restore <bicep-file>
```

El archivo Bicep que proporcione es el archivo que desea implementar. Debe contener un módulo que se vincula a un registro. Por ejemplo, puede restaurar el siguiente archivo:

```bicep
module stgModule 'br:exampleregistry.azurecr.io/bicep/modules/storage:v1' = {
  name: 'storageDeploy'
  params: {
    storagePrefix: 'examplestg1'
  }
}
```

La caché local se encuentra en:

```path
%USERPROFILE%\.bicep\br\<registry-name>.azurecr.io\<module-path\<tag>
```

## <a name="upgrade"></a>upgrade

El comando `upgrade` actualiza la versión instalada a la versión más reciente.

```azurecli
az bicep upgrade
```

## <a name="version"></a>version

El comando `version` devuelve la versión instalada.

```azurecli
az bicep version
```

El comando muestra el número de versión.

```azurecli
Bicep CLI version 0.4.1 (e2387595c9)
```

Si no ha instalado la CLI de Bicep, verá un error que indica que no se encontró la CLI de Bicep.

## <a name="next-steps"></a>Pasos siguientes

Para información sobre la implementación de un archivo de Bicep, consulte:

* [CLI de Azure](deploy-cli.md)
* [Cloud Shell](deploy-cloud-shell.md)
* [PowerShell](deploy-powershell.md)
