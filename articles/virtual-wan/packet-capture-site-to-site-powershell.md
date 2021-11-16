---
title: Configuración de una captura de paquetes para conexiones de sitio a sitio de Virtual WAN
description: Aprenda a capturar paquetes en puertas de enlace de VPN de sitio a sitio de Virtual WAN mediante PowerShell.
services: virtual-wan
titleSuffix: Azure Virtual WAN
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 11/09/2021
ms.author: cherylmc
ms.openlocfilehash: a5b4427627b164c4f87d2c65203a69c1d2cc2699
ms.sourcegitcommit: 838413a8fc8cd53581973472b7832d87c58e3d5f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/10/2021
ms.locfileid: "132138634"
---
# <a name="configure-a-packet-capture-for-virtual-wan-site-to-site-vpn-powershell"></a>Configuración de una captura de paquetes para VPN de sitio a sitio de Virtual WAN: PowerShell

Este artículo le ayuda a crear una captura de paquetes para una puerta de enlace de VPN de sitio de Azure Virtual WAN mediante Azure PowerShell. La captura de paquetes le ayuda a limitar el ámbito de un problema a algunas partes de la red. Puede ayudarle a determinar si el problema está en el lado local de la red, en el lado de Azure de la red o en algún punto entre ambos. Al reducir el problema, puede depurar y tomar medidas correctivas de forma más eficaz.

Aunque existen algunas herramientas de captura de paquetes disponibles, obtener capturas de paquetes pertinentes con estas herramientas puede ser complicado, especialmente en escenarios con gran volumen de tráfico. Las funcionalidades de filtrado que proporciona la captura de paquetes de Virtual WAN marcan una gran diferencia. La captura de paquetes de Virtual WAN se puede usar junto con otras herramientas de captura de paquetes disponibles habitualmente.

## <a name="prerequisites"></a>Requisitos previos

Compruebe que ya tiene la configuración siguiente definida en su entorno:

* Una WAN virtual y un centro virtual.
* Una puerta de enlace de VPN de sitio a sitio implementada en el centro virtual.
* También puede tener conexiones que conecten sitios VPN a su puerta de enlace de VPN de sitio a sitio.

### <a name="working-with-azure-powershell"></a>Trabajo con Azure PowerShell

[!INCLUDE [PowerShell](../../includes/vpn-gateway-cloud-shell-powershell.md)]

### <a name="set-up-the-environment"></a>Configuración del entorno

Use el siguiente comando para comprobar que está usando la suscripción correcta y que ha iniciado sesión como un usuario con permisos para realizar la captura de paquetes en la puerta de enlace de VPN de sitio a sitio.

```azurepowershell-interactive
$subid = “<insert Virtual WAN subscription ID here>”
Set-AzContext -SubscriptionId $subid
```

## <a name="create-a-storage-account-and-container"></a><a name="storage"></a> Creación de una cuenta de almacenamiento y un contenedor

Esta cuenta de almacenamiento se utiliza para almacenar los resultados de capturas de paquetes.

1. Cree una cuenta de almacenamiento. Consulte [Creación de una cuenta de almacenamiento](../storage/common/storage-account-create.md?tabs=azure-portal) para ver los pasos.
1. Cree un objeto contenedor dentro de su cuenta de almacenamiento. Consulte [Creación de un contenedor](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container) para ver los pasos.

## <a name="generate-the-sas-url"></a><a name="URL"></a> Generación de la dirección URL de SAS

Al detener una captura de paquetes, debe proporcionar la **dirección URL de SAS** del contenedor de almacenamiento que ha creado. Los resultados de la captura de paquetes se almacenarán a través de esta dirección URL. 

Ejecute los siguientes comandos para generar una dirección URL de firma de acceso compartido (SAS):

```azurepowershell-interactive
$rg = “<resource group name containing storage account>” 
$storeName = “<name of storage account> “
$containerName = “<name of container you want to store packet capture in>
$key = Get-AzStorageAccountKey -ResourceGroupName $rg -Name $storeName
$context = New-AzStorageContext -StorageAccountName  $storeName -StorageAccountKey $key[0].value
New-AzStorageContainer -Name $containerName -Context $context
$container = Get-AzStorageContainer -Name $containerName  -Context $context
$now = get-date
$sasurl = New-AzStorageContainerSASToken -Name $containerName -Context $context -Permission "rwd" -StartTime $now.AddHours(-1) -ExpiryTime $now.AddDays(1) -FullUri
```

## <a name="start-a-packet-capture"></a><a name="start"></a> Inicio de una captura de paquetes

Esta sección le ayuda a iniciar una captura de paquetes para la puerta de enlace de VPN de sitio a sitio (todas las conexiones).

1. Para ejecutar una captura de paquetes, necesita el valor **-Name** de la puerta de enlace de VPN de sitio a sitio. Para encontrar el valor **-Name**, en Azure Portal vaya al centro virtual y, en **Conectividad**, haga clic en **VPN (sitio a sitio)** .

   :::image type="content" source="./media/packet-capture-site-to-site-powershell/vpn-gateway-name.png" alt-text="Imagen del nombre de la puerta de enlace de Virtual WAN." lightbox="./media//packet-capture-site-to-site-powershell/vpn-gateway-name.png":::

1. Para iniciar una captura de paquetes, ejecute el siguiente comando:

   ```azurepowershell-interactive
   Start-AzVpnGatewayPacketCapture -ResourceGroupName $rg -Name "<name of the Gateway>"
   ```

## <a name="optional-specify-filters"></a><a name="filters"></a> Opcional: Especificación de filtros

Para simplificar las capturas de paquetes, puede especificar filtros para centrarse en comportamientos específicos.

>[!NOTE]
> Para TracingFlags y TCPFlags, puede especificar varios protocolos sumando los valores numéricos de los protocolos que quiere capturar (igual que un operador OR lógico). Por ejemplo, si desea capturar solo paquetes ESP y OPVN, especifique un valor TracingFlag de 8+1 = 9.  

| Parámetro | Descripción | Valores predeterminados | Valores disponibles|
|--- |--- | --- | ---|
| TracingFlags | Entero que determina qué tipos de paquetes se capturan | 11 (ESP, IKE, OVPN) | ESP = 1 IKE = 2 OPVN = 8 |
| TCPFlags | Entero que determina qué tipos de paquetes TCP se capturan | 0 (ninguno) | FIN = 1, SYN = 2, RST = 4, PSH = 8, ACK = 16, URG = 32, ECE = 64, CWR = 128| 
| MaxPacketBufferSize|Tamaño máximo de un paquete capturado en bytes. Los paquetes se truncan si son mayores que el valor proporcionado. |120|Any|
| MaxFileSize |Tamaño de archivo de captura máximo en MB. Las capturas se almacenan en un búfer circular, por lo que el desbordamiento se gestiona de manera FIFO (los paquetes más antiguos se quitan primero)|100|Any|
|SourceSubnets|Se capturan los paquetes de los intervalos de CIDR especificados. Se especifica como una matriz.|[] (todas las direcciones IPv4)|Matriz de subredes IPV4 separadas por comas|
| DestinationSubnets |Se capturan los paquetes destinados a los intervalos de CIDR especificados. Se especifica como una matriz. |[] (todas las direcciones IPv4)|Matriz de subredes IPV4 separadas por comas|
|SourcePort |Se capturan los paquetes con origen en los intervalos especificados. Se especifica como una matriz.|[] (todos los puertos)|Matriz de puertos separados por comas|
|DestinationPort |Se capturan los paquetes con destino en los intervalos especificados. Se especifica como una matriz.|[] (todos los puertos)|Matriz de puertos separados por comas|
| CaptureSingleDirectionTrafficOnly |Si el resultado es true, solo aparecerá una dirección de un flujo bidireccional en la captura de paquetes. Esto capturará todas las combinaciones posibles de IP y puertos.|True|True, False|
|Protocolo|Matriz de enteros que corresponden a protocolos IANA. |[] (todos los protocolos)| Todos los protocolos que se encuentran [aquí](https://www.iana.org/assignments/protocol-numbers/protocol-numbers.xhtml) |

En el ejemplo siguiente se muestra una captura de paquetes mediante una cadena de filtro. Puede cambiar los parámetros en función de sus necesidades.

```azurepowershell-interactive
$filter="{`"TracingFlags`":11,`"MaxPacketBufferSize`":120,`"MaxFileSize`":500,`"Filters`":[{`"SourceSubnets`":[`"10.19.0.4/32`",`"10.20.0.4/32`"],`"DestinationSubnets`":[`"10.20.0.4/32`",`"10.19.0.4/32`"],`"TcpFlags`":9,`"CaptureSingleDirectionTrafficOnly`":true}]}"
Start-AzVpnConnectionPacketCapture -ResourceGroupName $rg -Name "<name of the VPN connection>" -ParentResourceName “<name of the Gateway>” -LinkConnection “<comma separated list of links>” -SasUrl $sasurl -FilterData $filter

Start-AzVpnGatewayPacketCapture -ResourceGroupName $rg -Name "<name of the Gateway>" -FilterData $filter
```

## <a name="stop-a-packet-capture"></a><a name="stop"></a> Detención de una captura de paquetes

Se recomienda dejar que la captura de paquetes se ejecute durante al menos 600 segundos antes de detenerla. Cuando se detiene una captura de paquetes, los parámetros son similares a los parámetros de la sección [Inicio de una captura de paquetes](#start). En el comando, el valor de la dirección URL de SAS se generó en la sección [Creación de una cuenta de almacenamiento](#storage). Si el parámetro `SasUrl` no está configurado correctamente, es posible que se produzcan errores de almacenamiento en la captura.

Cuando sea el momento de detener la captura de paquetes, ejecute el siguiente comando:

```azurepowershell-interactive
Stop-AzVpnGatewayPacketCapture -ResourceGroupName $rg -Name <GatewayName> -SasUrl $sasurl
```

## <a name="view-a-packet-capture"></a><a name="view"></a> Visualización de una captura de paquetes

Esta sección le ayuda a descargar el archivo PCAP de captura de paquetes para verlo.

1. En Azure Portal, navegue hasta la cuenta de almacenamiento que ha creado.
1. Haga clic en **Contenedores** para ver los contenedores de la cuenta de almacenamiento.
1. Haga clic en el contenedor que ha creado.
1. Navegue por la estructura de carpetas para buscar el archivo PCAP. El nombre de la carpeta y la estructura se basan en la fecha y la hora UTC. Cuando encuentre el archivo PCAP, haga clic en **Descargar**.

   :::image type="content" source="./media/packet-capture-site-to-site-powershell/download.png" alt-text="Gráfico que muestra cómo descargar el archivo." lightbox="./media/packet-capture-site-to-site-powershell/download-expand.png":::
1. Los archivos de datos de captura de paquetes se generan en formato PCAP. Use Wireshark u otra aplicación que haya disponible para abrir archivos PCAP.

## <a name="next-steps"></a>Pasos siguientes

A continuación, para más información sobre Virtual WAN, consulte las [preguntas más frecuentes sobre Virtual WAN](virtual-wan-faq.md).
