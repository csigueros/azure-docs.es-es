---
title: 'Acerca de la conmutación por error y la conmutación por recuperación en Azure Site Recovery: versión preliminar'
description: 'Obtenga información acerca de la conmutación por error y la conmutación por recuperación en Azure Site Recovery: versión preliminar.'
ms.topic: conceptual
ms.date: 08/19/2021
ms.openlocfilehash: d7e08623a1e297d618365a126f835f88e9dfb716
ms.sourcegitcommit: 8000045c09d3b091314b4a73db20e99ddc825d91
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/19/2021
ms.locfileid: "122446745"
---
# <a name="about-on-premises-disaster-recovery-failoverfailback---preview"></a>Acerca de la conmutación por error o conmutación por recuperación de la recuperación ante desastres local: versión preliminar

En este artículo se proporciona información general sobre la conmutación por error y la conmutación por recuperación durante la recuperación ante desastres de máquinas locales en Azure mediante [Azure Site Recovery](site-recovery-overview.md): versión preliminar.

Para obtener información sobre la conmutación por error y la conmutación por recuperación en las versiones clásicas de Azure Site Recovery, [consulte este artículo](failover-failback-overview.md).

## <a name="recovery-stages"></a>Fases de recuperación

La conmutación por error y la conmutación por recuperación en Site Recovery constan de cuatro fases:

- **Fase 1: conmutación por error desde una ubicación local**: después de configurar la replicación en Azure para las máquinas locales, cuando el sitio local deje de funcionar, dichas máquinas se conmutarán por error en Azure. Tras la conmutación por error, se crean máquinas virtuales de Azure a partir de los datos replicados.
- **Fase 2: reproteger las máquinas virtuales de Azure**: en Azure, las máquinas virtuales de Azure se reprotegen para que empiecen a replicarse de nuevo en el sitio local. La máquina virtual local (si está disponible) está apagada durante la reprotección, para ayudar a garantizar la coherencia de los datos.
- **Fase 3: conmutación por error desde Azure**: cuando el sitio local funcione nuevamente con normalidad, ejecute otra conmutación por error. Esta vez, para conmutar por recuperación las máquinas virtuales de Azure en el sitio local. Puede realizar una conmutación por recuperación en la ubicación original de la conmutación por error o en una ubicación alternativa. Esta actividad se conoce como *conmutación por error planeada*.
- **Fase 4: reprotección de máquinas locales en Azure**: después de la conmutación por recuperación, habilite de nuevo la replicación de las máquinas locales en Azure.

## <a name="failover"></a>Conmutación por error

Se realiza una conmutación por error como parte de la estrategia de continuidad empresarial y recuperación ante desastres (BCDR).

- Como primer paso de la estrategia de BCDR, puede replicar las máquinas locales en Azure de forma continua. Los usuarios tienen acceso a las cargas de trabajo y aplicaciones que se ejecutan en las máquinas de origen locales.
- Si surge la necesidad (por ejemplo, si hay una interrupción en el entorno local), las máquinas de replicación se conmutan por error en Azure. Las máquinas virtuales de Azure se crean mediante los datos replicados.
- Para la continuidad empresarial, los usuarios pueden seguir accediendo a las aplicaciones en las máquinas virtuales de Azure.

La conmutación por error es una actividad de dos fases:

- **Conmutación por error**: la conmutación por error que crea y pone en marcha una máquina virtual de Azure mediante el punto de recuperación seleccionado.
- **Confirmación**: después de la conmutación por error, compruebe la máquina virtual en Azure:
    - Después, puede confirmar la conmutación por error al punto de recuperación seleccionado, o seleccionar un punto diferente para la confirmación.
    - No se puede cambiar el punto de recuperación después de confirmar la conmutación por error.


## <a name="connect-to-azure-after-failover"></a>Conexión a Azure después de la conmutación por error

Para conectarse a las máquinas virtuales de Azure creadas después de la conmutación por error mediante RDP o SSH, debe cumplir varios requisitos.

**Conmutación por error** | **Ubicación** | **Acciones**
--- | --- | ---
**Máquina virtual de Azure que ejecuta Windows** | En la máquina local antes de la conmutación por error | **Acceso a través de Internet**: habilite RDP. Asegúrese de que se hayan agregado las reglas de TCP y UDP para **Público**, y que RDP se permite en todos los perfiles de **Firewall de Windows** > **Aplicaciones permitidas**.<br/><br/> **Acceso a través de VPN de sitio a sitio**: habilite RDP en la máquina. Compruebe que se permite RDP en **Firewall de Windows** -> **Aplicaciones y características permitidas** para redes de **dominio y privadas**.<br/><br/>  Asegúrese de que la directiva SAN del sistema operativo está establecida en **OnlineAll**. [Más información](https://support.microsoft.com/kb/3031135).<br/><br/> Asegúrese de que no haya actualizaciones de Windows pendientes en la máquina virtual cuando se desencadena una conmutación por error. Windows Update puede iniciarse cuando realice la conmutación por error y no podrá iniciar sesión en la máquina virtual hasta que se completen las actualizaciones.
**Máquina virtual de Azure que ejecuta Windows** | En la máquina virtual de Azure después de la conmutación por error |  [Agregue una dirección IP pública](/archive/blogs/srinathv/how-to-add-a-public-ip-address-to-azure-vm-for-vm-failed-over-using-asr) para la máquina virtual.<br/><br/> Las reglas del grupo de seguridad de red de la máquina virtual conmutada por error (y la subred de Azure a la que esta se conecta) deben permitir las conexiones entrantes al puerto de RDP.<br/><br/> Seleccione **Diagnósticos de arranque** para comprobar una captura de pantalla de la máquina virtual. Si no puede conectarse, compruebe que se está ejecutando la máquina virtual y revise las [sugerencias de solución de problemas](https://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx).
**Máquina virtual de Azure que ejecuta Linux** | En la máquina local antes de la conmutación por error | Asegúrese de que el servicio de Secure Shell en la máquina virtual está configurado para iniciarse automáticamente en el arranque del sistema.<br/><br/> Compruebe que las reglas de firewall permiten una conexión SSH.
**Máquina virtual de Azure que ejecuta Linux** | En la máquina virtual de Azure después de la conmutación por error | Las reglas del grupo de seguridad de red de la máquina virtual conmutada por error (y la subred de Azure a la que esta se conecta) necesitan permitir las conexiones entrantes al puerto SSH.<br/><br/> [Agregue una dirección IP pública](/archive/blogs/srinathv/how-to-add-a-public-ip-address-to-azure-vm-for-vm-failed-over-using-asr) para la máquina virtual.<br/><br/> Seleccione **Diagnósticos de arranque** para obtener una captura de pantalla de la máquina virtual.<br/><br/>

## <a name="types-of-failover"></a>Tipos de conmutación por error

Site Recovery proporciona diferentes opciones de conmutación por error.

**Conmutación por error** | **Detalles** | **Recuperación** | **Workflow**
--- | --- | --- | ---
**Conmutación por error de prueba** | Se usa para ejecutar un simulacro que valida la estrategia de BCDR sin pérdida de datos ni tiempo de inactividad.| Crea una copia de la máquina virtual en Azure, sin afectar a la replicación en curso ni al entorno de producción. | 1. Ejecute una conmutación por error de prueba en una sola máquina virtual o en varias en un plan de recuperación.<br/><br/> 2. Seleccione un punto de recuperación para usarlo en la conmutación por error de prueba.<br/><br/> 3. Seleccione una red de Azure en la que la máquina virtual de Azure se encuentra cuando se crea después de la conmutación por error. La red solo se usa para la conmutación por error de prueba.<br/><br/> 4. Compruebe que el simulacro funciona según lo previsto. Site Recovery limpia automáticamente las máquinas virtuales creadas en Azure durante el simulacro.
**Conmutación por error planeada de Hyper-V**  | Normalmente se usa para el tiempo de inactividad planeado.<br/><br/> Las máquinas virtuales de origen se apagan. Los datos más recientes se sincronizan antes de iniciar la conmutación por error. | Cero pérdida de datos para el flujo de trabajo planeado. | 1. Planee un período de inactividad por mantenimiento y notifique a los usuarios.<br/><br/> 2. Desconecte las aplicaciones orientadas al usuario.<br/><br/> 3. Inicie una conmutación por error planeada con el punto de recuperación más reciente. La conmutación por error no se ejecuta si el equipo no se apaga, o si se producen errores.<br/><br/> 4. Después de la conmutación por error, compruebe que la máquina virtual de Azure de réplica está activa en Azure.<br/><br/> 5. Confirme la conmutación por error para finalizar. La acción de confirmación elimina todos los puntos de recuperación.
**Conmutación por error de Hyper-V** | Normalmente se ejecuta si se produce una interrupción no planeada o el sitio primario no está disponible.<br/><br/> También puede apagar la máquina virtual y sincronizar los cambios finales antes de iniciar la conmutación por error.  | Pérdida de datos mínima para las aplicaciones. | 1. Inicie el plan de BCDR. <br/><br/> 2. Inicie una conmutación por error. Especifique si Site Recovery debe apagar la máquina virtual y sincronizar/replicar los cambios más recientes antes de desencadenar la conmutación por error.<br/><br/> 3. Puede conmutar por error a varias opciones de puntos de recuperación, que están resumidas en la tabla siguiente.<br/><br/> Si no habilita la opción para apagar la máquina virtual, o si Site Recovery no puede apagarla, se usa el punto de recuperación más reciente.<br/>La conmutación por error se ejecuta incluso si la máquina no se puede apagar.<br/><br/> 4. Después de la conmutación por error, debe comprobar que la máquina virtual de Azure de réplica está activa en Azure.<br/> Si es necesario, puede seleccionar un punto de recuperación diferente dentro del período de retención de 24 horas.<br/><br/> 5. Confirme la conmutación por error para finalizar. La acción de confirmación elimina todos los puntos de recuperación disponibles.
**Conmutación por error de VMware** | Normalmente se ejecuta si se produce una interrupción no planeada o el sitio primario no está disponible.<br/><br/> De manera opcional, especifique si Site Recovery debe tratar de apagar la máquina virtual y sincronizar y replicar los cambios finales antes de iniciar la conmutación por error.  | Pérdida de datos mínima para las aplicaciones. | 1. Inicie el plan de BCDR. <br/><br/> 2. Inicie una conmutación por error desde Site Recovery. Especifique si Site Recovery debe tratar de apagar y sincronizar la máquina virtual antes de ejecutar la conmutación por error.<br/> La conmutación por error se ejecuta incluso si las máquinas no se pueden apagar.<br/><br/> 3. Después de la conmutación por error, compruebe que la máquina virtual de Azure de réplica está activa en Azure. <br/>Si es necesario, puede seleccionar un punto de recuperación diferente dentro del período de retención de 72 horas.<br/><br/> 5. Confirme la conmutación por error para finalizar. La acción de confirmación elimina todos los puntos de recuperación.<br/> En el caso de las máquinas virtuales Windows, Site Recovery deshabilita las herramientas de VMware durante la conmutación por error.
**Conmutación por error planeada: VMware** | Puede realizar una conmutación por error planeada de Azure a un entorno local. | Puesto que se trata de una actividad de conmutación por error planeada, el punto de recuperación se genera después de que se desencadene el trabajo de conmutación por error planeado. | Cuando se desencadena la conmutación por error planeada, los cambios pendientes se copian en el entorno local, se genera un punto de recuperación más reciente de la máquina virtual y se apaga la máquina virtual de Azure.<br/><br/> Siga el proceso de conmutación por error como se describe [aquí](vmware-azure-tutorial-failover-failback-preview.md#planned-failover-from-azure-to-on-premises). Después de esto, la máquina local está activada. Después de una conmutación por error planeada correctamente, la máquina estará activa en el entorno local.

## <a name="failover-processing"></a>Procesamiento de la conmutación por error

En algunos escenarios, la conmutación por error requiere un procesamiento adicional que tarda aproximadamente de ocho a diez minutos en completarse. Puede observar que la conmutación por error de prueba tarda más tiempo en realizarse:

* Máquinas virtuales de VMware que no tienen habilitado el servicio DHCP
* Máquinas virtuales de VMware que no tienen los siguientes controladores de arranque: storvsc, vmbus, storflt, intelide o atapi.

## <a name="recovery-point-options"></a>Opciones de punto de recuperación

Durante la conmutación por error, puede seleccionar varias opciones de punto de recuperación.

**Opción** | **Detalles**
--- | ---
**Más reciente (RPO más bajo)** | Esta opción proporciona el objetivo de punto de recuperación (RPO) más bajo. Procesa primero todos los datos que se han enviado al servicio Site Recovery para crear un punto de recuperación para cada máquina virtual antes de conmutarla por error a dicho punto de recuperación. Este punto de recuperación tiene todos los datos replicados en Site Recovery cuando se desencadenó la conmutación por error.
**Procesado más recientemente**  | Con esta opción se realiza una conmutación por error de las máquinas virtuales al último punto de recuperación que procesó Site Recovery. Para ver el último punto de recuperación de una máquina virtual específica, seleccione **Puntos de recuperación más recientes** en la configuración de la máquina virtual. Esta opción proporciona un objetivo de tiempo de recuperación (RTO) bajo, ya que no se invierte tiempo en el procesamiento de datos sin procesar.
**Más reciente coherente con la aplicación** |  Esta opción conmuta por error las máquinas virtuales al punto de recuperación más reciente coherente con la aplicación que haya procesado Site Recovery si están habilitados los puntos de recuperación coherentes con la aplicación. Compruebe el punto de recuperación más reciente en la configuración de la máquina virtual.
**Último procesamiento de máquinas virtuales múltiples** | esta opción está disponible para los planes de recuperación con una o varias máquinas virtuales que tienen habilitada la coherencia entre varias máquinas virtuales. Máquinas virtuales con la conmutación por error habilitada para el último punto de recuperación común coherente con varias máquinas virtuales. Las demás máquinas virtuales del plan realizan la conmutación por error al último punto de recuperación procesado.
**Último coherente con aplicación de máquinas virtuales múltiples** |  esta opción está disponible para los planes de recuperación con una o varias máquinas virtuales que tienen habilitada la coherencia entre varias máquinas virtuales. Las máquinas virtuales que forman parte de un grupo de replicación conmutan por error al último punto de recuperación común coherente con la aplicación de varias máquinas virtuales. Otras máquinas virtuales conmutan por error a su punto de recuperación coherente con la aplicación más reciente.
**Personalizada** | Use esta opción para conmutar por error una máquina virtual específica a un punto de recuperación concreto en el tiempo. Esta opción no está disponible para los planes de recuperación.

> [!NOTE]
> Los puntos de recuperación no se pueden migrar a otro almacén de Recovery Services.

## <a name="reprotectionplanned-failover"></a>Reprotección/conmutación por error planeada

Después de la conmutación por error en Azure, las máquinas virtuales de Azure replicadas quedan en un estado no protegido.

- Como primer paso para la conmutación por recuperación al sitio local, debe iniciar las máquinas virtuales de Azure que se replican en el entorno local. El proceso de reprotección depende del tipo de máquinas que se conmutaron por error.
- Una vez que las máquinas se replican de Azure a un entorno local, puede ejecutar una conmutación por error de Azure al sitio local.
- Una vez que las máquinas de nuevo estén en ejecución de forma local, puede habilitar la replicación para que se repliquen en Azure para la recuperación ante desastres.

**La conmutación por error planeada funciona de la siguiente manera**:

- Para conmutar por recuperación en el entorno local, una máquina virtual necesita al menos un punto de recuperación para realizar la conmutación por recuperación. En un plan de recuperación, todas las máquinas virtuales del plan necesitan al menos un punto de recuperación.
- Como se trata de una actividad de conmutación por error planeada, podrá seleccionar el tipo de punto de recuperación al que desea conmutar por recuperación. Se recomienda usar un punto coherente con los bloqueos.
    - También existe una opción de punto de recuperación coherente con la aplicación. En este caso, una sola máquina virtual se recupera al último punto de recuperación coherente con la aplicación disponible. Para el caso de un plan de recuperación con un grupo de replicación, todos los grupos de replicación se recuperarán a su punto de recuperación disponible común.
    - Los puntos de recuperación coherentes con la aplicación pueden ser anteriores, por lo que podrían perderse datos.
- Durante la conmutación por error de Azure al sitio local, Site Recovery apaga las máquinas virtuales de Azure. Al confirmar la conmutación por error, Site Recovery quita las máquinas virtuales de Azure conmutadas por error en Azure.


## <a name="vmwarephysical-reprotectionfailback"></a>VMware/reprotección física/conmutación por recuperación

Para volver a proteger y conmutar por recuperación máquinas de VMware y servidores físicos de Azure a un entorno local, asegúrese de que tiene un dispositivo correcto.

**Selección del dispositivo**

- Puede seleccionar cualquiera de los dispositivos de replicación de Azure Site Recovery registrados en un almacén para volver a protegerlos en el entorno local. No necesita un servidor de procesos independiente en Azure para la operación de reprotección ni un servidor de destino maestro de escalabilidad horizontal para máquinas virtuales Linux.
- El dispositivo de replicación no requiere puertos o conexiones de red adicionales (en comparación con la protección hacia delante) durante la conmutación por recuperación. El mismo dispositivo se puede usar para las protecciones hacia delante y hacia atrás si está en estado correcto. No debería afectar al rendimiento de las replicaciones.
- Asegúrese de que el almacén de datos o el host seleccionado es el lugar en el que se encuentra el dispositivo y al que puede acceder el dispositivo seleccionado.
  > [!NOTE]
  > Storage vMotion del dispositivo de replicación no se admite después de la operación de reprotección.


**Reprotección de un trabajo**

- Si se trata de una nueva operación de reprotección, de manera predeterminada, Azure Site Recovery creará automáticamente una nueva cuenta de almacenamiento de registros en la región de destino. El disco de retención no es necesario.
- En el caso de la recuperación de ubicación alternativa y la recuperación de ubicación original, se recuperarán las configuraciones originales de las máquinas de origen.
  > [!NOTE]
  > - La dirección IP estática no se puede conservar en el caso de la reprotección de ubicación alternativa (ALR) o la reprotección de ubicación original (OLR).
  > - Se cambiaría fstab, LVMconf.


**Error**

- Se puede reintentar cualquier trabajo de reprotección con errores. Durante el reintento, puede elegir cualquier dispositivo de replicación correcto.

Al volver a proteger las máquinas de Azure en el entorno local, se le notificará que está con errores en la ubicación original o en una ubicación alternativa.

- **Recuperación de ubicación original**: esta opción conmuta por recuperación de Azure a la misma máquina local de origen (si existe). En este escenario solo los cambios se replican de nuevo al entorno local.
  - **Selección del almacén de datos durante OLR**: el almacén de datos asociado a la máquina de origen se seleccionará automáticamente.
- **Recuperación de ubicación alternativa**: si no existe la máquina local, puede conmutar por recuperación de Azure a una ubicación alternativa. Al reproteger la máquina virtual de Azure en un entorno local, se crea la máquina local. La replicación de datos completa se produce de Azure a un entorno local. [Revise](concepts-types-of-failback.md) los requisitos y las limitaciones de la conmutación por recuperación en la ubicación.
  - **Selección del almacén de datos durante ALR**: se puede elegir cualquier almacén de datos administrado por vCenter en el que se encuentra el dispositivo y es accesible (permisos de lectura y escritura) por el dispositivo (original o nuevo). Puede elegir la cuenta de almacenamiento en caché usada para la reprotección.

- Una vez completada la conmutación por error, el agente de movilidad de la máquina virtual de Azure se registrará automáticamente con Site Recovery Services. Si se produce un error en el registro, se genera un problema de estado crítico en la máquina virtual conmutada por error. Una vez resuelto el problema, el registro se desencadenará automáticamente. Puede completar manualmente el registro después de resolver los errores.


## <a name="cancel-failover"></a>Cancelación de la conmutación por error

Si el entorno local no está listo o se enfrenta a algún desafío, puede cancelar la conmutación por error.

Una vez que haya iniciado la conmutación por error planeada y se complete correctamente, el entorno local estará disponible para su uso. Pero después de completar la operación, si desea conmutar por error a otro punto de recuperación, puede cancelar la conmutación por error.


- Solo se puede cancelar la conmutación por error planeada.

- Puede cancelar una conmutación por error planeada desde la página **Elementos replicados** del almacén de Recovery Services.

- Una vez cancelada la conmutación por error, las máquinas de Azure volverán a estar activadas y la replicación se iniciará de nuevo de Azure al entorno local.


## <a name="next-steps"></a>Pasos siguientes
- [Conmutación por error de máquinas virtuales de VMware en Azure (versión preliminar)](vmware-azure-tutorial-failover-failback-preview.md#run-a-failover-to-azure)
- [Conmutación por error planeada (versión preliminar)](vmware-azure-tutorial-failover-failback-preview.md#planned-failover-from-azure-to-on-premises)
- [Reprotección (versión preliminar)](vmware-azure-tutorial-failover-failback-preview.md#re-protect-the-on-premises-machine-to-azure-after-successful-planned-failover)
- [Cancelación de la conmutación por error (versión preliminar)](vmware-azure-tutorial-failover-failback-preview.md#cancel-planned-failover)
