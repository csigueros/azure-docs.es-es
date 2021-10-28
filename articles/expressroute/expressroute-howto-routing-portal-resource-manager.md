---
title: 'Tutorial: Configuración del emparejamiento de un circuito ExpressRoute: Azure Portal'
description: En este tutorial se muestra cómo crear y aprovisionar emparejamientos de instancias privadas de ExpressRoute y Microsoft mediante Azure Portal.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: tutorial
ms.date: 01/11/2021
ms.author: duau
ms.openlocfilehash: cc7eba43ad9706fd76526f99a9d041053b735179
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/22/2021
ms.locfileid: "130246801"
---
# <a name="tutorial-create-and-modify-peering-for-an-expressroute-circuit-using-the-azure-portal"></a>Tutorial: Creación y modificación del emparejamiento de un circuito ExpressRoute mediante Azure Portal.

En este tutorial se muestra cómo crear y administrar la configuración de enrutamiento de un circuito ExpressRoute en Azure Resource Manager mediante Azure Portal. También puede comprobar el estado de los emparejamientos de un circuito ExpressRoute, así como el modo de actualizarlos o eliminarlos y desaprovisionarlos. Si quiere usar un método diferente para trabajar con el circuito, seleccione un artículo de la lista siguiente:

> [!div class="op_single_selector"]
> * [Azure Portal](expressroute-howto-routing-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-routing-arm.md)
> * [CLI de Azure](howto-routing-cli.md)
> * [Emparejamiento público](about-public-peering.md)
> * [Vídeo: pares privados](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-private-peering-for-your-expressroute-circuit)
> * [Vídeo: emparejamiento de Microsoft](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-microsoft-peering-for-your-expressroute-circuit)
> * [PowerShell (clásico)](expressroute-howto-routing-classic.md)
> 

Puede configurar el emparejamiento privado de Azure y de Microsoft para un circuito ExpressRoute (el emparejamiento público de Azure está en desuso para los nuevos circuitos). El emparejamiento se puede configurar en el orden que elija. Pero tiene que asegurarse de que completa cada configuración entre pares de una en una. Para más información sobre los emparejamientos y dominios de enrutamiento, vea [Dominios de enrutamiento de ExpressRoute](expressroute-circuit-peerings.md). Para saber más sobre el emparejamiento público, vea [Emparejamiento público de ExpressRoute](about-public-peering.md).

En este tutorial, obtendrá información sobre cómo:
> [!div class="checklist"]
> - Configurar, actualizar y eliminar el emparejamiento de Microsoft para un circuito
> - Configurar, actualizar y eliminar el emparejamiento privado de Azure para un circuito

## <a name="prerequisites"></a>Requisitos previos

* Asegúrese de revisar las páginas siguientes antes de comenzar la configuración:
    * [Requisitos previos](expressroute-prerequisites.md) 
    * [Requisitos de enrutamiento](expressroute-routing.md)
    * [Flujos de trabajo](expressroute-workflows.md)
* Tiene que tener un circuito ExpressRoute activo. Antes de proceder, siga las instrucciones para [crear un circuito ExpressRoute](expressroute-howto-circuit-portal-resource-manager.md) y para que el proveedor de conectividad habilite el circuito. Para configurar los emparejamientos, el circuito ExpressRoute debe estar en estado habilitado y aprovisionado. 
* Si tiene previsto usar una clave compartida o un hash MD5, asegúrese de usar la clave en ambos lados del túnel. El límite es un máximo de 25 caracteres alfanuméricos. Los caracteres especiales no se admiten. 

Estas instrucciones se aplican solo a los circuitos creados con proveedores de servicios que ofrecen servicios de conectividad de capa 2. Si usa un proveedor de servicios que ofrece servicios administrados de nivel 3 (normalmente, IPVPN, como MPLS), el mismo proveedor de conectividad configura y administra el enrutamiento. 

> [!IMPORTANT]
> Actualmente no anunciamos los emparejamientos configurados por proveedores de servicios a través del Portal de administración de servicios. Se está trabajando para habilitar esta funcionalidad pronto. Antes de configurar los emparejamientos BGP, realice las comprobaciones pertinentes con su proveedor de servicios.
> 

## <a name="microsoft-peering"></a><a name="msft"></a>Emparejamiento de Microsoft

Esta sección le ayuda a crear, obtener, actualizar y eliminar la configuración de emparejamiento de Microsoft para un circuito ExpressRoute.

> [!IMPORTANT]
> Se anunciarán todos los prefijos de servicio para el emparejamiento de Microsoft de los circuitos ExpressRoute que se configuraron antes del 1 de agosto de 2017, incluso si no se definen filtros de ruta. No se anunciará ningún prefijo para el emparejamiento de Microsoft de los circuitos ExpressRoute que se configuraron el 1 de agosto de 2017 o con posterioridad, hasta que se asocie un filtro de ruta al circuito. Para más información, consulte [Configuración de un filtro de ruta para el emparejamiento de Microsoft](how-to-routefilter-powershell.md).
> 
> 

### <a name="to-create-microsoft-peering"></a>Creación del emparejamiento de Microsoft

1. Configure el circuito de ExpressRoute. Antes de continuar, compruebe el **estado del proveedor** para asegurarse de que el proveedor de conectividad ha aprovisionado el circuito por completo.

   Si el proveedor de conectividad ofrece servicios administrados de nivel 3, puede solicitar a su proveedor de conectividad que habilite para usted la configuración entre pares de Microsoft. No necesita seguir las instrucciones que aparecen en las secciones siguientes. Por otra parte, si su proveedor de conectividad no administra este enrutamiento, una vez que cree el circuito, continúe con los pasos siguientes.

   **Estado del circuito y proveedor: No aprovisionado**

    :::image type="content" source="./media/expressroute-howto-routing-portal-resource-manager/not-provisioned.png" alt-text="Captura de pantalla que muestra la página de información general del circuito de demostración de ExpressRoute con un cuadro rojo que resalta el estado del proveedor, establecido en No aprovisionado":::

   **Estado del circuito y proveedor: Aprovisionado**

    :::image type="content" source="./media/expressroute-howto-routing-portal-resource-manager/provisioned.png" alt-text="Captura de pantalla que muestra la página de información general del circuito de demostración de ExpressRoute con un cuadro rojo que resalta el estado del proveedor, establecido en Aprovisionado":::

2. Establezca la configuración del emparejamiento de Microsoft para el circuito. Asegúrese de que tiene la siguiente información antes de continuar:

   * Un par de subredes de su propiedad y registradas en un RIR o IRR. Una subred se usará para el vínculo principal, mientras que la otra se usará para el vínculo secundario. Desde cada una de estas subredes, asignará la primera dirección IP utilizable para el enrutador, ya que Microsoft usa la segunda dirección IP utilizable para su enrutador. Tiene tres opciones para este par de subredes:
       * IPv4: dos subredes /30. Deben ser prefijos IPv4 públicos válidos.
       * IPv6: dos subredes /126. Deben ser prefijos IPv6 públicos válidos.
       * Las dos: dos subredes /30 y dos subredes /126.
   * Un identificador VLAN válido para establecer esta configuración entre pares. Asegúrese de que ninguna otra configuración entre pares en el circuito usa el mismo identificador de VLAN. Para los vínculos principal y secundario, debe usar el mismo identificador de VLAN.
   * Número de sistema autónomo (AS) para la configuración entre pares. Puede usar 2 bytes o 4 bytes como números AS.
   * Prefijos anunciados: Proporcione una lista de todos los prefijos que planea anunciar en la sesión BGP. Se aceptan solo prefijos de direcciones IP públicas. Si tiene pensado enviar un conjunto de prefijos, puede enviar una lista separada por comas. Estos prefijos tienen que estar registrados a su nombre en un Registro regional de Internet (RIR) o un Registro de enrutamiento de Internet (IRR).
   * **Opcional -** ASN de cliente: Si anuncia prefijos que no están registrados en el número de AS de emparejamiento, puede especificar el número de AS en el que están registrados.
   * Nombre del enrutamiento del Registro: puede especificar el RIR o TIR en el que están registrados el número AS y los prefijos.
   * **Opcional:** un hash MD5 si elige usar uno.
1. Puede seleccionar el emparejamiento que desea configurar, como se muestra en el ejemplo siguiente. Seleccione la fila del emparejamiento de Microsoft.

   :::image type="content" source="./media/expressroute-howto-routing-portal-resource-manager/select-microsoft-peering.png" alt-text="Selección de la fila de emparejamiento de Microsoft":::

4. Configure el emparejamiento de Microsoft Seleccione **Guardar** la configuración una vez que haya especificado todos los parámetros. La siguiente imagen muestra un ejemplo de configuración:

   :::image type="content" source="./media/expressroute-howto-routing-portal-resource-manager/configuration-m-validation-needed.png" alt-text="Configuración del emparejamiento de Microsoft con validación necesaria":::

> [!IMPORTANT]
> Microsoft comprueba si los prefijos públicos anunciados y ASN del mismo nivel (o ASN de cliente) especificados se le han asignado en el registro de enrutamiento de Internet. Si obtiene los prefijos públicos de otra entidad y la asignación no se registra con el registro de enrutamiento, la validación automática no se completará y requerirá la validación manual. Si se produce un error en la validación automática, aparecerá el mensaje "Se necesita validación". 
>
> Si ve el mensaje "Se necesita validación", recopile los documentos que muestren los prefijos públicos asignados a la organización por la entidad que aparece como propietaria de los prefijos en el registro de enrutamiento, y envíe estos documentos para su validación manual. Para hacerlo, abra una incidencia de soporte técnico. 
>

   Si el circuito llega a un estado de validación necesaria, debe abrir una incidencia de soporte técnico para mostrar la prueba de propiedad de los prefijos a nuestro equipo de soporte técnico. Puede abrir un vale de soporte técnico directamente desde el portal, tal como se muestra en el ejemplo siguiente:

   :::image type="content" source="./media/expressroute-howto-routing-portal-resource-manager/ticket-portal-m.png" alt-text="Validación necesaria: incidencia de soporte técnico":::

5. Después de que la configuración se haya aceptado correctamente, verá algo similar a la imagen siguiente:

   :::image type="content" source="./media/expressroute-howto-routing-portal-resource-manager/microsoft-peering-validation-configured.png" alt-text="Estado de emparejamiento: Configurado":::

### <a name="to-view-microsoft-peering-details"></a><a name="getmsft"></a>Visualización de detalles del emparejamiento de Microsoft

Para ver las propiedades de un emparejamiento de Microsoft, seleccione la fila del emparejamiento.

:::image type="content" source="./media/expressroute-howto-routing-portal-resource-manager/view-peering-m.png" alt-text="Consulta de las propiedades de emparejamiento de Microsoft":::

### <a name="to-update-microsoft-peering-configuration"></a><a name="updatemsft"></a>Actualización de la configuración de emparejamiento de Microsoft

Puede seleccionar la fila del emparejamiento que desee modificar y, después, modificar las propiedades del mismo y guardar las modificaciones.

:::image type="content" source="./media/expressroute-howto-routing-portal-resource-manager/configuration-m.png" alt-text="Seleccionar fila de emparejamiento":::

## <a name="azure-private-peering"></a><a name="private"></a>Configuración entre pares privados de Azure

Esta sección le ayuda a crear, obtener, actualizar y eliminar la configuración de emparejamiento privado de Azure para un circuito ExpressRoute.

### <a name="to-create-azure-private-peering"></a>Creación de un emparejamiento privado de Azure

1. Configure el circuito de ExpressRoute. Antes de continuar, asegúrese de que el proveedor de conectividad ha aprovisionado el circuito por completo. 

   Si su proveedor de conectividad ofrece servicios administrados de nivel 3, puede solicitarle que habilite la configuración entre pares privados de Azure. No necesita seguir las instrucciones que aparecen en las secciones siguientes. Por otra parte, si su proveedor de conectividad no administra este enrutamiento, una vez que cree el circuito, continúe con los pasos siguientes.

   **Estado del circuito y proveedor: No aprovisionado**

   :::image type="content" source="./media/expressroute-howto-routing-portal-resource-manager/not-provisioned-private.png" alt-text="Captura de pantalla que muestra la página de información general del circuito de demostración de ExpressRoute con un cuadro rojo que resalta el estado del proveedor, que se establece en No aprovisionado":::

   **Estado del circuito y proveedor: Aprovisionado**

   :::image type="content" source="./media/expressroute-howto-routing-portal-resource-manager/provisioned-private-peering.png" alt-text="Captura de pantalla que muestra la página de información general del circuito de demostración de ExpressRoute con un cuadro rojo que resalta el estado del proveedor, que se establece en Aprovisionado":::

2. Establecimiento de la configuración entre pares privados de Azure para el circuito. Asegúrese de que tiene los elementos siguientes antes de continuar con los siguientes pasos:

   * Un par de subredes que no forman parte de ningún espacio de direcciones reservado para redes virtuales. Una subred se usará para el vínculo principal, mientras que la otra se usará para el vínculo secundario. Desde cada una de estas subredes, asignará la primera dirección IP utilizable para el enrutador, ya que Microsoft usa la segunda dirección IP utilizable para su enrutador. Tiene tres opciones para este par de subredes:
       * IPv4: dos subredes /30.
       * IPv6: dos subredes /126.
       * Las dos: dos subredes /30 y dos subredes /126.
   * Un identificador VLAN válido para establecer esta configuración entre pares. Asegúrese de que ninguna otra configuración entre pares en el circuito usa el mismo identificador de VLAN. Para los vínculos principal y secundario, debe usar el mismo identificador de VLAN.
   * Número de sistema autónomo (AS) para la configuración entre pares. Puede usar 2 bytes o 4 bytes como números AS. Puede usar un número de AS privado para este emparejamiento, excepto el comprendido entre 65515 y 65520, ambos inclusive.
   * Debe anunciar las rutas del enrutador perimetral local a Azure a través de BGP al configurar el emparejamiento privado.
   * **Opcional:** un hash MD5 si elige usar uno.
3. Seleccione la fila de emparejamiento privado de Azure, como se muestra en el ejemplo siguiente:

   :::image type="content" source="./media/expressroute-howto-routing-portal-resource-manager/select-private-peering.png" alt-text="Selección de la fila de emparejamiento privado":::

4. Configure el emparejamiento privado. Seleccione **Guardar** la configuración una vez que haya especificado todos los parámetros.

   :::image type="content" source="./media/expressroute-howto-routing-portal-resource-manager/private-peering-configuration.png" alt-text="Configuración del emparejamiento privado":::

5. Una vez que la configuración se haya aceptado correctamente, verá algo similar al siguiente ejemplo:

   :::image type="content" source="./media/expressroute-howto-routing-portal-resource-manager/save-private-peering.png" alt-text="Emparejamiento privado guardado":::

### <a name="to-view-azure-private-peering-details"></a><a name="getprivate"></a>Visualización de los detalles del emparejamiento privado

Para ver las propiedades del emparejamiento privado de Azure seleccione el emparejamiento.

:::image type="content" source="./media/expressroute-howto-routing-portal-resource-manager/view-peering-m.png" alt-text="Consulta de las propiedades de emparejamiento privado":::

### <a name="to-update-azure-private-peering-configuration"></a><a name="updateprivate"></a>Actualización del establecimiento de configuración del emparejamiento privado de Azure

Puede seleccionar la fila del emparejamiento y modificar las propiedades del mismo. Después de actualizar, guarde los cambios.

:::image type="content" source="./media/expressroute-howto-routing-portal-resource-manager/update-private-peering.png" alt-text="Actualizar emparejamiento privado":::

## <a name="clean-up-resources"></a>Limpieza de recursos

### <a name="to-delete-microsoft-peering"></a><a name="deletemsft"></a>Eliminación del emparejamiento de Microsoft

Puede eliminar la configuración de emparejamiento de Microsoft; para ello, haga clic con el botón derecho en el emparejamiento y seleccione **Eliminar**, como se muestra en la siguiente imagen:

:::image type="content" source="./media/expressroute-howto-routing-portal-resource-manager/delete-microsoft-peering.png" alt-text="Eliminación del emparejamiento de Microsoft":::

### <a name="to-delete-azure-private-peering"></a><a name="deleteprivate"></a>Eliminación del emparejamiento privado de Azure

Puede eliminar la configuración del emparejamiento privado; para ello, haga clic con el botón derecho en el emparejamiento y seleccione **Eliminar**, como se muestra en la siguiente imagen:

> [!WARNING]
> Debe asegurarse de que se eliminan todas las redes virtuales y las conexiones de ExpressRoute Global Reach antes de ejecutar esta operación. 
> 

:::image type="content" source="./media/expressroute-howto-routing-portal-resource-manager/delete-private-peering.png" alt-text="Eliminación del emparejamiento privado":::

## <a name="next-steps"></a>Pasos siguientes

Una vez configurado el emparejamiento privado de Azure, puede crear una puerta de enlace de ExpressRoute para vincular una red virtual al circuito. 

> [!div class="nextstepaction"]
> [Configuración de una puerta de enlace de red virtual para ExpressRoute](expressroute-howto-add-gateway-resource-manager.md)
