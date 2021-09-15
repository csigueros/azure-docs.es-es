---
title: 'Cambio del dispositivo de replicación de Azure Site Recovery: versión preliminar'
description: 'En este artículo se describe cómo cambiar entre diferentes dispositivos de replicación mientras se replican máquinas virtuales de VMware para Azure en Azure Site Recovery: versión preliminar'
ms.service: site-recovery
ms.topic: article
ms.date: 08/19/2021
ms.openlocfilehash: f95ed991833a9f6ed1b0635a8678e393f152cb56
ms.sourcegitcommit: 8000045c09d3b091314b4a73db20e99ddc825d91
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/19/2021
ms.locfileid: "122446739"
---
# <a name="switch-azure-site-recovery-replication-appliance"></a>Creación del dispositivo de replicación de Azure Site Recovery

>[!NOTE]
> La información de este artículo se aplica a Azure Site Recovery: versión preliminar.

Se debe [crear e implementar un dispositivo de replicación de Azure Site Recovery local](deploy-vmware-azure-replication-appliance-preview.md) cuando se usa [Azure Site Recovery](site-recovery-overview.md) para realizar la recuperación ante desastres de servidores físicos y máquinas virtuales de VMware en Azure. Para obtener información detallada sobre el dispositivo de replicación, vea [la arquitectura](vmware-azure-architecture-preview.md). Puede crear y usar varios dispositivos de replicación en función de los requisitos de capacidad de la organización.

En este artículo se proporciona información sobre cómo puede cambiar entre dispositivos de replicación.

## <a name="application-resilience"></a>Resistencia de la aplicación

Normalmente, en la arquitectura clásica, si necesita mantener la resistencia del servidor de configuración, la acción recomendada es realizar copias de seguridad manuales periódicas de la máquina. Se trata de un proceso muy complicado, también propenso a errores.  

Esta versión preliminar presenta una mejor manera de hacer que los dispositivos sean más resistentes. Si el dispositivo de replicación se agota o necesita equilibrar las máquinas que se ejecutan en un dispositivo, basta con poner en marcha otro dispositivo de replicación y cambiar todas las máquinas al nuevo dispositivo.


## <a name="consideration-for-switching-replication-appliance"></a>Consideración para cambiar el dispositivo de replicación

Puede cambiar el dispositivo de replicación en los escenarios siguientes:

- Tendrá que realizar una operación de cambio en caso de que el dispositivo de replicación de Azure Site Recovery actual se agote, es decir, si ninguno de sus componentes tiene latido.
  - Se considera que un dispositivo está agotado solo si ninguno de sus componentes tiene latido. Incluso si uno de los componentes tiene un latido, la operación de cambio se bloqueará.
  - Si el dispositivo actual se ha agotado, deberá proporcionar de nuevo las credenciales para acceder a las máquinas que está intentando cambiar. Si está equilibrando la carga y el dispositivo actual sigue en un estado no crítico, las credenciales se seleccionarán automáticamente y no será necesario volver a escribirlas al cambiar a otro dispositivo.
- Es posible que tenga que realizar la operación de cambio en caso de que necesite equilibrar la carga del dispositivo de replicación.
- Si está intentando realizar un cambio con la intención de equilibrar la carga en un dispositivo, todos los componentes del dispositivo actual deben estar en estado correcto o de advertencia. Si falta el latido de solo un componente, se bloqueará la operación de cambio.
-  Asegúrese de que el dispositivo al que va a cambiar está en estado correcto o de advertencia, para que la operación se haga correctamente.
-  Solo se pueden seleccionar aquellas máquinas que se replican desde el entorno local a Azure al realizar la operación de cambio a otro dispositivo.  


## <a name="switch-a-replication-appliance"></a>Cambio de un dispositivo de replicación

Por ejemplo, este es el escenario en el que el dispositivo de replicación 1 (RA1) se ha vuelto crítico y desea mover las cargas de trabajo protegidas al dispositivo de replicación 2 (RA2), que se encuentra en estado correcto. También si quiere cambiar las cargas de trabajo en RA1 a RA2 para cualquier cambio de nivel de organización o equilibrio de carga.

**Siga estos pasos para cambiar un dispositivo**:

1. Vaya a la hoja de **infraestructura de Site Recovery** y seleccione **Dispositivo de replicación de ASR**.

   Se muestra la lista de dispositivos disponibles y su estado. Por ejemplo, RA2 es correcto aquí.

   ![Lista de dispositivos de replicación correctos](./media/switch-replication-appliance-preview/appliance-health.png)

2. Seleccione el dispositivo de replicación (RA1) y seleccione **Cambiar dispositivo**.

   ![Selección del dispositivo de replicación que se va a cambiar](./media/switch-replication-appliance-preview/select-switch-appliance.png)


3. En **Seleccionar máquinas**, seleccione las máquinas que desea conmutar por error a otro dispositivo de replicación (RA2). Seleccione **Siguiente**.

   >[!NOTE]
   > Solo aquellas máquinas protegidas por el dispositivo actual estarán visibles en la lista. Las máquinas con conmutación por error no estarán presentes aquí  

    ![Selección de máquinas para el cambio](./media/switch-replication-appliance-preview/select-machines.png)

4. En la página  **Configuración de origen** , para cada una de las máquinas seleccionadas, seleccione otro dispositivo de replicación.

   ![Configuración de origen para el dispositivo de replicación](./media/switch-replication-appliance-preview/source-settings.png)

   >[!NOTE]
   > Si el dispositivo actual está agotado, tendrá que seleccionar las credenciales para acceder a las máquinas. De lo contrario, el campo se deshabilitará.

5. Revise la selección y luego haga clic en **Cambiar dispositivo**.

   ![revisión del dispositivo de replicación](./media/switch-replication-appliance-preview/review-switch-appliance.png)

   Una vez completada la resincronización, el estado de replicación se vuelve correcto para las máquinas virtuales que se mueven a un nuevo dispositivo.

## <a name="next-steps"></a>Pasos siguientes
Configure la recuperación ante desastres en Azure para [máquinas virtuales VMware](vmware-azure-tutorial.md).
