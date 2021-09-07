---
title: Mantener direcciones IP tras la conmutación por error de máquina virtual de Azure Site Recovery
description: Describe cómo retener las direcciones IP cuando se conmuta por error máquinas virtuales de Azure para la recuperación ante desastres en una región secundaria con Azure Site Recovery
ms.service: site-recovery
ms.date: 07/25/2021
author: mayurigupta13
ms.topic: conceptual
ms.author: mayg
ms.openlocfilehash: 782e6247bb17485e8e654c7e879f477fe531edc4
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121736230"
---
# <a name="retain-ip-addresses-during-failover"></a>Conservar las direcciones IP durante la conmutación por error

[Azure Site Recovery](site-recovery-overview.md) permite la recuperación ante desastres para máquinas virtuales de Azure mediante la replicación de máquinas virtuales a otra región de Azure, la conmutación por error si se produce una interrupción y la conmutación por recuperación a la región principal cuando las cosas vuelven a la normalidad.

Durante la conmutación por error, es posible que quiera conservar el mismo direccionamiento IP para la región de destino y la región de origen:

- De forma predeterminada, cuando se habilita la recuperación ante desastres para máquinas virtuales de Azure, Site Recovery crea recursos de destino en función de la configuración de los recursos de origen. En el caso de las máquinas virtuales de Azure configuradas con direcciones IP estáticas, Site Recovery trata de aprovisionar la misma dirección IP para la máquina virtual de destino, si no está en uso. Para obtener una explicación completa de cómo Site Recovery controla el direccionamiento, [revise este artículo](azure-to-azure-network-mapping.md#set-up-ip-addressing-for-target-vms).
- En lo referente a aplicaciones sencillas, la configuración predeterminada es suficiente. Para aplicaciones más complejas, es posible que deba aprovisionar recursos adicionales para asegurarse de que la conectividad funciona como se esperaba después de la conmutación por error.


En este artículo se proporcionan algunos ejemplos para conservar las direcciones IP en escenarios de ejemplo más complejos. Algunos ejemplos son:

- Conmutación por error para una empresa con todos los recursos ejecutándose en Azure
- Conmutación por error para una empresa con una implementación híbrida, y recursos que se ejecutan en el entorno local y en Azure

## <a name="resources-in-azure-full-failover"></a>Recursos de Azure: conmutación por error completa

Todas las aplicaciones de la empresa A se ejecutan en Azure.

### <a name="before-failover"></a>Antes de la conmutación por error

>[!NOTA La replicación ahora se puede realizar entre dos regiones de Azure de cualquier lugar del mundo. Los clientes ya no se limitan a habilitar la replicación en su continente.

Esta es la arquitectura antes de la conmutación por error.

- La empresa A tiene redes y subredes idénticas en las regiones de origen y destino de Azure.
- Para reducir el objetivo de tiempo de recuperación (RTO), la empresa usa los nodos de réplica de SQL Server Always On, controladores de dominio, etc. Estos nodos de réplica están en una red virtual diferente en la región de destino, por lo que puede establecer la conectividad VPN de sitio a sitio entre las regiones de origen y destino. Esto no es posible si se usa el mismo espacio de direcciones IP en el origen y en el destino.  
- Antes de la conmutación por error, la arquitectura de red es la siguiente:
    - La región principal es Este de Asia de Azure
        - Este de Asia tiene una red virtual (**VNET de origen**) con el espacio de direcciones 10.1.0.0/16.
        - Este de Asia tiene cargas de trabajo divididas en tres subredes de la red virtual:
            - **Subred 1**: 10.1.1.0/24
            - **Subred 2**: 10.1.2.0/24
            - **Subred 3**: 10.1.3.0/24
    - La región secundaria (destino) es la del Sudeste de Asia de Azure.
        - El Sudeste de Asia tiene una red virtual de recuperación (**VNET de recuperación**) idéntica a la red **VNET de origen**.
        - El Sudeste de Asia tiene una red virtual adicional (**VNET de Azure**) con el espacio de direcciones 10.2.0.0/16.
        - **VNET de Azure** contiene una subred (**Subred 4**) con el espacio de direcciones 10.2.4.0/24.
        - Los nodos de réplica de SQL Server Always On, el controlador de dominio, etc. se encuentran en la **Subred 4**.
    - Las redes **VNET de origen** y **VNET de Azure** están conectadas con una conexión VPN de sitio a sitio.
    - La red **Recovery VNet** no está conectada a ninguna otra red virtual.
    - La **empresa A** asigna o comprueba las direcciones IP de destino de los elementos replicados. La IP de destino es la misma que la IP de origen para cada máquina virtual.

![Recursos de Azure antes de la conmutación por error completa](./media/site-recovery-retain-ip-azure-vm-failover/azure-to-azure-connectivity-before-failover2.png)

### <a name="after-failover"></a>Después de la conmutación por error

Si se produce una interrupción regional de origen, la empresa A puede conmutar por error todos sus recursos a la región de destino.

- Con las direcciones IP de destino ya en vigor antes de la conmutación por error, la empresa A puede orquestar la conmutación por error y establecer automáticamente conexiones después de la conmutación por error entre la **VNET de recuperación** y la **VNET de Azure**. Esto se ilustra en el diagrama siguiente:
- En función de los requisitos de la aplicación, las conexiones entre las dos redes virtuales (**VNET de recuperación** y **VNET de Azure**) en la región de destino se pueden establecer antes, durante (como un paso intermedio) o después de la conmutación por error.
  - La empresa puede usar los [planes de recuperación](site-recovery-create-recovery-plans.md) para especificar cuándo se establecerán las conexiones.
  - Pueden conectarse entre las redes virtuales con el emparejamiento de VNET o VPN de sitio a sitio.
      - El emparejamiento de VNET no utiliza una puerta de enlace de VPN y tiene distintas restricciones.
      - Los [precios](https://azure.microsoft.com/pricing/details/virtual-network) del emparejamiento de VNET se calculan de forma diferente que los [precios](https://azure.microsoft.com/pricing/details/vpn-gateway) de VPN Gateway entre redes virtuales. Para las conmutaciones por error, solemos aconsejar usar el mismo método de conectividad como redes de origen, incluido el tipo de conexión, para minimizar incidentes impredecibles de la red.

    ![Recursos de la conmutación por error completa de Azure](./media/site-recovery-retain-ip-azure-vm-failover/azure-to-azure-connectivity-full-region-failover2.png)



## <a name="resources-in-azure-isolated-app-failover"></a>Recursos de Azure: conmutación por error de aplicaciones aisladas

Es posible que deba conmutar por error en el nivel de aplicación. Por ejemplo, para conmutar por error una aplicación específica o una capa de aplicación ubicada en una subred dedicada.

- En este escenario, aunque puede conservar el direccionamiento IP, no resulta aconsejable ya que aumenta la posibilidad de incoherencias de conectividad. Asimismo, también perderá la conectividad de subred a otras subredes dentro de la misma VNET de Azure.
- Una manera mejor de realizar la conmutación por error de la aplicación a nivel de subred es usar diferentes direcciones IP de destino para la conmutación por error (si necesita conectividad a otras subredes en la VNET de origen), o para aislar cada aplicación en su propia red virtual dedicada en la región de origen. Gracias al último enfoque, puede establecer una conectividad entre redes en la región de origen, y emular el mismo comportamiento cuando conmute por error a la región de destino.  

En este ejemplo, la empresa A coloca las aplicaciones en la región de origen de las redes virtuales dedicadas, y establece conectividad entre esas redes virtuales. Con este diseño, puede realizar la conmutación por error de aplicaciones aislada y conservar las direcciones IP privadas de origen en la red de destino.

### <a name="before-failover"></a>Antes de la conmutación por error

Antes de la conmutación por error, la arquitectura es la siguiente:

- Las máquinas virtuales de la aplicación se hospedan en la región Este de Asia de Azure:
    - Las máquinas virtuales de la **aplicación 1** se encuentran en **VNET de origen 1**: 10.1.0.0/16.
    - Las máquinas virtuales de la **aplicación 2** se encuentran en **VNET de origen 2**: 10.2.0.0/16.
    - **VNET de origen 1** tiene dos subredes.
    - **VNET de origen 2** tiene dos subredes.
- La región secundaria (destino) es Sudeste de Asia de Azure. Sudeste de Asia tiene redes virtuales de recuperación (**VNET de recuperación 1** y **VNET de recuperación 2**) que son idénticas a las redes **VNET de origen 1** y **VNET de origen 2**.
        Las redes - **VNET de recuperación 1** y **VNET de recuperación 2** tienen, cada una, dos subredes que coinciden con **VNET de origen 1** y **VNET de origen 2**. Sudeste de Asia tiene una red virtual adicional (**VNET de Azure**) con el espacio de direcciones 10.3.0.0/16.
        - **VNET de Azure** contiene una subred (**Subred 4**) con un espacio de direcciones 10.3.4.0/24.
        - Los nodos de réplica de SQL Server Always On, el controlador de dominio, etc. se encuentran en la **Subred 4**.
- Hay diferentes conexiones de VPN de sitio a sitio:
    - **VNET de origen 1** y **VNET de Azure**
    - **VNET de origen 2** y **VNET de Azure**
    - Las redes **VNET de origen 1** y **VNET de origen 2** están conectadas con VPN de sitio a sitio
- Las redes **VNET de recuperación 1** y **VNET de recuperación 2** no están conectadas a otras redes virtuales.
- La **empresa A** configura las puertas de enlace VPN en **VNET de recuperación 1** y en **VNET de recuperación 2**, para reducir el RTO.  
- Las redes **Recovery VNet1** y **Recovery VNet2** no están conectadas a ninguna otra red virtual.
- Para reducir el objetivo de tiempo de recuperación (RTO), las puertas de enlace de VPN se configuran en **Recovery VNet1** y **Recovery VNet2** antes de la conmutación por error.

    ![Recursos de Azure antes de la conmutación por error de la aplicación](./media/site-recovery-retain-ip-azure-vm-failover/azure-to-azure-connectivity-isolated-application-before-failover2.png)

### <a name="after-failover"></a>Después de la conmutación por error

Si se produce una interrupción o un problema que afecta a una sola aplicación (en **VNET de origen 2 de nuestro ejemplo), la empresa A puede recuperar la aplicación afectada del siguiente modo:


- Desconecte las conexiones VPN entre las redes **VNET de origen 1** y **VNET de origen 2**, y las redes **VNET de origen 2** y **VNET de Azure**.
- Establezca conexiones VPN entre las redes **VNET de origen 1** y **VNET de recuperación 2**, y las redes **VNET de recuperación 2** y **VNET de Azure**.
- Conmutar por error las máquinas virtuales de la red **VNET de origen 2** a la red **VNET de recuperación 2**.

![Recursos de la conmutación por error de la aplicación de Azure](./media/site-recovery-retain-ip-azure-vm-failover/azure-to-azure-connectivity-isolated-application-after-failover2.png)


- Este ejemplo se puede ampliar para que incluya más aplicaciones y conexiones de red. Se recomienda seguir un modelo de conexión similar siempre que sea posible, cuando se conmuta por error desde un origen a un destino.
- Las puertas de enlace VPN pueden usar direcciones IP públicas y saltos de puerta de enlace para establecer conexiones. Si no desea usar direcciones IP públicas o desea evitar saltos adicionales, puede usar el [emparejamiento de redes virtuales de Azure](../virtual-network/virtual-network-peering-overview.md) para emparejar redes virtuales a través de las [regiones de Azure admitidas](../virtual-network/virtual-network-manage-peering.md#cross-region).

## <a name="hybrid-resources-full-failover"></a>Recursos de Hybrid: conmutación por error completa

En este escenario, la **empresa B** ejecuta una empresa híbrida, en la cual una parte de la infraestructura de aplicaciones se ejecuta en Azure y el resto se ejecuta de forma local.

### <a name="before-failover"></a>Antes de la conmutación por error

Este es el aspecto de la arquitectura de red antes de la conmutación por error:

- Las máquinas virtuales de la aplicación se hospedan en la región Este de Asia de Azure.
- Este de Asia tiene una red virtual (**VNET de origen**) con el espacio de direcciones 10.1.0.0/16.
  - Este de Asia tiene cargas de trabajo divididas en tres subredes de la red **VNET de origen**:
    - **Subred 1**: 10.1.1.0/24
    - **Subred 2**: 10.1.2.0/24
    - **Subred 3**: 10.1.3.0/24, que usa una red virtual de Azure con el espacio de direcciones 10.1.0.0/16. Esta red virtual se denomina **VNET de origen**
      - La región secundaria (destino) es la del Sudeste de Asia de Azure:
  - El Sudeste de Asia tiene una red virtual de recuperación (**VNET de recuperación**) idéntica a la red **VNET de origen**.
- Las máquinas virtuales de la región de Este de Asia están conectadas a un centro de datos local mediante Azure ExpressRoute o una conexión VPN de sitio a sitio.
- Para reducir el RTO, la empresa B debe aprovisionar las puertas de enlace en VNET de recuperación en la región del Sudeste de Asia de Azure antes de realizar la conmutación por error.
- La empresa B asigna o comprueba las direcciones IP de destino de las máquinas virtuales replicadas. La dirección IP de destino es la misma que la dirección IP de origen para cada máquina virtual.


![Conectividad local a Azure antes de realizar la conmutación por error](./media/site-recovery-retain-ip-azure-vm-failover/on-premises-to-azure-connectivity-before-failover2.png)

### <a name="after-failover"></a>Después de la conmutación por error


Si se produce una interrupción regional de origen, la empresa B puede conmutar por error todos sus recursos a la región de destino.

- Con las direcciones IP de destino ya en vigor antes de la conmutación por error, la empresa B puede orquestar la conmutación por error y establecer automáticamente conexiones después de la conmutación por error entre las redes **VNET de recuperación** y **VNET de Azure**.
- En función de los requisitos de la aplicación, las conexiones entre las dos redes virtuales (**VNET de recuperación** y **VNET de Azure**) en la región de destino se pueden establecer antes, durante (como un paso intermedio) o después de la conmutación por error. La empresa puede usar los [planes de recuperación](site-recovery-create-recovery-plans.md) para especificar cuándo se establecerán las conexiones.
- Recuerde que debe desconectar la conexión original entre la región de Este de Asia de Azure y el centro de datos local antes de establecer la conexión entre la región del Sudeste de Asia de Azure y el centro de datos local.
- Asimismo, se vuelve a configurar el enrutamiento local para que apunte a la región de destino y a las puertas de enlace una vez realizada la conmutación por error.

![Conectividad local a Azure después de realizar la conmutación por error](./media/site-recovery-retain-ip-azure-vm-failover/on-premises-to-azure-connectivity-after-failover2.png)

## <a name="hybrid-resources-isolated-app-failover"></a>Recursos híbridos: conmutación por error de aplicaciones aisladas

La empresa B no puede conmutar por error aplicaciones aisladas en el nivel de subred. Esto es porque el espacio de direcciones en redes virtuales de origen y de recuperación es el mismo, y el origen de la conexión local está activo.

 - Para lograr la resistencia de las aplicaciones, la empresa B deberá colocar cada aplicación en su propia red virtual de Azure dedicada.
 - Con cada aplicación en una red virtual independiente, la empresa B puede conmutar por error las aplicaciones aisladas y enrutar las conexiones de origen a la región de destino.

## <a name="next-steps"></a>Pasos siguientes

Obtenga información acerca de los [planes de recuperación](site-recovery-create-recovery-plans.md).
