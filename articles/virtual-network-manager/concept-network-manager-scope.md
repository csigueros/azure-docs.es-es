---
title: Comprender y trabajar con ámbitos de Azure Virtual Network Manager
description: Obtenga información sobre los ámbitos de Azure Virtual Network Manager y los efectos que tienen en la administración de redes virtuales.
author: duongau
ms.author: duau
ms.service: virtual-network-manager
ms.topic: conceptual
ms.date: 11/02/2021
ms.custom: template-concept, ignite-fall-2021
ms.openlocfilehash: e21cfe528c3cba9c190ba667552deb7070884aa3
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131093105"
---
# <a name="understand-and-work-with-azure-virtual-network-manager-preview-scopes"></a>Comprender y trabajar con ámbitos de Azure Virtual Network Manager (versión preliminar)

En este artículo, aprenderá cómo usa Azure Virtual Network Manager el concepto de *ámbito* para permitir que los grupos de administración o las suscripciones usen determinadas características de Virtual Network Manager. También obtendrá información sobre la *jerarquía* y cómo esta puede afectar a los usuarios de Virtual Network Manager. 

> [!IMPORTANT]
> Azure Virtual Network Manager se encuentra actualmente en versión preliminar pública.
> Esta versión preliminar se ofrece sin Acuerdo de Nivel de Servicio y no se recomienda para cargas de trabajo de producción. Es posible que algunas características no sean compatibles o que tengan sus funcionalidades limitadas.
> Para más información, consulte [Términos de uso complementarios de las Versiones Preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="network-manager"></a>Network Manager

*Network Manager* es el objeto de nivel superior que consta de recursos secundarios, como *grupos de red*, *configuraciones* y *reglas*. 

* **Grupos de red**: son un subconjunto del ámbito general al que se pueden aplicar directivas específicas de administración de seguridad o conectividad.

* **Configuraciones**: Azure Virtual Network Manager proporciona dos tipos de configuraciones, una configuración de conectividad y otra de seguridad. Las configuraciones de conectividad le permiten crear topologías de red, mientras que las configuraciones de seguridad le permiten crear una colección de reglas que se pueden aplicar entre redes virtuales.

* Reglas: una colección de reglas es un conjunto de reglas de seguridad de red que pueden permitir o denegar el tráfico de red en el nivel global de las redes virtuales. 

> [!NOTE]
> Azure Virtual Network Manager requiere que se quiten todos los recursos secundarios antes de eliminarlos.
>

## <a name="scope"></a>Ámbito

Un *ámbito* de Azure Virtual Network Manager es un conjunto de recursos a los que se pueden aplicar características. Al especificar un ámbito, está limitando el acceso según el cual Network Manager puede administrar los recursos. El valor del ámbito puede estar en el nivel del grupo de administración o en el nivel de suscripción. Consulte [Grupos de administración de Azure](../governance/management-groups/overview.md) para obtener información sobre cómo administrar la jerarquía de recursos. Al seleccionar un grupo de administración como ámbito, todos los recursos secundarios se incluyen dentro del ámbito. 

> [!NOTE]
> No se admite la creación de varios administradores de red con un ámbito superpuesto de la misma jerarquía.
> 

## <a name="features"></a>Características

Las características son el acceso de ámbito que permite administrar Azure Virtual Network Manager. Azure Virtual Network Manager tiene actualmente dos ámbitos de características, que son *Connectivity* y *SecurityAdmin*. Puede habilitar ambos ámbitos de características en la misma instancia de Virtual Network Manager. Para obtener más información sobre cada característica, consulte [Connectivity](concept-connectivity-configuration.md) y [SecurityAdmin](concept-security-admins.md).

> [!NOTE]
> Las características solo se habilitan durante la implementación de Azure Virtual Network Manager. Si implementa una instancia de Virtual Network Manager con un solo ámbito de características, deberá volver a implementar una nueva instancia de Azure Virtual Network Manager para habilitar ambas características.
>

## <a name="hierarchy"></a>Hierarchy

Azure Virtual Network Manager le permite realizar la administración de los recursos de red en una jerarquía. Una jerarquía significa que puede tener varias instancias de Virtual Network Manager para administrar los ámbitos superpuestos; igualmente, las configuraciones de cada instancia de Virtual Network Manager también se pueden superponer entre sí. Por ejemplo, puede tener el [grupo de administración](../governance/management-groups/overview.md) de nivel superior de una instancia de Virtual Network Manager y tener un grupo de administración secundario como ámbito para una instancia de Virtual Network Manager diferente. Si tiene un conflicto de configuración entre diferentes instancias de Virtual Network Manager que contienen el mismo recurso. La configuración de la instancia de Virtual Network Manager que tenga el ámbito superior será la que se aplique.

## <a name="next-steps"></a>Pasos siguientes

- Obtenga información sobre cómo crear una instancia de [Azure Virtual Network Manager](create-virtual-network-manager-portal.md).
- Obtenga información sobre los [grupos de redes](concept-network-groups.md).
