---
title: Archivo de configuración de Bicep
description: Describe el archivo de configuración para las implementaciones de Bicep.
ms.topic: conceptual
ms.date: 11/16/2021
ms.openlocfilehash: 8679daf65402c939f9eea7651b57b1198970fb8d
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/16/2021
ms.locfileid: "132548147"
---
# <a name="configure-your-bicep-environment"></a>Configuración del entorno de Bicep

Bicep admite un archivo de configuración denominado **bicepconfig.json**. Dentro de este archivo, puede agregar valores que personalicen la experiencia de desarrollo de Bicep. Si no agrega este archivo, Bicep usará los valores predeterminados.

Para personalizar los valores, cree este archivo en el directorio donde almacena los archivos de Bicep. Puede agregar archivos bicepconfig.json en varios directorios. Se usa el archivo de configuración más cercano de la jerarquía de directorios.

## <a name="available-settings"></a>Opciones de configuración disponibles

Al trabajar con [módulos](modules.md), puede agregar alias para las rutas de acceso de los módulos. Estos alias simplifican el archivo de Bicep porque no tiene que repetir rutas de acceso complicadas. También puede configurar la precedencia de credenciales para la autenticación en el registro. La credencial se usa para restaurar módulos externos en la memoria caché local. Para más información, consulte [Agregar la configuración del módulo a la configuración de Bicep](bicep-config-modules.md).

Cuando trabaja con [Bicep linter](linter.md) puede invalidar la configuración predeterminada de la validación de archivos de Bicep. Para más información, consulte [Agregar la configuración de linter a la configuración de Bicep](bicep-config-linter.md).

## <a name="intellisense"></a>Intellisense

La extensión de Bicep para Visual Studio Code admite Intellisense para el archivo **bicepconfig.json**. Uso de IntelliSense para detectar las propiedades y valores disponibles.

:::image type="content" source="./media/bicep-config/bicep-linter-configure-intellisense.png" alt-text="Compatibilidad con IntelliSense para configurar bicepconfig.json":::

## <a name="next-steps"></a>Pasos siguientes

* [Adición de la configuración del módulo en la configuración de Bicep](bicep-config-modules.md)
* [Adición de la configuración de linter a la configuración de Bicep](bicep-config-linter.md)
* Más información acerca de [Bicep linter](linter.md)
