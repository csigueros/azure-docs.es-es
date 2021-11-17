---
title: ¿Qué es Azure Virtual Network Manager (versión preliminar)?
description: Obtenga información sobre cómo Azure Virtual Network Manager puede simplificar la administración y escalabilidad de las redes virtuales.
services: virtual-network-manager
author: duongau
ms.service: virtual-network-manager
ms.topic: overview
ms.date: 11/02/2021
ms.author: duau
ms.custom: references_regions, ignite-fall-2021
ms.openlocfilehash: 81603472d4a5a254fe86f2a6866d8a7edcbbef88
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2021
ms.locfileid: "131459641"
---
# <a name="what-is-azure-virtual-network-manager-preview"></a>¿Qué es Azure Virtual Network Manager (versión preliminar)?

Azure Virtual Network Manager es un servicio de administración que le permite agrupar, configurar, implementar y administrar redes virtuales globalmente entre suscripciones. Con Virtual Network Manager, puede definir grupos de redes para identificar y segmentar lógicamente las redes virtuales. Después, puede determinar las configuraciones de conectividad y seguridad que desee y aplicarlas en todas las redes virtuales seleccionadas de los grupos de red a la vez. 

> [!IMPORTANT]
> Azure Virtual Network Manager se encuentra actualmente en versión preliminar pública.
> Esta versión preliminar se ofrece sin Acuerdo de Nivel de Servicio y no se recomienda para cargas de trabajo de producción. Es posible que algunas características no sean compatibles o que tengan sus funcionalidades limitadas.
> Para más información, consulte [Términos de uso complementarios de las Versiones Preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="how-does-azure-virtual-network-manager-work"></a>¿Cómo funciona Azure Virtual Network Manager?

:::image type="content" source="./media/overview/management-group.png" alt-text="Diagrama del grupo de administración en Virtual Network Manager.":::

Durante el proceso de creación, puede definir el ámbito de lo que Azure Virtual Network Manager administrará. La definición de un ámbito requiere la creación de un [grupo de administración](../governance/management-groups/overview.md). Después de definir el ámbito, puede habilitar características como *Conectividad* y el rol *SecurityAdmin* para Virtual Network Manager.

Después de implementar la instancia de Virtual Network Manager, cree un *grupo de red* mediante instrucciones condicionales para seleccionar redes virtuales por nombre, etiquetas o identificadores (pertenencia dinámica). También puede seleccionar redes virtuales específicas (pertenencia estática). Las reglas definidas para el grupo de red se reflejan en Azure Policy como una definición de iniciativa personalizada y la asignación correspondiente que ilustra las reglas definidas para la pertenencia a la red virtual. Para más información sobre las iniciativas de Azure Policy, consulte [Estructura de iniciativas de Azure Policy](../governance/policy/concepts/initiative-definition-structure.md). Estas directivas están disponibles actualmente en modo de solo lectura. Para más información sobre cómo crear, actualizar y eliminar estas directivas, consulte [Grupos de red y Azure Policy](concept-network-groups.md#network-group-and-azure-policy). Después, cree configuraciones de seguridad o conectividad aplicadas a esos grupos de red en función de sus necesidades de topología y seguridad. 

Una configuración de conectividad le permite crear una malla o una topología de red en estrella tipo hub-and-spoke. Una configuración de seguridad permite definir una colección de reglas que puede aplicar a uno o varios grupos de red en el nivel global. Una vez que haya creado los grupos y configuraciones de red deseados, puede implementar las configuraciones en cualquier región de su elección.

## <a name="key-benefits"></a>Ventajas principales

* Administre de forma centralizada las directivas de conectividad y seguridad globalmente en regiones y suscripciones.

* Habilite la comunicación transitiva entre radios en una configuración radial sin la complejidad de administrar una red de malla.

* Servicio altamente escalable y de alta disponibilidad con redundancia y replicación en todo el mundo.

* Capacidad para crear reglas de seguridad de red globales que invaliden las reglas del grupo de seguridad de red.

* Baja latencia y ancho de banda alto entre los recursos de diferentes redes virtuales mediante el emparejamiento de redes virtuales.

* Implemente los cambios de red a través de una secuencia de región específica y la frecuencia que elija.

## <a name="public-preview-regions"></a>Regiones con versión preliminar pública

* Centro-Norte de EE. UU

* Oeste de EE. UU.

* Oeste de EE. UU. 2

* Este de EE. UU.

* Este de EE. UU. 2

* Norte de Europa

* Oeste de Europa

* Centro de Francia

## <a name="next-steps"></a>Pasos siguientes

- Cree una instancia de [Azure Virtual Network Manager](create-virtual-network-manager-portal.md) con Azure Portal.
- Obtenga más información sobre los [grupos de seguridad](concept-network-groups.md) en Azure Virtual Network Manager.
- Obtenga información sobre lo que puede hacer con una [configuración de conectividad](concept-connectivity-configuration.md).
- Obtenga más información sobre las [configuraciones de administración de seguridad](concept-security-admins.md).
