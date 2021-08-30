---
title: Uso de Bicep linter
description: Aprenda a usar Bicep linter.
ms.topic: conceptual
ms.date: 07/01/2021
ms.openlocfilehash: 82ab1462ca55a4329370d185aed626ae631e2f2b
ms.sourcegitcommit: d2738669a74cda866fd8647cb9c0735602642939
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/13/2021
ms.locfileid: "113649440"
---
# <a name="use-bicep-linter"></a>Uso de Bicep linter

Bicep linter se puede usar para analizar archivos Bicep. Comprueba los errores de sintaxis y detecta un conjunto personalizable de procedimientos recomendados de creación antes de compilar o implementar los archivos Bicep. Linter facilita la aplicación de estándares de codificación proporcionando instrucciones durante el desarrollo.

## <a name="install-linter"></a>Instalación de linter

Linter se puede usar con Visual Studio Code y la CLI de Bicep. Tiene los siguientes requisitos:

- CLI de Bicep, versión 0.4 o posterior.
- Extensión de Bicep para Visual Studio Code, versión 0.4 o posterior.

## <a name="customize-linter"></a>Personalización de linter

Con bicepconfig.json, puede habilitar o deshabilitar linter, proporcionar valores específicos de la regla y establecer también el nivel de reglas. A continuación, se muestra el archivo bicepconfig.json predeterminado:

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
- **verbose**: escriba **true** para mostrar el archivo bicepconfig.json utilizado por Visual Studio Code.
- **rules**: escriba valores específicos de la regla. Cada regla tiene al menos una propiedad y un nivel. Esta propiedad ordena el comportamiento de Bicep si el caso se encuentra en el archivo Bicep.

Puede usar varios valores para el nivel de regla:

| **level**  | **Comportamiento en tiempo de compilación** | **Comportamiento del editor** |
|--|--|--|
| `Error` | Las infracciones aparecen como Errores en la salida de compilación de la línea de comandos, e impiden que las compilaciones se lleven a cabo. | El código incorrecto se subraya en rojo y aparece en la pestaña Problemas. |
| `Warning` | Las infracciones aparecen como Advertencias en la salida de compilación de la línea de comandos, pero no impiden que las compilaciones se lleven a cabo. | El código incorrecto se subraya en amarillo y aparece en la pestaña Problemas. |
| `Info` | Las infracciones no aparecen en la salida de compilación de la línea de comandos. | El código incorrecto se subraya en azul y aparece en la pestaña Problemas. |
| `Off` | Se suprime por completo. | Se suprime por completo. |

El conjunto actual de reglas de linter es mínimo y se obtiene de los [casos de prueba arm-ttk](../templates/template-test-cases.md). Tanto la extensión de Visual Studio Code como la CLI de Bicep comprueban todas las reglas disponibles de forma predeterminada, y todas las reglas se establecen en el nivel de advertencia. En función del nivel de una regla, verá errores, advertencias o mensajes informativos en el editor.

- [no-hardcoded-env-urls](https://github.com/Azure/bicep/blob/main/docs/linter-rules/no-hardcoded-env-urls.md)
- [no-unused-params](https://github.com/Azure/bicep/blob/main/docs/linter-rules/no-unused-params.md)
- [no-unused-vars](https://github.com/Azure/bicep/blob/main/docs/linter-rules/no-unused-vars.md)
- [prefer-interpolation](https://github.com/Azure/bicep/blob/main/docs/linter-rules/prefer-interpolation.md)
- [secure-parameter-default](https://github.com/Azure/bicep/blob/main/docs/linter-rules/secure-parameter-default.md)
- [simplify-interpolation](https://github.com/Azure/bicep/blob/main/docs/linter-rules/simplify-interpolation.md)

La extensión Bicep de Visual Studio Code ofrece IntelliSense para editar archivos de configuración de Bicep:

:::image type="content" source="./media/linter/bicep-linter-configure-intellisense.png" alt-text="Compatibilidad con IntelliSense para configurar bicepconfig.json":::

## <a name="use-in-visual-studio-code"></a>Uso en Visual Studio Code

Instale la extensión Bicep 0.4 o posterior para usar linter.  En la captura de pantalla siguiente se muestra linter en acción:

:::image type="content" source="./media/linter/bicep-linter-show-errors.png" alt-text="Uso de Bicep linter en Visual Studio Code":::

En el panel **PROBLEMS**, hay cuatro errores, una advertencia y un mensaje informativo, como se muestra en la captura de pantalla.  El mensaje informativo muestra el archivo de configuración de Bicep que se usa. Solo muestra este fragmento de información cuando se establece **verbose** en **true** en el archivo de configuración.

Mantenga el cursor del mouse sobre una de las áreas problemáticas. Linter proporciona los detalles sobre el error o la advertencia. Haga clic en el área y también mostrará una bombilla azul:

:::image type="content" source="./media/linter/bicep-linter-show-quickfix.png" alt-text="Uso de Bicep linter en Visual Studio Code: mostrar la corrección rápida":::

Seleccione la bombilla o el vínculo **Corrección rápida** para ver la solución:

:::image type="content" source="./media/linter/bicep-linter-show-quickfix-solution.png" alt-text="Uso de Bicep linter en Visual Studio Code: mostrar solución de la corrección rápida":::

Seleccione la solución para corregir el problema automáticamente.

## <a name="use-in-bicep-cli"></a>Uso de la CLI de Bicep

Instale la CLI de Bicep 0.4 o posterior para usar linter.  En la captura de pantalla siguiente se muestra linter en acción. El archivo Bicep es el mismo que se utilizó en [Uso en Visual Studio Code](#use-in-visual-studio-code).

:::image type="content" source="./media/linter/bicep-linter-command-line.png" alt-text="Uso de Bicep linter en la línea de comandos":::

Puede integrar estas comprobaciones como parte de las canalizaciones de CI/CD. Puede usar una acción GitHub para intentar realizar una compilación de Bicep. Los errores producirán un error en las canalizaciones.

## <a name="next-steps"></a>Pasos siguientes

Para obtener más información sobre el uso de Visual Studio Code y la extensión Bicep, consulte [Inicio rápido: Creación de plantillas de archivos de Bicep con Visual Studio Code](./quickstart-create-bicep-use-visual-studio-code.md).
