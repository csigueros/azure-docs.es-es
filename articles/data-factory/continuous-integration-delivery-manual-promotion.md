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
ms.openlocfilehash: 367a0b7f231fcdd88a28237e83916995fd58062b
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2021
ms.locfileid: "129219375"
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

1. En la sección de configuración, escriba los valores de configuración, como las credenciales del servicio vinculado. Cuando haya terminado, seleccione **Comprar** para implementar la plantilla de Resource Manager.

   :::image type="content" source="media/continuous-integration-delivery/continuous-integration-image5.png" alt-text="Sección de configuración":::

## <a name="next-steps"></a>Pasos siguientes

- [Información general de integración y entrega continuas](continuous-integration-delivery.md)
- [Automatización de la integración continua mediante versiones de Azure Pipelines](continuous-integration-delivery-automate-azure-pipelines.md)
- [Uso de parámetros personalizados con una plantilla de Resource Manager](continuous-integration-delivery-resource-manager-custom-parameters.md)
- [Plantillas vinculadas de Resource Manager](continuous-integration-delivery-linked-templates.md)
- [Uso de un entorno de producción de revisión](continuous-integration-delivery-hotfix-environment.md)
- [Script de muestra anterior y posterior a la implementación](continuous-integration-delivery-sample-script.md)