---
title: Conexión de Azure Percept a través de redes 5G o LTE
description: En este artículo se explica cómo conectar Azure Percept DK a través de redes 5G o LTE.
author: juhaluoto
ms.author: amiyouss
ms.service: azure-percept
ms.topic: how-to
ms.date: 07/28/2021
ms.custom: template-how-to
ms.openlocfilehash: 202bec8182cc2ae04e9fadffe01977150ea93dda
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/30/2021
ms.locfileid: "123222327"
---
# <a name="connect-azure-percept-over-5g-or-lte-networks"></a>Conexión de Azure Percept a través de redes 5G o LTE

Conectar dispositivos de inteligencia artificial perimetral a través de redes 5G o LTE tiene muchas ventajas. La inteligencia artificial perimetral es más eficaz en escenarios donde la conectividad Wi-Fi y LAN es limitada, como las ciudades inteligentes, los vehículos autónomos y la agricultura. Además, las redes 5G y LTE proporcionan una mejor seguridad que la conexión Wi-Fi. Por último, el uso de dispositivos IoT que ejecutan inteligencia artificial en el perímetro ofrecen el modo de optimizar el ancho de banda en redes 5G y LTE. Solo se envía la información necesaria a la nube mientras que la mayoría de los datos se procesan en el dispositivo. En la actualidad, Azure Percept DK no puede conectarse directamente a redes 5G o LTE. Sin embargo, sí que puede conectarse a puertas de enlace 5G y LTE mediante las funcionalidades Ethernet y Wi-Fi integradas. En este artículo se explica el funcionamiento.

## <a name="options-for-connecting-the-azure-percept-dk-over-5g-or-lte-networks"></a>Opciones para conectar Azure Percept DK a través de redes 5G o LTE
Con hardware adicional, puede conectar Azure Percept DK mediante conectividad 5G o LTE. Actualmente se admiten dos opciones principales:
- **Dispositivo de zona activa Wi-Fi 5G o LTE:** el kit de desarrollo está conectado a la red Wi-Fi que proporciona la zona activa Wi-Fi. En este caso, el kit de desarrollo se conecta a la red como a cualquier otra red Wi-Fi. Para obtener más instrucciones, siga la [Guía de instalación de Azure Percept DK](./quickstart-percept-dk-set-up.md) y seleccione la red Wi-Fi 5G o LTE que se difunde desde la zona activa.
- **Dispositivo de puerta de enlace Ethernet 5G o LTE**: en este caso el kit de desarrollo está conectado a la puerta de enlace 5G o LTE a través de Ethernet, que aprovecha la seguridad mejorada en comparación con las conexiones Wi-Fi. En el resto de este artículo se explica con más detalle cómo se configura una red como esta.

## <a name="5glte-gateway-topology"></a>Topología de puerta de enlace 5G o LTE
:::image type="Image" source="media/connect-over-cellular/topology.png" alt-text="En este diagrama se muestra cómo se conecta Azure Percept DK a una puerta de enlace 5G o LTE a través de Ethernet.":::

En el diagrama anterior puede ver cómo se puede emparejar fácilmente una puerta de enlace 5G o LTE con Azure Percept DK.

## <a name="considerations-when-connecting-to-a-5g-or-lte-gateway"></a>Consideraciones al establecer conexión con una puerta de enlace 5G o LTE
Estos son algunos detalles importantes que se deben tener en cuenta al conectar Azure Percept DK a una puerta de enlace 5G o LTE.
- Configure primero la puerta de enlace y, a continuación, valide que recibe una conexión a través de la SIM. Después, será más fácil solucionar cualquier incidencia que aparezca al conectar Azure Percept DK.
- Asegúrese de que ambos extremos del cable Ethernet están conectados correctamente a la puerta de enlace y a Azure Percept DK.
- Siga las [instrucciones predeterminadas](./how-to-connect-over-ethernet.md) para conectar Azure Percept DK a través de Ethernet.
- Si el plan 5G o LTE tiene una cuota, se recomienda optimizar la cantidad de datos que los modelos de Azure Percept DK envían a la nube.
- Asegúrese de que tiene un [firewall configurado correctamente](./concept-security-configuration.md) que bloquea el tráfico entrante de origen externo.

## <a name="ssh-over-a-5g-or-lte-network"></a>SSH a través de una red 5G o LTE
Para establecer una conexión SSH con el kit de desarrollo a través de una puerta de enlace Ethernet 5G o LTE, tiene las siguientes opciones:
- **Uso del punto de acceso Wi-Fi del kit de desarrollo**. Si ha deshabilitado la conexión Wi-Fi, puede volver a habilitarla si reinicia el kit de desarrollo. Desde allí, puede conectarse al punto de acceso Wi-Fi del kit de desarrollo y seguir [estos procedimientos SSH](./how-to-ssh-into-percept-dk.md).
- **Uso de una conexión Ethernet a una red local (LAN)** . Con esta opción, desconectará el kit de desarrollo de la puerta de enlace 5G o LTE y lo conectará al enrutador LAN. Para más información, consulte [Conexión a través de Ethernet](./how-to-connect-over-ethernet.md). 
- **Uso de las características de acceso remoto de la puerta de enlace**. Muchas puertas de enlace 5G o LTE incluyen administradores de acceso remoto que se pueden usar para conectarse a dispositivos de la red a través de SSH. Consulte al fabricante de su puerta de enlace 5G o LTE para saber si tiene esta característica. A continuación se muestra un ejemplo de administrador de acceso remoto para [puertas de enlace 5G o LTE de Cradlepoint](https://customer.cradlepoint.com/s/article/NCM-Remote-Connect-LAN-Manager).
- **Uso del puerto serie del kit de desarrollo**. Azure Percept DK incluye un puerto de conexión de serie que se puede usar para conectarse directamente al dispositivo. Consulte [Conexión de Azure Percept DK a través de conexión de serie](./how-to-connect-to-percept-dk-over-serial.md) para obtener instrucciones detalladas.

## <a name="considerations-when-selecting-a-5g-or-lte-gateway-device"></a>Consideraciones al seleccionar un dispositivo de puerta de enlace 5G o LTE
Las puertas de enlace 5G y LTE admiten diferentes tecnologías que afectan a la velocidad máxima de descarga y carga de datos. Las tasas de datos anunciadas proporcionan instrucciones para la toma de decisiones, pero normalmente nunca se alcanzan. Estas son algunas instrucciones para seleccionar la puerta de enlace adecuada para sus necesidades.
 
- **LTE CAT-1** proporciona hasta 10 Mbps de bajada y 5 Mbps de subida. Es suficiente para las características predeterminadas de Azure Percept Devkit, como la detección de objetos y la creación de un asistente para voz. Sin embargo, puede que no sea suficiente para las soluciones que requieren transmisión de datos de vídeo a la nube.
- **LTE CAT-3 y 4** proporcionan hasta 100 Mbps de bajada y 50 Mbps de subida, lo que es suficiente para transmitir vídeo a la nube. Sin embargo, no basta para transmitir vídeo de calidad HD completa.
- **LTE CAT-5 y versiones posteriores** proporcionan velocidades de datos suficientes como para transmitir vídeo en HD para un único dispositivo. Si necesita conectar varios dispositivos a una única puerta de enlace, deberá considerar la conexión 5G.
- Las puertas de enlace **5G** son una mejor solución de cara al futuro. Tienen unas velocidades de datos y un ancho de banda que admiten un alto rendimiento de datos para varios dispositivos a la vez. Además, también proporcionan una latencia menor para la transferencia de datos.


## <a name="next-steps"></a>Pasos siguientes
Si tiene una puerta de enlace 5G o LTE y desea conectar a esta Azure Percept DK, siga los pasos que se detallan a continuación:
- [Conexión de Azure Percept DK a través de Ethernet](./how-to-connect-over-ethernet.md)
