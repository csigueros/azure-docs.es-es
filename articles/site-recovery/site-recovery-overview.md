---
title: Acerca de Azure Site Recovery
description: Proporciona información general acerca del servicio Azure Site Recovery y resume escenarios de recuperación ante desastres e implementación de migraciones.
ms.topic: overview
ms.date: 08/19/2021
ms.custom: MVC
ms.openlocfilehash: d5558930c77c115ba25cb4b35e88d470afc38a23
ms.sourcegitcommit: 8000045c09d3b091314b4a73db20e99ddc825d91
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/19/2021
ms.locfileid: "122445695"
---
# <a name="about-site-recovery"></a>Acerca de Site Recovery

¡Bienvenido al servicio Azure Site Recovery! En este artículo se ofrece una rápida información general del servicio.

Como organización, necesita adoptar una estrategia de continuidad empresarial y de recuperación ante desastres (BCDR) que mantenga sus datos seguros, y sus aplicaciones y cargas de trabajo en línea cuando se produzcan interrupciones planeadas o imprevistas.

Azure Recovery Services colabora con su estrategia de BCDR:

- **Servicio Site Recovery**: Site Recovery ayuda a garantizar la continuidad empresarial manteniendo las aplicaciones y cargas de trabajo empresariales en funcionamiento durante las interrupciones. Site Recovery replica las cargas de trabajo que se ejecutan en máquinas físicas y virtuales desde un sitio principal a una ubicación secundaria. Cuando se produce una interrupción en el sitio principal, se conmuta por error a la ubicación secundaria y se accede desde allí a las aplicaciones. Cuando la ubicación principal vuelva a estar en ejecución, puede realizar la conmutación por recuperación en ella.
- **Servicio Backup**: el servicio [Azure Backup](../backup/index.yml) mantiene los datos seguros y permite recuperarlos.

Site Recovery puede administrar la replicación de:

- Máquinas virtuales de Azure que se replican entre regiones de Azure.
- Máquinas virtuales locales, máquinas virtuales de Azure Stack y servidores físicos.

## <a name="what-does-site-recovery-provide"></a>¿Qué ofrece Site Recovery?

**Característica** | **Detalles**
--- | ---
**Solución de BCDR simple** | Mediante Site Recovery se pueden configurar y administrar la replicación, la conmutación por error y la conmutación por recuperación desde una sola ubicación en Azure Portal.
**Replicación de máquinas virtuales de Azure** | Puede configurar la recuperación ante desastres de máquinas virtuales de Azure de una región primaria a una secundaria.
**Replicación de máquinas virtuales de VMware** | Puede replicar máquinas virtuales de VMware en Azure mediante el dispositivo de replicación de Azure Site Recovery mejorado que ofrece una mayor seguridad y resistencia que el servidor de configuración. Para más información, consulte [Recuperación ante desastres de máquinas virtuales de VMware](vmware-azure-about-disaster-recovery.md).
**Replicación de máquinas virtuales local** | Las máquinas virtuales y los servidores físicos se pueden replicar en Azure o en un centro de datos local secundario. Si se replican en Azure, se elimina el costo y la complejidad de mantener un centro de datos secundario.
**Replicación de la carga de trabajo** | La replicación de cualquier carga de trabajo que se ejecuta en máquinas virtuales de Azure compatibles, máquinas virtuales de Hyper-V y VMware locales y servidores físicos Windows o Linux.
**Resistencia de datos** | Site Recovery coordina la replicación sin interceptar los datos de las aplicaciones. Cuando se realiza la replicación en Azure, los datos se almacenan en Azure Storage con toda la resistencia que proporciona. Cuando se produce la conmutación por error, las máquinas virtuales de Azure en función de los datos replicados.
**Destinos RTO y el RPO** | Mantenga los objetivos de tiempo de recuperación (RTO) y los objetivos de punto de recuperación (RPO) dentro de los límites de la organización. Site Recovery proporciona una replicación continua tanto a las máquinas virtuales de Azure como a las máquinas virtuales VMware con una frecuencia de tan solo 30 segundos para Hyper-V. Para reducir aún más el RTO, realice la integración con [Azure Traffic Manager](https://azure.microsoft.com/blog/reduce-rto-by-using-azure-traffic-manager-with-azure-site-recovery/).
**Mantener la coherencia de aplicaciones a través de la conmutación por error** | Puede realizar la replicación mediante puntos de configuración con instantáneas coherentes con la aplicación. Estas instantáneas capturan los datos del disco, todos los datos en que hay en la memoria y todas las transacciones en curso.
**Pruebas sin interrupciones** | Puede ejecutar fácilmente maniobras de recuperación ante desastres, sin que ello afecte a la replicación en curso.
**Conmutaciones por error flexibles** | Puede ejecutar conmutaciones por error planeadas para las interrupciones previstas sin pérdida de datos. O conmutaciones por error no planeadas con la mínima pérdida de datos, dependiendo de la frecuencia de replicación, para desastres inesperados. Puede conmutar por recuperación a su sitio principal fácilmente cuando vuelva a estar disponible.
**Planes de recuperación personalizados** | Con los planes de recuperación, puede personalizar y secuenciar la conmutación por error y la recuperación de aplicaciones de niveles múltiples en varias máquinas virtuales. Puede agrupar las máquinas en un plan de recuperación y, opcionalmente, agregar scripts y acciones manuales. Los planes de recuperación se pueden integrar con runbooks de Azure Automation.
**Integración de BCDR** | Site Recovery se integra con otras tecnologías de BCDR. Por ejemplo, puede utilizar Site Recovery para proteger el back-end de SQL Server de cargas de trabajo corporativas, con compatibilidad nativa para SQL Server AlwaysOn, a fin de administrar la conmutación por error de grupos de disponibilidad.
**Integración de Azure Automation** | Una ingente biblioteca de Azure Automation proporciona scripts específicos de la aplicación y preparados para la producción que se pueden descargar e integrarse con Site Recovery.
**Integración de red** | Site Recovery se integra con Azure para la administración de la red de aplicaciones. Por ejemplo, para reservar direcciones IP, configurar equilibradores de carga y usar Azure Traffic Manager para los cambios de red eficientes.

## <a name="what-can-i-replicate"></a>¿Qué puedo replicar?

**Compatible** | **Detalles**
--- | ---
**Escenarios de replicación** | Replique las máquinas virtuales de Azure de una región de Azure a otra.<br/><br/>  Replique las máquinas virtuales de VMware locales, las máquinas virtuales de Hyper-V, los servidores físicos (Windows y Linux) y las máquinas de virtuales Azure Stack en Azure.<br/><br/> Replique las instancias de Windows de AWS a Azure.<br/><br/> Replique las máquinas virtuales locales de VMware, de Hyper-V administradas por System Center VMM y los servidores físicos en un sitio secundario.
**Regiones** | Revise las [regiones admitidas](https://azure.microsoft.com/global-infrastructure/services/?products=site-recovery) para Site Recovery. |
**Máquinas replicadas** | Revise los requisitos de la replicación de [máquinas virtuales de Azure](azure-to-azure-support-matrix.md#replicated-machine-operating-systems), [máquinas virtuales y servidores físicos de VMware locales](vmware-physical-azure-support-matrix.md#replicated-machines) y los [máquinas virtuales de Hyper-V locales](hyper-v-azure-support-matrix.md#replicated-vms).
**Cargas de trabajo** | Puede replicar cualquier carga de trabajo que se ejecute en una máquina que se admita para la replicación. Y que el equipo de Site Recovery haya hecho pruebas específicas de la aplicación para un [número de aplicaciones](site-recovery-workload.md#workload-summary).

## <a name="next-steps"></a>Pasos siguientes

- Más información sobre la [compatibilidad con cargas de trabajo](site-recovery-workload.md).
- Empiece a trabajar con la [replicación de máquina virtual de Azure entre regiones](azure-to-azure-quickstart.md).
- Introducción a la [replicación de máquinas virtuales de VMware](vmware-azure-enable-replication.md).
