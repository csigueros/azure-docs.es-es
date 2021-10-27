---
title: Uso de Bicep linter
description: Aprenda a usar Bicep linter.
ms.topic: conceptual
ms.date: 10/12/2021
ms.openlocfilehash: 45d077245e2efb677348d3355ff10d8ff3668b2f
ms.sourcegitcommit: 01dcf169b71589228d615e3cb49ae284e3e058cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/19/2021
ms.locfileid: "130161614"
---
# <a name="use-bicep-linter"></a>Uso de Bicep linter

La herramienta linter de Bicep comprueba los archivos de Bicep en busca de errores de sintaxis e infracciones de procedimientos recomendados. Linter ayuda a aplicar estándares de codificación mediante las instrucciones que proporciona durante el desarrollo. Puede personalizar los procedimientos recomendados que se van a usar para comprobar el archivo.

## <a name="linter-requirements"></a>Requisitos de linter

La herramienta linter se integra en la CLI de Bicep y la extensión de Bicep para Visual Studio Code. Para usarlo, debe disponer de la versión **0.4 o posterior**.

## <a name="default-rules"></a>Reglas predeterminadas

El conjunto predeterminado de reglas de linter es mínimo y se obtiene de los [casos de prueba de arm-ttk](../templates/template-test-cases.md): La extensión y la CLI de Bicep comprueban las reglas siguientes, que se establecen en el nivel de advertencia.

- [adminusername-should-not-be-literal](./linter-rule-admin-username-should-not-be-literal.md)
- [no-hardcoded-env-urls](./linter-rule-no-hardcoded-environment-urls.md)
- [no-unused-params](./linter-rule-no-unused-parameters.md)
- [no-unused-vars](./linter-rule-no-unused-variables.md)
- [prefer-interpolation](./linter-rule-prefer-interpolation.md)
- [secure-parameter-default](./linter-rule-secure-parameter-default.md)
- [simplify-interpolation](./linter-rule-simplify-interpolation.md)

Puede personalizar cómo se aplican las reglas de linter. Para sobrescribir la configuración predeterminada, agregue un archivo **bicepconfig.json** y aplique la configuración personalizada. Para obtener más información sobre cómo aplicar dicha configuración, consulte [Agregar opciones personalizadas en el archivo de configuración de Bicep](bicep-config.md).

## <a name="use-in-visual-studio-code"></a>Uso en Visual Studio Code

En la captura de pantalla siguiente se muestra linter en Visual Studio Code:

:::image type="content" source="./media/linter/bicep-linter-show-errors.png" alt-text="Uso de Bicep linter en Visual Studio Code":::

En el panel **PROBLEMS**, hay cuatro errores, una advertencia y un mensaje informativo, como se muestra en la captura de pantalla.  El mensaje informativo muestra el archivo de configuración de Bicep que se usa. Solo muestra este fragmento de información cuando se establece **verbose** en **true** en el archivo de configuración.

Mantenga el cursor del mouse sobre una de las áreas problemáticas. Linter proporciona los detalles sobre el error o la advertencia. Seleccione el área, también muestra una bombilla azul:

:::image type="content" source="./media/linter/bicep-linter-show-quickfix.png" alt-text="Uso de Bicep linter en Visual Studio Code: mostrar la corrección rápida":::

Seleccione la bombilla o el vínculo **Corrección rápida** para ver la solución:

:::image type="content" source="./media/linter/bicep-linter-show-quickfix-solution.png" alt-text="Uso de Bicep linter en Visual Studio Code: mostrar solución de la corrección rápida":::

Seleccione la solución para corregir el problema automáticamente.

## <a name="use-in-bicep-cli"></a>Uso de la CLI de Bicep

En la captura de pantalla siguiente se muestra la herramienta linter en la línea de comandos. La salida del comando de compilación muestra si se ha infringido alguna regla.

:::image type="content" source="./media/linter/bicep-linter-command-line.png" alt-text="Uso de Bicep linter en la línea de comandos":::

Puede integrar estas comprobaciones como parte de las canalizaciones de CI/CD. Puede usar una acción GitHub para intentar realizar una compilación de Bicep. Los errores producirán un error en las canalizaciones.

## <a name="next-steps"></a>Pasos siguientes

* Para obtener más información sobre cómo personalizar las reglas de linter, consulte [Agregar opciones personalizadas en el archivo de configuración de Bicep](bicep-config.md).
* Para obtener más información sobre el uso de Visual Studio Code y la extensión Bicep, consulte [Inicio rápido: Creación de plantillas de archivos de Bicep con Visual Studio Code](./quickstart-create-bicep-use-visual-studio-code.md).
