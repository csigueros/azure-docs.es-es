---
title: Tutorial para conmutar por error máquinas virtuales de Azure a una región secundaria para la recuperación ante desastres con Azure Site Recovery.
description: Tutorial para aprender tanto a conmutar por error como a reproteger las máquinas virtuales de Azure replicadas en una región secundaria de Azure para la recuperación ante desastres con el servicio Azure Site Recovery.
ms.topic: tutorial
ms.date: 11/05/2020
ms.custom: mvc
ms.openlocfilehash: 692b6645b2446fa55233506a0479b95b0743b029
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/22/2021
ms.locfileid: "130223165"
---
# <a name="tutorial-fail-over-azure-vms-to-a-secondary-region"></a>Tutorial: Conmutación por error de máquinas virtuales de Azure a una región secundaria

Aprenda a conmutar por error aquellas máquinas virtuales de Azure qué estén habilitadas para la recuperación ante desastres con [Azure Site Recovery](site-recovery-overview.md) a una región de Azure secundaria. Después de la conmutación por error, vuelva a proteger las máquinas virtuales de la región de destino para que se vuelvan a replicar en la región primaria. En este artículo aprenderá a:

> [!div class="checklist"]
> * Comprobar los requisitos previos.
> * Comprobar la configuración de una máquina virtual.
> * Ejecutar una conmutación por error en la región secundaria.
> * Empezar a replicar de nuevo la máquina virtual en la región primaria.


> [!NOTE]
> En este tutorial se muestra cómo realizar la conmutación por error de máquinas virtuales con el mínimo número de pasos. Si desea ejecutar una conmutación por error con una configuración completa, obtenga información sobre las [redes](azure-to-azure-about-networking.md), la [automatización](azure-to-azure-powershell.md) y la [solución de problemas](azure-to-azure-troubleshoot-errors.md) de las máquinas virtuales de Azure.



## <a name="prerequisites"></a>Requisitos previos

Antes de comenzar este tutorial, debe haber:

1. Configurado la replicación en una o varias máquinas virtuales de Azure. Si no lo ha hecho, [complete el primer tutorial](azure-to-azure-tutorial-enable-replication.md) de esta serie.
2. Se recomienda [realizar un simulacro de la recuperación ante desastres](azure-to-azure-tutorial-dr-drill.md) en las máquinas virtuales replicadas. La realización de un simulacro antes de ejecutar una conmutación por error completa ayuda a garantizar que todo funciona según lo previsto sin afectar al entorno de producción. 


## <a name="verify-the-vm-settings"></a>Comprobación de la configuración de la máquina virtual

1. En el almacén > **Elementos replicados**, seleccione la máquina virtual.

    ![Opción para abrir las propiedades de la máquina virtual en la página de información general](./media/azure-to-azure-tutorial-failover-failback/vm-settings.png)

2. En la página **Información general** de la máquina virtual, compruebe que está protegida y en buen estado antes de ejecutar una conmutación por error.
    ![Página para comprobar las propiedades y el estado de la máquina virtual](./media/azure-to-azure-tutorial-failover-failback/vm-state.png)

3. Antes de la conmutación por error compruebe que:
    - La máquina virtual usa un sistema operativo [Windows](azure-to-azure-support-matrix.md#windows) o [Linux](azure-to-azure-support-matrix.md#replicated-machines---linux-file-systemguest-storage) compatible.
    - La máquina virtual cumple los requisitos de [proceso](azure-to-azure-support-matrix.md#replicated-machines---compute-settings), [almacenamiento](azure-to-azure-support-matrix.md#replicated-machines---storage) y [red](azure-to-azure-support-matrix.md#replicated-machines---networking).

## <a name="run-a-failover"></a>Ejecución de la conmutación por error


1. En la página **Información general**, seleccione **Conmutación por error**.

    ![Botón de conmutación por error del elemento replicado](./media/azure-to-azure-tutorial-failover-failback/failover-button.png)

3. En **Conmutación por error**, elija un punto de recuperación. Se usan los datos de este punto de recuperación para crear la máquina virtual de Azure en la región de destino.
  
   - **Procesado más recientemente**: usa el último punto de recuperación procesado por Site Recovery. Se muestra la marca de tiempo. No se emplea tiempo en el procesamiento de datos, por lo que se proporciona un objetivo de tiempo de recuperación (RTO) bajo.
   -  **Más reciente**: procesa todos los datos enviados a Site Recovery para crear un punto de recuperación para cada máquina virtual antes de conmutarla por error a dicho punto de recuperación. Proporciona el menor objetivo de punto de recuperación (RPO), ya que todos los datos se replican a Site Recovery cuando se desencadena la conmutación por error.
   - **Más reciente coherente con la aplicación**: esta opción conmuta por error las VM en el punto de recuperación más reciente coherente con la aplicación. Se muestra la marca de tiempo.
   - **Personalizado**: conmuta por error a un punto de recuperación concreto. La personalización solo está disponible cuando se conmuta por error una sola máquina virtual y no se usa un plan de recuperación.

    > [!NOTE]
    > Si agregó un disco a una máquina virtual después de habilitar la replicación, los puntos de replicación muestran los discos que se pueden recuperar. Por ejemplo, un punto de replicación creado antes de que agregara un segundo disco se mostrará como "1 de 2 discos".

4. Seleccione **Shut down machine before beginning failover** (Apagar la máquina antes de comenzar la conmutación por error) si desea que Site Recovery intente apagar las máquinas virtuales de origen antes de iniciar la conmutación por error. La operación de apagado ayuda a garantizar que no se pierden datos. La conmutación por error continúa aunque se produzca un error de cierre. 

    ![Página de configuración de la conmutación por error](./media/azure-to-azure-tutorial-failover-failback/failover-settings.png)    

3. Seleccione **Aceptar** para iniciar la conmutación por error.
4. Supervise la conmutación por error en las notificaciones.

    ![Notificación de progreso](./media/azure-to-azure-tutorial-failover-failback/notification-failover-start.png) ![Notificación de éxito](./media/azure-to-azure-tutorial-failover-failback/notification-failover-finish.png)     

5. Después de la conmutación por error, la máquina virtual de Azure creada en la región de destino aparece en **Máquinas virtuales**. Asegúrese de que la máquina virtual se ejecuta y que tiene el tamaño adecuado. Si desea usar otro punto de recuperación para la máquina virtual, seleccione **Cambiar punto de recuperación** en la página **Essentials**.
6. Cuando esté satisfecho con la máquina virtual conmutada por error, seleccione **Confirmar** en la página información general para finalizar la conmutación por error.

    ![Botón Confirmar](./media/azure-to-azure-tutorial-failover-failback/commit-button.png) 

7. En **Confirmar**, seleccione **Aceptar** para confirmar. Confirmar elimina todos los puntos de recuperación disponibles para la máquina virtual en Site Recovery, por lo que no podrá cambiar el punto de recuperación.

8. Supervise el progreso de la confirmación en las notificaciones.

    ![Notificación de progreso de confirmación](./media/azure-to-azure-tutorial-failover-failback/notification-commit-start.png) ![Notificación de confirmación realizada correctamente](./media/azure-to-azure-tutorial-failover-failback/notification-commit-finish.png)    

## <a name="reprotect-the-vm"></a>Reprotección de la máquina virtual

Después de la conmutación por error, vuelva a proteger la máquina virtual en la región secundaria para que se vuelva a replicar en la región primaria. 

1. Asegúrese de que en el campo **Estado** de la máquina virtual aparece *Conmutación por error confirmada* antes de empezar.
2. Compruebe que puede acceder a la región primaria y que tiene permisos para crear máquinas virtuales en ella.
3. En la página **Información general**, seleccione **Volver a proteger**.

   ![Botón para habilitar la reprotección de una máquina virtual para una máquina virtual.](./media/azure-to-azure-tutorial-failover-failback/reprotect-button.png)

4. En **Volver a proteger**, compruebe la dirección de replicación (región secundaria a primaria) y revise la configuración de destino de la región primaria. Los recursos marcados como nuevos los crea Site Recovery como parte de la operación de reprotección.

     ![Página Configuración de la reprotección](./media/azure-to-azure-tutorial-failover-failback/reprotect.png)

6. Seleccione **Aceptar** para iniciar el proceso de reprotección. El proceso envía los datos iniciales a la ubicación de destino y, después, replica la información delta de las máquinas virtuales en el destino.
7. Supervisión del progreso de la reprotección en las notificaciones. 

    ![Notificación del progreso de la reprotección ](./media/azure-to-azure-tutorial-failover-failback/notification-reprotect-start.png) ![Notificación de reprotección correcta](./media/azure-to-azure-tutorial-failover-failback/notification-reprotect-finish.png)
    

## <a name="next-steps"></a>Pasos siguientes

En este tutorial, ha realizado una conmutación por error desde la región primaria a la secundaria y ha empezado a replicar las máquinas virtuales en la región primaria. Ahora puede realizar una conmutación por recuperación desde la región secundaria a la primaria.

> [!div class="nextstepaction"]
> [Conmutación por recuperación a la región principal](azure-to-azure-tutorial-failback.md)
