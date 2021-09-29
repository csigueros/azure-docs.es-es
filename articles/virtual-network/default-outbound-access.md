---
title: Acceso de salida predeterminado en Azure
titleSuffix: Azure Virtual Network
description: Obtenga más información sobre el acceso de salida predeterminado en Azure.
services: virtual-network
author: asudbring
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: conceptual
ms.date: 07/13/2021
ms.author: allensu
ms.openlocfilehash: 0ca1f0ee6bcac39f0f73286fe8c426be9447c345
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124754465"
---
# <a name="default-outbound-access-in-azure"></a>Acceso de salida predeterminado en Azure

En Azure, a las máquinas virtuales creadas en una red virtual sin conectividad de salida explícita definida se les asigna una dirección IP pública de salida predeterminada. Esta dirección IP permite la conectividad de salida desde los recursos a Internet. Este acceso se conoce como acceso de salida predeterminado. 

Algunos ejemplos de conectividad de salida explícita son las máquinas virtuales:

* Creadas dentro de una subred asociada a una instancia de NAT Gateway.
* En el grupo de back-end de un equilibrador de carga estándar con reglas de salida definidas.
* En el grupo de back-end del equilibrador de carga público básico.
* Máquinas virtuales con direcciones IP públicas asociadas explícitamente a ellas.

## <a name="how-is-default-outbound-access-provided"></a>¿Cómo se proporciona el acceso de salida predeterminado?

La dirección IPv4 pública que se usa para el acceso se denomina dirección IP de acceso de salida predeterminada. Esta dirección IP es implícita y pertenece a Microsoft. Esta dirección IP está sujeta a cambios y no se recomienda depender de ella para cargas de trabajo de producción.

## <a name="when-is-default-outbound-access-provided"></a>¿Cuándo se proporciona el acceso de salida predeterminado?

Si implementa una máquina virtual en Azure y no tiene conectividad de salida explícita, se le asigna una dirección IP de acceso de salida predeterminada.
## <a name="why-is-disabling-default-outbound-access-recommended"></a>¿Por qué se recomienda deshabilitar el acceso de salida predeterminado?

* Seguro de forma predeterminada
    
    * No se recomienda abrir una red virtual a Internet de manera predeterminada mediante el principio de seguridad de red de confianza cero.

* Método explícito frente a implícito

    * Se recomienda tener métodos explícitos de conectividad en lugar de implícitos al conceder acceso a los recursos de la red virtual.

* Pérdida de dirección IP

    * La dirección IP de acceso de salida predeterminada no es propiedad de los clientes. Esta dirección IP está sujeta a cambios.  Cualquier dependencia de esta dirección IP podría causar problemas en el futuro.

## <a name="how-can-i-disable-default-outbound-access"></a>¿Cómo se puede deshabilitar el acceso de salida predeterminado?

Hay varias maneras de desactivar el acceso de salida predeterminado:

1.  Agregue un método de conectividad de salida explícito.

    * Asocie una instancia de NAT Gateway a la subred de la máquina virtual.

    * Asocie un equilibrador de carga estándar a las reglas de salida configuradas.

    * Asocie una dirección IP pública a la interfaz de red de la máquina virtual.

2.  Use el modo de orquestación flexible para conjuntos de escalado de máquinas virtuales.

    * Los conjuntos de escalado flexibles son seguros de manera predeterminada. Las instancias creadas a través de conjuntos de escalado flexibles no tendrán asociada la dirección IP de acceso de salida predeterminada. Para obtener más información, vea [Modo de orquestación flexible para conjuntos de escalado de máquinas virtuales](../virtual-machines/flexible-virtual-machine-scale-sets.md).

## <a name="if-i-need-outbound-access-what-is-the-recommended-way"></a>Si necesito acceso de salida, ¿cuál es la manera recomendada?

NAT Gateway es el enfoque recomendado para tener conectividad de salida explícita. También se puede usar un firewall para proporcionar este acceso.

## <a name="limitations"></a>Limitaciones

* Es posible que sea necesaria la conectividad para actualizaciones de Windows.
* La dirección IP de acceso de salida predeterminada no admite paquetes fragmentados. 

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre las conexiones salientes en Azure y Azure Virtual Network NAT (NAT Gateway), consulte:

* [Traducción de direcciones de red de origen (SNAT) para conexiones salientes](../load-balancer/load-balancer-outbound-connections.md).

* [¿Qué es Azure Virtual Network NAT?](./nat-gateway/nat-overview.md)