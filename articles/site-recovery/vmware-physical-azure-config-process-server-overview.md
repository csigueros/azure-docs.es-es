---
title: Acerca de la configuración, el proceso y los servidores de destino maestros de Azure Site Recovery
description: En este artículo se proporciona información general sobre el uso de servidores de configuración, proceso y destino maestro al configurar la recuperación ante desastres de máquinas virtuales locales de VMware en Azure con Azure Site Recovery.
ms.topic: conceptual
ms.date: 08/19/2021
ms.openlocfilehash: ad8cdbdf9d051ac9b0e5a7fd47660811e4a285e3
ms.sourcegitcommit: 8000045c09d3b091314b4a73db20e99ddc825d91
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/19/2021
ms.locfileid: "122446429"
---
# <a name="about-site-recovery-components-configuration-process-master-target"></a>Acerca de los componentes de Site Recovery (configuración, proceso y destino maestro)

En este artículo se describen los servidores de configuración, proceso y destino maestro que usa el servicio [Site Recovery](site-recovery-overview.md) para replicar tanto máquinas virtuales de VMware como servidores físicos en Azure. Este artículo es válido para las versiones clásicas.

Para replicar máquinas virtuales en la versión preliminar, hay que crear y usar un servidor de replicación de Azure Site Recovery. Para obtener información sobre el servidor de replicación de Azure Site Recovery y sus componentes, consulte [este artículo](vmware-azure-architecture-preview.md).

## <a name="configuration-server"></a>Servidor de configuración

Para realizar la recuperación ante desastres de servidores físicos y máquinas virtuales de VMware locales, implemente un servidor de configuración de Site Recovery local.

**Configuración** | **Detalles** | **Vínculos**
--- | --- | ---
**Componentes**  | La máquina del servidor de configuración ejecuta todos los componentes locales de Site Recovery, incluidos el servidor de configuración, el servidor de procesos y el servidor de destino maestro.<br/><br/> Al configurar el servidor de configuración, todos los componentes se instalan automáticamente. | [Lea](vmware-azure-common-questions.md#configuration-server) las preguntas más frecuentes sobre el servidor de configuración.
**Rol** | El servidor de configuración coordina la comunicación entre el entorno local y Azure, además de administrar la replicación de datos. | Obtenga más información sobre la arquitectura de recuperación ante desastres de [VMware](vmware-azure-architecture.md) y [servidor físico](physical-azure-architecture.md) en Azure.
**Requisitos de VMware** | Para la recuperación ante desastres de máquinas virtuales locales de VMware, debe instalar y ejecutar el servidor de configuración como una máquina virtual de VMware local de alta disponibilidad. | [Obtenga información](vmware-azure-deploy-configuration-server.md#prerequisites) sobre los requisitos previos.
**Implementación de VMware** | Se recomienda implementar el servidor de configuración con una plantilla OVA descargada. Este método permite configurar de manera sencilla un servidor de configuración que cumpla todos los requisitos y requisitos previos.<br/><br/> Si por algún motivo no puede implementar una máquina virtual de VMware mediante una plantilla OVA, puede configurar las máquinas del servidor de configuración manualmente, tal como se describe a continuación para la recuperación ante desastres de máquinas físicas. | [Implementación](vmware-azure-deploy-configuration-server.md#deploy-a-configuration-server-through-an-ova-template) con una plantilla OVA.
**Requisitos del servidor físico** | Para la recuperación ante desastres en servidores físicos locales, debe implementar manualmente el servidor de configuración. | [Obtenga información](physical-azure-set-up-source.md#prerequisites) sobre los requisitos previos.
**Implementación de servidor físico** | Si no se puede instalar como una máquina virtual de VMware, se puede instalar en un servidor físico. | [Implemente](physical-azure-set-up-source.md#set-up-the-source-environment) el servidor de configuración manualmente.

## <a name="process-server"></a>Servidor de proceso

Los servidores de procesos controlan los datos de replicación durante la conmutación por error y la conmutación por recuperación, e instalan el servicio Mobility en servidores físicos y máquinas virtuales de VMware.

**Configuración** | **Detalles** | **Vínculos**
--- | --- | ---
**Implementación**  | De manera predeterminada, al implementar el servidor de configuración se instala el servidor de procesos. <br/><br/> Para la recuperación ante desastres y la replicación tanto servidores físicos como de máquinas virtuales de VMware locales, se necesita un servidor de procesos local. | [Más información](vmware-azure-architecture.md#architectural-components).
**Rol (local**) | Recibe datos de replicación de las máquinas habilitadas para la replicación. <br/><br/> Optimiza los datos de replicación con el almacenamiento en caché, la compresión y el cifrado, y los envía a Azure Storage. <br/><br/> Realiza una instalación de inserción del servicio Mobility de Azure Site Recovery tanto en los servidores físicos como en la máquinas virtuales de VMware locales que se quieren replicar. <br/><br/> Realiza la detección automática de máquinas locales. | [Más información](vmware-azure-enable-replication.md).
**Rol (conmutación por recuperación desde Azure)** | Después de la conmutación por error desde su sitio local, debe configurar un servidor de procesos en Azure, como máquina virtual de Azure, para controlar la conmutación por recuperación en su ubicación local.<br/><br/> El servidor de procesos de Azure es temporal. La máquina virtual de Azure se puede eliminar después de realizar la conmutación por recuperación. | [Más información](vmware-azure-set-up-process-server-azure.md).
**Escalado** | Para implementaciones más grandes, puede configurar servidores de procesos de escalabilidad horizontal adicionales en el entorno local. Los servidores adicionales escalan horizontalmente la capacidad controlando una cantidad mayor de máquinas de replicación y volúmenes mayores de tráfico de replicación.<br/><br/> Puede mover máquinas entre dos servidores de procesos para equilibrar la carga del tráfico de replicación. | [Más información](vmware-azure-set-up-process-server-scale.md).

## <a name="master-target-server"></a>Servidor de destino principal

El servidor de destino maestro controla los datos de replicación durante la conmutación por recuperación desde Azure.

- De manera predeterminada, el servidor de destino maestro se instala en el servidor de configuración.
- En el caso de las implementaciones de gran tamaño, puede agregar un servidor de destino maestro independiente adicional para la conmutación por recuperación.

## <a name="next-steps"></a>Pasos siguientes

- Revise la [arquitectura](vmware-azure-architecture.md) de recuperación ante desastres de máquinas virtuales de VMware y servidores físicos.
- Revise los [requisitos y requisitos previos](vmware-physical-azure-support-matrix.md) para la recuperación ante desastres de máquinas virtuales de VMware y servidores físicos en Azure.
