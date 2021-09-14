---
title: Traslado de recursos de Azure VMware Solution entre regiones
description: En este artículo se describe cómo trasladar los recursos de Azure VMware Solution de una región de Azure a otra.
ms.custom: subject-moving-resources
ms.topic: how-to
ms.date: 06/01/2021
ms.openlocfilehash: 116350ccf32c18e232f22788d5dd3d693d7e1e9d
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/17/2021
ms.locfileid: "122323657"
---
# <a name="move-azure-vmware-solution-resources-to-another-region"></a>Traslado de recursos de Azure VMware Solution a otra región

>[!IMPORTANT]
>Los pasos de este artículo son solo para trasladar los recursos de Azure VMware Solution (origen) de una región a la instancia de Azure VMware Solution (destino) de otra región. 

Puede trasladar los recursos de Azure VMware Solution a otra región por varios motivos. Por ejemplo, implemente las características o los servicios disponibles solo en regiones concretas, cumpla los requisitos de gobernanza y directivas internas o responda a los requisitos de planeamiento de capacidad. 

Este artículo le ayuda a planear y migrar Azure VMware Solution de una región de Azure a otra como, por ejemplo, de la región de Azure A a la región de Azure B.


En el diagrama se muestra la conectividad de ExpressRoute recomendada entre los dos entornos de Azure VMware Solution.  Se crean una malla de servicio y emparejamiento de sitios HCX entre los dos entornos.  El tráfico de migración de HCX y la extensión de nivel 2 se mueven (representados por la línea roja) entre los dos entornos. Para más información sobre el planeamiento de HCX recomendado para VMware, consulte [Planeamiento de una migración de HCX](https://vmc.techzone.vmware.com/vmc-solutions/docs/deploy/planning-an-hcx-migration#section1).

:::image type="content" source="media/move-across-regions/move-ea-csp-across-regions-2.png" alt-text="Diagrama que muestra la comunicación de ExpressRoute Global Reach entre los entornos de origen y destino de Azure VMware Solution." border="false":::

>[!NOTE]
>No es necesario migrar ningún flujo de trabajo de vuelta al entorno local porque el tráfico fluirá entre las nubes privadas (de origen y destino):
>
>**Nube privada de Azure VMware Solution (origen) > puerta de enlace de ExpressRoute (origen) > puerta de enlace de ExpressRoute (destino) > nube privada de Azure VMware Solution (destino)**

El diagrama muestra la conectividad entre ambos entornos de Azure VMware Solution. 

:::image type="content" source="media/move-across-regions/move-ea-csp-across-regions-connectivity-diagram.png" alt-text="Diagrama que muestra la comunicación entre los entornos de origen y destino de Azure VMware Solution." border="false":::


En este artículo, se le indicarán los pasos para: 

> [!div class="checklist"]
> * Preparar y planear el traslado a otra región de Azure
> * Establecer la conectividad de red entre las dos nubes privadas de Azure VMware Solution
> * Exportar la configuración desde el entorno de origen de Azure VMware Solution
> * Volver a aplicar los elementos de configuración admitidos al entorno de Azure VMware Solution de destino
> * Migrar cargas de trabajo mediante VMware HCX

## <a name="prerequisites"></a>Requisitos previos

- [Se ha actualizado el dispositivo VMware HCX a la revisión más reciente](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-02DB88E1-EC81-434B-9AE9-D100E427B31C.html) para evitar problemas de migración.

- La biblioteca de contenido local del origen es una [biblioteca de contenido publicada](https://docs.vmware.com/en/VMware-Validated-Design/services/deployment-of-vrealize-suite-2019-on-vmware-cloud-foundation-310/GUID-59E0CBA1-2CF6-488D-AA58-C97C76FD8159.html?hWord=N4IghgNiBcIA4FcBGECWBnAFgAgMYHsA7AFwFMTs0kAnMagTxAF8g).

## <a name="prepare"></a>Preparación

En los pasos siguientes se muestra cómo preparar la nube privada de Azure VMware Solution para pasar a otra nube privada. 

### <a name="export-the-source-configuration"></a>Exportación de la configuración de origen

1. Desde el origen, [exporte los segmentos extendidos, las reglas de firewall, los detalles del puerto y las tablas de rutas](https://docs.vmware.com/en/VMware-NSX-T-Data-Center/3.1/administration/GUID-FCE6567E-1174-49CC-90F1-BA7B695B28F0.html).

1. [Exporte el contenido de una vista de lista de inventario a un archivo CSV](https://docs.vmware.com/en/VMware-vSphere/7.0/com.vmware.vsphere.vcenterhost.doc/GUID-C0E8DD52-677E-464F-A3EA-044EE20B7B92.html).

1. [Ordene las cargas de trabajo en grupos de migración (oleada de migración).](https://vmc.techzone.vmware.com/vmc-solutions/docs/deploy/planning-an-hcx-migration#sec4-sub1)


### <a name="deploy-the-target-environment"></a>Implementación del entorno de destino

Para poder mover la configuración de origen, deberá [implementar el entorno de destino](plan-private-cloud-deployment.md).


### <a name="back-up-the-source-configuration"></a>Copia de seguridad de la configuración de origen

Haga una copia de seguridad de la configuración de Azure VMware Solution (origen) que incluya reglas y directivas de firewall, NSX-T y VC. 

- **Proceso:** exporte la configuración de inventario existente. Para la copia de seguridad del inventario, puede usar RVtool (una aplicación de código abierto).

- **Directivas y reglas de red y firewall:** en el entorno de destino de Azure VMware Solution, cree los mismos segmentos de red que en el de origen.

Azure VMware Solution admite todas las soluciones de copia de seguridad. Necesitará privilegios de CloudAdmin para instalar, realizar copias de seguridad de datos y restaurarlas. Para obtener más información, consulte [Soluciones de copia de seguridad para las VM de Azure VMware Solution](ecosystem-back-up-vms.md).

- Copia de seguridad de la carga de trabajo de máquina virtual mediante la solución Commvault:

   - [Cree un cliente de VMware](https://documentation.commvault.com/commvault/v11_sp20/article?p=119380.htm) desde el centro de comandos de Azure VMware Solution vCenter.

   - [Cree un grupo de máquinas virtuales](https://documentation.commvault.com/commvault/v11_sp20/article?p=121182.htm) con las máquinas virtuales necesarias para las copias de seguridad.

   - [Ejecute copias de seguridad en grupos de máquinas virtuales](https://documentation.commvault.com/commvault/v11_sp20/article?p=121657.htm).

   - [Restaure las máquinas virtuales](https://documentation.commvault.com/commvault/v11_sp20/article?p=87275.htm).

- Copia de seguridad de la carga de trabajo de máquina virtual mediante la [solución Veritas NetBackup](https://vrt.as/nb4avs). 

>[!TIP]
>Puede usar [Azure Resource Mover](../resource-mover/move-region-within-resource-group.md?toc=%2fazure%2fazure-resource-manager%2fmanagement%2ftoc.json) para comprobar y migrar la lista de recursos admitidos para moverse entre regiones que dependen de Azure VMware Solution.

### <a name="locate-the-source-expressroute-circuit-id"></a>Búsqueda del identificador del circuito ExpressRoute de origen

1. En el entorno de origen, inicie sesión en [Azure Portal](https://portal.azure.com/).

2. Seleccione **Administrar** > **Conectividad** > **ExpressRoute.**

3. Copie el **identificador de ExpressRoute** del entorno de origen.  Lo necesitará para realizar el emparejamiento entre las nubes privadas.


### <a name="create-the-targets-authorization-key"></a>Creación de la clave de autorización del destino

1. En el entorno de destino, inicie sesión en [Azure Portal](https://portal.azure.com/).

1. Seleccione **Administrar** > **Conectividad** > **ExpressRoute** y seleccione **+ Solicitar una clave de autorización**.

   :::image type="content" source="media/expressroute-global-reach/start-request-authorization-key.png" alt-text="Captura de pantalla que muestra cómo solicitar una clave de autorización de ExpressRoute." border="true" lightbox="media/expressroute-global-reach/start-request-authorization-key.png":::

1. Proporciónele un nombre y luego seleccione **Crear**.

   La clave puede tardar unos 30 segundos en crearse. Una vez creada, la nueva clave aparece en la lista de claves de autorización para la nube privada.

   :::image type="content" source="media/expressroute-global-reach/show-global-reach-auth-key.png" alt-text="Captura de pantalla que muestra la clave de autorización de ExpressRoute Global Reach.":::
  
1. Copie la clave de autorización y el identificador de ExpressRoute. Necesitará la información para finalizar el emparejamiento. La clave de autorización desaparece tras un tiempo, por lo que debe copiarla en cuanto aparezca.

### <a name="peer-between-private-clouds"></a>Emparejamiento entre nubes privadas

Ahora que tiene los identificadores de circuito ExpressRoute y las claves de autorización para ambos entornos, puede emparejar el origen con el destino. Se usará el identificador de recurso y la clave de autorización del circuito ExpressRoute de la nube privada para finalizar el emparejamiento.
 
1. En el entorno de destino, inicie sesión en [Azure Portal](https://portal.azure.com) mediante la misma suscripción que el circuito ExpressRoute de origen.

1. En Administrar, seleccione **Conectividad** > **ExpressRoute Global Reach** > **Agregar**.

   :::image type="content" source="./media/expressroute-global-reach/expressroute-global-reach-tab.png" alt-text="Captura de pantalla que muestra la pestaña Global Reach de ExpressRoute en la nube privada de Azure VMware Solution.":::

1. Pegue el identificador del circuito ExpressRoute y la clave de autorización del destino que creó en el paso anterior.  A continuación, seleccione **Crear**:

   :::image type="content" source="./media/expressroute-global-reach/on-premises-cloud-connections.png" alt-text="Captura de pantalla que muestra el cuadro de diálogo para especificar información de conexión.":::   

### <a name="create-a-site-pairing-between-private-clouds"></a>Creación de un emparejamiento de sitios entre nubes privadas

Después de establecer la conectividad, creará un emparejamiento de sitios de VMware HCX entre las nubes privadas para facilitar la migración de las máquinas virtuales. En el centro de datos, puede conectar o emparejar VMware HCX Cloud Manager en Azure VMware Solution con la instancia de VMware HCX Connector del centro de datos. 

1. Inicie sesión en la instancia de origen de vCenter y, en **Inicio**, seleccione **HCX**.

1. En **Infrastructure** (Infraestructura), seleccione **Site Pairing** (Emparejamiento de sitios) y, a continuación, seleccione la opción **Connect To Remote Site** (Conectar con el sitio remoto) que se encuentra en medio de la pantalla. 

1. Escriba la dirección IP o la dirección URL del Administrador de la nube de HCX de Azure VMware Solution anotada anteriormente `https://x.x.x.9`, el nombre de usuario cloudadmin\@vsphere.local de Azure VMware Solution y la contraseña. A continuación, seleccione **Conectar**.

   > [!NOTE]
   > Para establecer correctamente un emparejamiento de sitios:
   > * VMware HCX Connector debe poder enrutarse a la dirección IP de HCX Cloud Manager a través del puerto 443.
   >
   > * Utilice la misma contraseña que usó para iniciar sesión en vCenter. Recuerde que definió esta contraseña en la pantalla de implementación inicial.

   Verá una pantalla que muestra cómo su instancia de VMware HCX Cloud Manager en Azure VMware Solution y su instancia local de VMware HCX Connector están conectadas (emparejadas).

   :::image type="content" source="media/tutorial-vmware-hcx/site-pairing-complete.png" alt-text="Captura de pantalla que muestra el emparejamiento de HCX Manager en Azure VMware Solution y VMware HCX Connector.":::

### <a name="create-a-service-mesh-between-private-clouds"></a>Creación de una malla de servicio entre nubes privadas

> [!NOTE]
> Para establecer correctamente una malla de servicios con Azure VMware Solution:
>
> * Los puertos UDP 500/4500 están abiertos entre las direcciones del perfil de red de "vínculo superior" de la instancia local de VMware HCX Connector y las direcciones del perfil de red de "vínculo superior" de Azure VMware Solution HCX Cloud.
>
> * Asegúrese de revisar los [puertos necesarios de VMware HCX](https://ports.vmware.com/home/VMware-HCX).

1. En la opción **Infrastructure** (Infraestructura), seleccione **Interconnect** > **Service Mesh** > **Create Service Mesh** (Interconexión > Malla de servicios > Crear malla de servicios).    

   :::image type="content" source="media/tutorial-vmware-hcx/create-service-mesh.png" alt-text="Captura de pantalla de las selecciones para empezar a crear una malla de servicios." lightbox="media/tutorial-vmware-hcx/create-service-mesh.png":::

1. Revise los sitios que se han rellenado previamente y, a continuación, seleccione **Continuar**. 

   > [!NOTE]
   > Si esta es la primera configuración de la malla de servicios, no tendrá que modificar esta pantalla.  

1. Seleccione los perfiles de proceso de origen y remoto en las listas desplegables y, a continuación, seleccione **Continue** (Continuar).  

   Las selecciones definen los recursos donde las VM pueden consumir los servicios de VMware HCX.  

   :::image type="content" source="media/tutorial-vmware-hcx/select-compute-profile-source.png" alt-text="Captura de pantalla que muestra la selección del perfil de proceso de origen." lightbox="media/tutorial-vmware-hcx/select-compute-profile-source.png":::

   :::image type="content" source="media/tutorial-vmware-hcx/select-compute-profile-remote.png" alt-text="Captura de pantalla que muestra la selección del perfil de proceso remoto." lightbox="media/tutorial-vmware-hcx/select-compute-profile-remote.png":::

1. Revise los servicios que desea habilitar y, a continuación, seleccione **Continue** (Continuar).  

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

## <a name="move"></a>Move

En los pasos siguientes se muestra cómo trasladar los recursos de la nube privada de Azure VMware Solution a otra nube privada de otra región. 

En esta sección, migrará:

- La configuración del grupo de recursos y la creación de carpetas

- Las plantillas de máquina virtual y las etiquetas asociadas

- La implementación de segmentos lógicos en función de los grupos de puertos del origen y las VLAN asociadas 

- Grupos y servicios de seguridad de red

- La directiva de firewall y las reglas de puerta de enlace basadas en las directivas de firewall del origen

### <a name="migrate-the-source-vsphere-configuration"></a>Migración de la configuración de vSphere del entorno de origen

En este paso, copiará la configuración de vSphere del entorno de origen y la trasladará al de destino. 

1. En la instancia de vCenter del entorno de origen, use la misma configuración del grupo de recursos y [cree la misma configuración del grupo de recursos](https://docs.vmware.com/en/VMware-vSphere/7.0/com.vmware.vsphere.resmgmt.doc/GUID-0F6C6709-A5DA-4D38-BE08-6CB1002DD13D.html#example-creating-resource-pools-4) en la instancia de vCenter del entorno de destino.

2. En la instancia de vCenter de origen, use el mismo nombre de carpeta de máquina virtual y [cree la misma carpeta de máquina virtual](https://docs.vmware.com/en/VMware-Validated-Design/6.1/sddc-deployment-of-cloud-operations-and-automation-in-the-first-region/GUID-9D935BBC-1228-4F9D-A61D-B86C504E469C.html) en la instancia de vCenter de destino en **Carpetas**.

3. Use VMware HCX para migrar todas las plantillas de máquina virtual de la instancia de vCenter de origen a la de destino.

   1. En el entorno de origen, convierta las plantillas existentes en máquinas virtuales y, a continuación, mígrelas al de destino.

   2. En el entorno de destino, convierta las máquinas virtuales en plantillas de máquina virtual.

4. En el entorno de origen, use el mismo nombre de etiquetas de máquina virtual y [créelas en la instancia de vCenter de destino](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.vcenterhost.doc/GUID-05323758-1EBF-406F-99B6-B1A33E893453.html). 

5. En la biblioteca de contenido de vCenter del origen, use la opción de biblioteca suscrita para copiar las plantillas ISO, OVF, OVA y VM en la biblioteca de contenido del entorno de destino:

   1. Si la biblioteca de contenido aún no está publicada, seleccione la opción **Enable publishing** (Habilitar publicación).

   2. En la biblioteca de contenido del origen, copie la dirección URL de la biblioteca publicada.

   3. En el entorno de destino, [cree una biblioteca de contenido suscrita](deploy-vm-content-library.md) con la dirección URL de la biblioteca de origen.

   4. Seleccione **Sync Now** (Sincronizar ahora).


### <a name="configure-the-target-nsx-t-environment"></a>Configuración del entorno de destino de NSX-T

En este paso, usará la configuración de NSX-T de origen para configurar el entorno de NSX-T de destino.

>[!NOTE]
>Tendrá varias características configuradas en el entorno de origen de NSX-T, por lo que deberá copiar o leer desde este entorno y volver a crearlo en la nube privada de destino. Use la extensión L2 para mantener la misma dirección IP y la misma dirección Mac de la máquina virtual al migrar el origen a la nube privada de AVS de destino para evitar el tiempo de inactividad debido al cambio de IP y la configuración relacionada.

1. [Configure los componentes de red de NSX](tutorial-nsx-t-network-segment.md) necesarios en el entorno de destino en la puerta de enlace de nivel 1 predeterminada.

1. [Cree la configuración del grupo de seguridad](https://docs.vmware.com/en/VMware-NSX-T-Data-Center/3.1/administration/GUID-41CC06DF-1CD4-4233-B43E-492A9A3AD5F6.html).

1. [Cree la directiva de firewall distribuido y las reglas](https://docs.vmware.com/en/VMware-NSX-T-Data-Center/3.1/administration/GUID-41CC06DF-1CD4-4233-B43E-492A9A3AD5F6.html).

1. [Cree la directiva de firewall y las reglas de puerta de enlace](https://docs.vmware.com/en/VMware-NSX-T-Data-Center/3.1/administration/GUID-DE6FE8CB-017E-41C8-85FC-D71CF27F85C2.html).

1. [Cree el servidor DHCP o el servicio de retransmisión DHCP](configure-dhcp-azure-vmware-solution.md). 

1. [Configure la creación de reflejo del puerto](configure-port-mirroring-azure-vmware-solution.md).

1. [Configure el reenviador de DNS](configure-dns-azure-vmware-solution.md).

1. [Configure una nueva puerta de enlace de nivel 1 (que no sea la predeterminada)](https://docs.vmware.com/en/VMware-NSX-T-Data-Center/3.1/administration/GUID-A6042263-374F-4292-892E-BC86876325A4.html).  Esta configuración se basa en la instancia de NSX-T configurada en el origen. 

### <a name="migrate-the-vms-from-the-source"></a>Migración de las máquinas virtuales desde el origen 

En este paso, usará VMware HCX para migrar las máquinas virtuales del origen al destino. Tendrá la opción de hacer una extensión de nivel 2 desde el origen y usar HCX para trasladar mediante vMotion las máquinas virtuales del origen al destino con una interrupción mínima. 

Además de vMotion, también se recomiendan otros métodos, como Bulk y Cold vMotion.  Más información acerca de:

- [Planeación de una migración de HCX](https://vmc.techzone.vmware.com/vmc-solutions/docs/deploy/planning-an-hcx-migration#sec4-sub1)

- [Migración de máquinas virtuales con HCX](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-14D48C15-3D75-485B-850F-C5FCB96B5637.html)

### <a name="cutover-extended-networks"></a>Migración total de redes extendidas 

En este paso, efectuará una migración total de puerta de enlace para finalizar las redes extendidas. También trasladará (migrará) las puertas de enlace del entorno de origen de Azure VMware Solution al de destino.

>[!IMPORTANT]
>Debe realizar la migración total de la puerta de enlace después de la migración de la carga de trabajo de VLAN al entorno de destino de Azure VMware Solution. Además, no debe haber ninguna dependencia de máquina virtual en los entornos de origen y de destino.

Antes de la migración total de la puerta de enlace, compruebe todos los servicios y el rendimiento de la carga de trabajo migrada. Una vez que los propietarios de aplicaciones y servicios web acepten el rendimiento (excepto para cualquier problema de latencia), puede continuar con la migración total de la puerta de enlace.  Una vez que haya completado la migración total, deberá modificar los registros DNS A y PTR públicos. 

Para obtener recomendaciones de VMware, consulte [Migración total de redes extendidas](https://vmc.techzone.vmware.com/vmc-solutions/docs/deploy/planning-an-hcx-migration#section9).


### <a name="public-ip-dnat-for-migrated-dmz-vms"></a>DNAT de IP pública para máquinas virtuales DMZ migradas

Hasta este punto, ha migrado las cargas de trabajo al entorno de destino. Estas cargas de trabajo de aplicación deben ser accesibles desde la red pública de Internet. El entorno de destino proporciona dos maneras de hospedar cualquier aplicación. Las aplicaciones se pueden:

- Hospedar y publicar en el equilibrador de carga de Application Gateway.

- Publicar mediante la característica de IP pública en vWAN.

La dirección IP pública normalmente es una NAT de destino traducida en Azure Firewall. Con las reglas de DNAT, la directiva de firewall traduciría la solicitud de direcciones IP públicas a una dirección privada (webserver) con un puerto. Para más información, consulte [Uso de la funcionalidad de IP pública en Azure Virtual WAN](./enable-public-internet-access.md).

>[!NOTE]
>SNAT está configurada de forma predeterminada en Azure VMware Solution, por lo que debe habilitarla en la configuración de conectividad de la nube privada de Azure VMware Solution en la pestaña Administrar.

## <a name="decommission"></a>Dar de baja

En este último paso, comprobará que todas las cargas de trabajo de máquina virtual se migraron correctamente, incluida la configuración de red. Si no hay ninguna dependencia, puede desconectar la malla de servicio HCX, el emparejamiento de sitios y la conectividad de red del entorno de origen. 

>[!NOTE]
>Una vez retirada la nube privada, no puede deshacerla, ya que la configuración y los datos se perderán.


## <a name="next-steps"></a>Pasos siguientes

Más información sobre:

- [Compatibilidad de la operación de traslado con Microsoft.AVS](../azure-resource-manager/management/move-support-resources.md#microsoftavs)
- [Directrices para el traslado de recursos de red](../azure-resource-manager/management/move-limitations/networking-move-limitations.md)
- [Guía del traslado de máquinas virtuales](../azure-resource-manager/management/move-limitations/virtual-machines-move-limitations.md)
- [Orientaciones para el traslado de recursos de App Service](../azure-resource-manager/management/move-limitations/app-service-move-limitations.md)
