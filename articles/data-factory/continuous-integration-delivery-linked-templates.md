---
title: Plantillas vinculadas de Resource Manager
description: Aprenda a usar plantillas vinculadas de Resource Manager con la integración y entrega continuas en canalizaciones de Azure Data Factory.
ms.service: data-factory
ms.subservice: ci-cd
author: nabhishek
ms.author: abnarain
ms.reviewer: jburchel
ms.topic: conceptual
ms.date: 09/24/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: d2df62471818bbca069802f3c60993ce79bb50dd
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2021
ms.locfileid: "129219386"
---
# <a name="linked-resource-manager-templates-with-cicd"></a>Plantillas vinculadas de Resource Manager con CI/CD

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Si ha configurado la integración continua y entrega continua (CI/CD) para las factorías de datos, puede que supere los límites de la plantilla de Azure Resource Manager a medida que la fábrica crece más. Por ejemplo, un límite es el número máximo de recursos de una plantilla de Resource Manager. Para acomodar factorías más grandes mientras se genera la plantilla completa de Resource Manager para una factoría, ahora Data Factory genera plantillas de Resource Manager vinculadas. Con esta característica, la carga de la factoría completa se divide en varios archivos, para que no esté restringido por los límites.

## <a name="finding-the-linked-templates"></a>Búsqueda de las plantillas vinculadas

Si ha configurado Git, se generan las plantillas vinculadas y se guardan junto con las plantillas completas de Resource Manager en la rama adf_publish, en una carpeta nueva denominada linkedTemplates:

:::image type="content" source="media/continuous-integration-delivery/linked-resource-manager-templates.png" alt-text="Carpeta de plantillas vinculadas de Resource Manager":::

Normalmente, las plantillas vinculadas de Resource Manager tienen una plantilla principal y un conjunto de plantillas secundarias vinculadas a la principal. La plantilla principal se denomina ArmTemplate_master.json y las plantillas secundarias se denominan con el patrón ArmTemplate_0.json, ArmTemplate_1.json, etc. 

## <a name="using-linked-templates"></a>Uso de plantillas vinculadas
Para usar plantillas vinculadas en lugar de la plantilla de Resource Manager completa, actualice la tarea de CI/CD para que apunte a ArmTemplate_master.json en lugar de ArmTemplateForFactory.json (la plantilla de Resource Manager completa). Resource Manager también necesita que cargue las plantillas vinculadas en una cuenta de almacenamiento para que Azure pueda acceder a ellas durante la implementación. Para más información, vea [Implementación de plantillas vinculadas de Resource Manager con VSTS](/archive/blogs/najib/deploying-linked-arm-templates-with-vsts).

No se olvide de agregar los scripts de Data Factory en la canalización de CI/CD antes y después de la tarea de implementación.

Si no ha configurado Git, puede acceder a las plantillas vinculadas a través de **Export ARM Template** en la lista **Plantilla de ARM**.

Al implementar los recursos, debe especificar si la implementación es una actualización incremental o una actualización completa. La diferencia entre estos dos modos es la forma en que Resource Manager controla los recursos existentes en el grupo de recursos que no están en la plantilla. Revise [Modos de implementación](../azure-resource-manager/templates/deployment-modes.md).

## <a name="next-steps"></a>Pasos siguientes

- [Información general de integración y entrega continuas](continuous-integration-delivery.md)
- [Automatización de la integración continua mediante versiones de Azure Pipelines](continuous-integration-delivery-automate-azure-pipelines.md)
- [Promoción manual de una plantilla de Resource Manager para cada entorno](continuous-integration-delivery-manual-promotion.md)
- [Uso de parámetros personalizados con una plantilla de Resource Manager](continuous-integration-delivery-resource-manager-custom-parameters.md)
- [Uso de un entorno de producción de revisión](continuous-integration-delivery-hotfix-environment.md)
- [Script de ejemplo anterior y posterior a la implementación](continuous-integration-delivery-sample-script.md)