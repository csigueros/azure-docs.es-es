---
title: Detección de Bicep en Microsoft Learn
description: Proporciona información general sobre las unidades de Bicep que hay disponibles en Microsoft Learn.
ms.topic: conceptual
ms.date: 08/08/2021
ms.openlocfilehash: a0459e44c4abd69810bcc70974ea2e160adf5a5e
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121739734"
---
# <a name="bicep-on-microsoft-learn"></a>Bicep en Microsoft Learn

Para obtener instrucciones paso a paso sobre el uso de Bicep para implementar su infraestructura en Azure, Microsoft Learn ofrece varios módulos de aprendizaje.

## <a name="introductory-path"></a>Ruta de introducción

La ruta de aprendizaje [Implementación y administración de recursos en Azure mediante Bicep](/learn/paths/bicep-deploy/) es el mejor lugar para empezar. Presenta el concepto de infraestructura como código. La ruta le guía por los pasos necesarios para crear archivos Bicep cada vez más complejos.

Esta ruta contiene los siguientes módulos.

| Módulo de Learn | Descripción |
| ------------ | ----------- |
| [Introducción a la infraestructura como código mediante Bicep](/learn/modules/introduction-to-infrastructure-as-code-using-bicep/) | En este módulo se describen las ventajas de usar la infraestructura como código, Azure Resource Manager y Bicep para escalar de forma rápida y segura las implementaciones en la nube. Ayuda a determinar los tipos de implementaciones para las que Bicep es una buena herramienta de implementación. |
| [Compilación de la primera plantilla de Bicep](/learn/modules/build-first-bicep-template/) | En este módulo, definirá los recursos de Azure dentro de una plantilla de Bicep. Mejore la coherencia y confiabilidad de sus implementaciones, reduzca el esfuerzo manual necesario y escale las implementaciones en los distintos entornos. La plantilla será flexible y reutilizable gracias al uso de parámetros, variables, expresiones y módulos. |
| [Creación de plantillas de Bicep reutilizables mediante parámetros](/learn/modules/build-reusable-bicep-templates-parameters/) | En este módulo se describe cómo puede usar los parámetros de Bicep para proporcionar información a la plantilla durante cada implementación. Aprenderá sobre los decoradores de parámetros, que hacen que sea fácil entender los parámetros y trabajar con ellos. También aprenderá las distintas formas en que puede proporcionar valores de parámetro y protegerlos cuando trabaje con información segura. |
| [Creación de plantillas flexibles de Bicep mediante condiciones y bucles](/learn/modules/build-flexible-bicep-templates-conditions-loops/) | Aprenda a usar condiciones para implementar recursos solo cuando se cumplan determinadas restricciones. Aprenda también a usar bucles para implementar varios recursos que tienen propiedades similares. |
| [Implementación de recursos secundarios y de extensión mediante Bicep](/learn/modules/child-extension-bicep-templates/) | En este módulo, se muestra cómo implementar una variedad de recursos de Azure en el código de Bicep. Obtenga información sobre los recursos secundarios y de extensión, y cómo se pueden definir y usar en Bicep. Use Bicep para trabajar con recursos creados fuera de una plantilla o módulo de Bicep. |
| [Implementación de recursos en suscripciones, grupos de administración e inquilinos mediante Bicep](/learn/modules/deploy-resources-scopes-bicep/) | Implemente recursos de Azure en el ámbito de suscripción, grupo de administración e inquilino. Descubra qué son estos recursos, por qué los usaría y cómo crear código Bicep para implementarlos. Aprenda también a crear un único conjunto de archivos Bicep que puede implementar en varios ámbitos en una sola operación. |
| [Extensión de plantillas mediante scripts de implementación](/learn/modules/extend-resource-manager-template-deployment-scripts/) | Aprenda a agregar pasos personalizados al archivo Bicep o a la plantilla de Azure Resource Manager (plantilla de ARM) mediante scripts de implementación. |

## <a name="other-modules"></a>Otros módulos

Además de la ruta anterior, los módulos siguientes incluyen contenido de Bicep.

| Módulo de Learn | Descripción |
| ------------ | ----------- |
| [Administración de cambios en el código de Bicep mediante Git](/learn/modules/manage-changes-bicep-code-git/) | Aprenda a usar Git para permitir el flujo de trabajo de desarrollo de Bicep mediante el seguimiento de los cambios que realiza mientras trabaja. Aquí va a averiguar cómo confirmar archivos, cómo ver el historial de los archivos que ha cambiado y cómo usar ramas para desarrollar varias versiones del código al mismo tiempo. También va a aprender a usar GitHub o Azure Repos para publicar un repositorio para poder colaborar con los miembros de un equipo. |
| [Publicación de bibliotecas de código de infraestructura reutilizable mediante especificaciones de plantilla](/learn/modules/arm-template-specs/) | Las especificaciones de plantilla permiten reutilizar y compartir las plantillas de ARM en toda la organización. Obtenga información sobre cómo crear y publicar especificaciones de plantilla y cómo implementarlas. También aprenderá a administrar las especificaciones de plantilla, incluido cómo controlar el acceso y cómo actualizarlas de forma segura mediante versiones. |
| [Vista previa de los cambios de implementación de Azure mediante el uso de what-if](/learn/modules/arm-template-whatif/) | En este módulo, se muestra cómo obtener una vista previa de los cambios con la operación what-if. Con what-if, puede asegurarse de que el archivo Bicep solo haga los cambios esperados. |
| [Estructura del código de Bicep para la colaboración](/learn/modules/structure-bicep-code-collaboration/) | Cree archivos Bicep que admitan el desarrollo colaborativo y siga los procedimientos recomendados. Planee los parámetros para facilitar la implementación de las plantillas. Use un estilo coherente, una estructura clara y comentarios para que el código de Bicep sea fácil de entender, usar y modificar. |
| [Autenticación de la canalización de implementación de Azure mediante entidades de servicio](/learn/modules/authenticate-azure-deployment-pipeline-service-principals/) | Las entidades de servicio permiten que las canalizaciones de implementación se autentiquen de forma segura con Azure. En este módulo, aprenderá qué son las entidades de servicio, cómo funcionan y cómo crearlas. También aprenderá a concederles permiso a los recursos de Azure para que las canalizaciones puedan implementar los archivos de Bicep. |
| [Cree la primera canalización de implementación de Bicep mediante Azure Pipelines](/learn/modules/build-first-bicep-deployment-pipeline-using-azure-pipelines/) | Cree una canalización de implementación básica para el código de Bicep. Use una conexión de servicio para identificar de forma segura la canalización en Azure. Configure cuándo se debe ejecutar la canalización mediante desencadenadores. |

## <a name="next-steps"></a>Pasos siguientes

* Para obtener una breve introducción a Bicep, consulte el [inicio rápido de Bicep](quickstart-create-bicep-use-visual-studio-code.md).
* Para obtener sugerencias sobre cómo mejorar los archivos de Bicep, consulte los [procedimientos recomendados para Bicep](best-practices.md).
