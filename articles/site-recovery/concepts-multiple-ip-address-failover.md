---
title: Configuración de la conmutación por error de varias direcciones IP con Azure Site Recovery
description: Se describe cómo configurar la conmutación por error de configuraciones de direcciones IP secundarias para máquinas virtuales de Azure.
services: site-recovery
author: rishjai-msft
manager: gaggupta
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/01/2021
ms.author: rishjai
ms.openlocfilehash: a7437650c11797d5a00c7c8684138632555b05a2
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2021
ms.locfileid: "131478310"
---
# <a name="configure-failover-of-multiple-ip-addresses-with-azure-site-recovery"></a>Configuración de la conmutación por error de varias direcciones IP con Azure Site Recovery

Cada NIC conectada a una VM tiene una o varias configuraciones de IP asociadas. Se asigna a cada configuración una dirección IP privada estática o dinámica. Cada configuración también puede tener un recurso de dirección IP pública asociado. Un recurso de dirección IP pública tiene una dirección IP pública dinámica o estática asignada. Para más información sobre las [direcciones IP en Azure](../virtual-network/ip-services/public-ip-addresses.md), lea el artículo sobre direcciones IP en Azure.

Actualmente, Site Recovery configura automáticamente la conmutación por error de la configuración de direcciones IP principales. En este artículo se describe cómo configurar la conmutación por error de las configuraciones de direcciones IP secundarias con Site Recovery. Tenga en cuenta que esto solo es compatible con VM de Azure.

## <a name="configure-secondary-ip-address-failover-via-azure-portal"></a>Configuración de la conmutación por error de direcciones IP secundarias mediante Azure Portal

Site Recovery configura automáticamente la conmutación por error de la configuración de direcciones IP principales al habilitar la replicación para la máquina virtual. Las configuraciones de direcciones IP secundarias deben configurarse manualmente una vez completada la habilitación de la replicación. Para ello, necesita una máquina virtual protegida que tenga una o varias configuraciones de direcciones IP secundarias.

Siga los pasos descritos:
1. Vaya a la hoja **Red** de la página Elementos replicados.

    :::image type="content" source="./media/concepts-multiple-ip-address-failover/network-tab.png" alt-text="Hoja Elementos replicados":::
    

2. Verá el siguiente texto resaltado.  Haga clic en **Editar** para modificarlo.
 
    :::image type="content" source="./media/concepts-multiple-ip-address-failover/network-edit.png" alt-text="Modo de edición de la pestaña Red" lightbox="./media/concepts-multiple-ip-address-failover/network-edit-expanded.png":::    

3. Haga clic en "+ Configuraciones de IP". Verá dos opciones: agregar todas las configuraciones de IP o agregar configuraciones de IP de forma selectiva.

    :::image type="content" source="./media/concepts-multiple-ip-address-failover/add-ip-configurations.png" alt-text="Agregar configuraciones de IP":::

4. Al hacer clic en **Agregar todas las configuraciones de IP secundarias**, todas ellas aparecerán en la cuadrícula siguiente y, a continuación, podrá configurarlas como quiera.

    :::image type="content" source="./media/concepts-multiple-ip-address-failover/add-all-ip-configurations.png" alt-text="Agregar todas las configuraciones de IP" lightbox="./media/concepts-multiple-ip-address-failover/add-all-ip-configurations-expanded.png":::    

5. Como alternativa, al hacer clic en **Seleccionar y agregar configuraciones de IP secundarias**, se abrirá una hoja donde puede elegir y agregar las configuraciones de IP que desea configurar para la conmutación por error.

    :::image type="content" source="./media/concepts-multiple-ip-address-failover/select-and-add-ip-configurations.png" alt-text="Seleccionar y agregar configuraciones de IP secundarias":::

Ahora, para cada configuración de IP que haya agregado, puede configurar los valores de IP privada, IP pública y Grupo de back-end para la conmutación por error y la conmutación por error de prueba por separado. Una vez que lo haya hecho todo, no olvide guardar los cambios.


## <a name="next-steps"></a>Pasos siguientes
- Obtenga más información sobre [Traffic Manager con Azure Site Recovery](../site-recovery/concepts-traffic-manager-with-site-recovery.md)
- Más información sobre los [métodos de enrutamiento](../traffic-manager/traffic-manager-routing-methods.md) de Traffic Manager.
- Más información sobre los [planes de recuperación](site-recovery-create-recovery-plans.md) para automatizar la conmutación por error de las aplicaciones.
