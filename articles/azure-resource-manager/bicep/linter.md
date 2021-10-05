---
title: Uso de Bicep linter
description: Aprenda a usar Bicep linter.
ms.topic: conceptual
ms.date: 09/10/2021
ms.openlocfilehash: cef701d9a9f64990c0afbe265c3355f9c1a850ce
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128631179"
---
# <a name="use-bicep-linter"></a>Uso de Bicep linter

Bicep linter se puede usar para analizar archivos Bicep. Permite buscar errores de sintaxis e infracciones de los procedimientos recomendados antes de compilar o implementar el archivo Bicep. Puede personalizar el conjunto de procedimientos recomendados de creación que se van a usar para comprobar el archivo. Linter facilita la aplicación de estándares de codificación proporcionando instrucciones durante el desarrollo.

## <a name="linter-requirements"></a>Requisitos de linter

Linter está integrado en la CLI de Bicep y la extensión VS Code. Para usarlo, debe disponer de la versión 0.4 o posterior.

## <a name="customize-linter"></a>Personalización de linter

Con bicepconfig.json, puede habilitar o deshabilitar linter, proporcionar valores específicos de la regla y establecer también el nivel de reglas. En el ejemplo siguiente se muestra el archivo bicepconfig.json predeterminado:

```json
{
  "analyzers": {
    "core": {
      "verbose": false,
      "enabled": true,
      "rules": {
        "no-hardcoded-env-urls": {
          "level": "warning",
          "disallowedhosts": [
            "management.core.windows.net",
            "gallery.azure.com",
            "management.core.windows.net",
            "management.azure.com",
            "database.windows.net",
            "core.windows.net",
            "login.microsoftonline.com",
            "graph.windows.net",
            "trafficmanager.net",
            "vault.azure.net",
            "datalake.azure.net",
            "azuredatalakestore.net",
            "azuredatalakeanalytics.net",
            "vault.azure.net",
            "api.loganalytics.io",
            "api.loganalytics.iov1",
            "asazure.windows.net",
            "region.asazure.windows.net",
            "api.loganalytics.iov1",
            "api.loganalytics.io",
            "asazure.windows.net",
            "region.asazure.windows.net",
            "batch.core.windows.net"
          ],
          "excludedhosts": [
            "schema.management.azure.com"
          ]
        }
      }
    }
  }
}
```

El archivo bicepconfig.json personalizado se puede colocar junto con las plantillas en el mismo directorio. Se usa el archivo de configuración más cercano que se encuentra en el árbol de carpetas.

El siguiente código json es un ejemplo de bicepconfig.json:

```json
{
  "analyzers": {
    "core": {
      "enabled": true,
      "verbose": true,
      "rules": {
        "no-hardcoded-env-urls": {
          "level": "warning"
        },
        "no-unused-params": {
          "level": "error"
        },
        "no-unused-vars": {
          "level": "error"
        },
        "prefer-interpolation": {
          "level": "warning"
        },
        "secure-parameter-default": {
          "level": "error"
        },
        "simplify-interpolation": {
          "level": "warning"
        }
      }
    }
  }
}
```

- **enabled**: escriba **true** para habilitar linter, o bien escriba **false** para deshabilitarlo.
- **verbose**: escriba **true** para mostrar el archivo bicepconfig.json empleado por Visual Studio Code.
- **rules**: escriba valores específicos de la regla. Cada regla tiene al menos una propiedad y un nivel. Esta propiedad ordena el comportamiento de Bicep si el caso se encuentra en el archivo Bicep.

Puede usar varios valores para el nivel de regla:

| **level**  | **Comportamiento en tiempo de compilación** | **Comportamiento del editor** |
|--|--|--|
| `Error` | Las infracciones aparecen como Errores en la salida de compilación de la línea de comandos, e impiden que las compilaciones se lleven a cabo. | El código incorrecto se subraya en rojo y aparece en la pestaña Problemas. |
| `Warning` | Las infracciones aparecen como Advertencias en la salida de compilación de la línea de comandos, pero no provocan un error de compilación. | El código incorrecto se subraya en amarillo y aparece en la pestaña Problemas. |
| `Info` | Las infracciones no aparecen en la salida de compilación de la línea de comandos. | El código incorrecto se subraya en azul y aparece en la pestaña Problemas. |
| `Off` | Se suprime por completo. | Se suprime por completo. |

El conjunto actual de reglas de linter es mínimo y se obtiene de los [casos de prueba arm-ttk](../templates/template-test-cases.md). Tanto la extensión de Visual Studio Code como la CLI de Bicep comprueban todas las reglas disponibles de forma predeterminada, y todas las reglas se establecen en el nivel de advertencia. En función del nivel de una regla, verá errores, advertencias o mensajes informativos en el editor.

- [no-hardcoded-env-urls](./linter-rule-no-hardcoded-environment-urls.md)
- [no-unused-params](./linter-rule-no-unused-parameters.md)
- [no-unused-vars](./linter-rule-no-unused-variables.md)
- [prefer-interpolation](./linter-rule-prefer-interpolation.md)
- [secure-parameter-default](./linter-rule-secure-parameter-default.md)
- [simplify-interpolation](./linter-rule-simplify-interpolation.md)

La extensión Bicep de Visual Studio Code ofrece IntelliSense para editar archivos de configuración de Bicep:

:::image type="content" source="./media/linter/bicep-linter-configure-intellisense.png" alt-text="Compatibilidad con IntelliSense para configurar bicepconfig.json":::

## <a name="use-in-visual-studio-code"></a>Uso en Visual Studio Code

Instale la extensión Bicep 0.4 o posterior para usar linter.  En la captura de pantalla siguiente se muestra linter en acción:

:::image type="content" source="./media/linter/bicep-linter-show-errors.png" alt-text="Uso de Bicep linter en Visual Studio Code":::

En el panel **PROBLEMS**, hay cuatro errores, una advertencia y un mensaje informativo, como se muestra en la captura de pantalla.  El mensaje informativo muestra el archivo de configuración de Bicep que se usa. Solo muestra este fragmento de información cuando se establece **verbose** en **true** en el archivo de configuración.

Mantenga el cursor del mouse sobre una de las áreas problemáticas. Linter proporciona los detalles sobre el error o la advertencia. Seleccione el área, también muestra una bombilla azul:

:::image type="content" source="./media/linter/bicep-linter-show-quickfix.png" alt-text="Uso de Bicep linter en Visual Studio Code: mostrar la corrección rápida":::

Seleccione la bombilla o el vínculo **Corrección rápida** para ver la solución:

:::image type="content" source="./media/linter/bicep-linter-show-quickfix-solution.png" alt-text="Uso de Bicep linter en Visual Studio Code: mostrar solución de la corrección rápida":::

Seleccione la solución para corregir el problema automáticamente.

## <a name="use-in-bicep-cli"></a>Uso de la CLI de Bicep

Instale la CLI de Bicep 0.4 o posterior para usar linter.  En la captura de pantalla siguiente se muestra linter en acción. El archivo Bicep es el mismo que se utilizó en [Uso en Visual Studio Code](#use-in-visual-studio-code).

:::image type="content" source="./media/linter/bicep-linter-command-line.png" alt-text="Uso de Bicep linter en la línea de comandos":::

Puede integrar estas comprobaciones como parte de las canalizaciones de CI/CD. Puede usar una acción GitHub para intentar realizar una compilación de Bicep. Los errores producirán un error en las canalizaciones.

## <a name="next-steps"></a>Pasos siguientes

Para obtener más información sobre el uso de Visual Studio Code y la extensión Bicep, consulte [Inicio rápido: Creación de plantillas de archivos de Bicep con Visual Studio Code](./quickstart-create-bicep-use-visual-studio-code.md).
