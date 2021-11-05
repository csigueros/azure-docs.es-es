---
title: Implementación de la recuperación ante desastres de Zerto en Azure VMware Solution (disponibilidad inicial)
description: Aprenda a implementar la recuperación ante desastres de Zerto para máquinas VMware locales o máquinas virtuales de Azure VMware Solution.
ms.topic: how-to
ms.date: 10/25/2021
ms.openlocfilehash: 8f7fcd27cdde9915f2e0c9c2467576aa59a0a478
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131016916"
---
# <a name="deploy-zerto-disaster-recovery-on-azure-vmware-solution-initial-availability"></a>Implementación de la recuperación ante desastres de Zerto en Azure VMware Solution (disponibilidad inicial)

En este artículo se explica cómo implementar la recuperación ante desastres para máquinas virtuales de VMware locales o máquinas virtuales (VM) basadas en Azure VMware Solution. La solución de este artículo usa la [recuperación ante desastres de Zerto](https://www.zerto.com/solutions/use-cases/disaster-recovery/). Las instancias de Zerto se implementan tanto en el sitio protegido como en el de recuperación. 

Zerto es una solución de recuperación ante desastres diseñada para minimizar el tiempo de inactividad de las máquinas virtuales en caso de desastre. La plataforma de Zerto se basa en la protección continua de datos (CDP), que permite una pérdida de datos mínima o cercana a cero. Proporciona el nivel de protección deseado para muchas aplicaciones empresariales críticas. Zerto también automatiza y orquesta la conmutación por error y la conmutación por recuperación, lo que garantiza un tiempo de inactividad mínimo si se produce un desastre. En general, Zerto simplifica la administración mediante la automatización y garantiza tiempos de recuperación rápidos y muy previsibles.


## <a name="core-components-of-the-zerto-platform"></a>Componentes principales de la plataforma de Zerto

| Componente | Descripción |
| --- | --- |
| **Zerto Virtual Manager (ZVM)**   | Aplicación de administración para Zerto implementada como un servicio de Windows instalado en una VM Windows. El administrador de la nube privada instala y administra la VM Windows. ZVM habilita la configuración de recuperación ante desastres de día 0 y día 2. Por ejemplo, la configuración de sitios de recuperación primaria y ante desastres, la protección y recuperación de VM, etc. Sin embargo, no controla los datos de replicación de las VM de cliente protegidas.     |
| **Dispositivo de replicación virtual (vRA)**   | VM Linux para controlar la replicación de datos desde el origen al destino de replicación. Se instala una instancia de vRA por host ESXi, lo que proporciona una arquitectura de escala real que se amplía y reduce junto con los hosts de la nube privada. El VRA administra la replicación de datos hacia y desde máquinas virtuales protegidas en su destino local o remoto y almacena los datos en el diario.    |
| **Controlador host ESXi de Zerto**   | Se instala en cada host VMware ESXi configurado para la recuperación ante desastres de Zerto. El controlador host intercepta la E/S de una VM de vSphere y envía los datos de replicación al vRA elegido para ese host. A continuación, el vRA es responsable de replicar los datos de la VM en uno o varios destinos de la recuperación ante desastres.    |
| **Zerto Cloud Appliance (ZCA)**   | La VM Windows solo se usa cuando se utiliza Zerto para recuperar máquinas virtuales de vSphere como máquinas virtuales de IaaS nativas de Azure. El ZCA consta de:<ul><li>**ZVM:** un servicio de Windows que hospeda la interfaz de usuario y se integra con las API nativas de Azure para la administración y orquestación.</li><li>**VRA:** un servicio de Windows que replica los datos desde o hacia Azure.</li></ul>La ZCA se integra de forma nativa con la plataforma en la que se implementa, lo que le permite usar Azure Blob Storage dentro de una cuenta de almacenamiento en Microsoft Azure. Como resultado, garantiza la implementación más rentable en cada una de estas plataformas.   |
| **Grupo de protección virtual (VPG)**   | Grupo lógico de máquinas virtuales creadas en ZVM. Zerto permite configurar directivas de recuperación ante desastres, copia de seguridad y movilidad en un VPG. Este mecanismo permite aplicar un conjunto coherente de directivas a un grupo de máquinas virtuales.  |


Para más información sobre la arquitectura de la plataforma de Zerto, consulte la Guía de arquitectura de la plataforma de [Zerto](https://www.zerto.com/wp-content/uploads/2021/07/Zerto-Platform-Architecture-Guide.pdf). 


## <a name="supported-zerto-scenarios"></a>Escenarios de Zerto admitidos

Puede usar Zerto con Azure VMware Solution para los tres escenarios siguientes. 

### <a name="scenario-1-on-premises-vmware-to-azure-vmware-solution-disaster-recovery"></a>Escenario 1: VMware local a recuperación ante desastres de Azure VMware Solution

En este escenario, el sitio primario es un entorno local basado en vSphere. El sitio de recuperación ante desastres es una nube privada de Azure VMware Solution. 

:::image type="content" source="media/zerto-disaster-recovery/zerto-disaster-recovery-scenario-1.png" alt-text="Diagrama que muestra el escenario 1 para la solución de recuperación ante desastres de Zerto en Azure VMware Solution.":::


### <a name="scenario-2-azure-vmware-solution-to-azure-vmware-solution-cloud-disaster-recovery"></a>Escenario 2: Azure VMware Solution a recuperación ante desastres en la nube de Azure VMware Solution

En este escenario, el sitio primario es una nube privada de Azure VMware Solution en una región de Azure. El sitio de recuperación ante desastres es una nube privada de Azure VMware Solution en otra región de Azure.

:::image type="content" source="media/zerto-disaster-recovery/zerto-disaster-recovery-scenario-2.png" alt-text="Diagrama que muestra el escenario 2 para la solución de recuperación ante desastres de Zerto en Azure VMware Solution." border="false":::


### <a name="scenario-3-azure-vmware-solution-to-iaas-vms-cloud-disaster-recovery"></a>Escenario 3: Azure VMware Solution para recuperación ante desastres en la nube de VM de IaaS

En este escenario, el sitio primario es una nube privada de Azure VMware Solution en una región de Azure. Los blobs de Azure y las VM de IaaS de Azure (basadas en Hyper-V) se usan en momentos de desastre.

:::image type="content" source="media/zerto-disaster-recovery/zerto-disaster-recovery-scenario-3.png" alt-text="Diagrama que muestra el escenario 3 para la solución de recuperación ante desastres de Zerto en Azure VMware Solution." border="false":::



## <a name="prerequisites"></a>Prerrequisitos

### <a name="on-premises-vmware-to-azure-vmware-solution-disaster-recovery"></a>VMware local a recuperación ante desastres de Azure VMware Solution

- Nube privada de Azure VMware Solution implementada como región secundaria.

- VPN o conectividad de ExpressRoute entre el entorno local y Azure VMware Solution.



### <a name="azure-vmware-solution-to-azure-vmware-solution-cloud-disaster-recovery"></a>Azure VMware Solution a recuperación ante desastres en la nube de Azure VMware Solution

- La nube privada de Azure VMware Solution debe implementarse en la región primaria y secundaria.

   :::image type="content" source="media/zerto-disaster-recovery/zerto-disaster-recovery-scenario-2a-prerequisite.png" alt-text="Diagrama que muestra el primer requisito previo para el escenario 2 de la solución de recuperación ante desastres de Zerto en Azure VMware Solution.":::
 
- Conectividad (como Global Reach de ExpressRoute) entre la nube privada de origen y de destino de Azure VMware Solution.

### <a name="azure-vmware-solution-iaas-vms-cloud-disaster-recovery"></a>Azure VMware Solution para recuperación ante desastres en la nube de VM de IaaS

- Conectividad de red, basada en ExpressRoute, de Azure VMware Solution a la red virtual que se usa para la recuperación ante desastres.   

- Siga las [directrices de Azure Enterprise de replicación virtual de Zerto](http://s3.amazonaws.com/zertodownload_docs/Latest/Zerto%20Virtual%20Replication%20Azure%20Enterprise%20Guidelines.pdf) para el resto de los requisitos previos.



## <a name="install-zerto-on-azure-vmware-solution"></a>Instalación de Zerto en Azure VMware Solution

Actualmente, la recuperación ante desastres de Zerto en Azure VMware Solution está en fase de disponibilidad inicial (IA). En la fase de IA, debe ponerse en contacto con Microsoft para solicitar y obtener aprobación para el soporte técnico de IA.

Para solicitar soporte técnico de IA para Zerto Azure VMware Solution, envíe una solicitud de correo electrónico a zertoonavs@microsoft.com. En la fase de IA, Azure VMware Solution solo admite la instalación manual y la incorporación de Zerto. Sin embargo, Microsoft trabajará con usted para asegurarse de que puede instalar manualmente Zerto en la nube privada.

> [!NOTE]
> Como parte de la instalación manual, Microsoft creará una nueva cuenta de usuario de vCenter para Zerto. Esta cuenta de usuario solo es para que Zerto Virtual Manager (ZVM) realice operaciones en vCenter de Azure VMware Solution. Al instalar ZVM en Azure VMware Solution, no seleccione la opción para elegir la aplicación de roles y permisos mediante privilegios de vCenter de Zerto. 


Después de la instalación de ZVM, seleccione las opciones siguientes en la **configuración del sitio** de Zerto Virtual Manager. 

:::image type="content" source="media/zerto-disaster-recovery/zerto-disaster-recovery-install-5.png" alt-text="Captura de pantalla de la sección de automatización de la carga de trabajo que muestra la selección de todas las opciones enumeradas para las casillas azules.":::

>[!NOTE]
>La disponibilidad general de Azure VMware Solution habilitará la instalación de autoservicio y las operaciones de día 2 de Zerto en Azure VMware Solution.


## <a name="configure-zerto-for-disaster-recovery"></a>Configuración de Zerto para la recuperación ante desastres

Para configurar Zerto para los escenarios de VMware local a recuperación ante desastres de Azure VMware Solution y de Azure VMware Solution a recuperación ante desastres en la nube de Azure VMware Solution, consulte el [entorno de vSphere de la guía de administración de Zerto Virtual Manager](https://s3.amazonaws.com/zertodownload_docs/8.5_Latest/Zerto%20Virtual%20Manager%20vSphere%20Administration%20Guide.pdf?cb=1629311409).


Para más información, consulte la [documentación técnica de Zerto](https://www.zerto.com/myzerto/technical-documentation/). Como alternativa, puede descargar todas las guías de Zerto que forman parte del [paquete de documentación en PDF de la herramienta de búsqueda v8.5 para el software de Zerto](https://s3.amazonaws.com/zertodownload_docs/8.5_Latest/SEARCH_TOOL.zip?cb=1629311409).



## <a name="ongoing-management-of-zerto"></a>Administración continua de Zerto

- A medida que escala las operaciones de nube privada de Azure VMware Solution, es posible que tenga que agregar nuevos hosts de Azure VMware Solution para la protección de Zerto o configurar la recuperación ante desastres de Zerto en nuevos clústeres de vSphere de Azure VMware Solution. En ambos escenarios, se le pedirá que abra una solicitud de soporte técnico con el equipo de Azure VMware Solution en la fase de disponibilidad inicial. Puede abrir la [incidencia de soporte técnico](https://rc.portal.azure.com/#create/Microsoft.Support) para estas configuraciones del día 2 desde Azure Portal. 

   :::image type="content" source="media/zerto-disaster-recovery/support-request-zerto-disaster-recovery.png" alt-text="Captura de pantalla que muestra la solicitud de soporte técnico para las configuraciones de recuperación ante desastres de Zerto del día 2.":::

- En la fase de GA, todas las operaciones anteriores se habilitarán en forma de autoservicio automatizado.


## <a name="faqs"></a>Preguntas más frecuentes

### <a name="can-i-use-a-pre-existing-zerto-product-license-on-azure-vmware-solution"></a>¿Puedo usar una licencia de producto de Zerto preexistente en Azure VMware Solution?

Puede reutilizar las licencias de producto de Zerto preexistentes para los entornos de Azure VMware Solution. Si necesita nuevas licencias de Zerto, envíe un correo electrónico a Zerto a **info@zerto.com** para adquirir nuevas licencias. 

### <a name="how-is-zerto-supported"></a>¿Cómo es el soporte de Zerto?

La recuperación ante desastres de Zerto es una solución que Zerto vende para la que ofrece soporte. Para cualquier problema de soporte técnico con Zerto, póngase en contacto siempre con el [soporte técnico de Zerto](https://www.zerto.com/company/support-and-service/support/).

Los equipos de soporte técnico de Zerto y Microsoft se pondrán en contacto entre sí según sea necesario para solucionar los problemas de recuperación ante desastres de Zerto en Azure VMware Solution.

