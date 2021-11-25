---
title: Errores de plantilla no válida
description: Describe cómo resolver errores de plantilla no válida al implementar plantillas de Azure Resource Manager.
ms.topic: troubleshooting
ms.date: 11/11/2021
ms.openlocfilehash: 2565a68b63e23ecd81338c0bfbbdc36878c1a95a
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/12/2021
ms.locfileid: "132401922"
---
# <a name="resolve-errors-for-invalid-template"></a>Resolución de errores de plantilla no válida

Ese artículo describe cómo resolver errores de plantilla no válida.

## <a name="symptom"></a>Síntoma

Al implementar una plantilla, recibe un error que indica:

```
Code=InvalidTemplate
Message=<varies>
```

El mensaje de error depende del tipo de error.

## <a name="cause"></a>Causa

Este error puede tener distintos orígenes. Normalmente implican un error estructural o de sintaxis en la plantilla.

<a id="syntax-error"></a>

## <a name="solution-1---syntax-error"></a>Solución 1: error de sintaxis

Si recibe un mensaje de error que indica que la plantilla no superó la validación, puede que esta tenga un problema de sintaxis.

```
Code=InvalidTemplate
Message=Deployment template validation failed
```

Es fácil cometer este error porque las expresiones de plantilla pueden ser complejas. Por ejemplo, la siguiente asignación de nombre de una cuenta de almacenamiento contiene un conjunto de corchetes, tres funciones, tres conjuntos de paréntesis, un conjunto de comillas simples y una propiedad:

```json
"name": "[concat('storage', uniqueString(resourceGroup().id))]",
```

Si no proporciona la sintaxis coincidente, la plantilla genera un valor que es diferente del que se espera.

Si recibe este tipo de error, revise cuidadosamente la sintaxis de las expresiones. Considere la posibilidad de utilizar un editor JSON como [Visual Studio](../templates/create-visual-studio-deployment-project.md) o [Visual Studio Code](../templates/quickstart-create-templates-use-visual-studio-code.md) que puede avisarlo de los errores de sintaxis.

<a id="incorrect-segment-lengths"></a>

## <a name="solution-2---incorrect-segment-lengths"></a>Solución 2: longitudes de segmentos incorrectas

Cuando el nombre del recurso no tiene el formato correcto, se produce otro error de plantilla no válida. Para resolver ese error, consulte [Resolución de errores de no coincidencia de nombre y tipo](error-invalid-name-segments.md).

<a id="parameter-not-valid"></a>

## <a name="solution-3---parameter-isnt-valid"></a>Solución 3: el parámetro no es válido

Si proporciona un valor de parámetro que no es uno de los valores permitidos, recibe un mensaje de error similar al siguiente:

```
Code=InvalidTemplate;
Message=Deployment template validation failed: 'The provided value {parameter value}
for the template parameter {parameter name} is not valid. The parameter value is not
part of the allowed values
```

Compruebe los valores permitidos de la plantilla y proporcione uno durante la implementación. Para más información sobre los valores de parámetros permitidos, consulte [Sección de parámetros de plantillas de Azure Resource Manager](../templates/syntax.md#parameters).

<a id="too-many-resource-groups"></a>

## <a name="solution-4---too-many-target-resource-groups"></a>Solución 4: hay demasiados grupos de recursos de destino

Puede que vea este error en implementaciones anteriores porque estaba limitado a cinco grupos de recursos de destino en una sola implementación. En mayo de 2020 ese límite se ha aumentado a 800 grupos de recursos. Para más información, consulte [Implementación de recursos de Azure en varias suscripciones y grupos de recursos](../templates/deploy-to-resource-group.md).

<a id="circular-dependency"></a>

## <a name="solution-5---circular-dependency-detected"></a>Solución 5: se detectó una dependencia circular

Este error se recibe cuando los recursos dependen unos de otros de una forma que impide que se inicie la implementación. Una combinación de interdependencias hace que dos o más recursos esperen otros recursos que también están esperando. Por ejemplo, resource1 depende de resource3, resource2 depende de resource1 y resource3 depende de resource2. Para resolver este problema, normalmente se eliminan las dependencias innecesarias.

Para resolver una dependencia circular:

1. En la plantilla, busque el recurso identificado en la dependencia circular.
2. Para ese recurso, examine la propiedad **dependsOn** y todos los usos de la función **reference** para ver de qué recursos depende.
3. Examine esos recursos para ver de qué recursos dependen. Siga las dependencias hasta que observe un recurso que dependa del recurso original.
5. En el caso de los recursos que intervienen en la dependencia circular, examine con cuidado todos los usos de la propiedad **dependsOn** para identificar las dependencias que no sean necesarias. Quite esas dependencias. Si no está seguro de si una dependencia es necesaria, pruebe a quitarla.
6. Vuelva a implementar la plantilla.

Al quitar los valores de la propiedad **dependsOn**, se pueden provocar errores al implementar la plantilla. Si recibe un error, vuelva a agregar la dependencia a la plantilla.

Si con ese enfoque no se soluciona la dependencia circular, considere la posibilidad de mover parte de la lógica de implementación a recursos secundarios (por ejemplo, extensiones o valores de configuración). Configure esos recursos secundarios para que se implementen después de los recursos que intervienen en la dependencia circular. Por ejemplo, suponga que va a implementar dos máquinas virtuales pero debe establecer propiedades en cada una que hagan referencia a la otra. Puede implementarlas en el orden siguiente:

1. vm1
2. vm2
3. La extensión en vm1 depende de vm1 y vm2. La extensión establece valores en vm1 que obtiene de vm2.
4. La extensión en vm2 depende de vm1 y vm2. La extensión establece valores en vm2 que obtiene de vm1.

El mismo enfoque funciona para las aplicaciones de App Service. Considere la posibilidad de mover los valores de configuración a un recurso secundario del recurso de aplicación. Puede implementar dos aplicaciones web en el orden siguiente:

1. webapp1
2. webapp2
3. La configuración de webapp1 depende de webapp1 y webapp2. Contiene la configuración de aplicación con valores de webapp2.
4. La configuración de webapp2 depende de webapp1 y webapp2. Contiene la configuración de aplicación con valores de webapp1.
