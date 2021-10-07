---
title: Uso de un entorno de producción de revisión
description: Obtenga información sobre cómo usar un entorno de producción de revisión con integración y entrega continuas en canalizaciones de Azure Data Factory.
ms.service: data-factory
ms.subservice: ci-cd
author: nabhishek
ms.author: abnarain
ms.reviewer: jburchel
ms.topic: conceptual
ms.date: 09/24/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 788b576d351984c4f6a3dff7fd43056aa95aba3c
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2021
ms.locfileid: "129219387"
---
# <a name="using-a-hotfix-production-environment"></a>Uso de un entorno de producción de revisión

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Si implementa una factoría en producción y se da cuenta de que hay un error que se debe corregir de inmediato, pero no puede implementar la rama de colaboración actual, es posible que deba implementar una revisión. Este enfoque se conoce como ingeniería de corrección rápida o QFE.

## <a name="steps-to-deploy-a-hotfix"></a>Pasos para implementar una revisión

Siga estos pasos para implementar una revisión en los entornos de producción y prueba.

1.    En Azure DevOps, vaya a la versión que se ha implementado en producción. Busque la última confirmación que se ha implementado.

1.    En el mensaje de confirmación, obtenga el identificador de confirmación de la rama de colaboración.

1.    Cree una rama de revisión a partir de esa confirmación.

1.    Vaya a Azure Data Factory Studio y cambie a la rama de revisión.

1.    Mediante Azure Data Factory Studio, corrija el error. Guarde los cambios.

1.    Una vez comprobada la corrección, seleccione **Export ARM Template** (Exportar plantilla de ARM) para obtener la plantilla de Resource Manager de revisión.

1.    Inserte manualmente esta compilación en la rama adf_publish.

1.    Si ha configurado la canalización de versión para que se desencadene automáticamente en función de las inserciones en el repositorio de adf_publish, se iniciará una versión nueva de forma automática. De lo contrario, ponga en cola manualmente una versión.

1.    Implemente la versión de revisión en las factorías de prueba y producción. Esta versión contiene la carga de producción anterior más la revisión realizada en el paso 5.

1.   Agregue los cambios de la revisión a la rama de desarrollo para que las versiones posteriores no incluyan el mismo error.

## <a name="video-tutorial"></a>Tutorial en vídeo
Vea el siguiente vídeo de un tutorial detallado sobre cómo corregir de modo urgente los entornos. 

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4I7fi]

## <a name="next-steps"></a>Pasos siguientes

- [Información general de integración y entrega continuas](continuous-integration-delivery.md)
- [Automatización de la integración continua mediante versiones de Azure Pipelines](continuous-integration-delivery-automate-azure-pipelines.md)
- [Promoción manual de una plantilla de Resource Manager para cada entorno](continuous-integration-delivery-manual-promotion.md)
- [Uso de parámetros personalizados con una plantilla de Resource Manager](continuous-integration-delivery-resource-manager-custom-parameters.md)
- [Plantillas vinculadas de Resource Manager](continuous-integration-delivery-linked-templates.md)
- [Script de ejemplo anterior y posterior a la implementación](continuous-integration-delivery-sample-script.md)