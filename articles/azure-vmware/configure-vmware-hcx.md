---
title: Configuración de VMware HCX en Azure VMware Solution
description: Configure el conector de VMware HCX local para la nube privada de Azure VMware Solution.
ms.topic: tutorial
ms.date: 09/07/2021
ms.openlocfilehash: 022936108246b57f27d26dfffdaa260399821740
ms.sourcegitcommit: 557ed4e74f0629b6d2a543e1228f65a3e01bf3ac
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/05/2021
ms.locfileid: "129457513"
---
# <a name="configure-on-premises-vmware-hcx-connector"></a>Configuración del conector de VMware HCX local

Una vez que haya instalado el [complemento VMware HCX](install-vmware-hcx.md), estará listo para configurar el conector de VMware HCX local para la nube privada de Azure VMware Solution.  

En esta guía paso a paso, hará lo siguiente:

* Emparejar VMware HCX Connector local con HCX Cloud Manager de Azure VMware Solution
* Configurar el perfil de red, el perfil de proceso y la malla de servicios
* Comprobar el estado del dispositivo y validar que la migración es posible

Después de completar estos pasos, tendrá un entorno listo para producción para crear máquinas virtuales (VM) y la migración. 

## <a name="prerequisites"></a>Requisitos previos

- Tener instalado el [conector de VMware HCX](install-vmware-hcx.md).

- Si tiene pensado usar VMware HCX Enterprise, asegúrese de habilitar el complemento [VMware HCX Enterprise](https://cloud.vmware.com/community/2019/08/08/introducing-hcx-enterprise/) mediante el envío de una [solicitud de soporte técnico](https://portal.azure.com/#create/Microsoft.Support). Es una evaluación gratuita de 12 meses en Azure VMware Solution.

- Si tiene previsto [habilitar VMware HCX MON](https://docs.vmware.com/en/VMware-HCX/4.1/hcx-user-guide/GUID-0E254D74-60A9-479C-825D-F373C41F40BC.html), asegúrese de que tiene lo siguiente:  

   - NSX-T o VDS local para la extensión de red de HCX (sin conmutador estándar)

   - Uno o varios segmentos de red extendidos activos

- Se han cumplido los [requisitos de versión de software de VMware](https://docs.vmware.com/en/VMware-HCX/4.1/hcx-user-guide/GUID-54E5293B-8707-4D29-BFE8-EE63539CC49B.html).

- El entorno de vSphere local (entorno de origen) cumple los [requisitos mínimos](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-54E5293B-8707-4D29-BFE8-EE63539CC49B.html).

- [Global Reach de Azure ExpressRoute](tutorial-expressroute-global-reach-private-cloud.md) se configura entre los circuitos de ExpressRoute de nube privada de Azure VMware Solution y del entorno local.

- [Todos los puertos necesarios](https://ports.vmware.com/home/VMware-HCX) están abiertos para realizar la comunicación entre los componentes locales y el entorno local de Azure VMware Solution.

- [Definición de segmentos de red de VMware HCX](plan-private-cloud-deployment.md#define-vmware-hcx-network-segments).  Los casos de uso principales de VMware HCX son las migraciones de cargas de trabajo y la recuperación ante desastres.

- [Revise la documentación de VMware HCX](https://docs.vmware.com/en/VMware-HCX/4.1/hcx-user-guide/GUID-BFD7E194-CFE5-4259-B74B-991B26A51758.html) para obtener información sobre el uso de HCX.

## <a name="add-a-site-pairing"></a>Agregar un emparejamiento de sitios

En el centro de datos, puede conectar o emparejar VMware HCX Cloud Manager en Azure VMware Solution con la instancia de VMware HCX Connector del centro de datos.

> [!IMPORTANT]
> Aunque la herramienta VMware Configuration Maximum estable que el número máximo de pares de sitios entre la instancia local de HCX Connector y HCx Cloud Manager es 25, la licencia lo limita a 3 para HCX Advanced y 10 para HCX Enterprise Edition.

1. Inicie sesión en la instancia local de vCenter y en **Inicio**, seleccione **HCX**.

1. En **Infrastructure** (Infraestructura), seleccione **Site Pairing** (Emparejamiento de sitios) y, a continuación, seleccione la opción **Connect To Remote Site** (Conectar con el sitio remoto) que se encuentra en medio de la pantalla.

1. Escriba la dirección URL o la dirección IP de HCX Cloud Manager de Azure VMware Solution que anotó anteriormente `https://x.x.x.9` y las credenciales de un usuario con el rol de CloudAdmin en la nube privada. A continuación, seleccione **Conectar**.

   > [!NOTE]
   > Para establecer correctamente un emparejamiento de sitios:
   > * VMware HCX Connector debe poder enrutarse a la dirección IP de HCX Cloud Manager a través del puerto 443.
   >
   > * Se recomienda una cuenta de servicio del origen de identidad externo, como Active Directory, para las conexiones de emparejamiento de sitios. Para obtener más información sobre cómo configurar cuentas independientes para los servicios conectados, vea [Conceptos de acceso e identidad](./concepts-identity.md).

   Verá una pantalla que muestra cómo su instancia de VMware HCX Cloud Manager en Azure VMware Solution y su instancia local de VMware HCX Connector están conectadas (emparejadas).

   :::image type="content" source="media/tutorial-vmware-hcx/site-pairing-complete.png" alt-text="Captura de pantalla que muestra el emparejamiento de sitios de HCX Manager en Azure VMware Solution y VMware HCX Connector.":::

Puede encontrar información de un extremo a otro de este procedimiento en el vídeo [Azure VMware Solution: Emparejamiento de sitios de HCX](https://www.youtube.com/embed/jXOmYUnbWZY?rel=0&amp;vq=hd720).

## <a name="create-network-profiles"></a>Creación de perfiles de red

El conector de VMware HCX implementa un subconjunto de aplicaciones virtuales (automatizadas) que requieren varios segmentos IP. Al crear los perfiles de red, se usan los segmentos IP que identificó durante la [fase de planeamiento](plan-private-cloud-deployment.md#define-vmware-hcx-network-segments).  Va a crear cuatro perfiles de red:

   - Administración
   - vMotion
   - Replicación
   - Vínculo superior

1. En **Infrastructure** (Infraestructura), seleccione **Interconnect** > **Multi-Site Service Mesh** > **Network Profiles** > **Create Network Profile** (Interconectar > Malla de servicios de varios sitios > Perfiles de red > Crear perfil de red).

   :::image type="content" source="media/tutorial-vmware-hcx/network-profile-start.png" alt-text="Captura de pantalla que muestra dónde crear un perfil de red en el cliente de vSphere." lightbox="media/tutorial-vmware-hcx/network-profile-start.png":::

1. En cada perfil de red, seleccione la red y el grupo de puertos, proporcione un nombre y cree el grupo de direcciones IP del segmento. Seleccione **Crear**.

   :::image type="content" source="media/tutorial-vmware-hcx/example-configurations-network-profile.png" alt-text="Captura de pantalla que muestra los detalles de un perfil de red nuevo." lightbox="media/tutorial-vmware-hcx/example-configurations-network-profile.png":::

Puede encontrar información de un extremo a otro de este procedimiento en el vídeo [Azure VMware Solution: Perfil de red de HCX](https://www.youtube.com/embed/O0rU4jtXUxc).

## <a name="create-a-compute-profile"></a>Creación de un perfil de proceso

1. En **Infraestructura**, seleccione **Interconnect (Interconectar)**  > **Compute Profiles (Perfiles de proceso)**  > **Create Compute Profile (Crear perfiles de proceso)** .

   :::image type="content" source="media/tutorial-vmware-hcx/compute-profile-create.png" alt-text="Captura de pantalla que muestra las selecciones para empezar a crear un perfil de proceso." lightbox="media/tutorial-vmware-hcx/compute-profile-create.png":::

1. Escriba un nombre para el perfil y seleccione **Continuar**.

   :::image type="content" source="media/tutorial-vmware-hcx/name-compute-profile.png" alt-text="Captura de pantalla que muestra la entrada de un nombre de perfil de proceso y el botón Continue (Continuar)." lightbox="media/tutorial-vmware-hcx/name-compute-profile.png":::

1. Seleccione los servicios que se van a habilitar, como la migración, la extensión de red o la recuperación ante desastres y, a continuación, seleccione **Continue** (Continuar).

   > [!NOTE]
   > Por lo general, no es necesario cambiar nada.

1. En **Select Service Resources** (Seleccionar recursos de servicio), seleccione uno o más recursos de servicio (clústeres) para habilitar los servicios de VMware HCX seleccionados.

1. Cuando vea los clústeres en el centro de datos local, seleccione **Continuar**.

   :::image type="content" source="media/tutorial-vmware-hcx/select-service-resource.png" alt-text="Captura de pantalla que muestra los recursos de servicio seleccionados y el botón Continue (Continuar)." lightbox="media/tutorial-vmware-hcx/select-service-resource.png":::

1. En **Select Datastore** (Seleccionar almacén de datos), seleccione el recurso de almacenamiento del almacén de datos para implementar los dispositivos de interconexión de VMware HCX. Después, seleccione **Continuar**.

   Cuando se seleccionan varios recursos, VMware HCX usa el primero seleccionado hasta que se agota su capacidad.

   :::image type="content" source="media/tutorial-vmware-hcx/deployment-resources-and-reservations.png" alt-text="Captura de pantalla que muestra un recurso de almacenamiento de datos seleccionado y el botón Continue (Continuar)." lightbox="media/tutorial-vmware-hcx/deployment-resources-and-reservations.png":::

1. En **Select Management Network Profile** (Seleccionar perfil de red de administración), seleccione el perfil de red de administración que creó en los pasos anteriores. Después, seleccione **Continuar**.

   :::image type="content" source="media/tutorial-vmware-hcx/select-management-network-profile.png" alt-text="Captura de pantalla que muestra la selección de un perfil de red de administración y el botón Continue (Continuar)." lightbox="media/tutorial-vmware-hcx/select-management-network-profile.png":::

1. En **Select Uplink Network Profile** (Seleccionar perfil de red de vínculo superior), seleccione el perfil de red de vínculo superior que ha creado en el procedimiento anterior. Después, seleccione **Continuar**.

   :::image type="content" source="media/tutorial-vmware-hcx/select-uplink-network-profile.png" alt-text="Captura de pantalla que muestra la selección de un perfil de red de vínculo superior y el botón Continue (Continuar)." lightbox="media/tutorial-vmware-hcx/select-uplink-network-profile.png":::

1. En **Seleccionar perfil de red de vMotion**, seleccione el perfil de red de vMotion que creó en los pasos anteriores. Después, seleccione **Continuar**.

   :::image type="content" source="media/tutorial-vmware-hcx/select-vmotion-network-profile.png" alt-text="Captura de pantalla que muestra la selección de un perfil de red de vMotion y el botón Continue (Continuar)." lightbox="media/tutorial-vmware-hcx/select-vmotion-network-profile.png":::

1. En **Seleccionar perfil de red de replicación de vSphere**, seleccione el perfil de red de replicación que creó en los pasos anteriores. Después, seleccione **Continuar**.

   :::image type="content" source="media/tutorial-vmware-hcx/select-replication-network-profile.png" alt-text="Captura de pantalla que muestra la selección de un perfil de red de replicación y el botón Continue (Continuar)." lightbox="media/tutorial-vmware-hcx/select-replication-network-profile.png":::

1. En **Seleccionar los conmutadores distribuidos para las extensiones de red**, seleccione los conmutadores que contienen las máquinas virtuales que se van a migrar a Azure VMware Solution en una red extendida de nivel 2. Después, seleccione **Continuar**.

   > [!NOTE]
   > Si no va a migrar máquinas virtuales en redes extendidas de nivel 2 (L2), puede omitir este paso.

   :::image type="content" source="media/tutorial-vmware-hcx/select-layer-2-distributed-virtual-switch.png" alt-text="Captura de pantalla que muestra la selección de los conmutadores virtuales distribuidos y el botón Continue (Continuar)." lightbox="media/tutorial-vmware-hcx/select-layer-2-distributed-virtual-switch.png":::

1. Revise las reglas de conexión y seleccione **Continue** (Continuar).

   :::image type="content" source="media/tutorial-vmware-hcx/review-connection-rules.png" alt-text="Captura de pantalla que muestra las reglas de conexión y el botón Continue (Continuar)." lightbox="media/tutorial-vmware-hcx/review-connection-rules.png":::

1. Seleccione **Finish** (Finalizar) para crear el perfil de proceso.

   :::image type="content" source="media/tutorial-vmware-hcx/compute-profile-done.png" alt-text="Captura de pantalla que muestra información del perfil de proceso." lightbox="media/tutorial-vmware-hcx/compute-profile-done.png":::

Puede encontrar información de un extremo a otro de este procedimiento en el vídeo [Azure VMware Solution: Perfil de proceso](https://www.youtube.com/embed/e02hsChI3b8).

## <a name="create-a-service-mesh"></a>Creación de una malla de servicios

>[!IMPORTANT]
>Asegúrese de que los puertos UDP 500/4500 están abiertos entre las direcciones del perfil de red de "vínculo superior" de la instancia local de VMware HCX Connector y las direcciones del perfil de red de "vínculo superior" de Azure VMware Solution HCX Cloud.


1. En la opción **Infrastructure** (Infraestructura), seleccione **Interconnect** > **Service Mesh** > **Create Service Mesh** (Interconexión > Malla de servicios > Crear malla de servicios).

   :::image type="content" source="media/tutorial-vmware-hcx/create-service-mesh.png" alt-text="Captura de pantalla de las selecciones para empezar a crear una malla de servicios." lightbox="media/tutorial-vmware-hcx/create-service-mesh.png":::

1. Revise los sitios que se han rellenado previamente y, a continuación, seleccione **Continuar**.

   > [!NOTE]
   > Si esta es la primera configuración de la malla de servicios, no tendrá que modificar esta pantalla.

1. Seleccione los perfiles de proceso de origen y remoto en las listas desplegables y, a continuación, seleccione **Continue** (Continuar).

   Las selecciones definen los recursos donde las VM pueden consumir los servicios de VMware HCX.

   :::image type="content" source="media/tutorial-vmware-hcx/select-compute-profile-source.png" alt-text="Captura de pantalla que muestra la selección del perfil de proceso de origen." lightbox="media/tutorial-vmware-hcx/select-compute-profile-source.png":::

   :::image type="content" source="media/tutorial-vmware-hcx/select-compute-profile-remote.png" alt-text="Captura de pantalla que muestra la selección del perfil de proceso remoto." lightbox="media/tutorial-vmware-hcx/select-compute-profile-remote.png":::

1. Revise los servicios que se van a habilitar y, a continuación, seleccione **Continue** (Continuar).

1. En **Advanced Configuration - Override Uplink Network profiles** (Configuración avanzada: Invalidar los perfiles de red de vínculo superior), seleccione **Continue** (Continuar).

   Los perfiles de red de vínculo superior se conectan a la red a través de la cual se puede acceder a los dispositivos de interconexión del sitio remoto.

1. En **Advanced Configuration - Network Extension Appliance Scale Out** (Configuración avanzada: escalabilidad horizontal del dispositivo de extensión de red), revise la información y seleccione **Continuar**.

   Puede tener hasta ocho VLAN por dispositivo. También puede implementar otro dispositivo para agregar ocho VLAN más. También debe disponer de espacio de IP para los dispositivos adicionales. Tiene que haber una IP por dispositivo.  Para más información, consulte los [límites de configuración de VMware HCX](https://configmax.vmware.com/guest?vmwareproduct=VMware%20HCX&release=VMware%20HCX&categories=41-0,42-0,43-0,44-0,45-0).

   :::image type="content" source="media/tutorial-vmware-hcx/extend-networks-increase-vlan.png" alt-text="Captura de pantalla que muestra dónde hay que aumentar el número de VLAN." lightbox="media/tutorial-vmware-hcx/extend-networks-increase-vlan.png":::

1. En **Advanced Configuration - Traffic Engineering** (Configuración avanzada: ingeniería de tráfico), revise y realice las modificaciones que considere necesarias y, a continuación, seleccione **Continue** (Continuar).

1. Revise la vista previa de la topología y seleccione **Continue** (Continuar).

1. A continuación, escriba un nombre descriptivo para esta malla de servicios y seleccione **Finalizar** para terminar el proceso.

1. Seleccione **Ver tareas** para supervisar la implementación.

   :::image type="content" source="media/tutorial-vmware-hcx/monitor-service-mesh.png" alt-text="Captura de pantalla que muestra el botón para ver las tareas.":::

   Una vez que la implementación de la malla de servicios finalice correctamente, verá los servicios en color verde.

   :::image type="content" source="media/tutorial-vmware-hcx/service-mesh-green.png" alt-text="Captura de pantalla que muestra indicadores verdes en los servicios." lightbox="media/tutorial-vmware-hcx/service-mesh-green.png":::

1. Para comprobar el estado de la malla de servicios, compruebe el estado del dispositivo.

1. Seleccione **Interconnect** > **Appliances** (Interconectar > Dispositivos).

   :::image type="content" source="media/tutorial-vmware-hcx/interconnect-appliance-state.png" alt-text="Captura de pantalla que muestra las selecciones para comprobar el estado del dispositivo." lightbox="media/tutorial-vmware-hcx/interconnect-appliance-state.png":::

   >[!NOTE]
   >Después de establecer la malla de servicio, es posible que observe un nuevo almacén de datos y un nuevo host en la nube privada. Este es un comportamiento perfectamente normal después de establecer una malla de servicio.
   >
   >:::image type="content" source="media/tutorial-vmware-hcx/hcx-service-mesh-datastore-host.png" alt-text="Captura de pantalla que muestra el almacén de datos y el host de malla de servicio de HCX." lightbox="media/tutorial-vmware-hcx/hcx-service-mesh-datastore-host.png":::

El estado del túnel de interconexión de HCX debe indicar **UP** (Activo) y estar en color verde. Está listo para migrar y proteger las máquinas virtuales de Azure VMware Solution mediante VMware HCX. Azure VMware Solution admite migraciones de cargas de trabajo (con o sin una extensión de red). Por lo tanto, aún puede migrar cargas de trabajo en el entorno de vSphere, junto con la creación local de redes y la implementación de máquinas virtuales en esas redes. Para más información, consulte la [documentación de VMware HCX](https://docs.vmware.com/en/VMware-HCX/index.html). 



Puede encontrar información de un extremo a otro de este procedimiento en el vídeo [Azure VMware Solution: Malla de servicios](https://www.youtube.com/embed/COY3oIws108).


## <a name="next-steps"></a>Pasos siguientes

Ahora que ha configurado el conector de HCX, también puede obtener información sobre:

- [Creación de una extensión de red HCX](configure-hcx-network-extension.md)

- [Guía de VMware HCX Mobility Optimized Networking (MON)](vmware-hcx-mon-guidance.md)

