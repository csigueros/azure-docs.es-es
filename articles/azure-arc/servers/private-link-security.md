---
title: Uso de Azure Private Link para conectar redes a Azure Arc de manera segura
description: Aprenda a usar Azure Private Link para conectar redes a Azure Arc de manera segura.
ms.topic: conceptual
ms.date: 07/20/2021
ms.openlocfilehash: 1bd683631e9a7edb321abb56ed423cac11b42557
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/22/2021
ms.locfileid: "114468007"
---
# <a name="use-azure-private-link-to-securely-connect-networks-to-azure-arc"></a>Uso de Azure Private Link para conectar redes a Azure Arc de manera segura

[Azure Private Link](../../private-link/private-link-overview.md) le permite vincular de forma segura los servicios PaaS de Azure a la red virtual mediante puntos de conexión privados. Para muchos servicios, solo tiene que configurar un punto de conexión por recurso. Esto significa que puede conectar servidores locales o de varias nubes con Azure Arc y enviar todo el tráfico a través de Azure [ExpressRoute](../../expressroute/expressroute-introduction.md) o una [conexión VPN](../../vpn-gateway/vpn-gateway-about-vpngateways.md) de sitio a sitio en lugar de utilizar redes públicas.

A partir de los servidores habilitados para Azure Arc, puede utilizar un modelo del Ámbito de Private Link para permitir que varios servidores o máquinas se comuniquen con sus recursos de Azure Arc a través de un punto de conexión privado único.

En este artículo se explica cuándo utilizar una instancia del Ámbito de Private Link de Azure Arc (versión preliminar) y cómo configurarlo.

> [!NOTE]
> El Ámbito de Private Link de Azure Arc (versión preliminar) está disponible en todas las regiones de nube comercial, pero actualmente no lo está en la nube US Government.

## <a name="advantages"></a>Ventajas

Con Private Link puede:

- Conectarse de manera privada con Azure Arc sin necesidad de abrir ningún acceso a la red pública.
- Asegurarse de que solo se acceda a los datos de la máquina o el servidor habilitado para Arc a través de redes privadas autorizadas. Esto también incluye los datos de las [extensiones de máquina virtual](manage-vm-extensions.md) instaladas en la máquina o el servidor que proporcionan compatibilidad con la supervisión y administración posteriores a la implementación.
- Evitar la filtración de datos desde las redes privadas mediante la definición de servidores específicos habilitados para Azure Arc y otros recursos de servicios de Azure, como Azure Monitor, que se conectan a través del punto de conexión privado.
- Conectar de forma segura la red local privada a Azure Arc mediante ExpressRoute y Private Link.
- Mantener todo el tráfico dentro de la red troncal de Microsoft Azure.

Para más información, consulte [Ventajas principales de Private Link](../../private-link/private-link-overview.md#key-benefits).

## <a name="how-it-works"></a>Funcionamiento

El Ámbito de Private Link de Azure Arc (versión preliminar) conecta los puntos de conexión privados (y la red virtual que los contiene) a un recurso de Azure; en este caso, servidores habilitados para Azure Arc. Al habilitar cualquiera de las extensiones de máquina virtual compatibles con servidores habilitados para Arc, como Update Management en Azure Automation o Azure Monitor, esos recursos conectan otros recursos de Azure. Como:

- Área de trabajo de Log Analytics, necesaria para Update Management en Azure Automation, Seguimiento de cambios e inventario de Azure Automation, VM Insights de Azure Monitor y la recopilación de registros de Azure Monitor con el agente de Log Analytics.
- Cuenta de Azure Automation, necesaria para Update Management y Seguimiento de cambios e inventario.
- Azure Key Vault
- Azure Blob Storage, necesario para la extensión de script personalizado.

:::image type="content" source="./media/private-link-security/private-link-topology.png" alt-text="Diagrama de la topología básica de los recursos" border="true":::

La conectividad con los demás recursos de Azure desde un servidor habilitado para Arc anteriormente mencionado requiere la configuración de Private Link para cada servicio. Si necesita más información, consulte lo siguiente para configurar Private Link para [Azure Automation](../../automation/how-to/private-link-security.md), [Azure Monitor](../../azure-monitor/logs/private-link-security.md), [Azure Key Vault](../../key-vault/general/private-link-service.md) o [Azure Blob Storage](../../private-link/tutorial-private-endpoint-storage-portal.md).

> [!IMPORTANT]
> Azure Private Link ya está disponible con carácter general. Tanto el punto de conexión privado como el servicio Private Link (servicio detrás del equilibrador de carga estándar) están disponibles con carácter general. La incorporación de los diferentes Azure PaaS a Azure Private Link se realizará en diferentes programaciones. Consulte [Disponibilidad de Private Link](../../private-link/availability.md) para ver un estado preciso de PaaS de Azure en Private Link. Para obtener información sobre las limitaciones conocidas, consulte [Punto de conexión privado](../../private-link/private-endpoint-overview.md#limitations) y [Servicio Private Link](../../private-link/private-link-service-overview.md#limitations).

* El punto de conexión privado de la red virtual le permite alcanzar los puntos de conexión de los servidores habilitados para Azure Arc a través de direcciones IP privadas desde el grupo de la red, en lugar de usar las direcciones IP públicas de estos puntos de conexión. Esto le permite seguir usando los recursos de los servidores habilitados para Azure Arc sin necesidad de abrir la red virtual a un tráfico saliente no solicitado.

* El tráfico desde el punto de conexión privado a los recursos pasará por la red troncal de Microsoft Azure y no se enrutará hacia las redes públicas.

* Puede configurar cada uno de los componentes a fin de permitir o denegar la ingesta y las consultas desde redes públicas. Esto proporciona una protección en el nivel de recurso para que pueda controlar el tráfico a recursos específicos.

## <a name="restrictions-and-limitations"></a>Restricciones y limitaciones

El objeto de Ámbito de Private Link de servidores habilitados para Arc tiene una serie de límites que se deben tener en cuenta al planear la configuración de Private Link.

- Como máximo, puede asociar un Ámbito de Private Link de Azure Arc con una red virtual.

- Un recurso de máquina o servidor habilitado para Azure Arc solo se puede conectar a un Ámbito de Private Link de servidores habilitados para Azure Arc.

- Todas las máquinas locales deben usar el mismo punto de conexión privado. Para ello, resuelva la información correcta del punto de conexión privado (nombre del registro FQDN y dirección IP privada) con el mismo reenviador DNS. Para más información, consulte [Configuración de DNS para puntos de conexión privados de Azure](../../private-link/private-endpoint-dns.md).

- La máquina o el servidor habilitado para Azure Arc, el Ámbito de Private Link de Azure Arc y la red virtual deben estar en la misma región de Azure.

- Durante la versión preliminar, se debe permitir el tráfico a las etiquetas de servicio de Azure Resource Manager y de Azure Active Directory a través del firewall de red local. 

- Otros servicios de Azure que utilizará (por ejemplo, Azure Monitor) requieren sus propios puntos de conexión privados en la red virtual.

- El Ámbito de Private Link de los servidores habilitados para Azure Arc no está disponible actualmente en las regiones de Azure US Government.

## <a name="planning-your-private-link-setup"></a>Planeamiento de la configuración de Private Link

Si desea conectar el servidor a Azure Arc a través de un vínculo privado, debe configurar la red para realizar lo siguiente:

1. Establecer una conexión entre la red local y una red virtual de Azure mediante una [VPN de sitio a sitio](../../vpn-gateway/tutorial-site-to-site-portal.md) o un [circuito ExpressRoute](../../expressroute/expressroute-howto-linkvnet-arm.md).

1. Implementar una instancia de Ámbito de Private Link de Azure Arc (versión preliminar) que controle qué máquinas o servidores se pueden comunicar con Azure Arc a través de puntos de conexión privados y asociarla con la red virtual de Azure mediante un punto de conexión privado.

1. Actualizar la configuración de DNS en la red local para resolver las direcciones de punto de conexión privado.

1. Configurar el firewall local para permitir el acceso a Azure Active Directory y Azure Resource Manager. Este es un paso temporal y no será necesario cuando los puntos de conexión privados de estos servicios entren en la versión preliminar.

1. Asociar las máquinas o los servidores registrados con servidores habilitados para Azure Arc con el ámbito de Private Link.

1. También puede implementar puntos de conexión privados para otros servicios de Azure que administren la máquina o el servidor, como los siguientes:

    - Azure Monitor
    - Azure Automation
    - Azure Blob Storage
    - Azure Key Vault

En este artículo se da por hecho que ya configuró un circuito ExpressRoute o una conexión VPN de sitio a sitio.

## <a name="network-configuration"></a>Network configuration (Configuración de red)

Los servidores habilitados para Azure Arc se integran con varios servicios de Azure a fin de incorporar la administración y gobernanza de la nube a sus máquinas o servidores híbridos. La mayoría de estos servicios ya ofrecen puntos de conexión privados, pero es necesario configurar reglas de enrutamiento y firewall a fin de permitir el acceso a Azure Active Directory y Azure Resource Manager a través de Internet hasta que estos servicios ofrezcan puntos de conexión privados.

Hay dos formas de hacerlo:

- Si la red está configurada para enrutar todo el tráfico enlazado a Internet a través de la VPN de Azure o el circuito ExpressRoute, puede configurar el grupo de seguridad de red (NSG) asociado a la subred en Azure para permitir el acceso TCP 443 (HTTPS) de salida a Azure AD y Azure mediante [etiquetas de servicio](../../virtual-network/service-tags-overview.md). Las reglas de NSG deben tener un aspecto similar al siguiente:

    |Configuración |Regla de Azure AD | Regla de Azure |
    |--------|--------------|-----------------------------|
    |Origen |Virtual network |Virtual network |
    |Source port ranges |* |* |
    |Destination |Etiqueta de servicio |Etiqueta de servicio |
    |Etiqueta de servicio de destino |AzureActiveDirectory |AzureResourceManager |
    |Intervalos de puertos de destino |443 |443 |
    |Protocolo |Tcp |Tcp |
    |Acción |Allow |Allow |
    |Prioridad |150 (debe ser inferior a cualquier regla que bloquee el acceso a Internet) |151 (debe ser inferior a cualquier regla que bloquee el acceso a Internet) |
    |Nombre |AllowAADOutboundAccess |AllowAzOutboundAccess |

- Configure el firewall en la red local para permitir el acceso TCP 443 (HTTPS) de salida a Azure AD y Azure con los archivos de etiquetas de servicio descargables. El archivo JSON contiene todos los intervalos de direcciones IP públicas que Azure AD y Azure utilizan y se actualiza de manera mensual a fin de reflejar cualquier cambio. La etiqueta de servicio de Azure AD es `AzureActiveDirectory` y la de Azure es `AzureResourceManager`. Consulte con el administrador de red y el proveedor del firewall de red cómo configurar las reglas de firewall.

Consulte el diagrama visual que aparece en la sección [Funcionamiento](#how-it-works) para ver cómo fluye el tráfico.

## <a name="create-a-private-link-scope"></a>Creación de un Ámbito de Private Link

1. Inicie sesión en [Azure Portal](https://portal.azure.com).

1. Vaya a **Crear un recurso** en Azure Portal y busque **Ámbito de Private Link de Azure Arc**. También puede usar el vínculo siguiente para abrir la página [Ámbito de Private Link de Azure Arc](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.HybridCompute%2FprivateLinkScopes) en el portal.

    :::image type="content" source="./media/private-link-security/find-scope.png" alt-text="Búsqueda de Ámbito de Private Link" border="true":::

1. Seleccione **Crear**.

1. Seleccione una suscripción y un grupo de recursos. Durante la versión preliminar, la red virtual y los servidores habilitados para Azure Arc deben estar en la misma suscripción que el Ámbito de Private Link de Azure Arc.

1. Asígnele un nombre al Ámbito de Private Link de Azure Arc. Se recomienda usar un nombre significativo y claro.

   También puede requerir que cada máquina o servidor habilitado para Arc asociado a este Ámbito de Private Link de Azure Arc (versión preliminar) envíe datos al servicio a través del punto de conexión privado. Si selecciona **Habilitar acceso a la red pública**, las máquinas o los servidores asociados a este Ámbito de Private Link de Azure Arc (versión preliminar) pueden comunicarse con el servicio a través de redes privadas o públicas. Si cambia de opinión, puede modificar esta configuración después de crear el ámbito.

1. Seleccione **Revisar + crear**.

    :::image type="content" source="./media/private-link-security/create-private-link-scope.png" alt-text="Creación de Ámbito de Private Link" border="true":::

1. Deje que se supere la validación y luego seleccione **Crear**.

## <a name="create-a-private-endpoint"></a>Creación de un punto de conexión privado

Una vez creado el Ámbito de Private Link de Azure Arc (versión preliminar), deberá conectarlo con una o varias redes virtuales a través de un punto de conexión privado. El punto de conexión privado expone el acceso a los servicios de Azure Arc en una dirección IP privada en el espacio de direcciones de la red virtual.

1. En el recurso de ámbito, seleccione **Conexiones de punto de conexión privado** en el menú de recursos de la izquierda. Seleccione **Agregar** para iniciar el proceso de creación del punto de conexión. Aquí también puede aprobar las conexiones que se iniciaron en el centro de Private Link seleccionándolas y, después, seleccionando **Aprobar**.

    :::image type="content" source="./media/private-link-security/create-private-endpoint.png" alt-text="Creación de un punto de conexión privado" border="true":::

1. Seleccione la suscripción, el grupo de recursos y el nombre del punto de conexión, así como la región en la que debe residir. La región debe ser la misma que la de la red virtual a la que se conecta.

1. Seleccione **Siguiente: Resource** (Siguiente: Recurso).

1. En la página **Recurso**,

   a. Seleccione la **Suscripción** que contiene el recurso de Ámbito de Private Link de Azure Arc.

   b. Para **Tipo de recurso**, elija **Microsoft.HybridCompute/privateLinkScopes**.

   c. En la lista desplegable **Recurso**, elija el ámbito de Private Link que creó anteriormente.

   d. Seleccione **Siguiente: Configuración >** .

    :::image type="content" source="./media/private-link-security/create-private-endpoint-configuration.png" alt-text="Creación completa del punto de conexión privado" border="true":::

1. En la página **Configuración**,

   a. Elija el **red virtual** y la **subred** a las que desea conectar los recursos de Azure Monitor. 

   b. Elija **Sí** para **Integrar con la zona DNS privada** y permita que cree automáticamente una nueva zona DNS privada. Las zonas DNS reales pueden ser distintas de las que se muestran en la siguiente captura de pantalla.

     > [!NOTE]
     > Si elige **No** y prefiere administrar los registros de DNS manualmente, complete primero la configuración de Private Link, incluido este punto de conexión privado y la configuración del ámbito privado. A continuación, configure DNS según las instrucciones que se indican en [Configuración de DNS para puntos de conexión privados de Azure](../../private-link/private-endpoint-dns.md). Asegúrese de no crear registros vacíos como preparación para la configuración de Private Link. Los registros DNS que se crean pueden invalidar la configuración existente e influir en la conectividad con servidores habilitados para Arc.

   c.    Seleccione **Revisar + crear**.

   d.    Permita que la validación se supere.

   e.    Seleccione **Crear**.

## <a name="configure-on-premises-dns-forwarding"></a>Configuración del reenvío de DNS local

Las máquinas o servidores locales deben ser capaces de resolver los registros DNS del vínculo privado en las direcciones IP del punto de conexión privado. La configuración depende de si usa zonas DNS privadas de Azure para mantener registros DNS o si usa su propio servidor DNS local y cuántos servidores configura.

### <a name="dns-configuration-using-azure-integrated-private-dns-zones"></a>Configuración de DNS mediante zonas DNS privadas integradas en Azure

Si configura zonas DNS privadas para servidores habilitados para Azure Arc y configuración de invitado al crear el punto de conexión privado, las máquinas o servidores locales deben poder reenviar consultas de DNS a los servidores de Azure DNS integrados para resolver correctamente las direcciones de punto de conexión privado. Necesita un reenviador DNS en Azure (ya sea una máquina virtual creada específicamente o una instancia de Azure Firewall con el proxy DNS habilitado). Luego puede configurar el servidor DNS local para reenviar consultas a Azure a fin de resolver las direcciones IP del punto de conexión privado.

La documentación del punto de conexión privado proporciona instrucciones para configurar [cargas de trabajo locales mediante un reenviador DNS](../../private-link/private-endpoint-dns.md#on-premises-workloads-using-a-dns-forwarder).

### <a name="manual-dns-server-configuration"></a>Configuración manual del servidor DNS

Si optó por no usar zonas DNS privadas de Azure durante la creación del punto de conexión privado, deberá crear los registros DNS requeridos en el servidor DNS local.

1. Vaya a Azure Portal.

1. Vaya al recurso de punto de conexión privado asociado a la red virtual y el ámbito de Private Link.

1. En el panel izquierdo, seleccione **Configuración de DNS** para ver una lista de los registros DNS y las direcciones IP correspondientes que deberá configurar en el servidor DNS. Los FQDN y las direcciones IP cambiarán en función de la región seleccionada para el punto de conexión privado y las direcciones IP disponibles en la subred.

    :::image type="content" source="./media/private-link-security/dns-configuration.png" alt-text="Detalles de configuración de DNS" border="true":::

1. Siga las instrucciones del proveedor del servidor DNS para agregar las zonas DNS necesarias y los registros A para que coincidan con la tabla del portal. Asegúrese de seleccionar un servidor DNS con el ámbito adecuado para la red. Todas las máquinas o servidores que usan este servidor DNS ahora resuelven las direcciones IP del punto de conexión privado y deben estar asociados con el Ámbito de Private Link de Azure Arc (versión preliminar) o se rechazará la conexión.

### <a name="single-server-scenarios"></a>Escenarios de servidor único

Si solo tiene previsto utilizar vínculos privados para admitir algunas máquinas o servidores, es posible que no quiera actualizar la configuración DNS de toda la red. En este caso, puede agregar las direcciones IP y los nombres de host del punto de conexión privado en el archivo **Hosts** de los sistemas operativos. En función de la configuración del sistema operativo, el archivo Hosts puede ser el método principal o alternativo para resolver el nombre de host en la dirección IP.

#### <a name="windows"></a>Windows

1. En una cuenta con privilegios de administrador, abra **C:\Windows\System32\drivers\etc\hosts**.

1. Agregue las direcciones IP y los nombres de host del punto de conexión privado tal como aparecen en la tabla del paso 3 en [Configuración manual del servidor DNS](#manual-dns-server-configuration). El archivo de hosts requiere primero la dirección IP seguida de un espacio y, a continuación, el nombre de host.

1. Guarde el archivo con los cambios. Es posible que primero tenga que guardar en otro directorio y, a continuación, copiar el archivo en la ruta de acceso original.

#### <a name="linux"></a>Linux

1. En una cuenta con el privilegio **sudoers**, ejecute `sudo nano /etc/hosts` para abrir el archivo Hosts.

1. Agregue las direcciones IP y los nombres de host del punto de conexión privado tal como aparecen en la tabla del paso 3 en [Configuración manual del servidor DNS](#manual-dns-server-configuration). El archivo de hosts primero pide la dirección IP seguida de un espacio y, a continuación, el nombre de host.

1. Guarde el archivo con los cambios.

## <a name="connect-to-an-azure-arc-enabled-servers"></a>Conexión a un servidor habilitado para Azure Arc

> [!NOTE]
> La versión mínima admitida del agente de Azure Arc Connected Machine con punto de conexión privado es la versión 1.4. El script de implementación de servidores habilitados para Arc que se genera en el portal descarga la versión más reciente.

### <a name="configure-a-new-arc-enabled-server-to-use-private-link"></a>Configuración de un servidor habilitado para Arc nuevo para utilizar Private Link

Al conectar una máquina o un servidor con servidores habilitados para Azure Arc por primera vez, también puede conectarlo a un Ámbito de Private Link. Los pasos siguientes son: 

1. En el explorador, vaya a [Azure Portal](https://portal.azure.com).

1. Vaya a **Servidores: Azure Arc**.

1. En la página **Servidores: Azure Arc**, seleccione **Agregar** en la parte superior izquierda.

1. En la página **Agregar servidores con Azure Arc**, seleccione la opción **Agregar un solo servidor** o **Agregar varios servidores** en función del escenario de implementación y, luego, seleccione **Generar script**.

1. En la página **Generar script**, seleccione la suscripción y el grupo de recursos en el que desea que se administre la máquina en Azure. Seleccione la ubicación de Azure en la que se almacenarán los metadatos de la máquina. Esta ubicación puede ser la misma u otra diferente que la del grupo de recursos.

1. En la página **Requisitos previos**, revise la información y, luego, seleccione **Siguiente: Detalles del recurso**.

1. En la página **Detalles del recurso**, proporcione lo siguiente:

    1. En la lista desplegable **Grupo de recursos**, seleccione el grupo de recursos desde el que se administrará la máquina.
    1. En la lista desplegable **Región**, seleccione la región de Azure para almacenar los metadatos de la máquina o del servidor.
    1. En la lista desplegable **Sistema operativo**, seleccione el sistema operativo en el que se ejecutará el script.
    1. En **Conectividad de red**, seleccione **Punto de conexión privado (versión preliminar)** y seleccione el Ámbito de Private Link de Azure Arc creado en la parte 1 en la lista desplegable.

       :::image type="content" source="./media/private-link-security/arc-enabled-servers-create-script.png" alt-text="Selección de la opción de conectividad de punto de conexión privado" border="true":::

    1. Seleccione **Siguiente: Etiquetas**.

1. Si seleccionó **Agregar varios servidores**, en la página **Autenticación**, seleccione la entidad de servicio creada para los servidores habilitados para Arc en la lista desplegable. Si no ha creado una entidad de servicio para servidores habilitados para Arc, revise primero [cómo crear una entidad de servicio](onboard-service-principal.md#create-a-service-principal-for-onboarding-at-scale) para familiarizarse con los permisos necesarios y los pasos para crear uno. Seleccione **Siguiente: Etiquetas** para continuar.

1. En la página **Etiquetas**, revise las **etiquetas de ubicación física** predeterminadas sugeridas y escriba un valor, o bien especifique una o varias **etiquetas personalizadas** para que cumplan sus estándares.

1. Seleccione **Siguiente: Descargar y ejecutar el script**.

1. En la pestaña **Descargar y ejecutar el script**, revise la información del resumen y seleccione **Descargar**. Si todavía necesita realizar cambios, seleccione **Anterior**.

Después de descargar el script, debe ejecutarlo en la máquina o el servidor mediante una cuenta con privilegios (administrador o raíz). En función de la configuración de la red, es posible que tenga que descargar el agente desde un equipo con acceso a Internet y transferirlo a la máquina o al servidor y, a continuación, modificar el script con la ruta de acceso al agente. 

El agente de Windows se puede descargar en [https://aka.ms/AzureConnectedMachineAgent](https://aka.ms/AzureConnectedMachineAgent) y el de Linux, en [https://packages.microsoft.com](https://packages.microsoft.com). Busque la versión más reciente de **azcmagent** en el directorio de distribución del sistema operativo e instale con el administrador de paquetes local. 

El script devolverá mensajes de estado que le permitirán saber si la incorporación se realizó correctamente una vez que se complete.

> [!NOTE]
> Si va a implementar el agente de Connected Machine en un servidor Linux, puede haber un retraso de cinco minutos durante la comprobación de la conectividad de red seguido de un error que dice que `you do not have access to login.windows.net`, incluso si el firewall está configurado correctamente. Este comportamiento es un problema conocido y se corregirá en una versión futura del agente. Si el firewall está configurado correctamente, la incorporación debería completarse sin problemas.

### <a name="configure-an-existing-arc-enabled-server"></a>Configuración de un servidor habilitado para Arc existente

En el caso de los servidores habilitados para Arc que se configuraron antes del ámbito de Private Link, puede permitir que empiecen a usar el Ámbito de Private Link de los servidores habilitados para Arc con estos pasos.

1. En Azure Portal, vaya al recurso Ámbito de Private Link de Azure Arc.

1. En el panel de la izquierda, seleccione **Recursos de Azure Arc** y, luego, **+ Agregar**.

1. Seleccione los servidores de la lista que desea asociar con el Ámbito de Private Link y, a continuación, seleccione **Seleccionar** para guardar los cambios.

    > [!NOTE]
    > Solo se muestran los servidores habilitados para Azure Arc que estén en la misma suscripción y región que el Ámbito de Private Link.

    :::image type="content" source="./media/private-link-security/select-servers-private-link-scope.png" alt-text="Selección de recursos de Azure Arc" border="true":::

El Ámbito de Private Link puede tardar hasta 15 minutos en aceptar conexiones de los servidores asociados recientemente.

## <a name="troubleshooting"></a>Solución de problemas

1. Revise los servidores DNS locales para comprobar que están reenviando a Azure DNS o están configurados con los registros A adecuados en la zona de vínculo privado. Estos comandos de búsqueda deben devolver direcciones IP privadas en la red virtual de Azure. Si resuelven direcciones IP públicas, compruebe de nuevo la configuración DNS de la máquina o el servidor y de la red.

    nslookup gbl.his.arc.azure.com  nslookup agentserviceapi.guestconfiguration.azure.com

1. Si tiene problemas para incorporar una máquina o un servidor, confirme que ha agregado las etiquetas de servicio de Azure Active Directory y Azure Resource Manager al firewall de red local. El agente debe comunicarse con estos servicios a través de Internet hasta que los puntos de conexión privados estén disponibles para estos servicios.

## <a name="next-steps"></a>Pasos siguientes

* Para más información sobre los puntos de conexión privados, vea [¿Qué es un punto de conexión privado de Azure?](../../private-link/private-endpoint-overview.md).

* Si tiene problemas con la configuración de conectividad del punto de conexión privado de Azure, consulte [Solución de problemas de conectividad de puntos de conexión privados de Azure](../../private-link/troubleshoot-private-endpoint-connectivity.md).

* Consulte lo siguiente para configurar Private Link para [Azure Automation](../../automation/how-to/private-link-security.md), [Azure Monitor](../../azure-monitor/logs/private-link-security.md), [Azure Key Vault](../../key-vault/general/private-link-service.md) o [Azure Blob Storage](../../private-link/tutorial-private-endpoint-storage-portal.md).
