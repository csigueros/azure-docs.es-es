---
title: Incorporación de direcciones IP públicas tras la conmutación por error con Azure Site Recovery
description: Se describe cómo configurar direcciones IP públicas con Azure Site Recovery y Azure Traffic Manager para la migración y la recuperación ante desastres.
services: site-recovery
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: mayg
ms.openlocfilehash: da0efe29c7b0ec722464055e70b966c33d6df88c
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131003606"
---
# <a name="set-up-public-ip-addresses-after-failover"></a>Configuración de direcciones IP públicas tras la conmutación por error

Las direcciones IP públicas permiten a los recursos de Internet la comunicación entrante a los recursos de Azure. También habilitan los recursos de Azure para la comunicación saliente a Internet y los servicios de Azure orientados al público con una dirección IP asignada al recurso.
- La comunicación entrante desde Internet a los recursos, como Azure Virtual Machines, Azure Application Gateway, Azure Load Balancer, Azure VPN Gateway y otros. Todavía puede comunicarse con recursos como máquinas virtuales desde Internet, si una máquina virtual no tiene asignada una dirección IP pública, y siempre que la máquina virtual forme parte de un grupo de back-end de un equilibrador de carga, y el equilibrador de carga tenga asignada una dirección IP pública.
- La conectividad saliente a Internet usa una dirección IP predecible. Por ejemplo, una máquina virtual puede establecer una comunicación saliente a Internet sin tener asignada una dirección IP pública, pero su dirección es una dirección de red que Azure traduce a una dirección pública impredecible, de manera predeterminada. Asignar una dirección IP pública a un recurso le permite saber cuál es la dirección IP que se usa para la conexión saliente. Si bien la dirección es predecible, puede cambiar en función del método de asignación que se elija. Para más información, consulte [Creación de una dirección IP pública](../virtual-network/ip-services/virtual-network-public-ip-address.md#create-a-public-ip-address). Para obtener más información sobre las conexiones salientes de recursos de Azure, consulte [Conexiones salientes](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

En Azure Resource Manager, una dirección IP pública es un recurso que tiene sus propias propiedades. Estos son algunos de los recursos con los que puede asociar un recurso de dirección IP pública:

* Interfaces de red de máquinas virtuales
* Equilibradores de carga accesibles desde Internet
* Puertas de enlace de VPN
* Puertas de enlace de aplicaciones

En este artículo se describe cómo puede usar direcciones IP públicas con Site Recovery.

## <a name="public-ip-address-assignment-using-recovery-plan"></a>Asignación de direcciones IP públicas mediante Recovery Plan

La dirección IP pública de la aplicación de producción **no se pueden retenerse en la conmutación por error**. Las cargas de trabajo que nos llegan como parte del proceso de conmutación por error deben tener asignado un recurso de dirección IP pública de Azure disponible en la región de destino. Este paso puede realizarse manualmente o se automatiza con planes de recuperación. Un plan de recuperación agrupa máquinas en grupos de recuperación. Le ayuda a definir un proceso de recuperación sistemático. Puede usar un plan de recuperación para imponer el orden y automatizar las acciones necesarias en cada paso utilizando para ello runbooks de Azure Automation para conmutar por error a Azure o scripts.

La configuración es la siguiente:
- Cree un [plan de recuperación](../site-recovery/site-recovery-create-recovery-plans.md#create-a-recovery-plan) y agrupe las cargas de trabajo según sea necesario en el plan.
- Personalice el plan agregando un paso para asociar una dirección IP pública con scripts de [runbooks de Azure Automation](../site-recovery/site-recovery-runbook-automation.md#customize-the-recovery-plan) para la VM conmutada por error.

 
## <a name="public-endpoint-switching-with-dns-level-routing"></a>Extremo público conmutado con enrutamiento de nivel de DNS

Azure Traffic Manager permite el enrutamiento de nivel de DNS entre los extremo y puede ayudarle a [reducir el RTO](../site-recovery/concepts-traffic-manager-with-site-recovery.md#recovery-time-objective-rto-considerations) para un escenario de recuperación ante desastres. 

Lea más sobre los escenarios de conmutación por error con Traffic Manager:
1. [Conmutación por error del entorno local a Azure](../site-recovery/concepts-traffic-manager-with-site-recovery.md#on-premises-to-azure-failover) con Traffic Manager 
2. [Conmutación por error de Azure a Azure](../site-recovery/concepts-traffic-manager-with-site-recovery.md#azure-to-azure-failover) con Traffic Manager 

La configuración es la siguiente:
- Crear un [perfil de Traffic Manager](../traffic-manager/quickstart-create-traffic-manager-profile.md).
- Con el método de enrutamiento **prioridad** cree dos extremos: **Principal** para el origen y **Conmutación por error** para Azure. Al punto de conexión **Principal** se le asigna la Prioridad 1 y al de **Conmutación por error** la Prioridad 2.
- El extremo **Principal** puede ser [Azure](../traffic-manager/traffic-manager-endpoint-types.md#azure-endpoints) o [externo](../traffic-manager/traffic-manager-endpoint-types.md#external-endpoints) dependiendo de si su entorno de origen está dentro o fuera de Azure.
- El extremo **Conmutación por error** se crea como un extremo de **Azure**. Use una **dirección IP pública estática** que será el extremo orientado al exterior de Traffic Manager en el evento de desastre.

## <a name="next-steps"></a>Pasos siguientes
- Obtenga más información sobre [Traffic Manager con Azure Site Recovery](../site-recovery/concepts-traffic-manager-with-site-recovery.md)
- Más información sobre los [métodos de enrutamiento](../traffic-manager/traffic-manager-routing-methods.md) de Traffic Manager.
- Más información sobre los [planes de recuperación](site-recovery-create-recovery-plans.md) para automatizar la conmutación por error de las aplicaciones.