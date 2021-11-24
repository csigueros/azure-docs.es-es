---
title: Módulos de Bicep
description: Describe cómo definir un módulo en un archivo de Bicep y cómo utilizar los ámbitos de módulo.
ms.topic: conceptual
ms.date: 11/12/2021
ms.openlocfilehash: a8aedd784875fccbad81957550380cc4fa236616
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/16/2021
ms.locfileid: "132551281"
---
# <a name="bicep-modules"></a>Módulos de Bicep

Bicep permite organizar las implementaciones en módulos. Un módulo es simplemente un archivo de Bicep que se implementa desde otro archivo de Bicep. Con los módulos, mejora la legibilidad de los archivos de Bicep mediante la encapsulación de detalles complejos de la implementación. También puede reutilizar fácilmente módulos para distintas implementaciones.

A fin de compartir módulos con otros miembros de su organización, [cree un registro privado](private-module-registry.md). Los módulos del registro solo están disponibles para los usuarios con los permisos correctos.

Los módulos de Bicep se convierten en una sola plantilla de Azure Resource Manager con [plantillas anidadas](../templates/linked-templates.md#nested-template).

### <a name="microsoft-learn"></a>Microsoft Learn

Para más información sobre los módulos y para tener una guía práctica, consulte [Creación de archivos Bicep que admiten composición mediante módulos](/learn/modules/create-composable-bicep-files-using-modules/) en **Microsoft Learn**.

## <a name="definition-syntax"></a>Sintaxis de definición

La sintaxis básica para definir un módulo es:

```bicep
module <symbolic-name> '<path-to-file>' = {
  name: '<linked-deployment-name>'
  params: {
    <parameter-names-and-values>
  }
}
```

Por lo tanto, un ejemplo sencillo del mundo real tendría un aspecto similar a este:

::: code language="bicep" source="~/azure-docs-bicep-samples/syntax-samples/modules/local-file-definition.bicep" :::

Use el nombre simbólico para hacer referencia al módulo en otra parte del archivo de Bicep. Por ejemplo, puede usar el nombre simbólico para obtener la salida de un módulo. El nombre simbólico puede tener estos caracteres: a-z, A-Z, 0-9 y subrayado (`_`). No puede empezar con un número. Un módulo no puede tener el mismo nombre que un parámetro, una variable o un recurso.

La ruta de acceso puede ser un archivo local o un archivo en un registro. Para más información, consulte [Ruta de acceso al módulo](#path-to-module).

La propiedad **name** es obligatoria. Se convierte en el nombre del recurso de implementación anidado en la plantilla generada.

Si necesita **especificar un ámbito** distinto del ámbito del archivo principal, agregue la propiedad correspondiente. Para más información, consulte [Establecimiento del ámbito del módulo](#set-module-scope).

::: code language="bicep" source="~/azure-docs-bicep-samples/syntax-samples/modules/scope-definition.bicep" highlight="4" :::

A fin de **implementar un módulo de manera condicional**, agregue una expresión `if`. El uso es similar a la [implementación condicional de un recurso](conditional-resource-deployment.md).

::: code language="bicep" source="~/azure-docs-bicep-samples/syntax-samples/modules/conditional-definition.bicep" highlight="2" :::

Para implementar **más de una instancia** de un módulo, agregue la expresión `for`. Puede usar el decorador `batchSize` para especificar si las instancias se implementan en serie o en paralelo. Para más información, consulte [Bucles iterativos en Bicep](loops.md).

::: code language="bicep" source="~/azure-docs-bicep-samples/syntax-samples/modules/iterative-definition.bicep" highlight="3" :::

Al igual que los recursos, los módulos se implementan en paralelo a menos que dependan de otros módulos o recursos. Normalmente, no es necesario establecer dependencias, ya que se determinan implícitamente. Si necesita establecer una dependencia explícita, puede agregar `dependsOn` a la definición del módulo. Para más información sobre las dependencias, consulte [Dependencias de recursos](resource-declaration.md#dependencies).

::: code language="bicep" source="~/azure-docs-bicep-samples/syntax-samples/modules/dependsOn-definition.bicep" highlight="6-8" :::

## <a name="path-to-module"></a>Ruta de acceso al módulo

El archivo del módulo puede ser un archivo local o uno externo en un registro de módulo de Bicep. Ambas opciones se muestran a continuación.

### <a name="local-file"></a>Archivo local

Si el módulo es un **archivo local**, proporcione una ruta de acceso relativa a ese archivo. Todas las rutas de acceso de Bicep deben especificarse mediante el separador de directorios de barra diagonal (/), con el fin de garantizar una compilación coherente entre plataformas. El carácter de barra diagonal inversa de Windows (\\) no se admite. Las rutas de acceso pueden contener espacios.

Por ejemplo, para implementar un archivo que está un nivel más arriba del archivo principal en el directorio, utilice:

::: code language="bicep" source="~/azure-docs-bicep-samples/syntax-samples/modules/local-file-definition.bicep" highlight="1" :::

### <a name="file-in-registry"></a>Archivo en el registro

Si ha [publicado un módulo en un registro](bicep-cli.md#publish), puede crear un vínculo a ese módulo. Proporcione el nombre del registro de contenedor de Azure y una ruta de acceso al módulo. Especifique la ruta de acceso al módulo con la sintaxis siguiente:

```bicep
module <symbolic-name> 'br:<registry-name>.azurecr.io/<file-path>:<tag>' = {
```

- **br** es el nombre del esquema de un registro de Bicep.
- La **ruta de acceso al archivo** en Azure Container Registry se denomina `repository`. La **ruta de acceso** del archivo puede contener segmentos separados por el carácter `/`.
- **tag** se usa para especificar una versión del módulo.

Por ejemplo:

::: code language="bicep" source="~/azure-docs-bicep-samples/syntax-samples/modules/registry-definition.bicep" highlight="1" :::

Cuando se hace referencia a un módulo en un registro, la extensión de Bicep en Visual Studio Code automáticamente llama a [bicep restore](bicep-cli.md#restore) para copiar el módulo externo en la caché local. El módulo externo tarda unos minutos en restaurarse. Si IntelliSense para el módulo no funciona de inmediato, espere a que se complete la restauración.

La ruta de acceso completa de un módulo en un registro puede ser larga. En lugar de proporcionar la ruta de acceso completa cada vez que desea usar el módulo, puede [configurar alias en el archivo bicepconfig.json](bicep-config-modules.md#aliases-for-modules). Los alias facilitan la referencia al módulo. Por ejemplo, con un alias, puede acortar la ruta de acceso a:

::: code language="bicep" source="~/azure-docs-bicep-samples/syntax-samples/modules/alias-definition.bicep" highlight="1" :::

## <a name="parameters"></a>Parámetros

Los parámetros que proporciona en la definición del módulo coinciden con los parámetros del archivo de Bicep.

El ejemplo de Bicep siguiente tiene tres parámetros: storagePrefix, storageSKU y location. El parámetro storageSKU tiene un valor predeterminado, por lo que no es necesario proporcionar un valor para ese parámetro durante la implementación.

::: code language="bicep" source="~/azure-docs-bicep-samples/samples/create-storage-account/main.bicep" highlight="3,15,17" :::

Para usar el ejemplo anterior como módulo, proporcione valores para esos parámetros.

::: code language="bicep" source="~/azure-docs-bicep-samples/samples/modules/parent-output.bicep" highlight="14-17" :::

## <a name="set-module-scope"></a>Establecimiento del ámbito del módulo

Al declarar un módulo, puede establecer un ámbito para el módulo que sea diferente del ámbito del archivo de Bicep que lo contiene. Use la propiedad `scope` para establecer el ámbito del módulo. Cuando no se proporciona la propiedad scope, el módulo se implementa en el ámbito de destino del elemento primario.

En el archivo de Bicep siguiente se crea un grupo de recursos y una cuenta de almacenamiento en ese grupo. El archivo se implementa en una suscripción, pero el ámbito del módulo es el grupo de recursos nuevo.

::: code language="bicep" source="~/azure-docs-bicep-samples/samples/modules/rg-and-storage.bicep" highlight="2,12,19" :::

En el ejemplo siguiente se implementan cuentas de almacenamiento en dos grupos de recursos diferentes. Ambos grupos de recursos ya deben existir.

::: code language="bicep" source="~/azure-docs-bicep-samples/samples/modules/scope-two-resource-groups.bicep" highlight="1,13,22" :::

Establezca la propiedad scope en un objeto de ámbito válido. Si el archivo Bicep implementa un grupo de recursos, una suscripción o un grupo de administración, puede establecer el ámbito de un módulo en el nombre simbólico de ese recurso. O bien, puede usar las funciones de ámbito para obtener un ámbito válido.

Estas funciones son las siguientes:

- [resourceGroup](bicep-functions-scope.md#resourcegroup)
- [suscripción](bicep-functions-scope.md#subscription)
- [managementGroup](bicep-functions-scope.md#managementgroup)
- [tenant](bicep-functions-scope.md#tenant)

En el ejemplo siguiente se usa la función `managementGroup` para establecer el ámbito.

::: code language="bicep" source="~/azure-docs-bicep-samples/syntax-samples/modules/function-scope.bicep" highlight="5" :::

## <a name="output"></a>Resultados

Puede obtener valores de un módulo y usarlos en el archivo de Bicep principal. Si desea obtener un valor de salida de un módulo, use la propiedad `outputs` en el objeto de módulo.

En el primer ejemplo se crea una cuenta de almacenamiento y se devuelven los puntos de conexión principales.

::: code language="bicep" source="~/azure-docs-bicep-samples/samples/create-storage-account/main.bicep" highlight="33" :::

Cuando se usa como módulo, puede obtener ese valor de salida.

::: code language="bicep" source="~/azure-docs-bicep-samples/samples/modules/parent-output.bicep" highlight="20" :::

## <a name="next-steps"></a>Pasos siguientes

- Para ver un tutorial, consulte [Implementación de recursos de Azure mediante plantillas de Bicep](/learn/modules/deploy-azure-resources-by-using-bicep-templates/).
- Para pasar un valor confidencial a un módulo, use la función [getSecret](bicep-functions-resource.md#getsecret).
