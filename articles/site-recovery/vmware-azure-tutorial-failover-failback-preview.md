---
title: Conmutación por error de máquinas virtuales de VMware a Azure con Site Recovery - Versión preliminar
description: Aprenda a conmutar por error máquinas virtuales de VMware a Azure en Azure Site Recovery - Versión preliminar
ms.service: site-recovery
ms.topic: tutorial
ms.date: 08/19/2021
ms.custom: MVC
ms.openlocfilehash: 42ccd96287fefed0a6c7ef4cee8ddf776541c8ba
ms.sourcegitcommit: 8000045c09d3b091314b4a73db20e99ddc825d91
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/19/2021
ms.locfileid: "122447138"
---
# <a name="fail-over-vmware-vms---preview"></a>Conmutación por error de máquinas virtuales de VMware - Versión preliminar

En este artículo se describe cómo conmutar por error una máquina virtual (VM) local de VMware a Azure con [Azure Site Recovery](site-recovery-overview.md) - Versión preliminar

Para obtener información sobre la conmutación por error en versiones clásicas, consulte [este artículo](vmware-azure-tutorial-failover-failback.md).

En este tutorial, aprenderá a:

> [!div class="checklist"]
> * Comprobar que las propiedades de la máquina virtual de VMware cumplen los requisitos de Azure
> * Conmutar por error máquinas virtuales específicas a Azure.

> [!NOTE]
> Los tutoriales muestran la ruta de implementación más sencilla para un escenario. Usan opciones predeterminadas siempre que es posible y no muestran todos los valores y rutas de acceso posibles. Si quiere conocer en detalle la conmutación por error, consulte [Conmutación por error de servidores físicos y máquinas virtuales de VMware](site-recovery-failover.md).

[Más información](failover-failback-overview.md#types-of-failover) sobre los diferentes tipos de conmutación por error. Si quiere conmutar por error varias máquinas virtuales de un plan de recuperación, revise [este artículo](site-recovery-failover.md).

## <a name="before-you-start"></a>Antes de comenzar

Complete los tutoriales anteriores:

1. Asegúrese de que ha [configurado Azure](tutorial-prepare-azure.md) para la recuperación ante desastres en un entorno local de máquinas virtuales de VMware.
2. Prepare el entorno de [VMware](vmware-azure-tutorial-prepare-on-premises.md) local para la recuperación ante desastres.
3. Configure la recuperación ante desastres para [máquinas virtuales de VMware](vmware-azure-set-up-replication-tutorial-preview.md).
4. Realice una [exploración en profundidad de la recuperación ante desastres](tutorial-dr-drill-azure.md) para asegurarse de que todo funciona según lo previsto.

## <a name="verify-vm-properties"></a>Comprobar las propiedades de la máquina virtual

Antes de ejecutar una conmutación por error, compruebe las propiedades de las máquinas virtuales para asegurarse de que cumplen los [requisitos de Azure](vmware-physical-azure-support-matrix.md#replicated-machines).

Compruebe que son estas las propiedades:

1. En **Elementos protegidos**, seleccione **Elementos replicados** y, luego, seleccione la máquina virtual que quiere comprobar.

2. En el panel **Elemento replicado**, puede ver un resumen de la información de la máquina virtual, el estado de mantenimiento y los puntos de recuperación disponibles más recientes. Seleccione **Propiedades** para ver más detalles.

3. En **Proceso y red**, estas propiedades se pueden modificar según sea necesario:
    * Nombre de Azure
    * Resource group
    * Tamaño de destino
    * Configuración de discos administrados

4. Puede ver y modificar la configuración de red, por ejemplo:

    * La red y la subred en la que se ubicará la máquina virtual de Azure después de la conmutación por error.
    * La dirección IP que se le asignará.

5. En **Discos** puede ver información sobre los discos de datos y el sistema operativo de la máquina virtual.

## <a name="run-a-failover-to-azure"></a>Ejecutar una conmutación por error en Azure.

1. En **Configuración** > **Elementos replicados**, seleccione la máquina virtual que quiere conmutar por error y, luego, seleccione **Conmutar por error**.
2. En **Conmutación por error**, seleccione un **Punto de recuperación** en el que realizar la conmutación por error. Puede seleccionar una de las siguientes opciones:
   * **Último**: esta opción procesa primero todos los datos enviados a Site Recovery. Proporciona el objetivo de punto de recuperación (RPO) mínimo, ya que la máquina virtual de Azure creada después de la conmutación por error tiene todos los datos que se replicaron en Site Recovery al desencadenarse la conmutación por error.
   * **Procesado más recientemente**: con esta opción se realiza una conmutación por error de la máquina virtual al último punto de recuperación procesado por Site Recovery. Esta opción proporciona un objetivo de tiempo de recuperación (RTO) bajo, ya que no se invierte tiempo en el procesamiento de datos sin procesar.
   * **Más reciente coherente con la aplicación**: con esta opción se realiza una conmutación por error de la máquina virtual al punto de recuperación más reciente coherente con la aplicación que procesó Site Recovery.
   * **Personalizado**: esta opción permite especificar un punto de recuperación.

3. Seleccione **Apague la máquina antes de comenzar con la conmutación por error.** para intentar apagar las máquinas virtuales de origen antes de desencadenar la conmutación por error. La conmutación por error continúa aunque se produzca un error de cierre. Puede seguir el progreso de la conmutación por error en la página **Trabajos**.

  En algunos escenarios, la conmutación por error requiere un procesamiento adicional que tarda aproximadamente de ocho a diez minutos en completarse. Puede observar que la conmutación por error de prueba tarda más tiempo en realizarse:

  * Máquinas virtuales de VMware Linux
  * Máquinas virtuales de VMware que no tienen habilitado el servicio DHCP
  * Máquinas virtuales de VMware que no tienen los siguientes controladores de arranque: storvsc, vmbus, storflt, intelide o atapi.

  > [!WARNING]
  > No cancele una conmutación por error en curso. Antes de iniciar la conmutación por error, se detiene la replicación de la máquina virtual. Si se cancela una conmutación por error en curso, la conmutación por error se detiene, pero no se replica la máquina virtual de nuevo.

## <a name="connect-to-failed-over-vm"></a>Conexión a la máquina virtual conmutada por error

1. Si quiere conectarse a las máquinas virtuales de Azure después de la conmutación por error mediante el Protocolo de escritorio remoto (RDP) y Secure Shell (SSH), [compruebe que se han cumplido los requisitos](failover-failback-overview.md#connect-to-azure-after-failover).
2. Después de la conmutación por error, vaya a la máquina virtual y [conéctese](../virtual-machines/windows/connect-logon.md) a ella para realizar la validación.
3. Use **Cambiar punto de recuperación** si desea usar otro punto de recuperación después de la conmutación por error. Después de confirmar la conmutación por error en el paso siguiente, esta opción dejará de estar disponible.
4. Tras la validación, seleccione **Confirmar** para finalizar el punto de recuperación de la máquina virtual después de la conmutación por error.
5. Tras la confirmación, los demás puntos de recuperación disponibles se eliminan. Este paso finaliza la conmutación por error.

>[!TIP]
> Si tiene algún problema de conectividad después de la conmutación por error, siga la [guía para la solución de problemas](site-recovery-failover-to-azure-troubleshoot.md).

## <a name="planned-failover-from-azure-to-on-premises"></a>Conmutación por error planeada desde Azure en el entorno local

Puede realizar una conmutación por error planeada de Azure a un entorno local. Puesto que se trata de una actividad de conmutación por error planeada, el punto de recuperación se genera después de que se desencadene el trabajo de conmutación por error planeado.

>[!NOTE]
> Antes de continuar, asegúrese de que el estado de replicación de la máquina es correcto. Asegúrese también de que el dispositivo y todos sus componentes están en un estado correcto.

Cuando se desencadena la conmutación por error planeada, los cambios pendientes se copian en el entorno local, se genera un punto de recuperación más reciente de la máquina virtual y se apaga la máquina virtual de Azure. Después de esto, la máquina local está activada.

Después de una conmutación por error planeada correctamente, la máquina estará activa en el entorno local.

> [!NOTE]
> Si la máquina protegida tiene discos iSCSI, la configuración se conserva en Azure tras la conmutación por error. Después de la conmutación por error planeada de Azure en el entorno local, no se puede conservar la configuración de iSCSI. Por lo tanto, los discos vmdk se crean en la máquina local. Para quitar discos duplicados, elimine el disco iSCSI a medida que los datos se reemplazan por discos vmdk.


### <a name="failed-over-vm-to-azure---requirements"></a>Conmutación por error de máquina virtual en Azure: requisitos

Asegúrese de que la máquina virtual cumple con lo siguiente, después de que se haya conmutado por error en Azure:

1. La máquina virtual en Azure siempre debe estar encendida.
2. Asegúrese de que los servicios de agente de movilidad, *servicio 1* y *servicio 2*, se ejecutan en la máquina virtual. Esto es para garantizar que el agente de movilidad de la máquina virtual puede comunicarse con los servicios de Azure Site Recovery en Azure.
3. Las direcciones URL mencionadas [aquí](vmware-azure-architecture-preview.md#set-up-outbound-network-connectivity) son accesibles desde la máquina virtual.

## <a name="cancel-planned-failover"></a>Cancelar conmutación por error planeada

Si el entorno local no está listo o en caso de que se produzca algún desafío, puede cancelar la conmutación por error planeada. Puede realizar una conmutación por error planeada en cualquier momento más tarde, una vez que las condiciones locales se vuelvan favorables.

**Para cancelar una conmutación por error planeada**:

1. Vaya a la máquina en el almacén de servicios de recuperación y seleccione **Cancelar conmutación por error**.
2. Haga clic en **Aceptar**.
3. Asegúrese de leer la información sobre en qué consiste la operación de *cancelación de la conmutación por error*.

Si hay algún problema que impida que Azure Site Recovery cancele correctamente el trabajo fallido, siga los pasos recomendados que se proporcionan en el trabajo. Después de seguir la acción recomendada, vuelva a intentar la cancelación del trabajo.

Se cancelará la operación de conmutación por error planeada anterior. La máquina de Azure volverá al estado en el que se encontraba justo antes de que se desencadenara la *conmutación por error planeada*.

Para la conmutación por error planeada, después de desasociar los discos de máquina virtual del dispositivo, tomaremos una instantánea antes de encenderla.

Si la máquina virtual no arranca correctamente o alguna aplicación no se inicia adecuadamente, o por alguna razón decide cancelar la conmutación por error planeada e intentarlo de nuevo, entonces:

1. Revertiríamos todos los cambios realizados.

2. Vuelva a poner los discos en el estado en el que se encontraban antes de encenderlos, mediante las instantáneas tomadas anteriormente.

3. Por último, conecte los discos de nuevo al dispositivo y reanude la replicación.

Este comportamiento es distinto del que tenía la arquitectura heredada o clásica.

- Con la versión preliminar, puede volver a realizar la operación de conmutación por recuperación en un momento posterior.

- En la arquitectura heredada, no puede cancelar ni volver a intentar la conmutación por recuperación si la máquina virtual no arranca, o la aplicación no se inicia, o por cualquier otro motivo.  


> [!NOTE]
> Solo se puede cancelar la conmutación por error planeada de Azure a un entorno local. No se puede cancelar la conmutación por error del entorno local a Azure.

### <a name="planned-failover---failure"></a>Error de la conmutación por error planeada

Si se produce un error en la conmutación por error planeada, Azure Site Recovery inicia automáticamente un trabajo para cancelar el trabajo fallido y recupera el estado de la máquina en el que se encontraba justo antes de la conmutación por error planeada.

En caso de que se produzca un error en la cancelación del último trabajo de conmutación por error planeado, Azure Site Recovery le pedirá que inicie la cancelación manualmente.

Esta información se proporciona como parte de la operación de conmutación por error planeada fallida y como un problema de mantenimiento del elemento replicado.

Si el problema persiste, póngase en contacto con el servicio de soporte técnico de Microsoft. **No** deshabilite la replicación.

## <a name="re-protect-the-on-premises-machine-to-azure-after-successful-planned-failover"></a>Volver a proteger la máquina local en Azure después de una conmutación por error planeada correcta

Después de una conmutación por error planeada correcta, la máquina está activa en el entorno local. Para proteger la máquina en el futuro, asegúrese de que la máquina se replique en Azure (protegida).

Para ello, vaya a la máquina > **Volver a proteger**, seleccione el dispositivo que prefiera, seleccione la directiva de replicación y continúe.

Después de habilitar correctamente la replicación y la replicación inicial, se generarán puntos de recuperación para ofrecer continuidad empresarial ante interrupciones no deseadas.

## <a name="next-steps"></a>Pasos siguientes

Después de la conmutación por error, vuelva a proteger las máquinas virtuales de Azure en el entorno local. Después de que las máquinas virtuales vuelvan a protegerse y se repliquen en el sitio local, realice una conmutación por recuperación desde Azure cuando esté a punto.

> [!div class="nextstepaction"]
> [Volver a proteger máquinas virtuales de Azure](vmware-azure-reprotect.md)
> [Conmutación por recuperación desde Azure](vmware-azure-failback.md)
