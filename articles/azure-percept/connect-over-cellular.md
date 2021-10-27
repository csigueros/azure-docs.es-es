---
title: Conexión de Azure Percept a través de redes 5G o LTE
description: En este artículo se explica cómo conectar Azure Percept DK a través de redes 5G o LTE.
author: juhaluoto
ms.author: amiyouss
ms.service: azure-percept
ms.topic: how-to
ms.date: 09/23/2021
ms.custom: template-how-to
ms.openlocfilehash: 0df673937ebd195c250a0f0a8880ecf89d42c844
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/14/2021
ms.locfileid: "129997238"
---
# <a name="connect-azure-percept-over-5g-or-lte-networks"></a>Conexión de Azure Percept a través de redes 5G o LTE

Conectar dispositivos de inteligencia artificial perimetral a través de redes 5G o LTE tiene muchas ventajas. La inteligencia artificial perimetral es más eficaz en escenarios donde la conectividad Wi-Fi y LAN es limitada, como las ciudades inteligentes, los vehículos autónomos y la agricultura. Además, las redes 5G y LTE proporcionan una mejor seguridad que la conexión Wi-Fi. Por último, el uso de dispositivos IoT que ejecutan inteligencia artificial en el perímetro ofrecen el modo de optimizar el ancho de banda en redes 5G y LTE. Solo se envía la información necesaria a la nube, mientras que la mayoría de los datos se procesan en el dispositivo. En la actualidad, Azure Percept DK admite incluso la conexión directa a redes 5G/LTE mediante un módem USB simple. A continuación, encontrará más información sobre las distintas opciones.

## <a name="options-for-connecting-azure-percept-dk-over-5g-or-lte-networks"></a>Opciones para conectar Azure Percept DK a través de redes 5G o LTE
Con hardware adicional, puede conectar Azure Percept DK mediante conectividad 5G o LTE. Actualmente, se admiten tres opciones; puede encontrar un vínculo a más detalles sobre cada una:
- **Dispositivo módem USB 5G/LTE**: ahora hemos publicado una nueva imagen de software que admite ModemManager de código abierto que agrega compatibilidad con módems USB a nuestro sistema operativo Linux. Esto le permite conectar Azure Percept a través de redes LTE o 5G mediante varios módems USB a menudo económicos. Para más información, consulte [Conexión mediante un módem USB](./connect-over-cellular-usb.md).   
- **Dispositivo de puerta de enlace Ethernet 5G/LTE**: aquí Azure Percept está conectado a la puerta de enlace 5G/LTE a través de Ethernet. Puede encontrar más información en [Conexión mediante una puerta de enlace de telefonía móvil](./connect-over-cellular-gateway.md).
- **Dispositivo de zona activa Wi-Fi 5G/LTE**: donde Azure Percept se conecta a la red Wi-Fi que proporciona la zona activa Wi-Fi. En este caso, el kit de desarrollo se conecta a la red como a cualquier otra red Wi-Fi. Para obtener más instrucciones, siga la [Guía de instalación de Azure Percept DK](./quickstart-percept-dk-set-up.md) y seleccione la red Wi-Fi 5G o LTE que se difunde desde la zona activa.


## <a name="considerations-when-selecting-a-5g-or-lte-device-in-general"></a>Consideraciones al seleccionar un dispositivo 5G o LTE en general
Los dispositivos 5G/LTE, ya sean módems USB o puertas de enlace Ethernet, admiten distintas tecnologías que afectan a la velocidad de datos máxima de carga y descarga. Las velocidades de datos anunciadas ofrecen una guía para la toma de decisiones, pero normalmente nunca se alcanzan en el mundo real. Estas son algunas instrucciones para seleccionar el dispositivo correcto para sus necesidades.
 
- **LTE CAT-1** proporciona hasta 10 Mbps de bajada y 5 Mbps de subida. Es suficiente para las características predeterminadas de Azure Percept Devkit, como la detección de objetos y la creación de un asistente para voz. Sin embargo, puede que no sea suficiente para las soluciones que requieren transmisión de datos de vídeo a la nube.
- **LTE CAT-3 y 4** proporcionan hasta 100 Mbps de bajada y 50 Mbps de subida, lo que es suficiente para transmitir vídeo a la nube. Sin embargo, no basta para transmitir vídeo de calidad HD completa.
- **LTE CAT-5 y versiones posteriores** proporcionan velocidades de datos suficientes como para transmitir vídeo en HD para un único dispositivo. Si necesita conectar varios dispositivos a una única puerta de enlace, deberá considerar la conexión 5G.
- Las puertas de enlace **5G** son una mejor solución de cara al futuro. Tienen unas velocidades de datos y un ancho de banda que admiten un alto rendimiento de datos para varios dispositivos a la vez. Además, también proporcionan una latencia menor para la transferencia de datos.


## <a name="next-steps"></a>Pasos siguientes
En función del dispositivo móvil al que pueda tener acceso, siga estos vínculos para conectar el kit de desarrollo de Azure Percept:

[Conexión mediante una puerta de enlace de telefonía móvil](./connect-over-cellular-gateway.md).

[Conexión mediante un módem USB](./connect-over-cellular-usb.md).
