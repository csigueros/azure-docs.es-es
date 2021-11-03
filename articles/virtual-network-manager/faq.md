---
title: Preguntas más frecuentes sobre Azure Virtual Network Manager
description: Busque respuestas a las preguntas más frecuentes sobre Azure Virtual Network Manager.
services: virtual-network-manager
author: duongau
ms.service: virtual-network-manager
ms.topic: article
ms.date: 11/02/2021
ms.author: duau
ms.custom: references_regions, ignite-fall-2021
ms.openlocfilehash: f25a2f21713684024dcacee18e5666dfbb33a1b2
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131093666"
---
# <a name="azure-virtual-network-manager-faq"></a>Preguntas más frecuentes sobre Azure Virtual Network Manager

## <a name="general"></a>General

### <a name="what-regions-are-available-in-public-preview"></a>¿Qué regiones están disponibles en versión preliminar pública?

* Centro-Norte de EE. UU

* Oeste de EE. UU.

* Oeste de EE. UU. 2

* Este de EE. UU.

* Este de EE. UU. 2

* Norte de Europa

* Oeste de Europa

* Centro de Francia

### <a name="what-are-common-use-cases-for-using-azure-virtual-network-manager"></a>¿Cuáles son los casos de uso comunes para usar Azure Virtual Network Manager?

* Como administrador de seguridad de TI, puede crear grupos de red diferentes para satisfacer los requisitos del entorno y sus funciones. Por ejemplo, puede crear grupos de red para entornos de red de producción y pruebas, equipos de desarrollo, departamento financiero, etc., a fin de administrar sus reglas de conectividad y seguridad a gran escala. 

* Puede aplicar configuraciones de conectividad para crear una malla o una topología de red de concentrador y radio para un gran número de redes virtuales en las suscripciones de la organización. 

* Puede denegar el tráfico de alto riesgo: como administrador de una empresa, puede bloquear protocolos u orígenes específicos que invalidarán todas las reglas de NSG que normalmente permitirían el tráfico.   

* Permitir siempre el tráfico: quiere permitir que un analizador de seguridad específico siempre tenga conectividad entrante a todos los recursos, incluso si hay reglas de NSG configuradas para denegar el tráfico.   

## <a name="technical"></a>Técnico

### <a name="can-a-virtual-network-belong-to-multiple-azure-virtual-network-managers"></a>¿Puede una red virtual pertenecer a varias instancias de Azure Virtual Network Manager?

Sí, una red virtual puede pertenecer a más de una instancia de Azure Virtual Network Manager.

### <a name="what-is-a-global-mesh-network-topology"></a>¿Qué es una topología de red de malla global?

Una malla global permite que las redes virtuales de distintas regiones se comuniquen entre sí. Los efectos son similares a cómo funciona el emparejamiento de redes virtuales globales.

### <a name="is-there-a-limit-to-how-many-network-groups-can-be-created"></a>¿Existe un límite en cuanto el número de grupos de red que se pueden crear?

No existe ningún límite en cuanto el número de grupos de red que se pueden crear.

### <a name="how-do-i-remove-the-deployment-of-all-applied-configurations"></a>¿Cómo se quita la implementación de todas las configuraciones aplicadas?

Tendrá que implementar una configuración **Ninguna** en todas las regiones que tengan una configuración aplicada.

### <a name="can-i-add-virtual-networks-from-another-subscription-not-managed-by-myself"></a>¿Se pueden agregar redes virtuales desde otra suscripción que no administre yo personalmente?

Sí, deberá tener los permisos adecuados para acceder a esas redes virtuales.

### <a name="what-is-dynamic-group-membership"></a>¿Qué es la pertenencia dinámica a grupos?

Para más información, vea [pertenencia dinámica](concept-network-groups.md#dynamic-membership).

### <a name="how-does-the-deployment-of-configuration-differ-for-dynamic-membership-and-static-membership"></a>¿En qué se diferencia la implementación de la configuración para la pertenencia dinámica y la pertenencia estática?

Para más información, vea [Implementación en tipos de pertenencia](concept-deployments.md#deployment).

### <a name="how-do-i-delete-an-azure-virtual-network-manager-component"></a>¿Cómo se elimina un componente de Azure Virtual Network Manager?

Para más información, vea [lista de comprobación de eliminación de componentes](concept-remove-components-checklist.md).

### <a name="how-can-i-see-what-configurations-are-applied-to-help-me-troubleshoot"></a>¿Cómo se puede ver qué configuraciones se aplican para facilitar la solución de problemas?

Puede ver la configuración de Azure Virtual Network Manager en **Administrador de redes** para una red virtual. Puede ver la configuración de administración de seguridad y conectividad que se aplica. Para más información, vea [visualización de la configuración aplicada](how-to-view-applied-configurations.md).

### <a name="can-a-virtual-network-managed-by-azure-virtual-network-manager-be-peered-to-a-non-managed-virtual-network"></a>¿Se puede emparejar una red virtual administrada por Azure Virtual Network Manager a una red virtual no administrada?

Sí, puede optar por invalidar o eliminar un emparejamiento existente ya creado.

## <a name="limits"></a>Límites

### <a name="what-are-the-service-limitation-of-azure-virtual-network-manager"></a>¿Cuál es la limitación del servicio de Azure Virtual Network Manager?

* Un concentrador en una topología de concentrador y radio se puede emparejar hasta 500 radios. 

* Las subredes de una red virtual no se pueden comunicar entre ellas si tienen el mismo espacio de direcciones en una configuración de malla. 

* Azure Virtual Network Manager solo permite 500 conexiones de emparejamiento de red virtual en toda la configuración de conectividad de una red virtual determinada. También puede administrar el emparejamiento heredado de forma independiente. 

* El número máximo de prefijos IP en todas las reglas de administración combinadas es de 1000. 

* El número máximo de reglas de administración en un nivel de Azure Virtual Network Manager es de 100. 

* Azure Virtual Network Manager no tiene compatibilidad entre inquilinos en la versión preliminar pública.

* Una red virtual puede formar parte de hasta cinco configuraciones de malla. 

## <a name="next-steps"></a>Pasos siguientes

Cree una instancia de [Azure Virtual Network Manager](create-virtual-network-manager-portal.md) con Azure Portal.
