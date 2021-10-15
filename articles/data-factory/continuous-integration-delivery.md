---
title: Integración y entrega continuas
description: Aprenda a usar la integración y la entrega continuas para mover canalizaciones de Azure Data Factory de un entorno (desarrollo, prueba o producción) a otro.
ms.service: data-factory
ms.subservice: ci-cd
author: nabhishek
ms.author: abnarain
ms.reviewer: jburchel
ms.topic: conceptual
ms.date: 09/29/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 21223cc29857b439f87231a5b4649ea9864e2202
ms.sourcegitcommit: 03e84c3112b03bf7a2bc14525ddbc4f5adc99b85
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/03/2021
ms.locfileid: "129400404"
---
# <a name="continuous-integration-and-delivery-in-azure-data-factory"></a>Integración y entrega continuas en Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

La integración continua es el procedimiento de probar cada cambio realizado en el código base automáticamente y tan pronto como sea posible.  La entrega continua sigue las pruebas realizadas durante la integración continua y envía los cambios a un sistema de ensayo o producción.

En Azure Data Factory, la integración y la entrega continuas (CI/CD) implican el traslado de canalizaciones de Data Factory de un entorno (desarrollo, prueba o producción) a otro. Azure Data Factory usa las [plantillas de Azure Resource Manager](../azure-resource-manager/templates/overview.md) para almacenar la configuración de las distintas entidades de ADF (canalizaciones, conjuntos de datos, flujos de datos, etc.). Se sugieren dos métodos para promover una factoría de datos a otro entorno:

-    Implementación automatizada mediante la integración de Data Factory con [Azure Pipelines](/azure/devops/pipelines/get-started/what-is-azure-pipelines).
-    Carga manual de una plantilla de Resource Manager mediante la integración de la experiencia de usuario de Data Factory con Azure Resource Manager.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="cicd-lifecycle"></a>Ciclo de vida de CI/CD

Aquí se muestra una descripción general de ejemplo del ciclo de vida de CI/CD en una factoría de datos de Azure configurada con Git de Azure Repos. Para más información sobre cómo configurar un repositorio Git, vea [Control de código fuente en Azure Data Factory](source-control.md).

1.  Se crea una factoría de datos de desarrollo y se configura con GIT de Azure Repos. Todos los desarrolladores deben tener permiso para crear recursos de Data Factory como canalizaciones y conjuntos de usuarios.

1.  Un desarrollador [crea una rama de características](source-control.md#creating-feature-branches) para realizar un cambio. Depuran sus ejecuciones de canalización con los cambios más recientes. Para más información sobre cómo depurar una ejecución de canalización, consulte [Desarrollo y depuración iterativos con Azure Data Factory](iterative-development-debugging.md).

1.  Cuando un desarrollador está satisfecho con los cambios, crea una solicitud de incorporación de cambios desde la rama de características a la rama principal o de colaboración para que otros equipos del mismo nivel revisen sus cambios.

1.  Después de que la solicitud de incorporación de cambios se haya aprobado y los cambios se hayan combinado en la rama principal, los cambios se publican en la fábrica de desarrollo.

1.  Cuando el equipo está listo para implementar los cambios en una fábrica UAT (pruebas de aceptación de usuario), se dirige a su versión de Azure Pipelines e implementa la versión deseada de la fábrica de desarrollo en UAT. Esta implementación tiene lugar como parte de una tarea de Azure Pipelines y usa los parámetros de plantilla de Resource Manager para aplicar la configuración adecuada.

1.  Una vez comprobados los cambios en la fábrica de pruebas, realice la implementación en la fábrica de producción mediante la siguiente tarea de versión de canalizaciones.

> [!NOTE]
> Solo la fábrica de desarrollo está asociada a un repositorio de Git. Las fábricas de pruebas y producción no deben tener un repositorio de Git asociado y solo deben actualizarse a través de una canalización de Azure DevOps o de una plantilla de Resource Manager.

En la imagen siguiente se resaltan los distintos pasos de este ciclo de vida.

:::image type="content" source="media/continuous-integration-delivery/continuous-integration-image12.png" alt-text="Diagrama de integración continua con Azure Pipelines":::

## <a name="best-practices-for-cicd"></a>Procedimientos recomendados para CI/CD

Si usa la integración de Git con la factoría de datos y tiene una canalización de CI/CD que mueve los cambios desde el entorno de desarrollo al de prueba y, luego, al de producción, los procedimientos recomendados son los siguientes:

-   **Integración de Git**. Configure solo la factoría de datos de desarrollo con la integración de Git. Los cambios en los entornos de prueba y producción se implementan a través de CI/CD y no se necesita la integración Git.

-   **Script anterior y posterior a la implementación**. Antes del paso de implementación de Resource Manager en CI/CD, debe completar ciertas tareas, como detener y reiniciar los desencadenadores y realizar la limpieza. Se recomienda usar scripts de PowerShell antes y después de la tarea de implementación. Para más información, vea [Actualización de desencadenadores activos](continuous-integration-delivery-automate-azure-pipelines.md#updating-active-triggers). El equipo de Data Factory ha [proporcionado el script](continuous-integration-delivery-sample-script.md) que se va a usar y que se encuentra en la parte inferior de esta página.

-   **Entornos de ejecución de integración y uso compartido**. Los entornos de ejecución de integración no cambian a menudo y son similares en todas las fases de CI/CD. Por tanto, Data Factory espera que el usuario tenga el mismo nombre y tipo de entorno de ejecución de integración en todas las etapas de CI/CD. Si quiere compartir entornos de ejecución de integración en todas las fases, considere la posibilidad de usar una factoría ternaria solo para contener los entornos de ejecución de integración compartidos. Puede usar esta factoría compartida en todos los entornos como un tipo de entorno de ejecución de integración vinculado.

-   **Implementación de un punto de conexión privado administrado**. Si ya existe un punto de conexión privado en una fábrica y se intenta implementar una plantilla de Resource Manager que contiene un punto de conexión privado con el mismo nombre pero con propiedades modificadas, se produce un error en la implementación. Es decir, se puede implementar correctamente un punto de conexión privado siempre que tenga las mismas propiedades que el que ya existe en la fábrica. Si alguna propiedad difiere entre entornos, se puede invalidar si se parametriza esa propiedad y se proporciona el valor respectivo durante la implementación.

-   **Key Vault**. Cuando se usan servicios vinculados cuya información de conexión se almacena en Azure Key Vault, se recomienda mantener almacenes de claves independientes para entornos diferentes. También puede configurar niveles de permisos independientes para cada almacén de claves. Por ejemplo, es posible que no quiera que los miembros del equipo tengan permisos para ver los secretos de producción. Si sigue este enfoque, se recomienda mantener los mismos nombres de secreto en todas las fases. Si mantiene los mismos nombres de secreto, no es necesario parametrizar cada cadena de conexión en los entornos de CI/CD, porque lo único que cambia es el nombre del almacén de claves, que es un parámetro independiente.

-  **Nomenclatura de recursos**. Debido a las restricciones de las plantillas de ARM, pueden surgir problemas en la implementación si los recursos contienen espacios en el nombre. Para los recursos, el equipo de Azure Data Factory recomienda usar los caracteres "_" o "-" en lugar de espacios. Por ejemplo, "Canalización_1" sería un nombre preferible en lugar de "Pipeline 1".

- **Control de exposición y marcas de características**.  Cuando se trabaja en equipo, hay casos en los que se pueden combinar los cambios, pero no quiere que se ejecuten en entornos con privilegios elevados, como PROD y QA. Para abordar este escenario, el equipo de ADF recomienda [el concepto de DevOps de uso de marcas de características](/azure/devops/migrate/phase-features-with-feature-flags). En ADF, puede combinar [parámetros globales](author-global-parameters.md) y la [actividad de condición if](control-flow-if-condition-activity.md) para ocultar conjuntos de lógica basados en estas marcas de entorno.

    Para información sobre cómo configurar una marca de características, vea el siguiente tutorial en vídeo:

>[!VIDEO https://www.microsoft.com/videoplayer/embed/RE4IxdW]

## <a name="unsupported-features"></a>Características no admitidas

- Por diseño, Data Factory no permite la selección exclusiva de confirmaciones ni la publicación selectiva de recursos. Las publicaciones incluirán todos los cambios realizados en la factoría de datos.

    - Las entidades de Data Factory dependen unas de otras. Por ejemplo, los desencadenadores dependen de las canalizaciones y las canalizaciones dependen de los conjuntos de datos y otras canalizaciones. La publicación selectiva de un subconjunto de recursos podría provocar comportamientos y errores inesperados.
    - En casos excepcionales, cuando necesite la publicación selectiva, considere la posibilidad de usar una revisión. Para más información, vea [Entorno de producción de revisión](continuous-integration-delivery-hotfix-environment.md).

- El equipo de Azure Data Factory no recomienda asignar controles de RBAC de Azure a entidades individuales (canalizaciones, conjuntos de datos, etc.) de una factoría de datos. Por ejemplo, si un desarrollador tiene acceso a una canalización o un conjunto de datos, debe poder acceder a todas las canalizaciones o conjuntos de datos de la factoría de datos. Si cree que necesita implementar muchos roles de Azure en una factoría de datos, estudie la implementación de una segunda factoría de datos.

-   No es posible publicar desde ramas privadas.

-   En la actualidad no se pueden hospedar proyectos en Bitbucket.

-   Actualmente no se pueden exportar ni importar alertas y matrices como parámetros. 

- En el repositorio de código de la rama *adf_publish*, se agrega actualmente una carpeta denominada "PartialArmTemplates" junto a la carpeta "linkedTemplates", los archivos "ARMTemplateForFactory.json" y "ARMTemplateParametersForFactory.json" como parte de la publicación con control de código fuente. 

    :::image type="content" source="media/continuous-integration-delivery/partial-arm-templates-folder.png" alt-text="Diagrama de la carpeta &quot;PartialArmTemplates&quot;.":::

    A partir del 1 de noviembre de 2021, se dejará de publicar "PartialArmTemplates" en la rama *adf_publish*.    

    **No se requiere ninguna acción a menos que se use "PartialArmTemplates". De lo contrario, cambie a cualquier mecanismo compatible para las implementaciones mediante los archivos "ARMTemplateForFactory.json" o "linkedTemplates".**

## <a name="next-steps"></a>Pasos siguientes

- [Automatización de la integración continua mediante versiones de Azure Pipelines](continuous-integration-delivery-automate-azure-pipelines.md)
- [Promoción manual de una plantilla de Resource Manager para cada entorno](continuous-integration-delivery-manual-promotion.md)
- [Uso de parámetros personalizados con una plantilla de Resource Manager](continuous-integration-delivery-resource-manager-custom-parameters.md)
- [Plantillas vinculadas de Resource Manager](continuous-integration-delivery-linked-templates.md)
- [Uso de un entorno de producción de revisión](continuous-integration-delivery-hotfix-environment.md)
- [Script anterior y posterior a la implementación](continuous-integration-delivery-sample-script.md)
