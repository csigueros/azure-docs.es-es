---
title: Configuración de DHCP para Azure VMware Solution
description: Obtenga información sobre cómo configurar DHCP mediante NSX-T Manager para hospedar un servidor DHCP o mediante un servidor DHCP externo de terceros.
ms.topic: how-to
ms.custom: contperf-fy21q2, contperf-fy22q1
ms.date: 09/13/2021
ms.openlocfilehash: 10234147303ab9959fa1a907b0c558be9e3fe0f6
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128600971"
---
# <a name="configure-dhcp-for-azure-vmware-solution"></a>Configuración de DHCP para Azure VMware Solution

[!INCLUDE [dhcp-dns-in-azure-vmware-solution-description](includes/dhcp-dns-in-azure-vmware-solution-description.md)]

En este artículo de procedimientos, usará NSX-T Manager a fin de configurar DHCP para Azure VMware Solution de una de las maneras siguientes: 


- [Uso de Azure Portal para crear un servidor DHCP o una retransmisión](#use-the-azure-portal-to-create-a-dhcp-server-or-relay)

- [Uso de NSX-T para hospedar el servidor DHCP](#use-nsx-t-to-host-your-dhcp-server)

- [Uso de un servidor DHCP externo de terceros](#use-a-third-party-external-dhcp-server)

>[!TIP]
>Si quiere configurar DHCP mediante una vista simplificada de las operaciones de NSX-T, vea [Configuración de DHCP para Azure VMware Solution](configure-dhcp-azure-vmware-solution.md).


>[!IMPORTANT]
>En el caso de las nubes creadas el 1 de julio de 2021 o después de esta fecha, se debe usar la vista simplificada de las operaciones de NSX-T para configurar DHCP en la puerta de enlace de nivel 1 predeterminada en su entorno.
>
>DHCP no funciona para las máquinas virtuales (VM) de la red elástica L2 de VMware HCX cuando el servidor DHCP está en el centro de datos local.  De forma predeterminada, NSX impide que todas las solicitudes DHCP atraviesen la red elástica L2. Para ver la solución, consulte el procedimiento [Configuración de DHCP en redes VMware HCX extendidas L2](configure-l2-stretched-vmware-hcx-networks.md).

## <a name="use-the-azure-portal-to-create-a-dhcp-server-or-relay"></a>Uso de Azure Portal para crear un servidor DHCP o una retransmisión

Puede crear un servidor o una retransmisión DHCP directamente desde Azure VMware Solution en Azure Portal. El servidor o la retransmisión DHCP se conectan a la puerta de enlace de nivel 1, que se creó al implementar Azure VMware Solution. Todos los segmentos en los que dio intervalos DHCP formarán parte de este DHCP. Después de crear un servidor DHCP o una retransmisión DHCP, debe definir una subred o un intervalo en el nivel de segmento para consumirlo.

1. En la nube privada de Azure VMware Solution, en **Workload Networking** (Red de la carga de trabajo), seleccione **DHCP** > **Add** (DHCP > Agregar).

2. Seleccione **DHCP Server** (Servidor DHCP) o **DHCP Relay** (Retransmisión DHCP) y, luego, proporcione un nombre para el servidor o la retransmisión y tres direcciones IP. 

   >[!NOTE]
   >En el caso de la retransmisión DHCP, solo se necesita una dirección IP para una configuración correcta.

   :::image type="content" source="media/networking/add-dhcp-server-relay.png" alt-text="Captura de pantalla que muestra cómo agregar un servidor DHCP o una retransmisión DHCP en Azure VMware Solution.":::

4. Complete la configuración de DHCP; para ello [proporcione intervalos DHCP en los segmentos lógicos](tutorial-nsx-t-network-segment.md#use-azure-portal-to-add-an-nsx-t-segment) y, luego, seleccione **OK** (Aceptar).



## <a name="use-nsx-t-to-host-your-dhcp-server"></a>Uso de NSX-T para hospedar el servidor DHCP
Si quiere usar NSX-T para hospedar el servidor DHCP, tendrá que crear un servidor DHCP y un servicio de retransmisión. A continuación, agregará un segmento de red y especificará el intervalo de direcciones IP de DHCP.

### <a name="create-a-dhcp-server"></a>Creación de un servidor DHCP

1. En NSX-T Manager, seleccione **Networking** > **DHCP** (Redes > DHCP) y, a continuación, seleccione **Add Server** (Agregar servidor).

1. Seleccione **DHCP** para **Server Type** (Tipo de servidor), proporcione el nombre del servidor y la dirección IP y, después, seleccione **Save** (Guardar).

   :::image type="content" source="./media/manage-dhcp/dhcp-server-settings.png" alt-text="Captura de pantalla en la que se muestra cómo agregar un servidor DHCP en NSX-T Manager." border="true":::

1. Seleccione **Tier 1 Gateways** (Puertas de enlace de nivel 1), seleccione los puntos suspensivos verticales en la puerta de enlace de nivel 1 y seleccione **Edit** (Editar).

   :::image type="content" source="./media/manage-dhcp/edit-tier-1-gateway.png" alt-text="Captura de pantalla en la que se muestra cómo editar la puerta de enlace de nivel 1 para usar un servidor DHCP." border="true":::

1. Para agregar una subred, seleccione **No IP Allocation Set** (No hay conjuntos de asignación de direcciones IP).

   :::image type="content" source="./media/manage-dhcp/add-subnet.png" alt-text="Captura de pantalla en la que se muestra cómo agregar una subred a la puerta de enlace de nivel 1 para usar un servidor DHCP." border="true":::

1. En **Type** (Tipo), seleccione **DHCP Local Server** (Servidor local DHCP). 
   
1. Para **DHCP Server** (Servidor DHCP), seleccione **Default DHCP** (DHCP predeterminado) y luego **Save** (Guardar).

1. Seleccione **Save** (Guardar) de nuevo y luego **Close Editing** (Cerrar edición).

### <a name="add-a-network-segment"></a>Incorporación de un segmento de red

[!INCLUDE [add-network-segment-steps](includes/add-network-segment-steps.md)]

### <a name="specify-the-dhcp-ip-address-range"></a>Especifique el intervalo de direcciones IP de DHCP.
 
Al crear una retransmisión a un servidor DHCP, también deberá especificar el intervalo de direcciones IP de DHCP.

>[!NOTE]
>El rango de direcciones IP no debe superponerse con el rango de IP que se usa en otras redes virtuales de la suscripción y las redes locales.

1. En NSX-T Manager, seleccione **Networking** > **Segments** (Redes > Segmentos). 
   
1. Seleccione los puntos suspensivos verticales en el nombre del segmento y seleccione **Edit** (Editar).
   
1. Seleccione **Set Subnets** (Establecer subredes) para especificar la dirección IP de DHCP para la subred. 
   
   :::image type="content" source="./media/manage-dhcp/network-segments.png" alt-text="Captura de pantalla en la que se muestra cómo establecer las subredes para especificar la dirección IP de DHCP para usar un servidor DHCP." border="true":::
      
1. Modifique la dirección IP de puerta de enlace si es necesario y escriba la dirección IP del intervalo de DHCP. 
      
   :::image type="content" source="./media/manage-dhcp/edit-subnet.png" alt-text="Captura de pantalla en la que se muestran la dirección IP de la puerta de enlace y los intervalos DHCP para usar un servidor DHCP." border="true":::
      
1. Seleccione **Apply** (Aplicar) y, a continuación, **Save** (Guardar). Se asigna un grupo de servidores DHCP al segmento.
      
   :::image type="content" source="./media/manage-dhcp/assigned-to-segment.png" alt-text="Captura de pantalla en la que se muestra el grupo de servidores DHCP asignado al segmento para usar un servidor DHCP." border="true":::


## <a name="use-a-third-party-external-dhcp-server"></a>Uso de un servidor DHCP externo de terceros

Si quiere usar un servidor DHCP externo de terceros, deberá crear un servicio de retransmisión DHCP en NSX-T Manager. También deberá especificar el intervalo de direcciones IP de DHCP.


>[!IMPORTANT]
>En el caso de las nubes creadas el 1 de julio de 2021 o después de esta fecha, se debe usar la vista simplificada de las operaciones de NSX-T para configurar DHCP en la puerta de enlace de nivel 1 predeterminada en su entorno.


### <a name="create-dhcp-relay-service"></a>Creación de un servicio de retransmisión DHCP

Use una retransmisión DHCP para cualquier servicio DHCP que no se base en NSX. Por ejemplo, una máquina virtual que ejecuta DHCP en Azure VMware Solution, en IaaS de Azure o de forma local.

1. En NSX-T Manager, seleccione **Networking** > **DHCP** (Redes > DHCP) y, a continuación, seleccione **Add Server** (Agregar servidor).

1. Seleccione **DHCP Relay** (Retransmisión DHCP) para **Server Type** (Tipo de servidor), proporcione el nombre del servidor y la dirección IP y, después, seleccione **Save** (Guardar).

   :::image type="content" source="./media/manage-dhcp/create-dhcp-relay.png" alt-text="Captura de pantalla en la que se muestra cómo crear un servicio de retransmisión DHCP en NSX-T Manager." border="true":::

1. Seleccione **Tier 1 Gateways** (Puertas de enlace de nivel 1), seleccione los puntos suspensivos verticales en la puerta de enlace de nivel 1 y seleccione **Edit** (Editar).

   :::image type="content" source="./media/manage-dhcp/edit-tier-1-gateway.png" alt-text="Captura de pantalla en la que se muestra cómo editar la puerta de enlace de nivel 1." border="true":::

1. Seleccione **No IP Allocation Set** (No hay conjuntos de asignación de direcciones IP) para definir la asignación de direcciones IP.

   :::image type="content" source="./media/manage-dhcp/add-subnet.png" alt-text="Captura de pantalla en la que se muestra cómo agregar una subred a la puerta de enlace de nivel 1." border="true":::

1. En **Type** (Tipo), seleccione **DHCP Server** (Servidor DHCP). 
   
1. Para **DHCP Server** (Servidor DHCP), seleccione **DHCP Relay** (Retransmisión DHCP) y luego **Save** (Guardar).

1. Seleccione **Save** (Guardar) de nuevo y luego **Close Editing** (Cerrar edición).


### <a name="specify-the-dhcp-ip-address-range"></a>Especifique el intervalo de direcciones IP de DHCP.

Al crear una retransmisión a un servidor DHCP, también deberá especificar el intervalo de direcciones IP de DHCP.

>[!NOTE]
>El rango de direcciones IP no debe superponerse con el rango de IP que se usa en otras redes virtuales de la suscripción y las redes locales.

1. En NSX-T Manager, seleccione **Networking** > **Segments** (Redes > Segmentos). 
   
1. Seleccione los puntos suspensivos verticales en el nombre del segmento y seleccione **Edit** (Editar).
   
1. Seleccione **Set Subnets** (Establecer subredes) para especificar la dirección IP de DHCP para la subred. 
   
   :::image type="content" source="./media/manage-dhcp/network-segments.png" alt-text="Captura de pantalla en la que se muestra cómo establecer las subredes para especificar la dirección IP de DHCP." border="true":::
      
1. Modifique la dirección IP de puerta de enlace si es necesario y escriba la dirección IP del intervalo de DHCP. 
      
   :::image type="content" source="./media/manage-dhcp/edit-subnet.png" alt-text="Captura de pantalla en la que se muestran la dirección IP de la puerta de enlace y los intervalos DHCP." border="true":::
      
1. Seleccione **Apply** (Aplicar) y, a continuación, **Save** (Guardar). Se asigna un grupo de servidores DHCP al segmento.
      
   :::image type="content" source="./media/manage-dhcp/assigned-to-segment.png" alt-text="Captura de pantalla en la que se muestra el grupo de servidores DHCP asignado al segmento." border="true":::


## <a name="next-steps"></a>Pasos siguientes

Si quiere enviar solicitudes DHCP desde las máquinas virtuales de Azure VMware Solution a un servidor DHCP que no es de NSX-T, consulte el artículo [Configuración de DHCP en redes de VMware HCX extendido L2](configure-l2-stretched-vmware-hcx-networks.md).
