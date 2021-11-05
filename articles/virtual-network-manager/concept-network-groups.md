---
title: ¿Qué es un grupo de red en Azure Virtual Network Manager (versión preliminar)?
description: Obtenga información sobre cómo los grupos de redes pueden ayudarle a administrar las redes virtuales.
author: duongau
ms.author: duau
ms.service: virtual-network-manager
ms.topic: conceptual
ms.date: 11/02/2021
ms.custom: template-concept, ignite-fall-2021
ms.openlocfilehash: 82de5cc623193163aef3243c9c47a205bbbcffd7
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131091201"
---
# <a name="what-is-a-network-group-in-azure-virtual-network-manager-preview"></a>¿Qué es un grupo de red en Azure Virtual Network Manager (versión preliminar)?

En este artículo, obtendrá información sobre los *grupos de red* y cómo pueden ayudarle a agrupar redes virtuales para facilitar la administración. También obtendrá información sobre la *pertenencia a grupos estáticos* y la *pertenencia a grupos dinámicos* y cómo usar cada tipo de pertenencia.

> [!IMPORTANT]
> Azure Virtual Network Manager se encuentra actualmente en versión preliminar pública.
> Esta versión preliminar se ofrece sin Acuerdo de Nivel de Servicio y no se recomienda para cargas de trabajo de producción. Es posible que algunas características no sean compatibles o que tengan sus funcionalidades limitadas.
> Para más información, consulte [Términos de uso complementarios de las Versiones Preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="network-group"></a>Grupo de red

Un *grupo de red* es un conjunto de redes virtuales seleccionadas manualmente o mediante instrucciones condicionales. Cuando se seleccionan redes virtuales manualmente, se denominan *miembros estáticos*. Las redes virtuales que se seleccionan mediante instrucciones condicionales se denominan *miembros dinámicos*. 

## <a name="static-membership"></a>Pertenencia estática

Al crear un grupo de redes, puede agregar redes virtuales a un grupo seleccionando manualmente una red virtual individual de una lista proporcionada. La lista de redes virtuales depende del ámbito (suscripción o grupo de administración) definido en el momento de la implementación de Azure Virtual Network Manager. Este método es útil cuando tiene algunas redes virtuales que quiere agregar al grupo de red. Las actualizaciones de las configuraciones que contienen miembros estáticos tendrán que implementarse de nuevo para que se apliquen los nuevos cambios.

## <a name="dynamic-membership"></a>Pertenencia dinámica

La pertenencia dinámica ofrece la flexibilidad de seleccionar varias redes virtuales a la vez si cumplen las instrucciones condicionales definidas. Este método es útil para escenarios en los que tiene cientos o miles de redes virtuales en una o varias suscripciones y necesita seleccionar unas cuantas por nombre, identificadores o etiquetas. Cada condición se procesa en el orden indicado y las configuraciones se aplican a las redes virtuales para cumplir esas condiciones. Vea [Exclusión de elementos de la pertenencia dinámica](how-to-exclude-elements.md) para obtener información sobre cómo configurar instrucciones condicionales.

## <a name="network-group-and-azure-policy"></a>Grupo de red y directiva de Azure

Al crear un grupo de red, se crea una directiva de Azure para que Azure Virtual Network Manager reciba notificaciones sobre los cambios realizados en la pertenencia a la red virtual. Estas directivas definidas están disponibles para que las vea. Las directivas definidas están disponibles para que las vea, pero los usuarios no las pueden editar en la actualidad. Actualmente, la creación, el cambio y la eliminación de definiciones y asignaciones de directivas de Azure para grupos de red solo es posible a través de Azure Network Manager. 

Para crear una asignación y definición de iniciativa de Azure Policy para los recursos de Azure Network Manager, cree e implemente un grupo de red con las configuraciones necesarias. Para actualizar una definición de iniciativa de directiva de Azure existente o la asignación correspondiente, deberá cambiar e implementar los cambios en el grupo de red dentro del recurso de Azure Virtual Network Manager. Para eliminar una asignación y definición de iniciativa de directiva de Azure, deberá anular la implementación y eliminación de los recursos de Azure Virtual Network Manager asociados a la directiva. Esto puede incluir la anulación de la implementación de una configuración, su eliminación y la eliminación de un grupo de red. Para más información sobre la eliminación, revise la [lista de comprobación para quitar componentes](concept-remove-components-checklist.md) de Azure Virtual Network Manager.  

## <a name="next-steps"></a>Pasos siguientes

- Cree una instancia de [Azure Virtual Network Manager](create-virtual-network-manager-portal.md) con Azure Portal.
- Aprenda a crear una [topología en estrella de tipo hub-and-spoke](how-to-create-hub-and-spoke.md) con Azure Virtual Network Manager.
- Obtenga información sobre cómo bloquear el tráfico de red con una [configuración de administración de seguridad](how-to-block-network-traffic-portal.md).
