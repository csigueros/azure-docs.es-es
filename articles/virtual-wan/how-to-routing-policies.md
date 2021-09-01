---
title: Configuración de directivas de enrutamiento del centro de conectividad de Virtual WAN
titleSuffix: Azure Virtual WAN
description: Aprenda a configurar directivas de enrutamiento de Virtual WAN.
services: virtual-wan
author: wellee
ms.service: virtual-wan
ms.topic: how-to
ms.date: 08/01/2021
ms.author: wellee
ms.openlocfilehash: d61e6c3847d9ce64f9c3f17da1300b32a1a8e643
ms.sourcegitcommit: 7f3ed8b29e63dbe7065afa8597347887a3b866b4
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "122206602"
---
# <a name="how-to-configure-virtual-wan-hub-routing-intent-and-routing-policies"></a>Configuración de intención y directivas de enrutamiento del centro de conectividad de Virtual WAN

>[!NOTE] 
> La intención de enrutamiento del centro de conectividad se encuentra actualmente en versión preliminar pública. 
> 
> Esta versión preliminar se ofrece sin contrato de nivel de servicio y no es aconsejable usarla en las cargas de trabajo de producción. Es posible que algunas características no sean compatibles o que tengan funcionalidades limitadas. Para obtener más información, consulte [Términos de uso complementarios de las Versiones preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
> 
> Para obtener acceso a la versión preliminar, póngase en contacto con previewinterhub@microsoft.com con el identificador de Virtual WAN, el identificador de suscripción y la región de Azure en la que desea configurar la intención de enrutamiento. Espere una respuesta en un plazo de 48 horas laborables (de lunes a viernes) con la confirmación de la habilitación de la característica.

## <a name="background"></a>Información previa 

Los clientes que usan Azure Firewall Manager para configurar directivas para el tráfico público y privado ahora pueden configurar sus redes de una manera mucho más sencilla mediante la intención y las directivas de enrutamiento.

La intención y las directivas de enrutamiento permiten especificar cómo el centro de conectividad de Virtual WAN reenvía el tráfico vinculado a Internet y el privado (punto a sitio, sitio a sitio, ExpressRoute, aplicaciones virtuales de red dentro del centro de conectividad de Virtual WAN y Virtual Network). Hay dos tipos de directivas de enrutamiento: directivas de enrutamiento de tráfico de Internet y de tráfico privado. Cada centro de conectividad de Virtual WAN puede tener como máximo una directiva de enrutamiento de tráfico de Internet y una de tráfico privado, cada una con un recurso de próximo salto. 

Aunque el tráfico privado incluye prefijos de dirección de ramas y de Virtual Network, las directivas de enrutamiento los consideran como una entidad dentro de los conceptos de intención de enrutamiento.

>[!NOTE]
> En la versión preliminar pública de las directivas de enrutamiento del centro de conectividad de Virtual WAN, el tráfico entre centros de conectividad solo se inspecciona mediante Azure Firewall si estos centros se encuentran en la misma región. 


* **Directiva de enrutamiento de tráfico de Internet**: cuando se configura una directiva de enrutamiento de tráfico de Internet en un centro de conectividad de Virtual WAN, todas las conexiones de las ramas (VPN de usuario, VPN de punto a sitio, VPN de sitio a sitio y ExpressRoute) y de Virtual Network a ese centro de conectividad de Virtual WAN reenviarán el tráfico vinculado a Internet al recurso de Azure Firewall o al proveedor de seguridad de terceros especificado como parte de la directiva de enrutamiento.
 

* **Directiva de enrutamiento de tráfico privado**: cuando se configura una directiva de enrutamiento de tráfico privado en un centro de conectividad de Virtual WAN, **todo** el tráfico de las ramas y de Virtual Network dentro y fuera del centro de conectividad de Virtual WAN, incluido el tráfico entre centros, se reenviará al recurso de próximo salto de Azure Firewall que se especificó en la directiva de enrutamiento del tráfico privado. 

    En otras palabras, cuando se configura una directiva de enrutamiento de tráfico privado en el centro de conectividad de Virtual WAN, todo el tráfico de rama a rama, de rama a red virtual, de red virtual a rama y entre centros de conectividad se enviará a través de Azure Firewall.


## <a name="key-considerations"></a>Consideraciones clave
* **No** podrá habilitar directivas de enrutamiento en las implementaciones con tablas de rutas personalizadas existentes configuradas o si hay rutas estáticas configuradas en la tabla de rutas predeterminada.
* Actualmente, las directivas de enrutamiento de tráfico privado no se admiten en centros de conectividad con conexiones de ExpressRoute cifradas (túnel VPN de sitio a sitio que se ejecuta mediante conectividad privada de ExpressRoute). 
* En la versión preliminar pública de las directivas de enrutamiento del centro de conectividad de Virtual WAN, el tráfico entre centros de conectividad solo se inspecciona mediante Azure Firewall si estos centros se encuentran en la misma región.
* Las directivas de intención de enrutamiento y de enrutamiento se deben configurar actualmente mediante el vínculo del portal personalizado que se proporciona en el paso 3 de los **requisitos previos**. Las intenciones y directivas de enrutamiento no se admiten a través de Terraform, PowerShell y la CLI. 

## <a name="prerequisites"></a>Requisitos previos
1. Creación de una instancia de Virtual WAN. Asegúrese de que crea al menos dos centros de conectividad virtual en la **misma** región. Por ejemplo, puede crear una instancia de Virtual WAN con dos centros de conectividad virtuales en la región Este de EE. UU. 
2. Convierta su centro de conectividad de Virtual WAN en un centro protegido mediante la implementación de Azure Firewall en los centros de conectividad virtuales de la región elegida. Para más información sobre cómo convertir el centro de conectividad de Virtual WAN en un centro protegido, consulte este [documento](howto-firewall.md).
3. Póngase en contacto con **previewinterhub@microsoft.com** con el **identificador de recurso de Virtual WAN** y la **región del centro de conectividad de Azure Virtual WAN** en la que desea configurar las directivas de enrutamiento. Para localizar el identificador de Virtual WAN, abra Azure Portal, vaya al recurso de Virtual WAN y seleccione Configuración > Propiedades > Id. del recurso. Por ejemplo: 
```
    /subscriptions/<subscriptionID>/resourceGroups/<resourceGroupName>/providers/Microsoft.Network/virtualWans/<virtualWANname>
```
4.  Espere una respuesta en un plazo de 24 a 48 horas con la confirmación de la habilitación de la característica. 
5. Asegúrese de que los centros de conectividad virtuales **no** tienen tablas de rutas personalizadas ni rutas que pueda haber agregado a defaultRouteTable. **No** podrá habilitar directivas de enrutamiento en las implementaciones con tablas de rutas personalizadas existentes configuradas o si hay rutas estáticas configuradas en la tabla de rutas predeterminada. 

## <a name="configuring-routing-policies"></a>Configuración de directivas de enrutamiento
1. Mediante el vínculo del portal personalizado que se proporciona en el correo electrónico de confirmación del paso 3 de la sección **Requisitos previos**, vaya al centro de conectividad de Virtual WAN en el que desea configurar las directivas de enrutamiento.
1. En Seguridad, seleccione **Configuración del centro virtual protegido** y, después, **Administrar la configuración de la ruta y el proveedor de seguridad para este centro virtual seguro en Azure Firewall Manager**
:::image type="content" source="./media/routing-policies/secured-hub-settings.png"alt-text="Captura de pantalla en la que se muestra cómo modificar la configuración de un centro de conectividad protegido."lightbox="./media/routing-policies/secured-hub-settings.png":::
1. Seleccione el centro de conectividad en el que desea configurar las directivas de enrutamiento en el menú.
1. En **Configuración**, seleccione **Configuración de seguridad**.
1. Si desea configurar una directiva de enrutamiento de tráfico de Internet, seleccione **Azure Firewall** o el proveedor de seguridad de Internet pertinente en el menú desplegable **Tráfico de Internet**. En caso contrario, seleccione **Ninguno**.
1. Si desea configurar una directiva de enrutamiento de tráfico privado (para el tráfico de rama y de Virtual Network) a través de Azure Firewall, seleccione **Azure Firewall** en el menú desplegable para el **tráfico privado**. En caso contrario, seleccione **Bypass Azure Firewall** (Omitir Azure Firewall).

:::image type="content" source="./media/routing-policies/configuring-intents.png"alt-text="Captura de pantalla que muestra cómo configurar directivas de enrutamiento."lightbox="./media/routing-policies/configuring-intents.png":::

7. Si desea configurar una directiva de enrutamiento de tráfico privado y tiene ramas o redes virtuales que anuncian prefijos RFC1918 que no son de IANA, seleccione **Private Traffic Prefixes** (Prefijos de tráfico privado) y especifique los intervalos de prefijos RFC1918 que no son de IANA en el cuadro de texto que aparece. Seleccione **Listo**. 

:::image type="content" source="./media/routing-policies/private-prefixes.png"alt-text="Captura de pantalla que muestra cómo editar prefijos de tráfico privado."lightbox="./media/routing-policies/private-prefixes.png":::

8. Seleccione **Inter-hub** (Entre centros de conectividad) para **habilitarlo**. Al habilitar esta opción, se garantiza que las directivas de enrutamiento se aplicarán a la intención de enrutamiento de este centro de conectividad de Virtual WAN. 
9. Seleccione **Guardar**. Esta operación tardará unos 10 minutos en completarse. 
10. Repita los pasos 2 a 8 para los demás centros de conectividad de Virtual WAN protegidos para los que desea configurar directivas de enrutamiento. 
 
## <a name="routing-policy-configuration-examples"></a>Ejemplos de configuración de directivas de enrutamiento 

En la sección siguiente se describen dos escenarios comunes en los que los clientes aplican directivas de enrutamiento a centros de conectividad de Virtual WAN protegidos.

### <a name="all-virtual-wan-hubs-are-secured-deployed-with-azure-firewall"></a>Todos los centros de conectividad de Virtual WAN están protegidos (implementación con Azure Firewall)

En este escenario, todos los centros de conectividad de Virtual WAN se implementan con una instancia de Azure Firewall en ellos. En este escenario, puede configurar una directiva de enrutamiento de tráfico de Internet, una directiva de enrutamiento de tráfico privado o ambas en cada centro de conectividad de Virtual WAN. 

:::image type="content" source="./media/routing-policies/two-secured-hubs-diagram.png"alt-text="Captura de pantalla que muestra la arquitectura con dos centros de conectividad protegidos."lightbox="./media/routing-policies/two-secured-hubs-diagram.png":::

Tenga en cuenta la siguiente configuración en la que Centro de conectividad 1 y Centro de conectividad 2 tienen directivas de enrutamiento para el tráfico privado y de Internet. 

**Configuración del Centro de conectividad 1:**
* Directiva de tráfico privado con una instancia de Azure Firewall en el Centro de conectividad 1 de próximo salto
* Directiva de tráfico de Internet con una instancia de Azure Firewall en el Centro de conectividad 1 de próximo salto 

**Configuración del Centro de conectividad 2:**
* Directiva de tráfico privado con una instancia de Azure Firewall en el Centro de conectividad 2 de próximo salto
* Directiva de tráfico de Internet con una instancia de Azure Firewall en el Centro de conectividad 2 de próximo salto 

Estos son los flujos de tráfico que se derivan de esta configuración. 

> [!NOTE]
> El tráfico de Internet debe salir a través de la instancia **local** de Azure Firewall ya que la ruta predeterminada (0.0.0.0/0) **no** se propaga entre centros de conectividad.

| From |   En |  Redes virtuales del Centro de conectividad 1 | Ramas del Centro de conectividad 1 | Redes virtuales del Centro de conectividad 2 | Ramas del Centro de conectividad 2| Internet|
| -------------- | -------- | ---------- | ---| ---| ---| ---|
| Redes virtuales del Centro de conectividad 1     | &#8594;| Hub 1 AzFW |   Hub 1 AzFW    | Hub 1 y 2 AzFW | Hub 1 y 2 AzFW | Hub 1 AzFW |
| Ramas del Centro de conectividad 1   | &#8594;|  Hub 1 AzFW  |   Hub 1 AzFW    | Hub 1 y 2 AzFW | Hub 1 y 2 AzFW | Hub 1 AzFW|
| Redes virtuales del Centro de conectividad 2     | &#8594;| Hub 1 y 2 AzFW |   Hub 1 y 2 AzFW    | Hub 2 AzFW  | Hub 2 AzFW | Hub 2 AzFW|
| Ramas del Centro de conectividad 2   | &#8594;|   Hub 1 y 2 AzFW  |    Hub 1 y 2 AzFW    | Hub 2 AzFW |  Hub 2 AzFW | Hub 2 AzFW|


### <a name="mixture-of-secured-and-regular-virtual-wan-hubs"></a>Combinación de centros de conectividad de Virtual WAN protegidos y normales 

En este escenario, no todos los centros de conectividad de Virtual WAN se implementan con una instancia de Azure Firewall en ellos. En este escenario, puede configurar una directiva de enrutamiento de tráfico de Internet o una directiva de enrutamiento de tráfico privado en los centros de conectividad de Virtual WAN protegidos. 

Tenga en cuenta la siguiente configuración en la que Centro de conectividad 1 (normal) y Centro de conectividad 2 (protegido) se implementan en una instancia de Virtual WAN. El Centro de conectividad 2 tiene directivas de enrutamiento de tráfico privado e Internet. 

**Configuración del Centro de conectividad 1:**
* N/D (no se pueden configurar directivas de enrutamiento si el centro de conectividad no está implementado con Azure Firewall)

**Configuración del Centro de conectividad 2:**
* Directiva de tráfico privado con una instancia de Azure Firewall en el Centro de conectividad 2 de próximo salto
* Directiva de tráfico de Internet con una instancia de Azure Firewall en el Centro de conectividad 2 de próximo salto 


:::image type="content" source="./media/routing-policies/one-secured-one-normal-diagram.png"alt-text="Captura de pantalla que muestra la arquitectura con un centro de conectividad protegido y uno normal."lightbox="./media/routing-policies/one-secured-one-normal-diagram.png":::


 Estos son los flujos de tráfico que se derivan de esta configuración. Las ramas y redes virtuales conectadas al Centro de conectividad 1 **no pueden** acceder a Internet a través de la instancia de Azure Firewall del centro de conectividad porque la ruta predeterminada (0.0.0.0/0) **no** se propaga entre centros de conectividad.

| From |   En |  Redes virtuales del Centro de conectividad 1 | Ramas del Centro de conectividad 1 | Redes virtuales del Centro de conectividad 2 | Ramas del Centro de conectividad 2| Internet |
| -------------- | -------- | ---------- | ---| ---| ---| --- |
| Redes virtuales del Centro de conectividad 1     | &#8594;| Directo |   Directo   | Hub 2 AzFW | Hub 2 AzFW | - |
| Ramas del Centro de conectividad 1   | &#8594;|  Directo |   Directo    | Hub 2 AzFW | Hub 2 AzFW | - |
| Redes virtuales del Centro de conectividad 2     | &#8594;| Hub 2 AzFW |   Hub 2 AzFW    | Hub 2 AzFW  | Hub 2 AzFW | Hub 2 AzFW|
| Ramas del Centro de conectividad 2   | &#8594;|   Hub 2 AzFW  |    Hub 2 AzFW    | Hub 2 AzFW |  Hub 2 AzFW | Hub 2 AzFW|


## <a name="troubleshooting"></a>Solución de problemas

En la sección siguiente se describen los problemas comunes que se encuentran al configurar directivas de enrutamiento en el centro de conectividad de Virtual WAN.  Lea las secciones siguientes y, si el problema sigue sin resolverse, póngase en contacto con previewinterhub@microsoft.com para obtener soporte técnico. Espere una respuesta en un plazo de 48 horas laborables (de lunes a viernes). 

### <a name="troubleshooting-configuration-issues"></a>Solución de problemas de configuración
*  Asegúrese de que ha recibido la confirmación de previewinterhub@microsoft.com de que se ha concedido acceso a la versión preliminar pública confirmada para su suscripción y región elegida. **No** podrá configurar directivas de enrutamiento si no se le concede acceso a la versión preliminar.
* Después de habilitar la característica de directiva de enrutamiento en la implementación, asegúrese de que usa **únicamente** el vínculo del portal personalizado que se proporciona como parte del correo electrónico de confirmación. No use PowerShell, la CLI ni las llamadas API REST para administrar las implementaciones de Virtual WAN.  Esto incluye la creación de nuevas conexiones de ramas (VPN de sitio a sitio, VPN de punto a sitio o ExpressRoute). 

>[!NOTE]
> Si usa Terraform, las directivas de enrutamiento no se admiten actualmente.

*  Asegúrese de que los centros de conectividad virtuales no tengan tablas de rutas personalizadas ni rutas estáticas en defaultRouteTable. **No** podrá seleccionar **Enable interhub** (Habilitar entre centros de conectividad) en Firewall Manager en el centro de conectividad de Virtual WAN si hay tablas de rutas personalizadas configuradas o si hay rutas estáticas en defaultRouteTable. 

### <a name="troubleshooting-data-path"></a>Solución de problemas de ruta de acceso de datos 

* Actualmente, el uso de Azure Firewall para inspeccionar el tráfico entre centros de conectividad solo está disponible para centros de Virtual WAN que estén implementados en la **misma** región de Azure. 
* Actualmente, las directivas de enrutamiento de tráfico privado no se admiten en centros de conectividad con conexiones de ExpressRoute cifradas (túnel VPN de sitio a sitio que se ejecuta mediante conectividad privada de ExpressRoute). 
* Para comprobar que las directivas de enrutamiento se han aplicado correctamente, compruebe las rutas vigentes de DefaultRouteTable. Si las directivas de enrutamiento privado están configuradas, debería ver las rutas en DefaultRouteTable para los prefijos de tráfico privado con la instancia de Azure Firewall de próximo salto. Si las directivas de enrutamiento de tráfico de Internet están configuradas, debería ver una ruta predeterminada (0.0.0.0/0) en DefaultRouteTable con la instancia de Azure Firewall de próximo salto.

### <a name="troubleshooting-azure-firewall"></a>Solución de problemas de Azure Firewall

* Si usa prefijos [RFC1918 que no son de IANA](https://datatracker.ietf.org/doc/html/rfc1918) en las ramas o redes virtuales, asegúrese de que ha especificado esos prefijos en el cuadro de texto "Prefijos privados" de Firewall Manager.
* Si ha especificado direcciones que no son RFC1918 como parte del cuadro de texto **Prefijos de tráfico privado** de Firewall Manager, es posible que tenga que configurar directivas SNAT en el firewall para deshabilitar SNAT para el tráfico privado que no sea RFC1918. Para más información, consulte el siguiente [documento](../firewall/snat-private-range.md). 
* Considere la posibilidad de configurar y ver los registros de Azure Firewall para solucionar problemas y analizar el tráfico de red. Para más información sobre cómo configurar la supervisión de Azure Firewall, consulte el siguiente [documento](../firewall/firewall-diagnostics.md). Puede encontrar información general sobre los distintos tipos de registros de Firewall [aquí](../firewall/logs-and-metrics.md).
* Para más información acerca de Azure Firewall, consulte la [documentación de Azure Firewall](../firewall/overview.md).



## <a name="frequently-asked-questions"></a>Preguntas más frecuentes

### <a name="why-cant-i-edit-the-defaultroutetable-from-the-custom-portal-link-provided-by-previewinterhubmicrosoftcom"></a>¿Por qué no puedo editar defaultRouteTable desde el vínculo del portal personalizado que me proporcionó previewinterhub@microsoft.com?

Como parte de la versión preliminar pública de las directivas de enrutamiento, Firewall Manager administra todo el proceso de enrutamiento del centro de conectividad virtual de Virtual WAN. Además, la versión preliminar administrada de las directivas de enrutamiento **no** se admite junto con el enrutamiento personalizado. El enrutamiento personalizado con directivas de enrutamiento se admitirá próximamente. 

Sin embargo, todavía puede ver las rutas vigentes de DefaultRouteTable navegando a la pestaña **Rutas eficaces**.

### <a name="can-i-configure-a-routing-policy-for-private-traffic-and-also-send-internet-traffic-00000-via-a-network-virtual-appliance-in-a-spoke-virtual-network"></a>¿Puedo configurar una directiva de enrutamiento para tráfico privado y también para enviar tráfico de Internet (0.0.0.0/0) a través de una aplicación virtual de red en una red virtual radial?

Este escenario no se admite en la versión preliminar pública confirmada. No obstante, puede ponerse en contacto con previewinterhub@microsoft.com para expresar su interés en implementar este escenario. 

### <a name="does-the-default-route-00000-propagate-across-hubs"></a>¿Se propaga la ruta predeterminada (0.0.0.0/0) entre centros de conectividad?

No. Actualmente, las ramas y las redes virtuales salen a Internet mediante una instancia de Azure Firewall implementada dentro del centro de conectividad de Virtual WAN al que están conectadas las ramas y redes virtuales. No se puede configurar una conexión para acceder a Internet a través del firewall en un centro de conectividad remoto.

### <a name="why-do-i-see-rfc1918-prefixes-advertised-to-my-on-premises-devices"></a>¿Por qué veo prefijos RFC1918 anunciados en mis dispositivos locales?

Cuando se configuran directivas de enrutamiento de tráfico privado, las puertas de enlace de Virtual WAN anunciarán automáticamente rutas estáticas que se encuentran en la tabla de rutas predeterminada (prefijos RFC1918: 10.0.0.0/8,172.16.0.0/12,192.168.0.0/16) además de los prefijos explícitos de ramas y Virtual Network.

## <a name="next-steps"></a>Pasos siguientes

Para obtener más información sobre el enrutamiento de centros virtuales, vea [Acerca del enrutamiento de centros virtuales](about-virtual-hub-routing.md).
Para más información sobre Virtual WAN, consulte las [preguntas más frecuentes](virtual-wan-faq.md).
