---
title: Más desarrollo de aplicaciones y menos administración de la infraestructura
description: Azure sin servidor le ayuda a centrarse en crear aplicaciones basadas en la nube y, al mismo tiempo, gastar menos dinero en administrar la infraestructura al usar Azure Logic Apps y Azure Functions.
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, azla
ms.topic: conceptual
ms.date: 07/15/2021
ms.openlocfilehash: 335a553897f2487eef317852f23cd475a92b2f81
ms.sourcegitcommit: 47ac63339ca645096bd3a1ac96b5192852fc7fb7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/16/2021
ms.locfileid: "114362686"
---
# <a name="azure-serverless-overview-create-cloud-based-apps-and-solutions-with-azure-logic-apps-and-azure-functions"></a>Introducción a Azure sin servidor: creación de soluciones y aplicaciones basadas en la nube con Azure Logic Apps y Azure Functions

Azure sin servidor le ayuda a gastar menos dinero en administrar la infraestructura, si bien la expresión "sin servidor" no significa que no haya servidores. En el desarrollo de aplicaciones tradicional, puede dedicar mucho tiempo y energía a analizar y abordar soluciones de hospedaje, escalado y supervisión para satisfacer los requisitos y las demandas de las aplicaciones. Con las aplicaciones y soluciones sin servidor, puede controlar estas cuestiones más fácilmente como parte de la aplicación o la solución. Las ofertas sin servidor incluyen otras ventajas, como un desarrollo más rápido, menos código, simplicidad y flexibilidad de escalado. Todas estas funcionalidades le permiten centrarse más en la lógica de negocios. Además, las ofertas sin servidor suelen facturarse o cobrarse en función del uso. Por lo tanto, si no se produce ningún consumo, no se incurre en cargos. Descubra en qué consiste [Azure sin servidor](https://azure.microsoft.com/solutions/serverless/) para obtener más información.

En este artículo se resumen las principales ofertas sin servidor de Azure, es decir, Azure Logic Apps y Azure Functions. Ambos servicios se alinean con los principios descritos anteriormente y ayudan a crear aplicaciones y soluciones sólidas en la nube con un código mínimo.

Para obtener información introductoria, visite las páginas de Azure dedicadas a [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/) y [Azure Functions](https://azure.microsoft.com/services/functions/). Si le interesa consultar información más detallada, revise las páginas de documentación [¿Qué es Azure Logic Apps?](logic-apps-overview.md) e [Introducción a Azure Functions](../azure-functions/functions-overview.md).

## <a name="azure-logic-apps"></a>Azure Logic Apps

Este servicio ofrece formas simplificadas de diseñar, desarrollar y orquestar flujos de trabajo de integración automatizados y controlados por eventos que se ejecutan y se escalan en la nube. Con Azure Logic Apps, puede usar un diseñador visual para modelar rápidamente los procesos empresariales como flujos de trabajo. Un flujo de trabajo siempre comienza con un desencadenador como primer paso. Después del desencadenador, una o varias acciones ejecutan operaciones posteriores en el flujo de trabajo. Estas operaciones pueden incluir varias combinaciones de acciones, incluida lógica condicional y conversiones de datos.

Para conectar los flujos de trabajo a otros servicios de Azure, servicios de Microsoft, entornos basados en la nube y entornos locales sin necesidad de escribir código, puede agregar acciones y desencadenadores precompilados a los flujos de trabajo que podrá elegir entre [cientos de conectores](/connectors/connector-reference/connector-reference-logicapps-connectors/), todos ellos administrados por Microsoft. Cada conector es en realidad un proxy o contenedor en torno a una API, lo que permite que el servicio subyacente se comunique con Azure Logic Apps. Por ejemplo, el desencadenador de Office 365 Outlook ofrece un desencadenador denominado **Cuando llega un nuevo correo electrónico**. En el caso de las aplicaciones y las soluciones sin servidor, puede usar Azure Logic Apps para orquestar varias funciones creadas en Azure Functions. De este modo, puede llamar fácilmente a varias funciones como un único proceso, en particular cuando el proceso requiere trabajar con una API o un sistema externos.

Si no hay ningún conector disponible que satisfaga sus necesidades, puede usar la operación HTTP integrada o el desencadenador de solicitud para comunicarse con cualquier punto de conexión de servicio. También puede crear su propio conector mediante una API existente.

En función del tipo de recurso de aplicación lógica que elija, el flujo de trabajo asociado se ejecuta en Azure Logic Apps multiinquilino, en Azure Logic Apps de un solo inquilino o en un entorno de servicio de integración (ISE) dedicado. Cada uno tiene sus propias funcionalidades, ventajas y modelos de facturación. Azure Portal proporciona la manera más rápida de empezar a crear flujos de trabajo de aplicación lógica. Aun así, también puede usar otras herramientas, como Visual Studio Code, Visual Studio, Azure PowerShell y otras. Para obtener más información, consulte [¿Qué es Azure Logic Apps?](logic-apps-overview.md)

Para empezar a trabajar con Azure Logic Apps, pruebe un [inicio rápido para crear su primer flujo de trabajo de aplicación lógica en Azure Portal](quickstart-create-first-logic-app-workflow.md). También puede seguir estos [pasos para crear una aplicación sin servidor de ejemplo con Azure Logic Apps y Azure Functions en Visual Studio](create-serverless-apps-visual-studio.md).

Para obtener más información, revise la siguiente documentación:

* [¿Qué es Azure Logic Apps?](logic-apps-overview.md)
* [Conectores para Azure Logic Apps](../connectors/apis-list.md)
* [Conectores: Azure Logic Apps, Microsoft Power Automate y Microsoft Power Apps](/connectors/connectors)
* [Comparación de las opciones de un solo inquilino, multiinquilino y entorno del servicio de integración para Azure Logic Apps](single-tenant-overview-compare.md)
* [Modelos de medición de uso, facturación y precios para Azure Logic Apps](logic-apps-pricing.md)

## <a name="azure-functions"></a>Azure Functions

Este servicio proporciona una manera simplificada de escribir y ejecutar fragmentos de código o *funciones* en la nube. Puede escribir únicamente el código que necesita para el problema actual, sin necesidad de configurar una aplicación completa o la infraestructura necesaria, lo que hace que el desarrollo sea más rápido y productivo. Use el lenguaje de desarrollo elegido, como C#, Java, JavaScript, PowerShell, Python y TypeScript. Solo se le facturará por el tiempo durante el que se ejecute el código y Azure se escalará según sea necesario.

Para empezar a trabajar con Azure Functions, pruebe a [crear su primera función de Azure en Azure Portal](../azure-functions/functions-create-function-app-portal.md).

Para obtener más información, revise la siguiente documentación:

* [¿Qué es Azure Functions?](../azure-functions/functions-overview.md)
* [Introducción a Azure Functions](../azure-functions/functions-get-started.md)
* [Lenguajes admitidos en Azure Functions](../azure-functions/supported-languages.md)
* [Opciones de hospedaje de Azure Functions](../azure-functions/functions-scale.md)
* [Precios de Azure Functions](../azure-functions/pricing.md)

## <a name="get-started-with-serverless-apps-in-azure"></a>Empiece a usar aplicaciones sin servidor en Azure

Azure proporciona un gran número de herramientas para desarrollar, implementar y administrar aplicaciones sin servidor. Puede crear aplicaciones sin servidor mediante Azure Portal, Visual Studio o [Visual Studio Code](quickstart-create-logic-apps-visual-studio-code.md). Después de compilar la aplicación, [puede implementarla rápidamente con las plantillas de Azure Resource Manager](logic-apps-deploy-azure-resource-manager-templates.md). Azure proporciona supervisión, a la que se puede acceder desde Azure Portal, la API o los SDK, o bien con herramientas integradas para los registros de Azure Monitor y Application Insights.

## <a name="next-steps"></a>Pasos siguientes

* [Creación de una aplicación sin servidor de ejemplo con Azure Logic Apps y Azure Functions en Visual Studio](create-serverless-apps-visual-studio.md)
* [Creación de un panel de streaming de Customer Insights con Azure Logic Apps y Azure Functions](logic-apps-scenario-social-serverless.md)