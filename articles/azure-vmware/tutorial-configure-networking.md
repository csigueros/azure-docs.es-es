---
title: 'Tutorial: Configuración de redes para la nube privada de VMware en Azure'
description: Aprenda a crear y configurar las redes necesarias para implementar una nube privada en Azure.
ms.topic: tutorial
ms.custom: contperf-fy22q1
ms.date: 07/30/2021
ms.openlocfilehash: 99389c55ab13b6c6c181a4d7d1bcf14c3b8ba08d
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128597804"
---
# <a name="tutorial-configure-networking-for-your-vmware-private-cloud-in-azure"></a>Tutorial: Configuración de redes para la nube privada de VMware en Azure

Una nube privada de Azure VMware Solution requiere una instancia de Azure Virtual Network. Dado que Azure VMware Solution no admite la instancia de vCenter local, tendrá que seguir unos pasos adicionales para la integración con el entorno local. También es necesario configurar un circuito de ExpressRoute y una puerta de enlace de red virtual.

[!INCLUDE [disk-pool-planning-note](includes/disk-pool-planning-note.md)]


En este tutorial, aprenderá a:

> [!div class="checklist"]
> * Creación de una red virtual 
> * Creación de una puerta de enlace de red virtual
> * Conectar el circuito ExpressRoute a la puerta de enlace

>[!NOTE]
>Antes de crear una nueva red virtual, evalúe si ya tiene una existente en Azure y puede usarla para conectarse a Azure VMware Solution o si quiere crear una totalmente nueva.  
>* Para usar una red virtual existente en la misma suscripción de Azure que Azure VMware Solution, use la pestaña **[Azure vNet connect](#select-an-existing-vnet)** en **Conectividad**. 
>* Para usar una red virtual existente en una suscripción de Azure distinta a Azure VMware Solution, use la guía **[Conectarse a la nube privada manualmente](#connect-to-the-private-cloud-manually)** . 
>* Para crear una red virtual nueva en la misma suscripción de Azure que Azure VMware Solution, use la pestaña **[Azure vNet connect](#create-a-new-vnet)** o cree una [manualmente](#create-a-vnet-manually).

## <a name="connect-with-the-azure-vnet-connect-feature"></a>Característica de conexión con la red virtual de Azure

Puede usar la característica **Azure vNet Connect** (Conexión de red virtual de Azure) para usar una red virtual existente o crear una nueva red virtual para conectarse a Azure VMware Solution.   

>[!NOTE]
>El espacio de direcciones de la red virtual no se puede superponer con el CIDR de la nube privada de Azure VMware Solution.


### <a name="select-an-existing-vnet"></a>Selección de una red virtual existente

Al seleccionar una red virtual existente, se vuelve a implementar la plantilla de Azure Resource Manager (ARM) que crea la red virtual, junto con otros recursos. Los recursos, en este caso, son la dirección IP pública, la puerta de enlace, la conexión de puerta de enlace y la clave de autorización de ExpressRoute. Si todo está configurado, la implementación no cambiará nada. Sin embargo, si falta algo, se creará automáticamente. Por ejemplo, si falta GatewaySubnet, se agrega durante la implementación.

1. En la nube privada de Azure VMware Solution, en **Administrar**, seleccione **Conectividad**.

2. Seleccione la pestaña **Azure vNet connect**  (Conexión de red virtual de Azure) y, a continuación, la red virtual existente.

   :::image type="content" source="media/networking/azure-vnet-connect-tab.png" alt-text="Captura de pantalla que muestra la pestaña Azure vNet connect (Conexión de red virtual de Azure) en Conectividad con una red virtual existente seleccionada":::.

3. Seleccione **Guardar**.

   En este punto, la red virtual valida si se detectan espacios de direcciones IP superpuestos entre Azure VMware Solution y la red virtual. Si se detectan, cambie la dirección de red de la nube privada o de la red virtual para que no se superpongan. 


### <a name="create-a-new-vnet"></a>Creación de una nueva red virtual

Al crear una nueva red virtual, los componentes necesarios para conectarse a Azure VMware Solution se crean automáticamente.

1. En la nube privada de Azure VMware Solution, en **Administrar**, seleccione **Conectividad**.

2. Seleccione la pestaña **Azure vNet connect**  (Conexión de red virtual de Azure) y, a continuación, **Crear nueva**.

   :::image type="content" source="media/networking/azure-vnet-connect-tab-create-new.png" alt-text="Captura de pantalla que muestra la pestaña Azure vNet connect (Conexión de red virtual de Azure) en Conectividad.":::

3. Proporcione o actualice la información de la nueva red virtual y, a continuación, seleccione **Aceptar**.

   En este punto, la red virtual valida si se detectan espacios de direcciones IP superpuestos entre Azure VMware Solution y la red virtual. Si se detectan, cambie la dirección de red de la nube privada o la red virtual para que no se superpongan. 

   :::image type="content" source="media/networking/create-new-virtual-network.png" alt-text="Captura de pantalla que muestra la ventana Crear red virtual.":::

La red virtual con el intervalo de direcciones proporcionado y GatewaySubnet se crea en la suscripción y el grupo de recursos.  


## <a name="connect-to-the-private-cloud-manually"></a>Conexión manual a la nube privada

### <a name="create-a-vnet-manually"></a>Creación manual de una red virtual

1. Inicie sesión en [Azure Portal](https://portal.azure.com).

1. Vaya al grupo de recursos que creó en el [tutorial para crear una nube privada](tutorial-create-private-cloud.md) y seleccione **+ Agregar** para definir un nuevo recurso. 

1. En el campo **Buscar en Marketplace**, escriba **Virtual Network**. Busque el recurso Virtual Network y selecciónelo.

1. En la página **Virtual Network**, seleccione **Crear** para configurar la red virtual para la nube privada.

1. En la página **Crear red virtual**, escriba los detalles de la red virtual.

1. En la pestaña **Aspectos básicos**, escriba el nombre de la red virtual, seleccione la región adecuada y haga clic en **Siguiente: Direcciones IP**.

1. En la pestaña **Direcciones IP**, en **Espacio de direcciones IPv4**, escriba el espacio de direcciones que creó en el tutorial anterior.

   > [!IMPORTANT]
   > Debe usar un espacio de direcciones que **no** se superponga con el que usó al crear la nube privada en el tutorial anterior.

1. Seleccione **+ Agregar subred** y, en la página **Agregar subred**, asigne a la subred un nombre y un intervalo de direcciones adecuados. Cuando haya terminado, seleccione **Agregar**.

1. Seleccione **Revisar + crear**.

   :::image type="content" source="./media/tutorial-configure-networking/create-virtual-network.png" alt-text="Captura de pantalla que muestra la configuración de la nueva red virtual." border="true":::

1. Compruebe la información y seleccione **Crear**. Una vez completada la implementación, verá la red virtual en el grupo de recursos.



### <a name="create-a-virtual-network-gateway"></a>Creación de una puerta de enlace de red virtual

Una vez creada una red virtual, creará una puerta de enlace de red virtual.

1. En el grupo de recursos, seleccione **+ Agregar** para agregar un recurso nuevo.

1. En el cuadro de texto **Buscar en el Marketplace**, escriba **Puerta de enlace de red virtual**. Busque el recurso Virtual Network y selecciónelo.

1. En la página **Puerta de enlace de red virtual**, seleccione **Crear**.

1. En la pestaña Aspectos básicos de la página **Crear puerta de enlace de red virtual**, proporcione valores para los campos y seleccione **Revisar y crear**. 

   | Campo | Value |
   | --- | --- |
   | **Suscripción** | Valor rellenado previamente con la suscripción a la que pertenece el grupo de recursos. |
   | **Grupos de recursos** | Valor rellenado previamente para el grupo de recursos actual. El valor debe ser el grupo de recursos que creó en una prueba anterior. |
   | **Nombre** | Escriba un nombre único para la puerta de enlace de red virtual. |
   | **Región** | Seleccione la ubicación geográfica de la puerta de enlace de red virtual. |
   | **Tipo de puerta de enlace** | seleccione **ExpressRoute**. |
   | **SKU** | Deje el valor predeterminado: **standard**. |
   | **Red virtual** | Seleccione la red virtual que ha creado antes. Si no ve la red virtual, asegúrese de que la región de la puerta de enlace se corresponde con la región de la red virtual. |
   | **Intervalo de direcciones de subred de puerta de enlace** | Este valor se rellena cuando se selecciona la red virtual. No cambie el valor predeterminado. |
   | **Dirección IP pública** | Seleccione **Crear nuevo**. |

   :::image type="content" source="./media/tutorial-configure-networking/create-virtual-network-gateway.png" alt-text="Captura de pantalla que muestra los detalles de la puerta de enlace de red virtual." border="true":::

1. Compruebe que los detalles son correctos y seleccione **Crear** para iniciar la implementación de la puerta de enlace de red virtual.

1. Una vez finalizada la implementación, vaya a la siguiente sección para conectar ExpressRoute a la puerta de enlace de red virtual que contiene la nube privada de Azure VMware Solution.

### <a name="connect-expressroute-to-the-virtual-network-gateway"></a>Conexión de ExpressRoute a la puerta de enlace de red virtual

Ahora que ha implementado una puerta de enlace de red virtual, deberá agregar una conexión entre ella y la nube privada de Azure VMware Solution.

[!INCLUDE [connect-expressroute-to-vnet](includes/connect-expressroute-vnet.md)]


## <a name="next-steps"></a>Pasos siguientes

En este tutorial, ha aprendido a:

> [!div class="checklist"]
> * Creación de una red virtual mediante la característica de conexión a red virtual
> * Creación manual de una red virtual
> * Creación de una puerta de enlace de red virtual
> * Conectar el circuito ExpressRoute a la puerta de enlace


Continúe con el siguiente tutorial para aprender a crear los segmentos de red NSX-T que se usan para las VM en vCenter.

> [!div class="nextstepaction"]
> [Creación de segmentos de red de NSX-T](./tutorial-nsx-t-network-segment.md)
