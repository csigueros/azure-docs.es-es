---
title: Personalización de las configuraciones de red de una máquina virtual de conmutación por error | Microsoft Docs
description: Se proporciona información general sobre la personalización de configuraciones de red de una máquina virtual de conmutación por error en la replicación de máquinas virtuales de Azure mediante Azure Site Recovery.
services: site-recovery
author: rishjai-msft
manager: gaggupta
ms.service: site-recovery
ms.topic: article
ms.date: 10/01/2021
ms.author: rishjai
ms.openlocfilehash: 79a33226da71071d8985daf723dde7314116ce37
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2021
ms.locfileid: "131475176"
---
# <a name="customize-networking-configurations-of-the-target-azure-vm"></a>Personalización de las configuraciones de red de la máquina virtual de Azure de destino

En este artículo se proporciona una guía para personalizar las configuraciones de red en la máquina virtual de Azure de destino cuando se replican y recuperan máquinas virtuales de Azure de una región a otra mediante [Azure Site Recovery](site-recovery-overview.md).

## <a name="before-you-start"></a>Antes de comenzar

Obtenga información sobre cómo Site Recovery proporciona recuperación ante desastres para [este escenario](azure-to-azure-architecture.md).

## <a name="supported-networking-resources"></a>Compatibilidad con los recursos de red

Al replicar máquinas virtuales de Azure, se pueden proporcionar las siguientes configuraciones de recursos clave para la máquina virtual de conmutación por error:

- [Equilibrador de carga interno](../load-balancer/load-balancer-overview.md)
- [Dirección IP pública](../virtual-network/public-ip-addresses.md)
- [Dirección IP secundaria](../virtual-network/ip-services/virtual-network-multiple-ip-addresses-portal.md)
- [Grupo de seguridad de red](../virtual-network/manage-network-security-group.md) tanto de la subred como de la NIC

## <a name="prerequisites"></a>Prerrequisitos

- Asegúrese de planear las configuraciones de recuperación de antemano.
- Cree los recursos de red por adelantado. Proporciónelos como una entrada para que el servicio de Azure Site Recovery pueda respetar esta configuración y asegúrese de que la máquina virtual de conmutación por error se adhiere a ella.

## <a name="customize-failover-and-test-failover-networking-configurations"></a>Personalización de las configuraciones de conmutación por error y de red de conmutación por error de prueba

1. Vaya a **Elementos replicados**. 
2. Seleccione la máquina virtual de Azure que desee.
3. Seleccione **Red** y, después, **Editar**. Observe que los valores de la configuración de la NIC incluyen los recursos correspondientes en el origen. 

    :::image type="content" source="./media/azure-to-azure-customize-networking/edit-networking-properties.png" alt-text="Personalización de las configuraciones de red de conmutación por error" lightbox="./media/azure-to-azure-customize-networking/edit-networking-properties-expanded.png":::

4. Seleccione una red virtual de conmutación por error de prueba.
5. Seleccione la pestaña NIC que quiere configurar. Ahora, seleccione los recursos creados previamente correspondientes en la conmutación por error de prueba y la ubicación de conmutación por error.

    :::image type="content" source="./media/azure-to-azure-customize-networking/nic-drilldown.png" alt-text="Edición de la configuración de la NIC" lightbox="./media/azure-to-azure-customize-networking/nic-drilldown-expanded.png":::

6. Seleccione **Aceptar**.

Site Recovery ahora respetará esta configuración y garantizará que la máquina virtual de la conmutación por error esté conectada al recurso seleccionado a través de la NIC correspondiente.

Al desencadenar la conmutación por error de prueba a través del plan de recuperación, siempre pedirá la red virtual de Azure. Esta red virtual se usará para la conmutación por error de prueba de las máquinas que no tienen la configuración de conmutación por error de prueba preconfigurada.

## <a name="troubleshooting"></a>Solución de problemas

### <a name="unable-to-view-or-select-a-resource"></a>No se puede ver o seleccionar un recurso

Si no puede seleccionar o ver alguno de los recursos de red, realice las siguientes comprobaciones y condiciones:

- El campo de destino de un recurso de red solo se habilita si la máquina virtual de origen tenía una entrada correspondiente. Esto se basa en el principio de que en un escenario de recuperación ante desastres, se necesita la versión exacta o una versión reducida verticalmente del origen.
- En cada uno de los recursos de red, se aplican algunos filtros de la lista desplegable para asegurarse de que la máquina virtual de conmutación por error se puede asociar automáticamente al recurso seleccionado y que se mantiene la confiabilidad de la conmutación por error. Estos filtros se basan en las mismas condiciones de red que se habrían verificado al configurar la máquina virtual de origen.

Validaciones del equilibrador de carga interno:

- La suscripción y la región del equilibrador de carga y de la máquina virtual de destino deben ser iguales.
- La red virtual asociada con el equilibrador de carga interno y la de la máquina virtual de destino deben coincidir.
- La SKU de la dirección IP pública de la máquina virtual de destino y la del equilibrador de carga interno deben coincidir.
- Si la máquina virtual de destino está configurada para colocarse en una zona de disponibilidad, compruebe si el equilibrador de carga tiene redundancia de zona o forma parte de cualquier zona de disponibilidad. (los equilibradores de carga de la SKU básica no admiten zonas y, en este caso, no se mostrarán en la lista desplegable).
- Asegúrese de que el equilibrador de carga interno tiene un grupo back-end y una configuración de front-end creados previamente.

Dirección IP pública:

- La suscripción y la región de la dirección IP pública y de la máquina virtual de destino deben ser las mismas.
- La SKU de la dirección IP pública de la máquina virtual de destino y la del equilibrador de carga interno deben coincidir.

Grupo de seguridad de red:
- La suscripción y la región del grupo de seguridad de red y de la máquina virtual de destino deben ser las mismas.


> [!WARNING]
> Si la máquina virtual de destino está asociada a un conjunto de disponibilidad, debe asociar tanto la dirección IP pública como el equilibrador de carga interno de la misma SKU con la de la dirección IP pública y el equilibrador de carga interno de la otra máquina virtual del conjunto de disponibilidad. Si no lo hace, la conmutación por error podría no realizarse correctamente.