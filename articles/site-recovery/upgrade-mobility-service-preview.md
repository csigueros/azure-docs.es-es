---
title: 'Actualización de los componentes del dispositivo y Mobility Service: versión preliminar'
description: 'En este artículo se describen las actualizaciones automáticas del agente de movilidad y el procedimiento relacionado con las actualizaciones manuales: versión preliminar.'
ms.service: site-recovery
ms.topic: article
ms.date: 09/01/2021
ms.openlocfilehash: 97cfba3e14268385632a47a5898b21e8d68811cb
ms.sourcegitcommit: f2d0e1e91a6c345858d3c21b387b15e3b1fa8b4c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/07/2021
ms.locfileid: "123539738"
---
# <a name="upgrade-mobility-service-and-appliance-components-preview"></a>Actualización de los componentes del dispositivo y Mobility Service (versión preliminar)

Desde esta versión preliminar, no es necesario mantener las credenciales raíz o de administrador de la máquina de origen para realizar actualizaciones. Las credenciales solo son necesarias para la instalación inicial del agente. Una vez hecho esto, puede quitar las credenciales.


## <a name="update-mobility-agent-automatically"></a>Actualización automática del agente de movilidad

De manera predeterminada, las actualizaciones automáticas están habilitadas en un almacén. Las actualizaciones automáticas se desencadenarán a las 12:00 a. m. hora local todos los días, si hay una nueva versión disponible.

> [!NOTE]
> Si usa bits de versión preliminar privada, las actualizaciones automáticas se bloquean para las máquinas protegidas. Asegúrese de configurar el Site Recovery en la máquina de nuevo, mediante un nuevo dispositivo de versión preliminar.

Para aprovechar las características, mejoras y correcciones más recientes, se recomienda elegir la opción **Permitir la administración por parte de Site Recovery** en la **configuración de actualización del agente de movilidad (versión preliminar)** . Las actualizaciones automáticas no requieren un reinicio ni afectan a la replicación en curso de las máquinas virtuales. Las actualizaciones automáticas también garantizan que todos los dispositivos de replicación del almacén se actualizan automáticamente.

![Actualizaciones automáticas activadas en el agente de movilidad](./media/upgrade-mobility-service-preview/automatic-updates-on.png)

Para desactivar las actualizaciones automáticas, active el botón **Permitir la administración por parte de Site Recovery**.

![Actualizaciones automáticas desactivadas en el agente de movilidad](./media/upgrade-mobility-service-preview/automatic-updates-off.png)


## <a name="update-mobility-agent-manually"></a>Actualización manual del agente de movilidad

Si ha desactivado las actualizaciones automáticas del agente de movilidad, puede actualizarlo manualmente mediante los procedimientos siguientes:

### <a name="upgrade-mobility-agent-on-multiple-protected-items"></a>Actualización del agente de movilidad en varios elementos protegidos

Para actualizar manualmente el agente de movilidad en varios elementos protegidos, siga estos pasos:

1. Vaya al **almacén de Recovery Services** > **Elementos replicado**, haga clic en *New Site Recovery mobility agent update is available* (Hay una nueva actualización del agente de movilidad de Site Recovery disponible). Haga clic para instalarla.

   ![Actualización del agente de movilidad en varios elementos protegidos](./media/upgrade-mobility-service-preview/agent-update.png)

2. Elija las máquinas de origen que desea actualizar y, a continuación, haga clic en **Aceptar**.

   >[!NOTE]
   >Si no se cumplen los requisitos previos de Mobility Service para actualizar la máquina virtual, esta no se puede seleccionar. Vea información sobre [cómo resolver este problema](#resolve-blocking-issues-for-agent-upgrade).


4. Después de iniciar la actualización, se crea un trabajo de Site Recovery en el almacén para cada operación de actualización y puede realizar un seguimiento si navega a **Supervisión** > **Trabajos de Site Recovery**.

### <a name="update-mobility-agent-for-a-single-protected-machine"></a>Actualización del agente de movilidad para una sola máquina protegida

Para actualizar el agente de movilidad de un elemento protegido, siga estos pasos:
1. Vaya al **almacén de Recovery Services** > **Elementos replicados** y seleccione una máquina virtual.
2. En la hoja **Información general** de la máquina virtual, en **Versión del agente**, vea la versión actual del agente de movilidad. Si hay disponible una nueva actualización, el estado se actualiza como **Nueva actualización disponible**.

   ![Actualización del agente de movilidad en un solo elemento protegido](./media/upgrade-mobility-service-preview/agent-version.png)

3. Haga clic en **Nueva actualización disponible** y se mostrará la versión más reciente disponible. Haga clic en **Actualizar a esta versión** para iniciar el trabajo de actualización.

   ![detalles de actualización del agente de movilidad](./media/upgrade-mobility-service-preview/agent-update-details.png)

   > [!NOTE]
   > Si la actualización está bloqueada, compruebe y resuelva los errores como se detalla [aquí](#resolve-blocking-issues-for-agent-upgrade).

## <a name="mobility-agent-on-latest-version"></a>Agente de Movilidad en la versión más reciente

Una vez que el agente de movilidad se actualiza a la versión más reciente o se actualiza automáticamente a la versión más reciente, el estado se muestra como **Actualizado**.

### <a name="resolve-blocking-issues-for-agent-upgrade"></a>Resolución de problemas de bloqueo para la actualización del agente

Si no se cumplen los requisitos previos para actualizar el agente de movilidad, la máquina virtual no se puede actualizar. Resuelva estos problemas para continuar con la actualización.

El requisito previo incluye, entre otros aspectos:

- Un reinicio obligatorio pendiente en la máquina protegida.

- Si el dispositivo de replicación está en una versión incompatible.

- Si los componentes del dispositivo de replicación (el servidor proxy o el servidor de procesos) no pueden comunicarse con los servicios de Azure.

- Si el agente de movilidad de la máquina protegida no puede comunicarse con el dispositivo de replicación.

En caso de que cualquiera de los problemas anteriores sea aplicable, el estado se actualiza como **No se puede actualizar a la versión más reciente**. Haga clic en el estado para ver los motivos por los que se bloquea la actualización y las acciones recomendadas para corregir el problema.

>[!NOTE]
>Después de resolver los motivos de bloqueo, espere 30 minutos para volver a intentar las operaciones. La información más reciente tarda tiempo en actualizarse en los servicios de Site Recovery.

### <a name="mobility-agent-upgrade-job-failure"></a>Error de trabajo de actualización del agente de movilidad

En caso de que se produzca un error en la operación de actualización del agente de movilidad (operación de actualización automática o desencadenada manualmente), el trabajo se actualiza con el motivo del error. Resuelva los errores e intente la operación de nuevo.

Para ver los errores, puede ir a Trabajos de Site Recovery y hacer clic en un trabajo específico para obtener la resolución de los errores. También puede seguir estos pasos:

1. Vaya a la sección de elementos replicados y seleccione una máquina virtual específica.

2. En la hoja **Información general**, en **Versión del agente**, se muestra la versión actual del agente de movilidad.

3. Junto a la versión actual, el estado se actualiza con el mensaje **Error de actualización**. Haga clic en el estado para reintentar la operación de actualización.

4.  Hay disponible un vínculo al trabajo de actualización anterior. Haga clic en el trabajo para ir al trabajo específico.

5. Resuelva los errores del trabajo anterior.

Desencadene la operación de actualización después de resolver los errores del trabajo con errores anterior.

## <a name="upgrade-appliance"></a>Actualización del dispositivo

De manera predeterminada, las actualizaciones automáticas están habilitadas en el dispositivo. Las actualizaciones automáticas se desencadenan a las 12:00 a. m. hora local todos los días si hay una nueva versión disponible para cualquiera de los componentes.

Para comprobar el estado de actualización de cualquiera de los componentes, vaya al servidor del dispositivo y abra **Configuration Manager del dispositivo de Microsoft Azure**. Vaya a **Componentes del dispositivo** y expándalo para ver la lista de todos los componentes y su versión.

Si alguna de estas opciones debe actualizarse, el **estado** lo indicará. Haga clic en el mensaje de estado para actualizar el componente.

  ![componentes del dispositivo de replicación](./media/upgrade-mobility-service-preview/appliance-components.png)

### <a name="resolve-issues-with-component-upgrade"></a>Resolución de problemas con la actualización de componentes

Si no se cumplen los requisitos previos para actualizar alguno de los componentes, no se puede actualizar. Los motivos y requisitos previos incluyen, entre otros,

- Si uno de los componentes del dispositivo de replicación está en una versión incompatible.

- Si el dispositivo de replicación no puede comunicarse con los servicios de Azure.

En caso de que cualquiera de los problemas anteriores sea aplicable, el estado se actualiza como **No se puede actualizar a la versión más reciente**. Haga clic en el estado para ver los motivos por los que se bloquea la actualización y las acciones recomendadas para corregir el problema. Después de resolver los motivos de bloqueo, pruebe la actualización manualmente.
