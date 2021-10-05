---
title: Actualizaciones de la plataforma para Azure VMware Solution
description: Más información sobre las actualizaciones de la plataforma en Azure VMware Solution.
ms.topic: reference
ms.date: 09/21/2021
ms.openlocfilehash: 3159323fa567bc622c9627ded4e8f70bfac20b4f
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128636123"
---
# <a name="platform-updates-for-azure-vmware-solution"></a>Actualizaciones de la plataforma para Azure VMware Solution

Azure VMware Solution va a aplicar actualizaciones importantes a partir de marzo de 2021. Va a recibir una notificación por medio de Azure Service Health con la escala de tiempo del mantenimiento. Para más información, consulte [Mantenimiento del host y administración del ciclo de vida](concepts-private-clouds-clusters.md#host-maintenance-and-lifecycle-management).

## <a name="september-21-2021"></a>21 de septiembre de 2021
Según el aviso de seguridad de VMware [VMSA-2021-0020](https://www.vmware.com/security/advisories/VMSA-2021-0020.html), se ha informado a VMware de varias vulnerabilidades en VMware vCenter Server.
 
Para abordar las vulnerabilidades (CVE-2021-21980, CVE-2021-21991, CVE-2021-21992, CVE-2021-21993, CVE-2021-22005, CVE-2021-22006, CVE-2021-22007, CVE-2021-22008, CVE-2021-22009, CVE-2021-22010, CVE-2021-22011, CVE-2021-22012,CVE-2021-22013, CVE-2021-22014, CVE-2021-22015, CVE-2021-22016, CVE-2021-2021-2021 22017, CVE-2021-22018, CVE-2021-22019, CVE-2021-22020) notificadas en el aviso de seguridad de VMware [VMSA-2021-0020](https://www.vmware.com/security/advisories/VMSA-2021-0020.html), vCenter Server se ha actualizado a la versión 6.7 Update 3o en todas las nubes privadas de Azure VMware Solution. Todas las nubes privadas de Azure VMware Solution se implementan con la versión 6.7 Update 3o de vCenter Server.
 
Para obtener más información, consulte [Notas de la versión de VMware vCenter Server 6.7 Update 3o](https://docs.vmware.com/en/VMware-vSphere/6.7/rn/vsphere-vcenter-server-67u3o-release-notes.html).
 
No se requiere ninguna acción adicional.

## <a name="september-10-2021"></a>10 de septiembre de 2021

Todas las nubes privadas nuevas de Azure VMware Solution se implementan ahora con la versión de ESXi ESXi670-202103001 (número de compilación: 17700523). Los hosts ESXi de las nubes privadas existentes se han corregido a esta versión.

Para obtener más información sobre esta versión de ESXi, consulte [VMware ESXi 6.7, versión de la revisión ESXi670-202103001](https://docs.vmware.com/en/VMware-vSphere/6.7/rn/esxi670-202103001.html).




## <a name="july-23-2021"></a>23 de julio de 2021

Todas las nubes privadas nuevas de Azure VMware Solution se implementan ahora con la versión [!INCLUDE [nsxt-version](includes/nsxt-version.md)] de NSX-T. La versión de NSX-T de las nubes privadas existentes se actualizarán hasta septiembre de 2021 a la versión [!INCLUDE [nsxt-version](includes/nsxt-version.md)] de NSX-T.
 
Va a recibir un correo electrónico con la fecha y hora de mantenimiento planeado. Puede volver a programar una actualización. El correo electrónico también proporciona detalles sobre el componente actualizado, su efecto en las cargas de trabajo, el acceso a la nube privada y otros servicios de Azure. 

Para más información sobre esta versión de NSX-T, consulte las [Notas de la versión de VMware NSX-T Data Center [!INCLUDE [nsxt-version](includes/nsxt-version.md)]](https://docs.vmware.com/en/VMware-NSX-T-Data-Center/3.1/rn/VMware-NSX-T-Data-Center-312-Release-Notes.html).




## <a name="may-25-2021"></a>25 de mayo de 2021
Según el aviso de seguridad de VMware [VMSA-2021-0010](https://www.vmware.com/security/advisories/VMSA-2021-0010.html), se ha informado a VMware de múltiples vulnerabilidades en VMware ESXi y vSphere Client (HTML5). 

Para hacer frente a las vulnerabilidades ([CVE-2021-21985](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2021-21985) y [CVE-2021-21986](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2021-21986)) notificadas en el aviso de seguridad de VMware [VMSA-2021-0010](https://www.vmware.com/security/advisories/VMSA-2021-0010.html), se ha actualizado vCenter Server en todas las nubes privadas de Azure VMware Solution.

No es necesario hacer nada.

## <a name="may-21-2021"></a>21 de mayo de 2021
 
El servicio Azure VMware Solution realizará tareas de mantenimiento hasta el 23 de mayo de 2021 para aplicar actualizaciones importantes a vCenter Server en su nube privada.  Va a recibir una notificación por medio de Azure Service Health con la escala de tiempo del mantenimiento de su nube privada.
 
Durante este tiempo, VMware vCenter no estará disponible y no podrá administrar las máquinas virtuales (detener, iniciar, crear o eliminar). Se recomienda que, durante este tiempo, no planee ninguna otra actividad, como el escalado vertical de la nube privada, la creación de redes nuevas, entre otras, en la nube privada.
 
No hay ningún impacto en las cargas de trabajo que se ejecutan en la nube privada.


## <a name="april-26-2021"></a>26 de abril de 2021
Todas las nuevas nubes privadas de Azure VMware Solution se implementan ahora con VMware vCenter, versión 6.7U3l y NSX-T, versión 2.5.2. No se usa NSX-T 3.1.1 en este escenario debido a un problema identificado en este entorno que afecta a la conectividad de las máquinas virtuales del cliente. 

La mitigación recomendada de VMware se aplicó a todas las nubes privadas existentes que actualmente ejecutan NSX-T 3.1.1 en Azure VMware Solution. Se ha confirmado que la solución alternativa no tiene afecto alguno sobre la conectividad de la máquina virtual del cliente.

## <a name="march-24-2021"></a>24 de marzo de 2021
Todas las nubes privadas de Azure VMware Solution se implementan con VMware vCenter, versión 6.7U3l y NSX-T, versión 3.1.1. Todas las nubes privadas existentes se actualizarán a las versiones mencionadas anteriormente **hasta junio de 2021**.

Va a recibir un correo electrónico con la fecha y hora de mantenimiento planeado. Puede volver a programar una actualización. El correo electrónico también proporciona detalles sobre el componente actualizado, su efecto en las cargas de trabajo, el acceso a la nube privada y otros servicios de Azure.  Una hora antes de la actualización va a recibir una notificación y luego otra cuando termine.

## <a name="march-15-2021"></a>15 de marzo de 2021 

- El servicio Azure VMware Solution va a efectuar tareas de mantenimiento **hasta el 19 de marzo de 2021** para actualizar la instancia de vCenter Server de la nube privada a la versión vCenter Server 6.7 Update 3l.

- VMware vCenter no estará disponible durante este tiempo, por lo que no podrá administrar las máquinas virtuales (detener, iniciar, crear, eliminar) ni el escalado de la nube privada (agregar o quitar servidores y clústeres). Pero la alta disponibilidad de VMware va a seguir funcionando para proteger las máquinas virtuales existentes. 
 
Para obtener más información sobre esta versión de vCenter, vea [Notas de la versión de VMware vCenter Server 6.7, actualización 3l](https://docs.vmware.com/en/VMware-vSphere/6.7/rn/vsphere-vcenter-server-67u3l-release-notes.html).

## <a name="march-4-2021"></a>4 de marzo de 2021

- Azure VMware Solution va a aplicar [VMware ESXi 6.7, Patch Release ESXi670-202011002](https://docs.vmware.com/en/VMware-vSphere/6.7/rn/esxi670-202011002.html) a las nubes privadas existentes **hasta el 15 de marzo de 2021**.

- Las soluciones alternativas documentadas para la pila de vSphere, según [VMSA-2021-0002](https://www.vmware.com/security/advisories/VMSA-2021-0002.html), también se van a aplicar **hasta el 15 de marzo de 2021**.

>[!NOTE]
>Esto no provoca interrupciones y no debe afectar a los servicios o las cargas de trabajo de Azure VMware. Durante el mantenimiento, varias alertas de VMware, como _Lost network connectivity on DVPorts_ (Conectividad de red perdida en DVPorts) y _Lost uplink redundancy on DVPorts_ (Redundancia de vínculo superior perdido en DVPorts), aparecen en vCenter y se van borrando automáticamente a medida que progresa el mantenimiento.

## <a name="post-update"></a>Después de la actualización
Una vez finalizada, aparecen las versiones más recientes de los componentes de VMware. Si nota algún problema o tiene alguna pregunta, póngase en contacto con nuestro equipo de soporte técnico al abrir una incidencia de soporte técnico.
