---
title: Configuración de direcciones IP para una interfaz de red de Azure | Microsoft Docs
description: Obtenga información sobre cómo agregar, cambiar y quitar direcciones IP privadas y públicas para una interfaz de red.
services: virtual-network
documentationcenter: na
author: asudbring
ms.service: virtual-network
ms.subservice: ip-services
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/22/2020
ms.author: allensu
ms.openlocfilehash: 4d4c826a48a569b7032d23376646bfcbe9bab669
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/01/2021
ms.locfileid: "129367867"
---
# <a name="add-change-or-remove-ip-addresses-for-an-azure-network-interface"></a>Incorporación, cambio o eliminación de direcciones IP para una interfaz de red de Azure

Obtenga información sobre cómo agregar, cambiar y quitar direcciones IP públicas y privadas para una interfaz de red. Las direcciones IP privadas asignadas a una interfaz de red permiten que una máquina virtual se comunique con otros recursos en una red virtual de Azure y en redes conectadas. Una dirección IP privada también permite la comunicación saliente a Internet mediante el uso de una dirección IP impredecible. Una [dirección IP pública](virtual-network-public-ip-address.md) asignada a una interfaz de red permite la comunicación entrante a una máquina virtual desde Internet. La dirección también permite la comunicación saliente desde la máquina virtual a Internet mediante el uso de una dirección IP impredecible. Para detalles, consulte [Comprender las conexiones salientes en Azure](../../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Si necesita crear, cambiar o eliminar una interfaz de red, lea el artículo sobre cómo [administrar una interfaz de red](../../virtual-network/virtual-network-network-interface.md). Si necesita agregar interfaces de red a una máquina virtual, o quitar interfaces de red de ella, consulte el artículo [Incorporación de interfaces de red a máquinas virtuales o eliminación de estas](../../virtual-network/virtual-network-network-interface-vm.md).

## <a name="before-you-begin"></a>Antes de empezar

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Complete las tareas siguientes antes de seguir los pasos de las secciones de este artículo:

- Si todavía no tiene una cuenta de Azure, regístrese para obtener una [cuenta de evaluación gratuita](https://azure.microsoft.com/free).
- Si usa el portal, abra https://portal.azure.com e inicie sesión con la cuenta de Azure.
- Si usa comandos de PowerShell para completar las tareas de este artículo, ejecute los comandos que se encuentran en [Azure Cloud Shell](https://shell.azure.com/powershell) o ejecute PowerShell en el equipo. Azure Cloud Shell es un shell interactivo gratuito que puede usar para ejecutar los pasos de este artículo. Tiene las herramientas comunes de Azure preinstaladas y configuradas para usarlas en la cuenta. Para realizar este tutorial, se necesita la versión 1.0.0 del módulo de Azure PowerShell u otra posterior. Ejecute `Get-Module -ListAvailable Az` para buscar la versión instalada. Si necesita actualizarla, consulte [Instalación del módulo de Azure PowerShell](/powershell/azure/install-az-ps). Si PowerShell se ejecuta localmente, también debe ejecutar `Connect-AzAccount` para crear una conexión con Azure.
- Si usa la interfaz de la línea de comandos (CLI) de Azure para completar las tareas de este artículo, ejecute los comandos que se encuentran en [Azure Cloud Shell](https://shell.azure.com/bash) o ejecute la CLI en el equipo. Para realizar este tutorial, es necesaria la versión 2.0.31 de la CLI de Azure o una versión posterior. Ejecute `az --version` para buscar la versión instalada. Si necesita instalarla o actualizarla, vea [Instalación de la CLI de Azure](/cli/azure/install-azure-cli). Si ejecuta de forma local la CLI de Azure, también debe ejecutar `az login` para crear una conexión con Azure.

La cuenta en la que inicia sesión o con la que se conecta a Azure debe tener asignado el rol de [colaborador de red](../../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) o un [rol personalizado](../../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) que tenga asignadas las acciones apropiadas que figuren en [Permisos de interfaz de red](../../virtual-network/virtual-network-network-interface.md#permissions).

## <a name="add-ip-addresses"></a>Incorporación de direcciones IP

Puede agregar a una interfaz de red tantas direcciones [privadas](#private) y [públicas](#public)[IPv4](#ipv4) como sea necesario, dentro de los límites indicados en el artículo sobre los [límites de Azure](../../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits). Puede agregar una dirección IPv6 privada a una [configuración IP secundaria](#secondary) (siempre que no haya ninguna configuración IP secundaria existente) para una interfaz de red existente. Cada interfaz de red puede tener como máximo una dirección IPv6 privada. Opcionalmente, puede agregar una dirección IPv6 pública a una configuración de interfaz de red IPv6. Consulte [IPv6](#ipv6) para detalles sobre cómo usar las direcciones IPv6.

1. En el cuadro que contiene el texto *Buscar recursos*, en la parte superior de Azure Portal, escriba *interfaces de red*. Cuando aparezcan las **interfaces de red** en los resultados de búsqueda, selecciónelas.
2. En la lista, seleccione la interfaz de red para la que quiere agregar una dirección IPv4.
3. En **CONFIGURACIÓN**, seleccione **Configuraciones IP**.
4. En **Configuraciones IP**, seleccione **+ Agregar**.
5. Especifique lo siguiente y luego seleccione **Aceptar**:

   |Configuración|¿Necesario?|Detalles|
   |---|---|---|
   |Nombre|Sí|Debe ser único para la interfaz de red|
   |Tipo|Sí|Debido a que agrega una configuración IP a una interfaz de red existente y cada interfaz de red debe tener una configuración IP [principal](#primary), la única opción es **Secundaria**.|
   |Método de asignación de direcciones IP privadas|Sí|[**Dinámica**](#dynamic): Azure asigna la siguiente dirección disponible para el intervalo de dirección de subred en el que se implementa la interfaz de red. [**Estática**](#static): asigna una dirección o usada para el intervalo de dirección de subred en el que se implementa la interfaz de red.|
   |Dirección IP pública|No|**Deshabilitado:** ningún recurso de dirección IP pública está asociado actualmente a la configuración de IP. **Habilitada:** seleccione una dirección IP pública IPv4 existente o cree una nueva. Para más información sobre cómo crear una dirección IP pública, lea el artículo [Direcciones IP públicas](virtual-network-public-ip-address.md#create-a-public-ip-address).|
6. Agregue manualmente las direcciones IP privadas secundarias al sistema operativo de la máquina virtual siguiendo las instrucciones que aparecen en el artículo [Asignación de varias direcciones IP a sistemas operativos de máquinas virtuales](virtual-network-multiple-ip-addresses-portal.md#os-config). Consulte las direcciones IP [privadas](#private) para ver consideraciones especiales antes de agregar manualmente direcciones IP a un sistema operativo de máquina virtual. No agregue ninguna dirección IP pública al sistema operativo de máquina virtual.

**Comandos**

|Herramienta|Get-Help|
|---|---|
|CLI|[az network nic ip-config create](/cli/azure/network/nic/ip-config)|
|PowerShell|[Add-AzNetworkInterfaceIpConfig](/powershell/module/az.network/add-aznetworkinterfaceipconfig)|

## <a name="change-ip-address-settings"></a>Cambio de configuración de las direcciones IP

Es posible que necesite cambiar el método de asignación de una dirección IPv4, cambie la dirección IPv4 estática o cambie la dirección IP pública asignada a una interfaz de red. Si va a cambiar la dirección IPv4 privada de una configuración IP secundaria asociada con una interfaz de red secundaria en una máquina virtual (más información sobre las [interfaces de red principales y secundarias](../../virtual-network/virtual-network-network-interface-vm.md)), detenga (desasigne) la máquina virtual antes de completar los pasos siguientes:

1. En el cuadro que contiene el texto *Buscar recursos*, en la parte superior de Azure Portal, escriba *interfaces de red*. Cuando aparezcan las **interfaces de red** en los resultados de búsqueda, selecciónelas.
2. En la lista, seleccione la interfaz de red para la que quiere ver o cambiar la configuración de dirección IP.
3. En **CONFIGURACIÓN**, seleccione **Configuraciones IP**.
4. En la lista, seleccione la configuración IP que desea modificar.
5. Cambie la configuración según la información que se proporciona en el paso 5 de [Incorporación de una configuración IP](#add-ip-addresses).
6. Seleccione **Guardar**.

>[!NOTE]
>Si la interfaz de red principal tiene varias configuraciones de IP y cambia la dirección IP privada de la configuración IP principal, debe reasignar manualmente las direcciones IP principal y secundaria a la interfaz de red en Windows (no es necesario para Linux). Para asignar manualmente las direcciones IP a una interfaz de red dentro de un sistema operativo, consulte [Asignación de varias direcciones IP a máquinas virtuales con Azure Portal](virtual-network-multiple-ip-addresses-portal.md#os-config). Para ver consideraciones especiales antes de agregar manualmente direcciones IP a un sistema operativo de máquina virtual, consulte las direcciones IP [privadas](#private). No agregue ninguna dirección IP pública al sistema operativo de máquina virtual.

**Comandos**

|Herramienta|Get-Help|
|---|---|
|CLI|[az network nic ip-config update](/cli/azure/network/nic/ip-config)|
|PowerShell|[Set-AzNetworkInterfaceIpConfig](/powershell/module/az.network/set-aznetworkinterfaceipconfig)|

## <a name="remove-ip-addresses"></a>Eliminación de direcciones IP

Puede quitar direcciones IP [privadas](#private) y [públicas](#public) de una interfaz de red, pero una interfaz de red siempre debe tener asignada al menos una dirección IPv4 privada.

1. En el cuadro que contiene el texto *Buscar recursos*, en la parte superior de Azure Portal, escriba *interfaces de red*. Cuando aparezcan las **interfaces de red** en los resultados de búsqueda, selecciónelas.
2. En la lista, seleccione la interfaz de red de la que desee quitar direcciones IP.
3. En **CONFIGURACIÓN**, seleccione **Configuraciones IP**.
4. Haga clic con el botón derecho en una configuración de IP [secundaria](#secondary) (no se puede eliminar la configuración [principal](#primary)) que desee eliminar, seleccione **Eliminar** y, luego, elija **Sí** para confirmar la eliminación. Si la configuración tenía asociado un recurso de dirección IP pública, el recurso se desasocia de la configuración de IP, pero no se elimina.

**Comandos**

|Herramienta|Get-Help|
|---|---|
|CLI|[az network nic ip-config delete](/cli/azure/network/nic/ip-config)|
|PowerShell|[Remove-AzNetworkInterfaceIpConfig](/powershell/module/az.network/remove-aznetworkinterfaceipconfig)|

## <a name="ip-configurations"></a>Configuraciones IP

Las direcciones IP [privadas](#private) y (opcionalmente) [públicas](#public) se asignan a una o más configuraciones IP asignadas a una interfaz de red. Hay dos tipos de configuraciones IP:

### <a name="primary"></a>Principal

Cada interfaz de red tiene asignada una configuración IP principal. Una configuración IP principal:

- Tiene asignada una dirección [IPv4](#private)[privada](#ipv4). No puede asignar una dirección [IPv6](#ipv6) privada a una configuración IP principal.
- También puede tener asignada una dirección IPv4 [pública](#public). No puede asignar una dirección IPv6 pública a una configuración IP principal (IPv4). 

### <a name="secondary"></a>Secundario

Además de una configuración IP principal, una interfaz de red puede tener ninguna o más configuraciones IP secundarias asignadas. Una configuración IP secundaria:

- Debe tener asignada una dirección IPv4 o IPv6 privada. Si la dirección es IPv6, la interfaz de red solo puede tener una configuración IP secundaria. Si la dirección es IPv4, la interfaz de red puede tener asignadas varias configuraciones IP secundarias. Para más información sobre cuántas direcciones IPv4 privadas y públicas se pueden asignar a una interfaz de red, consulte el artículo sobre los [límites de Azure](../../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).
- También puede tener asignada una dirección IPv6 o IPv4 pública. Asignar varias direcciones IPv4 a una interfaz de red resulta útil en escenarios como:
  - Hospedaje de varios sitios web o servicios con direcciones IP y certificados TLS/SSL diferentes en un único servidor.
  - Una máquina virtual que actúa como una aplicación virtual de red, por ejemplo, un firewall o un equilibrador de carga.
  - La capacidad de agregar cualquiera de las direcciones IPv4 privadas para cualquiera de las interfaces de red a un grupo de servidores de back-end de Azure Load Balancer. En el pasado, solo la dirección IPv4 principal de la interfaz de red principal podía agregarse a un grupo de servidores back-end. Para más información sobre cómo equilibrar la carga de varias configuraciones IPv4, consulte el artículo [Equilibrio de carga en varias configuraciones IP](../../load-balancer/load-balancer-multiple-ip.md?toc=%2fazure%2fvirtual-network%2ftoc.json). 
  - La capacidad de equilibrar carga una vez que se asigna una dirección IPv6 a una interfaz de red. Para más información sobre cómo equilibrar carga a una dirección IPv6 privada, consulte el artículo sobre el [equilibrio de carga de direcciones IPv6](../../load-balancer/load-balancer-ipv6-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="address-types"></a>Tipos de direcciones

Puede asignar los tipos siguientes de direcciones IP a una [configuración IP](#ip-configurations):

### <a name="private"></a>Privada

Las direcciones [IPv4](#ipv4) o IPv6 privadas permiten que una máquina virtual se comunique con otros recursos en una red virtual o en otras redes conectadas. 

De manera predeterminada, los servidores DHCP de Azure asignan la dirección IPv4 privada para la [configuración IP principal](#primary) de la interfaz de red a la interfaz de red de Azure dentro del sistema operativo de la máquina virtual. A menos que sea necesario, nunca debe establecer la dirección IP de una interfaz de red dentro del sistema operativo de la máquina virtual.

Hay escenarios en los cuales es necesario establecer manualmente la dirección IP de una interfaz de red dentro del sistema operativo de la máquina virtual. Por ejemplo, debe establecer manualmente las direcciones IP principales y secundarias de un sistema operativo Windows cuando se agregan varias direcciones IP a una máquina virtual de Azure. En el caso de una máquina virtual Linux, solo debe establecer manualmente las direcciones IP secundarias. Consulte [Incorporación de direcciones IP a un sistema operativo de la máquina virtual](virtual-network-multiple-ip-addresses-portal.md#os-config) para detalles. Si alguna vez debe cambiar la dirección asignada a una configuración IP, se recomienda que haga lo siguiente:

1. Asegúrese de que la máquina virtual recibe una dirección IP principal de los servidores DHCP de Azure. No establezca esta dirección en el sistema operativo si ejecuta una máquina virtual Linux.
2. Elimine la configuración de IP que se va a cambiar.
3. Cree una nueva configuración de IP con la nueva dirección que le gustaría establecer.
4. [Configure manualmente](virtual-network-multiple-ip-addresses-portal.md#os-config) las direcciones IP secundarias dentro del sistema operativo (y también la dirección IP principal dentro de Windows) para que coincidan con las que estableció en Azure. No establezca manualmente la dirección IP principal en la configuración de red del sistema operativo en Linux, o es posible que no pueda conectarse a Internet cuando se vuelva a cargar la configuración.
5. Vuelva a cargar la configuración de red en el sistema operativo invitado. Para ello, simplemente reinicie el sistema o ejecute "nmcli con down 'System eth0 && nmcli con up' 'System eth0'" en los sistemas Linux que ejecutan NetworkManager.
6. Compruebe que la configuración de red es la deseada. Pruebe la conectividad de todas las direcciones IP del sistema.

Si sigue los pasos anteriores, la dirección IP privada asignada a la interfaz de red dentro de Azure y dentro del sistema operativo de una máquina virtual no serán distintas. Para realizar un seguimiento de las máquinas virtuales dentro de la suscripción para las cuales estableció manualmente direcciones IP dentro de un sistema operativo, considere agregar una [etiqueta](../../azure-resource-manager/management/tag-resources.md) de Azure a las máquinas virtuales. Por ejemplo, puede usar "Asignación de dirección IP: estática". De este modo, puede encontrar fácilmente las máquinas virtuales dentro de la suscripción para las cuales estableció manualmente la dirección IP dentro del sistema operativo.

Además de permitir que una máquina virtual se comunique con otros recursos dentro de la misma red virtual o dentro de redes virtuales conectadas, una dirección IP privada también permite que una máquina virtual establezca una conexión saliente a Internet. Las conexiones salientes son direcciones de red de origen que Azure traduce a una dirección IP pública impredecible. Para más información acerca de la conectividad de salida a Internet de Azure, consulte el artículo de [Comprender las conexiones salientes en Azure](../../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json). No puede establecer una comunicación entrante a la dirección IP privada de una máquina virtual desde Internet. Si las conexiones salientes requieren una dirección IP pública predecible, asocie un recurso de dirección IP pública a una interfaz de red.

### <a name="public"></a>Público

Las direcciones IP públicas asignadas a través de un recurso de dirección IP pública permiten la conectividad entrante a una máquina virtual desde Internet. Las conexiones salientes a Internet usan una dirección IP predecible. Consulte [Comprender las conexiones salientes en Azure](../../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json) para detalles. Puede asignar una dirección IP pública a una configuración de IP, pero no es obligatorio. Si no asigna una dirección IP pública a una máquina virtual mediante la asociación de un recurso de direcciones IP públicas, la máquina virtual puede seguir estableciendo una comunicación saliente a Internet. En este caso, la dirección IP privada es la dirección de red de origen que Azure traduce a una dirección IP pública impredecible. Para más información sobre los recursos de direcciones IP públicas, vea [Recurso de direcciones IP públicas](virtual-network-public-ip-address.md).

Hay límites en el número de direcciones IP privadas y públicas que se pueden asignar a una interfaz de red. Para más información, lea el artículo acerca de los [límites de Azure](../../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

> [!NOTE]
> Azure traduce la dirección IP privada de una máquina virtual a una dirección IP pública. Como resultado, el sistema operativo de una máquina virtual no detecta ninguna de las direcciones IP públicas que tiene asignada, por lo que no es necesario asignar manualmente una dirección IP pública dentro del sistema operativo.

## <a name="assignment-methods"></a>Métodos de asignación

Las direcciones IP públicas y privadas se asignan con uno de los siguientes métodos de asignación:

### <a name="dynamic"></a>Dinámica

Las direcciones IPv4 e IPv6 (opcionalmente) privadas dinámicas se asignan de manera predeterminada.

- **Solo pública**: Azure asigna la dirección de un intervalo único a cada región de Azure. Para saber qué intervalos se asignan a cada región, vea [Intervalos de direcciones IP del centro de datos de Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653). La dirección puede cambiar cuando una máquina virtual está detenida (desasignada) y se vuelve a iniciar. No puede asignar una dirección IPv6 pública a una configuración IP con un método de asignación.
- **Solo privado**: Azure reserva las cuatro primeras direcciones en cada intervalo de direcciones de subred y no las asigna. Azure asigna la siguiente dirección disponible a un recurso del intervalo de direcciones de subred. Por ejemplo, si el intervalo de direcciones de la subred es 10.0.0.0/16 y ya están asignadas las direcciones 10.0.0.4-10.0.0.14 (.0 a .3 están reservados), Azure asigna 10.0.0.15 al recurso. Este es el método de asignación predeterminado. Una vez asignadas, las direcciones IP dinámicas solo se liberan si se elimina una interfaz de red y se asigna a otra subred diferente de la misma red virtual, o bien el método de asignación se cambia a estática y se especifica otra dirección IP. De forma predeterminada, cuando se cambia el método de asignación de dinámica a estática, Azure asigna la dirección asignada dinámicamente anterior como dirección estática. 

### <a name="static"></a>estática

De manera opcional, puede asignar una dirección IPv4 o IPv6 estática pública o privada a una configuración IP. Para más información sobre cómo Azure asigna direcciones IPv4 estáticas públicas, consulte [Dirección IP pública](virtual-network-public-ip-address.md).

- **Solo pública**: Azure asigna la dirección de un intervalo único a cada región de Azure. Puede descargar la lista de intervalos (prefijos) para las nubes de Azure [Pública](https://www.microsoft.com/download/details.aspx?id=56519), [Gobierno de Estados Unidos](https://www.microsoft.com/download/details.aspx?id=57063), [China](https://www.microsoft.com/download/details.aspx?id=57062) y [Alemania](https://www.microsoft.com/download/details.aspx?id=57064). La dirección no cambia hasta que el recurso de dirección IP pública se asigna o se elimina, o el método de asignación cambia a dinámico. Si el recurso de dirección IP pública está asociado a una configuración de dirección IP, se debe desasociar de la configuración de dirección IP antes de cambiar su método de asignación.
- **Solo privado**: se selecciona y asigna una dirección del intervalo de direcciones de la subred. La dirección que se asigna puede ser cualquiera que esté en el intervalo de direcciones de subred, salvo que sea una de las cuatro primeras y que no esté asignada a otro recurso de la subred. Las direcciones estáticas solo se liberan cuando se elimina la interfaz de red. Si cambia el método de asignación a estática, Azure asigna dinámicamente la dirección IP estática asignada anteriormente como dirección estática, aunque no sea la siguiente dirección disponible en el intervalo de direcciones de la subred. La dirección también cambia si la interfaz de red se asigna a otra subred de la misma red virtual, pero para ello, antes hay que cambiar el método de asignación de estática a dinámica. Una vez que ha asignado la interfaz de red a otra subred, puede volver a cambiar el método de asignación a estática y asignar una dirección IP del intervalo de direcciones de la nueva subred.

## <a name="ip-address-versions"></a>Versiones de direcciones IP

Puede especificar las versiones siguientes cuando asigna direcciones:

### <a name="ipv4"></a>IPv4

Cada interfaz de red debe tener una configuración de IP [principal](#primary) con una dirección [IPv4](#private) [privada](#ipv4) asignada. Puede agregar una o más configuraciones IP [secundarias](#secondary) en que cada una tenga una dirección IPv4 privada y, de manera opcional, una dirección IP [pública](#public) IPv4.

### <a name="ipv6"></a>IPv6

Puede no asignar ninguna dirección [IPv6](#ipv6) privada o asignar una a una configuración IP secundaria de una interfaz de red. La interfaz de red no puede tener ninguna configuración IP secundaria existente. Cada interfaz de red puede tener como máximo una dirección IPv6 privada. Opcionalmente, puede agregar una dirección IPv6 pública a una configuración de interfaz de red IPv6.

> [!NOTE]
> Aunque puede crear una interfaz de red con una dirección IPv6 mediante el portal, no se puede agregar una interfaz de red a una máquina virtual nueva o existente, mediante el portal. Use PowerShell o la CLI de Azure para crear una interfaz de red con una dirección IPv6 privada y, luego, conectar la interfaz de red cuando crea una máquina virtual. No puede conectar a una máquina virtual existente una interfaz de red con una dirección IPv6 privada asignada. No puede agregar una dirección IPv6 privada a una configuración IP para ninguna interfaz de red conectada a una máquina virtual con ninguna herramienta (portal, CLI o PowerShell).

## <a name="skus"></a>SKU

Se crea una dirección IP pública con la SKU estándar o básica. Para más información sobre las diferencias entre SKU, vea [Creación, modificación o eliminación de una dirección IP pública](virtual-network-public-ip-address.md).

> [!NOTE]
> Cuando asigna una dirección IP pública de SKU estándar a una interfaz de red de una máquina virtual, debe permitir explícitamente el tráfico previsto con un [grupo de seguridad de red](../../virtual-network/network-security-groups-overview.md#network-security-groups). Para evitar que se produzca un error en la comunicación con el recurso, debe crear un grupo de seguridad de red, asociarlo y permitir explícitamente el tráfico deseado.

## <a name="next-steps"></a>Pasos siguientes
Para crear una máquina virtual con distintas configuraciones IP, lea los artículos siguientes:

|Tarea|Herramienta|
|---|---|
|Creación de una máquina virtual con varias interfaces de red|[CLI](../../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [PowerShell](../../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|
|Creación de una máquina virtual con una sola interfaz de red y varias direcciones IPv4|[CLI](virtual-network-multiple-ip-addresses-cli.md), [PowerShell](virtual-network-multiple-ip-addresses-powershell.md)|
|Creación de una máquina virtual con una sola interfaz de red y una dirección IPv6 privada (detrás de Azure Load Balancer)|[CLI](../../load-balancer/load-balancer-ipv6-internet-cli.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [PowerShell](../../load-balancer/load-balancer-ipv6-internet-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [Plantilla de Azure Resource Manager](../../load-balancer/load-balancer-ipv6-internet-template.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|
