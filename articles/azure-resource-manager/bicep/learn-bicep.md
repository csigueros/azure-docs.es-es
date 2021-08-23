---
title: Detección de Bicep en Microsoft Learn
description: Proporciona información general sobre las unidades de Bicep que hay disponibles en Microsoft Learn.
ms.topic: conceptual
ms.date: 06/11/2021
ms.openlocfilehash: 0238741f4ce6a246c9fd4c8279a55beb494e52ad
ms.sourcegitcommit: 942a1c6df387438acbeb6d8ca50a831847ecc6dc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/11/2021
ms.locfileid: "112017854"
---
# <a name="bicep-in-microsoft-learn"></a>Bicep en Microsoft Learn

Para obtener instrucciones paso a paso sobre el uso de Bicep para implementar su infraestructura en Azure, Microsoft Learn ofrece varios módulos de aprendizaje.

## <a name="introductory-path"></a>Ruta de introducción

La ruta de aprendizaje [Implementación y administración de recursos en Azure mediante Bicep](/learn/paths/bicep-deploy/) es el mejor lugar para empezar. Presenta el concepto de infraestructura como código. La ruta le guía por los pasos necesarios para crear archivos Bicep cada vez más complejos.

Esta ruta contiene los siguientes módulos.

| Módulo de Learn | Descripción |
| ------------ | ----------- |
| [Introducción a la infraestructura como código mediante Bicep](/learn/modules/introduction-to-infrastructure-as-code-using-bicep/) | En este módulo se describen las ventajas de usar la infraestructura como código, Azure Resource Manager y Bicep para escalar de forma rápida y segura las implementaciones en la nube. Ayuda a determinar los tipos de implementaciones para las que Bicep es una buena herramienta de implementación. |
| [Compilación de la primera plantilla de Bicep](/learn/modules/deploy-azure-resources-by-using-bicep-templates/) | En este módulo, definirá los recursos de Azure dentro de una plantilla de Bicep. Mejore la coherencia y confiabilidad de sus implementaciones, reduzca el esfuerzo manual necesario y escale las implementaciones en los distintos entornos. La plantilla será flexible y reutilizable gracias al uso de parámetros, variables, expresiones y módulos. |
| [Creación de plantillas de Bicep reutilizables mediante parámetros](/learn/modules/build-reusable-bicep-templates-parameters/) | En este módulo se describe cómo puede usar los parámetros de Bicep para proporcionar información a la plantilla durante cada implementación. Aprenderá sobre los decoradores de parámetros, que hacen que sea fácil entender los parámetros y trabajar con ellos. También aprenderá las distintas formas en que puede proporcionar valores de parámetro y protegerlos cuando trabaje con información segura. |
| [Creación de plantillas flexibles de Bicep mediante condiciones y bucles](/learn/modules/build-flexible-bicep-templates-conditions-loops/) | Aprenda a usar condiciones para implementar recursos solo cuando se cumplan determinadas restricciones. Aprenda también a usar bucles para implementar varios recursos que tienen propiedades similares. |
| [Implementación de recursos secundarios y de extensión mediante Bicep](/learn/modules/child-extension-bicep-templates/) | En este módulo, se muestra cómo implementar una variedad de recursos de Azure en el código de Bicep. Obtenga información sobre los recursos secundarios y de extensión, y cómo se pueden definir y usar en Bicep. Use Bicep para trabajar con recursos creados fuera de una plantilla o módulo de Bicep. |
| [Implementación de recursos en suscripciones, grupos de administración e inquilinos mediante Bicep](/learn/modules/deploy-resources-scopes-bicep/) | Implemente recursos de Azure en el ámbito de suscripción, grupo de administración e inquilino. Descubra qué son estos recursos, por qué los usaría y cómo crear código Bicep para implementarlos. Aprenda también a crear un único conjunto de archivos Bicep que puede implementar en varios ámbitos en una sola operación. |
| [Extensión de plantillas mediante scripts de implementación](/learn/modules/extend-resource-manager-template-deployment-scripts/) | Aprenda a agregar pasos personalizados al archivo Bicep o a la plantilla de Azure Resource Manager (plantilla de ARM) mediante scripts de implementación. |

## <a name="other-modules"></a>Otros módulos

Además de la ruta anterior, el módulo siguiente incluye contenido de Bicep.

| Módulo de Learn | Descripción |
| ------------ | ----------- |
| [Vista previa de los cambios de implementación de Azure mediante el uso de what-if](/learn/modules/arm-template-whatif/) | En este módulo, se muestra cómo obtener una vista previa de los cambios con la operación what-if. Con what-if, puede asegurarse de que el archivo Bicep solo haga los cambios esperados. |

## <a name="next-steps"></a>Pasos siguientes

* Para obtener una breve introducción a Bicep, consulte el [inicio rápido de Bicep](quickstart-create-bicep-use-visual-studio-code.md).
* Para obtener sugerencias sobre cómo mejorar los archivos de Bicep, consulte los [procedimientos recomendados para Bicep](best-practices.md).
