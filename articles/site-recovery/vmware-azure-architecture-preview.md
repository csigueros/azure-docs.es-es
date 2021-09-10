---
title: 'Arquitectura de recuperación ante desastres de máquinas virtuales de VMware en Azure Site Recovery: versión preliminar'
description: 'En este artículo se proporciona información general sobre los componentes y la arquitectura que se usan al configurar la recuperación ante desastres de máquinas virtuales locales de VMware en Azure con Azure Site Recovery: versión preliminar'
ms.service: site-recovery
ms.topic: conceptual
ms.date: 08/19/2021
ms.openlocfilehash: 999c2c74c92c6e6106bc6e1e36ff4f62e29e6e1c
ms.sourcegitcommit: 7b6ceae1f3eab4cf5429e5d32df597640c55ba13
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/31/2021
ms.locfileid: "123272800"
---
# <a name="vmware-to-azure-disaster-recovery-architecture---preview"></a>Arquitectura de recuperación ante desastres de VMware a Azure: versión preliminar

En este artículo se describe la arquitectura y los procesos usados al implementar la replicación, la conmutación por error y la recuperación de máquinas virtuales (VM) de VMware entre un sitio de VMware local y Azure en la recuperación ante desastres, mediante el servicio [Azure Site Recovery](site-recovery-overview.md): versión preliminar

>[!NOTE]
> Asegúrese de crear un nuevo almacén de Recovery Services para configurar el dispositivo de versión preliminar. No use un almacén existente.

Para obtener información sobre la arquitectura de Azure Site Recovery en la arquitectura clásica, consulte [este artículo](vmware-azure-architecture.md).


## <a name="architectural-components"></a>Componentes de la arquitectura

En la tabla y el gráfico siguientes se proporciona una visión general de los componentes que se usaron para la recuperación ante desastres de máquinas físicas/máquinas virtuales de VMware en Azure.

[![Arquitectura de VMware a Azure](./media/vmware-azure-architecture-preview/architecture-preview.png)](./media/vmware-azure-architecture-preview/architecture-preview.png#lightbox)

**Componente** | **Requisito** | **Detalles**
--- | --- | ---
**Azure** | Una suscripción a Azure, una cuenta de Azure Storage para almacenamiento en caché, un disco administrado y una red de Azure. | Los datos replicados desde las máquinas virtuales locales se almacenan en Azure Storage. Las máquinas virtuales de Azure se crean con los datos replicados cuando se ejecuta una conmutación por error desde el entorno local en Azure. Las máquinas virtuales de Azure se conectan a la red virtual de Azure cuando se crean.
**Dispositivo de replicación de Azure Site Recovery** |     Este es el bloque de creación básico de toda la infraestructura local de Azure Site Recovery. <br/><br/> Todos los componentes del dispositivo se coordinan con el dispositivo de replicación. Este servicio supervisa todas las actividades de Site Recovery de un extremo a otro, incluida la supervisión del estado de las máquinas protegidas, la replicación de datos, las actualizaciones automáticas, etc. | El dispositivo hospeda varios componentes fundamentales, como:<br/><br/>**Servidor proxy de Azure Site Recovery:** este componente actúa como canal de proxy entre los servicios de Site Recovery y el agente de movilidad en la nube. Garantiza que no se requiera conectividad a Internet adicional de las cargas de trabajo de producción para generar puntos de recuperación.<br/><br/>**Elementos detectados:** este componente recopila información de vCenter y se coordina con el servicio de administración de Azure Site Recovery en la nube.<br/><br/>**Servidor de reprotección:** este componente se coordina entre las máquinas locales y Azure durante las operaciones de reprotección y conmutación por recuperación.<br/><br/>**Servidor de proceso de Azure Site Recovery:** este componente se usa para almacenar en caché y comprimir los datos antes de su envío a Azure. <br/><br/> [Obtenga más información](switch-replication-appliance-preview.md) sobre el dispositivo de replicación y cómo usar varios dispositivos de replicación.<br/><br/>**Agente de Recovery Services:** este componente se usa para la configuración y el registro en los servicios de Site Recovery, así como para la supervisión del estado de todos los componentes.<br/><br/>**Proveedor de Site Recovery:** este componente se usa para facilitar la reprotección. Identifica entre la reprotección de ubicación alternativa y la reprotección de ubicación original para una máquina de origen. <br/><br/> **Servicio de replicación:** este componente se usa para replicar datos de la ubicación de origen en Azure.
**Servidores de VMware** | Las máquinas virtuales VMware se hospedan en servidores ESXi de vSphere locales. Se recomienda un servidor vCenter para administrar los hosts. | Durante la implementación de Site Recovery, se agregan servidores VMware al almacén de Recovery Services.
**Máquinas replicadas** | Mobility Service está instalado en cada una de las máquinas virtuales de VMware que se van a replicar. | Se recomienda permitir la instalación automática del servicio Mobility. Como alternativa, puede instalar el [servicio manualmente](vmware-physical-mobility-service-overview.md#install-the-mobility-service-using-ui-preview).


## <a name="set-up-outbound-network-connectivity"></a>Configuración de la conectividad de red saliente

Para que Site Recovery funcione de la forma esperada, debe modificar la conectividad de red saliente para permitir la replicación de su entorno.

> [!NOTE]
> Site Recovery no admite el uso de un proxy de autenticación para controlar la conectividad de la red.

### <a name="outbound-connectivity-for-urls"></a>Conectividad de salida para las direcciones URL

Si usa un proxy de firewall basado en direcciones URL para controlar la conectividad de salida, debe permitir el acceso a ellas:

| **URL**                  | **Detalles**                             |
| ------------------------- | -------------------------------------------|
| portal.azure.com          | Vaya a Azure Portal.              |
| `*.windows.net `<br>`*.msftauth.net`<br>`*.msauth.net`<br>`*.microsoft.com`<br>`*.live.com `<br>`*.office.com ` | Para iniciar sesión en la suscripción de Azure.  |
|`*.microsoftonline.com `|Cree aplicaciones de Azure Active Directory (AD) para que el dispositivo se comunique con Azure Site Recovery. |
|management.azure.com |Cree aplicaciones de Azure AD para que el dispositivo se comunique con el servicio Azure Site Recovery. |
|`*.services.visualstudio.com `|Cargue los registros de aplicaciones que se usan para la supervisión interna. |
|`*.vault.azure.net `|Administre secretos en Azure Key Vault. Nota: Asegúrese de que las máquinas que se replican tengan acceso a ella. |
|aka.ms |Permiso de acceso a vínculos aka. Se usa para las actualizaciones del dispositivo de Azure Site Recovery. |
|download.microsoft.com/download |Permita descargas de Microsoft. |
|`*.servicebus.windows.net `|Comunicación entre el dispositivo y el servicio Azure Site Recovery. |
|`*.discoverysrv.windowsazure.com `|Conéctese a la dirección URL del servicio de detección de Azure Site Recovery. |
|`*.hypervrecoverymanager.windowsazure.com `|Conéctese a las direcciones URL de microservicios de Azure Site Recovery.  |
|`*.blob.core.windows.net `|Cargue datos en Azure Storage que se usan para crear discos de destino. |
|`*.backup.windowsazure.com `|Dirección URL del servicio de protección: un microservicio que Azure Site Recovery usa para procesar y crear discos replicados en Azure. |



## <a name="replication-process"></a>Proceso de replicación

1. Al habilitar la replicación para una VM, comienza la replicación inicial en el almacenamiento de Azure mediante la directiva de replicación especificada. Tenga en cuenta lo siguiente:
    - Para las VM de VMware, la replicación es de nivel de bloque, casi continua, mediante el agente del servicio de movilidad que se ejecuta en la VM.
    - Se aplica la configuración de directivas de replicación:
        - **Umbral de RPO**. Esta configuración no afecta a la replicación. Ayuda con la supervisión. Se genera un evento y, opcionalmente, se envía un correo electrónico, si el RPO actual supera el límite del umbral que especifique.
        - **Retención de punto de recuperación**. Esta configuración especifica cuánto tiempo atrás quiere ir cuando se produce una interrupción. La retención máxima en almacenamiento premium es de 24 horas. En el almacenamiento estándar es de 72 horas.
        - **Instantáneas coherentes con la aplicación**. Las instantáneas coherentes con la aplicación pueden tomarse cada 1 a 12 horas, según las necesidades de la aplicación. Son instantáneas de blob de Azure estándar. El agente de movilidad que se ejecuta en una VM solicita una instantánea de VSS de acuerdo con esta configuración, y marca ese momento como punto coherente con la aplicación en el flujo de replicación.

2. El tráfico se replica en los puntos de conexión públicos de Azure Storage a través de Internet. Como alternativa, puede usar Azure ExpressRoute con [emparejamiento de Microsoft](../expressroute/expressroute-circuit-peerings.md#microsoftpeering). No se admite la replicación del tráfico entre un sitio local y Azure a través de una red privada virtual (VPN) de sitio a sitio.
3. La operación de replicación inicial garantiza que los datos completos en el equipo al momento de habilitar la replicación se envían a Azure. Una vez terminada la replicación inicial, comienza la de los cambios incrementales en Azure. Los cambios marcados para una máquina se envían al servidor de procesos.
4. Comunicación se realiza como se indica a continuación:

    - Las máquinas virtuales se comunican con el dispositivo local en el puerto HTTPS 443 entrante para la administración de la replicación.
    - El dispositivo organiza la replicación con Azure a través del puerto HTTPS 443 saliente.
    - Las máquinas virtuales envían los datos de replicación al servidor de procesos en el puerto HTTPS 9443 entrante. Este puerto se puede modificar.
    - El servidor de procesos recibe los datos de la replicación, los optimiza, los cifra y los envía a Azure Storage a través del puerto 443 de salida.
5. Los datos de replicación registran el primer aterrizaje en una cuenta de almacenamiento en caché de Azure. Estos registros se procesan y los datos se almacenan en un disco administrado de Azure (denominado *asrseeddisk*). Los puntos de recuperación se crean en ese disco.

## <a name="resynchronization-process"></a>Proceso de resincronización

1. En ocasiones, durante la replicación inicial o mientras se transfieren los cambios diferenciales, puede haber problemas de conectividad de red entre la máquina de origen y el servidor de proceso, o entre el servidor de procesos y Azure. Cualquiera de estas situaciones puede producir errores momentáneos en la transferencia de datos a Azure.
2. Para evitar problemas con la integridad de datos y minimizar los costos de transferencia de datos, Site Recovery marca una máquina para la resincronización.
3. Una máquina también se puede marcar para la resincronización en situaciones como las siguientes para mantener la coherencia entre la máquina de origen y los datos almacenados en Azure
    - Si un equipo sufre un apagado forzado
    - Si un equipo sufre cambios de configuración, como el cambio de tamaño de disco (modificar el tamaño del disco de 2 TB a 4 TB)
4. La resincronización solo envía los datos diferenciales a Azure. La transferencia de datos entre el entorno local y Azure se minimiza mediante el cálculo de sumas de comprobación de los datos entre la máquina de origen y los datos almacenados en Azure.
5. De forma predeterminada, la resincronización está programada para ejecutarse automáticamente fuera del horario de oficina. Si no desea esperar para volver a sincronizar fuera del horario de forma predeterminada, puede volver a sincronizar una máquina virtual manualmente. Para ello, vaya a Azure Portal y seleccione la máquina virtual > **Volver a sincronizar**.
6. Si se produce un error en la resincronización predeterminada fuera del horario de oficina y es necesaria una intervención manual, se genera un error en el equipo específico en Azure Portal. Puede resolver el error y desencadenar la resincronización de forma manual.
7. Una vez finalizada la resincronización, se reanudará la replicación de los cambios diferenciales.

## <a name="replication-policy"></a>Directiva de replicación

Al habilitar la replicación de máquinas virtuales de Azure, de forma predeterminada Site Recovery crea una directiva de replicación con la configuración predeterminada que se resume en la tabla.

**Configuración de la directiva** | **Detalles** | **Valor predeterminado**
--- | --- | ---
**Retención de punto de recuperación** | Especifica el tiempo que Site Recovery conserva los puntos de recuperación | 72 horas
**Frecuencia de las instantáneas coherentes con la aplicación** | La frecuencia con la que Site Recovery toma una instantánea coherente con la aplicación. | Cada 4 horas

### <a name="managing-replication-policies"></a>Administración de las directivas de replicación

Puede administrar y modificar la configuración de las directivas de replicación predeterminada como sigue:
- Puede modificar la configuración al habilitar la replicación.
- Puede crear o editar una nueva directiva de replicación al intentar habilitar la replicación.

### <a name="multi-vm-consistency"></a>Coherencia con múltiples máquinas virtuales

Si desea que varias máquinas se repliquen al mismo tiempo y estas comparten puntos de recuperación coherentes frente a bloqueos y coherentes con la aplicación al conmutar por error, puede reunirlas en un grupo de replicación. La coherencia entre varias máquinas virtuales afecta al rendimiento de la carga de trabajo y solo debe usarse para las cargas de trabajo de 4 máquinas virtuales que necesiten coherencia en todas las máquinas.



## <a name="snapshots-and-recovery-points"></a>Instantáneas y puntos de recuperación

Los puntos de recuperación se crean a partir de instantáneas de los discos de máquina virtual tomadas en un momento determinado. Al conmutar por error una máquina virtual se usa un punto de recuperación para restaurarla en la ubicación de destino.

Al conmutar por error, normalmente queremos garantizar que la máquina virtual empieza sin daños ni pérdida de datos y que los datos de la máquina virtual son coherentes para el sistema operativo y para las aplicaciones que se ejecutan en ella. Esto depende del tipo de instantánea realizada.

Site Recovery toma instantáneas como sigue:

1. Site Recovery toma instantáneas coherentes frente a bloqueos de datos de forma predeterminada e instantáneas coherentes con la aplicación si especifica una frecuencia para ello.
2. Los puntos de recuperación se crean a partir de las instantáneas y se almacenan según la configuración de retención establecida en la directiva de replicación.

### <a name="consistency"></a>Coherencia

La siguiente tabla explica los diferentes tipos de coherencia.

### <a name="crash-consistent"></a>Coherente frente a bloqueos

**Descripción** | **Detalles** | **Recomendación**
--- | --- | ---
Una instantánea coherente frente a bloqueos captura los datos que estaban en el disco cuando se tomó la instantánea. No incluye nada de la memoria.<br/><br/> Contiene el equivalente de los datos de disco que estuvieran presentes si se bloqueó la máquina virtual o se extrajo el cable de alimentación del servidor en el momento en que se tomara la instantánea.<br/><br/> Un punto de recuperación coherente frente a bloqueos no garantiza la coherencia de datos para el sistema operativo o las aplicaciones de la máquina virtual. | Site Recovery crea puntos de recuperación coherentes frente a bloqueos cada cinco minutos de forma predeterminada. No se puede modificar esta configuración.<br/><br/>  | Hoy en día, la mayoría de las aplicaciones se pueden recuperar bien a partir de instantáneas coherentes frente a bloqueos.<br/><br/> Los puntos de replicación coherentes frente a bloqueos suelen bastar para la replicación de sistemas operativos y aplicaciones de tipo servidor DHCP y de impresión.

### <a name="app-consistent"></a>Coherente con la aplicación

**Descripción** | **Detalles** | **Recomendación**
--- | --- | ---
Se crean puntos de recuperación coherentes con la aplicación a partir de instantáneas coherentes con la aplicación.<br/><br/> Una instantánea coherente con la aplicación contiene toda la información en una instantánea coherente frente a bloqueos, además de todos los datos en memoria y las transacciones en curso. | Las instantáneas coherentes con la aplicación usan el servicio Volume Shadow Copy (VSS):<br/><br/>   1) Azure Site Recovery usa el método de copia de seguridad de solo copia (VSS_BT_COPY) que no cambia la hora de la copia de seguridad ni el número de secuencia del registro de transacciones de Microsoft SQL. </br></br> 2) Cuando se inicia una instantánea, VSS realiza una operación de copia en escritura (COW) en el volumen.<br/><br/>   3) Antes de realizar la operación COW, VSS informa a todas las aplicaciones de la máquina que deben vaciar los datos en memoria en el disco.<br/><br/>   4) A continuación, VSS permite que la aplicación de recuperación ante desastres/copia de seguridad (en este caso, Site Recovery) lea los datos de la instantánea y continúe. | Se toman instantáneas coherentes con la aplicación de acuerdo con la frecuencia que especifique. Esta frecuencia debe ser siempre menor que la establecida para retención de los puntos de recuperación. Por ejemplo, si retiene puntos de recuperación con la configuración predeterminada de 24 horas, debe establecer la frecuencia en menos de 24 horas.<br/><br/>Son más complejas y tardan más tiempo en completarse que las instantáneas coherentes frente a bloqueos.<br/><br/> Afectan el rendimiento de las aplicaciones que se ejecutan en la máquina virtual habilitada para la replicación.

## <a name="failover-and-failback-process"></a>Proceso de conmutación por error y conmutación por recuperación

Una vez que la replicación está configurada y tras ejecutar una exploración de la recuperación ante desastres (conmutación por error de prueba) para comprobar que todo funciona según lo previsto, puede ejecutar la conmutación por error y la conmutación por recuperación, según sea necesario.

1. Puede ejecutar la conmutación por error para una sola máquina o crear un plan de recuperación para conmutar por error varias máquinas virtuales al mismo tiempo. La ventaja de un plan de recuperación en lugar de la conmutación por error de una única máquina incluye:
    - Puede modelar las dependencias de aplicaciones con la inclusión de todas las máquinas virtuales por la aplicación en un único plan de recuperación.
    - Puede agregar scripts, runbooks de Azure y ponerlos en pausa para acciones manuales.
2. Después de desencadenar la conmutación por error inicial, debe confirmarla para que se inicie. Para ello, acceda a la carga de trabajo desde la máquina virtual de Azure.
3. Cuando el sitio local principal esté disponible de nuevo, podrá prepararse para la conmutación por recuperación. Si necesita conmutar por recuperación grandes volúmenes de tráfico, configure un nuevo dispositivo de replicación de Azure Site Recovery.

    - Fase 1 Vuelva a proteger las máquinas virtuales de modo que realicen la replicación desde Azure de vuelta a las máquinas virtuales VMware locales.
    - Fase 2: Ejecute una conmutación por error en el sitio local.
    - Fase 3: Una vez que las cargas de trabajo han conmutado por recuperación, debe habilitar de nuevo la replicación de las máquinas virtuales locales.

## <a name="next-steps"></a>Pasos siguientes

Siga [este tutorial](vmware-azure-tutorial.md) para habilitar la replicación de VMware en Azure.
