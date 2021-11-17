---
title: Promoción manual de plantillas de Resource Manager
description: Aprenda a promover manualmente una plantilla de Resource Manager para varios entornos con integración y entrega continuas en Azure Data Factory.
ms.service: data-factory
ms.subservice: ci-cd
author: nabhishek
ms.author: abnarain
ms.reviewer: jburchel
ms.topic: conceptual
ms.date: 09/24/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 485ace0d04375a406c8013d60d45635a596964a1
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/11/2021
ms.locfileid: "132319532"
---
# <a name="manually-promote-a-resource-manager-template-to-each-environment"></a>Promoción manual de una plantilla de Resource Manager para cada entorno

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Siga los pasos a continuación a fin de promover una plantilla de Resource Manager para cada entorno con integración y entrega continuas en Azure Data Factory.

## <a name="steps-to-manually-promote-a-template"></a>Pasos para promover manualmente una plantilla

1. Vaya al centro **Administrar** en la factoría de datos y seleccione **Plantilla de ARM** en la sección "Control de código fuente". En la lista **Plantilla de ARM**, seleccione **Exportar plantilla de ARM** para exportar la plantilla de Resource Manager de la factoría de datos en el entorno de desarrollo.

   :::image type="content" source="media/continuous-integration-delivery/continuous-integration-image-1.png" alt-text="Exportación de una plantilla de Resource Manager":::

1. En las factorías de datos de prueba y de producción, seleccione **Import ARM Template** (Importar plantilla de ARM). Esta acción abrirá Azure Portal, donde puede importar la plantilla exportada. Seleccione **Cree su propia plantilla en el editor** para abrir el editor de plantillas de Resource Manager.

   :::image type="content" source="media/continuous-integration-delivery/custom-deployment-build-your-own-template.png" alt-text="Creación de una plantilla propia"::: 

1. Seleccione **Cargar archivo** y después la plantilla de Resource Manager generada. Este es el archivo **arm_template.json** que se encuentra en el archivo ZIP exportado en el paso 1.

   :::image type="content" source="media/continuous-integration-delivery/custom-deployment-edit-template.png" alt-text="Editar plantilla":::

1. En la sección de configuración, escriba los valores de configuración, como las credenciales del servicio vinculado, necesarios para la implementación. Cuando haya terminado, seleccione **Revisar y crear** para implementar la plantilla de Resource Manager.

   :::image type="content" source="media/continuous-integration-delivery/continuous-integration-image5.png" alt-text="Sección de configuración":::

## <a name="next-steps"></a>Pasos siguientes

- [Información general de integración y entrega continuas](continuous-integration-delivery.md)
- [Automatización de la integración continua mediante versiones de Azure Pipelines](continuous-integration-delivery-automate-azure-pipelines.md)
- [Uso de parámetros personalizados con una plantilla de Resource Manager](continuous-integration-delivery-resource-manager-custom-parameters.md)
- [Plantillas vinculadas de Resource Manager](continuous-integration-delivery-linked-templates.md)
- [Uso de un entorno de producción de revisión](continuous-integration-delivery-hotfix-environment.md)
- [Script anterior y posterior a la implementación](continuous-integration-delivery-sample-script.md)