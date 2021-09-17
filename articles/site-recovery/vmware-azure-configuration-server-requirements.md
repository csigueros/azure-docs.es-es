---
title: 'Recuperación ante desastres de VMware: requisitos del servidor de configuración en Azure Site Recovery'
description: En este artículo se describen el soporte y los requisitos al implementar el servidor de configuración para realizar la recuperación ante desastres de VMware en Azure con Azure Site Recovery.
ms.service: site-recovery
ms.topic: article
ms.date: 08/19/2021
ms.openlocfilehash: 71861ec8f3b1e10f0052fe46705b726f06e717dd
ms.sourcegitcommit: 8000045c09d3b091314b4a73db20e99ddc825d91
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/19/2021
ms.locfileid: "122444306"
---
# <a name="configuration-server-requirements-for-vmware-disaster-recovery-to-azure"></a>Requisitos del servidor de configuración para la recuperación ante desastres de VMware en Azure

>[!NOTE]
> La información de este artículo se aplica a Azure Site Recovery: versiones clásicas. En versión preliminar, para la replicación de máquinas virtuales, debe crear y usar el dispositivo de replicación de Azure Site Recovery. Para obtener información detallada sobre los requisitos del dispositivo de replicación y cómo implementarlo, [consulte este artículo](deploy-vmware-azure-replication-appliance-preview.md).

Cuando se usa [Azure Site Recovery](site-recovery-overview.md) para realizar la recuperación ante desastres de servidores físicos y máquinas virtuales de VMware en Azure, se implementa un servidor de configuración local.

- El servidor de configuración coordina la comunicación entre Azure y VMware local. También administra la replicación de datos.
- [Más información](vmware-azure-architecture.md) acerca de los componentes y procesos del servidor de configuración.

## <a name="configuration-server-deployment"></a>Implementación del servidor de configuración

Para la recuperación ante desastres de máquinas virtuales de VMware en Azure, implemente el servidor de configuración como una VM de VMware.

- Site Recovery proporciona una plantilla OVA que puede descargar desde Azure Portal y, después, importar en vCenter Server para configurar la máquina virtual del servidor de configuración.
- Al implementar el servidor de configuración con la plantilla OVA, la máquina virtual cumplirá automáticamente con los requisitos que se enumeran en este artículo.
- Se recomienda que use la plantilla OVA para configurar el servidor de configuración. Sin embargo, si está configurando la recuperación ante desastres para máquinas virtuales de VMware y no puede usar la plantilla OVA, puede implementar el servidor de configuración con [las instrucciones que se proporcionan](physical-azure-set-up-source.md).
- Si va a implementar el servidor de configuración para la recuperación ante desastres de máquinas físicas locales en Azure, siga las instrucciones que se incluyen [en este artículo](physical-azure-set-up-source.md).

[!INCLUDE [site-recovery-configuration-server-requirements](../../includes/site-recovery-configuration-and-scaleout-process-server-requirements.md)]

## <a name="next-steps"></a>Pasos siguientes
Configure la recuperación ante desastres en Azure para [máquinas virtuales VMware](vmware-azure-tutorial.md).
