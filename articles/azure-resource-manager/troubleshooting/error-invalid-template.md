---
title: Errores de plantilla no válida
description: Describe cómo resolver errores de plantilla no válida al implementar archivos de Bicep o plantillas de Azure Resource Manager.
ms.topic: troubleshooting
ms.date: 11/15/2021
ms.openlocfilehash: d5bd7494300ac8861ebd85478e380132acbac007
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/16/2021
ms.locfileid: "132550672"
---
# <a name="resolve-errors-for-invalid-template"></a>Resolución de errores de plantilla no válida

En este artículo se describe cómo resolver errores de plantilla no válida en archivos de Bicep y plantillas de Azure Resource Manager.

## <a name="symptom"></a>Síntoma

Al implementar una plantilla, recibe un error que indica:

```Output
Code=InvalidTemplate
Message=<varies>
```

El mensaje de error depende del tipo de error.

## <a name="cause"></a>Causa

Este error puede tener distintos orígenes. Normalmente implican un error estructural o de sintaxis en la plantilla.

<a id="syntax-error"></a>

## <a name="solution-1---syntax-error"></a>Solución 1: error de sintaxis

Si recibe un mensaje de error que indica que la plantilla no superó la validación, puede que esta tenga un problema de sintaxis.

```Output
Code=InvalidTemplate
Message=Deployment template validation failed
```

Pueden producirse errores de sintaxis porque las expresiones de la plantilla tienen muchos elementos. Por ejemplo, la asignación de nombres en una cuenta de almacenamiento incluye pares de comillas simples o dobles, llaves, corchetes y paréntesis. Las expresiones también contienen funciones y caracteres como signos de dólar, comas y puntos.


# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
name: 'storage${uniqueString(resourceGroup().id)}'
```

# <a name="json"></a>[JSON](#tab/json)

```json
"name": "[concat('storage', uniqueString(resourceGroup().id))]",
```

---

Si recibe este tipo de error, examine meticulosamente la sintaxis de la expresión. Para identificar errores de plantilla, puede usar [Visual Studio Code](https://code.visualstudio.com) con la [extensión Bicep](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-bicep) más reciente o con la [extensión Herramientas de Azure Resource Manager](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools).

<a id="incorrect-segment-lengths"></a>

## <a name="solution-2---incorrect-segment-lengths"></a>Solución 2: longitudes de segmentos incorrectas

Cuando el nombre del recurso no tiene el formato correcto, se produce otro error de plantilla no válida. Para resolver ese error, consulte [Resolución de errores de no coincidencia de nombre y tipo](error-invalid-name-segments.md).

<a id="parameter-not-valid"></a>

## <a name="solution-3---parameter-isnt-valid"></a>Solución 3: el parámetro no es válido

Puede especificar los valores permitidos de un parámetro en una plantilla. Durante la implementación, si proporciona un valor que no es un valor permitido, recibe un mensaje de error similar al siguiente:

```Output
Code=InvalidTemplate;
Message=Deployment template validation failed: 'The provided value {parameter value}
for the template parameter {parameter name} is not valid. The parameter value is not
part of the allowed values
```

Compruebe la plantilla para conocer los valores permitidos del parámetro y use un valor permitido durante la implementación. Para más información, consulte los valores permitidos para [Bicep](../bicep/parameters.md#allowed-values) o [las plantillas de Resource Manager](../templates/parameters.md#allowed-values).

<a id="too-many-resource-groups"></a>

## <a name="solution-4---too-many-target-resource-groups"></a>Solución 4: hay demasiados grupos de recursos de destino

Puede que vea este error en implementaciones anteriores porque estaba limitado a cinco grupos de recursos de destino en una sola implementación. En mayo de 2020 ese límite se ha aumentado a 800 grupos de recursos. Para más información, consulte cómo realizar la implementación en varios grupos de recursos de [Bicep](../bicep/deploy-to-resource-group.md#deploy-to-multiple-resource-groups) o [plantillas de Resource Manager](../templates/deploy-to-resource-group.md#deploy-to-multiple-resource-groups).

<a id="circular-dependency"></a>

## <a name="solution-5---circular-dependency-detected"></a>Solución 5: se detectó una dependencia circular

Este error se recibe cuando los recursos dependen unos de otros de una forma que impide que se inicie la implementación. Una combinación de interdependencias hace que dos o más recursos esperen otros recursos que también están esperando. Por ejemplo, resource1 depende de resource3, resource2 depende de resource1 y resource3 depende de resource2. Para resolver este problema, normalmente se eliminan las dependencias innecesarias.

Bicep crea una dependencia implícita cuando un recurso usa el nombre simbólico de otro recurso. Normalmente, no necesario una dependencia explícita que use `dependsOn`. Para más información, consulte las [dependencias](../bicep/resource-declaration.md#dependencies) de Bicep.

Para resolver una dependencia circular:

1. En la plantilla, busque el recurso identificado en la dependencia circular.
1. Para ese recurso, examine la propiedad `dependsOn` y todos los usos de la función `reference` para ver de qué recursos depende.
1. Examine esos recursos para ver de qué recursos dependen. Siga las dependencias hasta que observe un recurso que dependa del recurso original.
1. En el caso de los recursos que intervienen en la dependencia circular, examine meticulosamente todos los usos de la propiedad `dependsOn` para identificar las dependencias que no sean necesarias. Quite esas dependencias. Si no está seguro de si una dependencia es necesaria, pruebe a quitarla.
1. Vuelva a implementar la plantilla.

La eliminación de valores de la propiedad `dependsOn` puede provocar que aparezcan errores al implementar la plantilla. Si recibe un error, vuelva a agregar la dependencia a la plantilla.

Si con ese enfoque no se soluciona la dependencia circular, considere la posibilidad de mover parte de la lógica de implementación a recursos secundarios (por ejemplo, extensiones o valores de configuración). Configure esos recursos secundarios para que se implementen después de los recursos que intervienen en la dependencia circular. Por ejemplo, suponga que va a implementar dos máquinas virtuales pero debe establecer propiedades en cada una que hagan referencia a la otra. Puede implementarlas en el orden siguiente:

1. vm1
1. vm2
1. La extensión en vm1 depende de vm1 y vm2. La extensión establece valores en vm1 que obtiene de vm2.
1. La extensión en vm2 depende de vm1 y vm2. La extensión establece valores en vm2 que obtiene de vm1.

El mismo enfoque funciona para las aplicaciones de App Service. Considere la posibilidad de mover los valores de configuración a un recurso secundario del recurso de aplicación. Puede implementar dos aplicaciones web en el orden siguiente:

1. webapp1
1. webapp2
1. La configuración de webapp1 depende de webapp1 y webapp2. Contiene la configuración de aplicación con valores de webapp2.
1. La configuración de webapp2 depende de webapp1 y webapp2. Contiene la configuración de aplicación con valores de webapp1.
