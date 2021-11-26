---
title: Configuración de una captura de paquetes para conexiones VPN S2S mediante Azure Portal
titleSuffix: Azure Virtual WAN
description: Aprenda a configurar la captura de paquetes de sitio a sitio para Azure Virtual WAN mediante el portal
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 11/09/2021
ms.author: cherylmc
ms.openlocfilehash: c378a6a6e38739600ddad8b0b956e31136d6fffe
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/17/2021
ms.locfileid: "132733875"
---
# <a name="configure-a-packet-capture-for-virtual-wan-site-to-site-vpn-azure-portal"></a>Configuración de una captura de paquetes para VPN de sitio a sitio de Virtual WAN: Azure Portal

Este artículo le ayuda a crear una captura de paquetes para una puerta de enlace de VPN de sitio a sitio de Azure Virtual WAN mediante Azure Portal. La captura de paquetes le ayuda a limitar el ámbito de un problema a algunas partes de la red. Puede ayudarle a determinar si el problema está en el lado local de la red, en el lado de Azure de la red o en algún punto entre ambos. Al reducir el problema, puede depurar y tomar medidas correctivas de forma más eficaz.

Aunque existen algunas herramientas de captura de paquetes disponibles, obtener capturas de paquetes pertinentes con estas herramientas puede ser complicado, especialmente en escenarios con gran volumen de tráfico. Las funcionalidades de filtrado que proporciona la captura de paquetes de Virtual WAN marcan una gran diferencia. La captura de paquetes de Virtual WAN se puede usar junto con otras herramientas de captura de paquetes disponibles habitualmente.

[!INCLUDE [Portal rollout](../../includes/virtual-wan-portal-feature-rollout.md)]

## <a name="prerequisites"></a>Requisitos previos

Compruebe que ya tiene la configuración siguiente definida en su entorno:

* Una WAN virtual y un centro virtual.
* Una puerta de enlace de VPN de sitio a sitio implementada en el centro virtual.
* También puede tener conexiones que conecten sitios VPN a su puerta de enlace de VPN de sitio a sitio.

## <a name="create-a-storage-account-and-container"></a><a name="storage"></a> Creación de una cuenta de almacenamiento y un contenedor

Esta cuenta de almacenamiento se utiliza para almacenar los resultados de capturas de paquetes.

1. Cree una cuenta de almacenamiento. Consulte [Creación de una cuenta de almacenamiento](../storage/common/storage-account-create.md?tabs=azure-portal) para ver los pasos.
1. Cree un objeto contenedor dentro de su cuenta de almacenamiento. Consulte [Creación de un contenedor](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container) para ver los pasos.

## <a name="generate-the-sas-url"></a><a name="URL"></a> Generación de la dirección URL de SAS

Al detener una captura de paquetes, debe proporcionar la **dirección URL de SAS** del contenedor de almacenamiento que ha creado. Los resultados de la captura de paquetes se almacenarán a través de esta dirección URL.
Para generar la dirección URL de SAS para el contenedor de almacenamiento:

1. Vaya al contenedor recién creado.
1. En **Configuración**, seleccione **Shared access tokens** (Tokens de acceso compartido).
1. En la pestaña **Permissions** (Permisos), compruebe que están habilitadas tanto **Read** (Lectura) como **Write** (Escritura).
1. En la parte inferior de la página, haga clic en el botón **Generar URL y token de SAS**.
1. Haga clic para copiar el vínculo **URL de SAS de Blob** que se genera en el Portapapeles.

   :::image type="content" source="./media/packet-capture-site-to-site-portal/generate-url.png" alt-text="Página Shared access tokens (Tokens de acceso compartido) con las opciones (Read) Lectura y Write (Escritura) seleccionadas." lightbox="./media/packet-capture-site-to-site-portal/generate-url-expand.png":::

## <a name="start-a-packet-capture"></a><a name="start"></a> Inicio de una captura de paquetes

En esta sección, se inicia la captura de paquetes en el centro virtual.

1. Vaya al centro virtual.
1. Haga clic en **VPN (sitio a sitio)** .
1. En la página **VPN (sitio a sitio)** , haga clic en el botón **Captura de paquetes** de la parte superior de la página.

   :::image type="content" source="./media/packet-capture-site-to-site-portal/packet-capture.png" alt-text="Gráfico que muestra la página Iniciar captura." lightbox="./media/packet-capture-site-to-site-portal/packet-capture-expand.png":::
1. En la página **Captura de paquetes**, haga clic en **Inicio**.
1. En la página **Iniciar captura de paquetes**, modifique la configuración si fuera necesario. En la sección [Filtros](#filters) puede ver las opciones.
1. Haga clic en el botón **Iniciar** para iniciar la captura de paquetes. Se recomienda dejar que la captura de paquetes se ejecute durante un mínimo de 600 segundos. Debido a los problemas de sincronización entre varios componentes de la ruta de acceso, es posible que las capturas de paquetes más cortas no proporcionen datos completos.

## <a name="optional-specify-filters"></a><a name="filters"></a> Opcional: Especificación de filtros

Para simplificar las capturas de paquetes, puede especificar filtros para centrarse en comportamientos específicos.

| Parámetro | Descripción | Valores predeterminados | Valores disponibles |
|---|---|---|---|
|TracingFlags|  Entero que determina qué tipos de paquetes se capturan| 11 (ESP, IKE, OVPN) |ESP = 1 IKE = 2 OPVN = 8|
|TCPFlags|  Entero que determina qué tipos de paquetes TCP se capturan |0 (ninguno)|  FIN = 1, SYN = 2, RST = 4, PSH = 8, ACK = 16, URG = 32, ECE = 64, CWR = 128|
|MaxPacketBufferSize|   Tamaño máximo de un paquete capturado en bytes. Los paquetes se truncan si son mayores que el valor proporcionado.    |120    |Any|
|MaxFileSize |  Tamaño de archivo de captura máximo en MB. Las capturas se almacenan en un búfer circular, por lo que el desbordamiento se gestiona de manera FIFO (los paquetes más antiguos se quitan primero)|    100|    Any|
|SourceSubnets |    Se capturan los paquetes de los intervalos de CIDR especificados. Se especifica como una matriz. |   [ ] (todas las direcciones IPv4)    |Matriz de subredes IPV4 separadas por comas|
|DestinationSubnets |Se capturan los paquetes destinados a los intervalos de CIDR especificados. Se especifica como una matriz. |  [ ] (todas las direcciones IPv4)|   Matriz de subredes IPV4 separadas por comas|
|SourcePort|    Se capturan los paquetes con origen en los intervalos especificados. Se especifica como una matriz.|   [ ] (todos los puertos)|    Matriz de puertos separados por comas|
|DestinationPort|   Se capturan los paquetes con destino en los intervalos especificados. Se especifica como una matriz.   |[ ] (todos los puertos)|   Matriz de puertos separados por comas|
|CaptureSingleDirectionTrafficOnly |    Si el resultado es true, solo aparecerá una dirección de un flujo bidireccional en la captura de paquetes. Esto capturará todas las combinaciones posibles de IP y puertos.|  True|   True, False|
|Protocolo|  Matriz de enteros que corresponden a protocolos IANA.|   [ ] (todos los protocolos)|    Los protocolos enumerados en la página [iana.org](https://www.iana.org/assignments/protocol-numbers/protocol-numbers.xhtml).|

> [!NOTE]
> En el caso de TracingFlags y TCPFlags, puede especificar varios protocolos sumando los valores numéricos de los protocolos que desea capturar (igual que un operador OR lógico). Por ejemplo, si desea capturar solo paquetes ESP y OPVN, especifique un valor TracingFlag de 8+1 = 9.
>

## <a name="stop-a-packet-capture"></a>Detención de una captura de paquetes

Esta sección le ayuda a detener o anular una captura de paquetes.

1. En la página del centro virtual, haga clic en el botón **Captura de paquetes** para abrir la página **Captura de paquetes** y haga clic en **Detener**. De esta forma se abre la página **Stop Packet Capture** (Detener captura de paquetes). En este momento, la captura de paquetes aún no se ha detenido.
   :::image type="content" source="./media/packet-capture-site-to-site-portal/packet-stop.png" alt-text="Gráfico que muestra el botón Detener." lightbox="./media/packet-capture-site-to-site-portal/packet-stop-expand.png":::

1. En la página *Stop Packet Capture* (Detener la captura de paquetes), pegue la **dirección URL de SAS** del contenedor de almacenamiento que creó anteriormente en el campo **Output Sas Url** (URL de SAS de salida). Esta es la ubicación en que se almacenarán los archivos de captura de paquetes.

   :::image type="content" source="./media/packet-capture-site-to-site-portal/output.png" alt-text="Gráfico que muestra dónde se pega la dirección URL de SAS de salida." lightbox="./media/packet-capture-site-to-site-portal/output-expand.png":::
1. Luego, haga clic en **Stop** (Detener). Se detendrá la captura de paquetes y el archivo PCAP se crea y, posteriormente, se guardará en la cuenta de almacenamiento. Esta operación puede tardar unos minutos en completarse.

### <a name="to-abort-a-packet-capture"></a>Para anular una captura de paquetes

Si por algún motivo necesita anular la captura de paquetes, vaya al centro virtual, haga clic en el botón **Captura de paquetes** para abrir la **página Captura de paquetes** y, a continuación, haga clic en **Anular**. Los archivos PCAP no se generarán ni almacenarán cuando se anule una captura de paquetes.

## <a name="view-a-packet-capture"></a><a name="view"></a> Visualización de una captura de paquetes

Esta sección le ayuda a descargar el archivo PCAP de captura de paquetes para verlo.

1. En Azure Portal, navegue hasta la cuenta de almacenamiento que ha creado.
1. Haga clic en **Contenedores** para ver los contenedores de la cuenta de almacenamiento.
1. Haga clic en el contenedor que ha creado.
1. Navegue por la estructura de carpetas para buscar el archivo PCAP. El nombre de la carpeta y la estructura se basan en la fecha y la hora UTC. Cuando encuentre el archivo PCAP, haga clic en **Descargar**.

   :::image type="content" source="./media/packet-capture-site-to-site-portal/download.png" alt-text="Gráfico que muestra cómo descargar el archivo." lightbox="./media/packet-capture-site-to-site-portal/download-expand.png":::
1. Los archivos de datos de captura de paquetes se generan en formato PCAP. Use Wireshark u otra aplicación que haya disponible para abrir archivos PCAP.

## <a name="key-considerations"></a>Consideraciones clave

* La ejecución de capturas de paquetes puede afectar al rendimiento. Recuerde detener la captura de paquetes cuando no sea necesaria.
* La duración de la captura de paquetes mínima sugerida es de 600 segundos. Debido a los problemas de sincronización entre varios componentes de la ruta de acceso, es posible que las capturas de paquetes más cortas no proporcionen datos completos.
* Los archivos de datos de captura de paquetes se generan en formato PCAP. Use Wireshark u otras aplicaciones disponibles habitualmente para abrir archivos PCAP.
* Si el parámetro SASurl no está configurado correctamente, es posible que se produzcan errores de almacenamiento en el seguimiento.

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre Azure Virtual WAN, consulte las [preguntas frecuentes](virtual-wan-faq.md).
