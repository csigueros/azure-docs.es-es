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
ms.openlocfilehash: 921c829cefae8ab4ea96066d70c5d110fc9188f6
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2021
ms.locfileid: "131451946"
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

### <a name="how-can-i-explicitly-allow-sqlmi-traffic-before-having-deny-rules"></a>¿Cómo puedo permitir explícitamente el tráfico SQLMI antes de tener reglas de denegación?

Azure SQL Managed Instance tiene algunos requisitos de red. Si las reglas de administración de seguridad pueden bloquear los requisitos de red, puede usar las siguientes reglas de ejemplo para permitir el tráfico SQLMI con mayor prioridad que las reglas de denegación que pueden bloquear el tráfico de SQL Managed Instance.

#### <a name="inbound-rules"></a>Reglas de entrada

| Port | Protocolo | Source | Destination | Acción |
| ---- | -------- | ------ | ----------- | ------ |
| 9000, 9003, 1438, 1440, 1452 | TCP | SqlManagement | **VirtualNetwork** | Allow |
| 9000, 9003 | TCP | CorpnetSaw | **VirtualNetwork** | Allow |
| 9000, 9003 | TCP | CorpnetPublic | **VirtualNetwork** | Allow |
| Any | Any | **VirtualNetwork** | **VirtualNetwork** | Allow |
| Any | Any | **AzureLoadBalancer** | **VirtualNetwork** | Allow |

#### <a name="outbound-rules"></a>Reglas de salida

| Port | Protocolo | Source | Destination | Acción |
| ---- | -------- | ------ | ----------- | ------ |
| 443, 12000 | TCP  | **VirtualNetwork** | AzureCloud | Allow |
| Any | Any | **VirtualNetwork** | **VirtualNetwork** | Allow |

## <a name="limits"></a>Límites

### <a name="what-are-the-service-limitation-of-azure-virtual-network-manager"></a>¿Cuál es la limitación del servicio de Azure Virtual Network Manager?

* Un concentrador de una topología en estrella tipo hub-and-spoke se puede emparejar con hasta 250 radios. 

* Una topología de malla puede tener hasta 250 redes virtuales.

* Las subredes de una red virtual no se pueden comunicar entre ellas si tienen el mismo espacio de direcciones en una configuración de malla. 

* El número máximo de prefijos IP en todas las reglas de administración combinadas es de 1000. 

* El número máximo de reglas de administración en un nivel de Azure Virtual Network Manager es de 100. 

* Azure Virtual Network Manager no tiene compatibilidad entre inquilinos en la versión preliminar pública.

* Una red virtual puede formar parte de hasta dos configuraciones de malla. 

## <a name="next-steps"></a>Pasos siguientes

Cree una instancia de [Azure Virtual Network Manager](create-virtual-network-manager-portal.md) con Azure Portal.
