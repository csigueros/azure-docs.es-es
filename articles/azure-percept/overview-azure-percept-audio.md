---
title: Introducción al dispositivo Azure Percept Audio
description: Más información acerca de Azure Percept Audio.
author: MrHamlet
ms.author: amiyouss
ms.service: azure-percept
ms.topic: conceptual
ms.date: 03/23/2021
ms.custom: template-concept
ms.openlocfilehash: 6afe4da59b837d4901bfb7243d5e0734b4c8df28
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/30/2021
ms.locfileid: "123226889"
---
# <a name="azure-percept-audio-device-overview"></a>Introducción al dispositivo Azure Percept Audio

Azure Percept Audio es un dispositivo accesorio que agrega funcionalidades de voz con inteligencia artificial a [Azure Percept DK](./overview-azure-percept-dk.md). Contiene un procesador de audio preconfigurado y una matriz lineal de cuatro micrófonos, lo que permite usar comandos de voz, detección de palabras clave y reconocimiento de voz a gran distancia mediante Azure Cognitive Services. Se integra de forma perfecta con Azure Percept DK, [Azure Percept Studio](https://go.microsoft.com/fwlink/?linkid=2135819) y otros servicios de administración perimetral de Azure. Azure Percept Audio se puede adquirir en la [tienda en línea de Microsoft](https://go.microsoft.com/fwlink/p/?LinkId=2155270).

> [!div class="nextstepaction"]
> [Comprar ahora](https://go.microsoft.com/fwlink/p/?LinkId=2155270)

</br>

> [!VIDEO https://www.youtube.com/embed/Qj8NGn-7s5A]

## <a name="azure-percept-audio-components"></a>Componentes de Azure Percept Audio

Azure Percept Audio contiene los siguientes componentes principales:

- Dispositivo de Azure Percept Audio (módulo de sistema) listo para producción con una matriz lineal de cuatro micrófonos y procesamiento de audio mediante un códec XMOS.
- Placa para desarrolladores (intermediador) que incluye 2 botones, 3 indicadores LED, micro USB y conector de audio de 3,5 mm.
- Cables necesarios: cable FPC, micro USB tipo B a USB A
- Tarjeta de bienvenida
- Placa de montaje mecánico con montaje de serie 80/20 1010 integrado

## <a name="compute-capabilities"></a>Capacidades de proceso 

Azure Percept Audio pasa la entrada de audio a través de la pila de voz que se ejecuta en la CPU de la placa base del dispositivo Azure Percept DK en forma híbrida de nube-perímetro. Por lo tanto, Azure Percept Audio necesita una placa base con un sistema operativo que admita la pila de voz para funcionar. 

El procesamiento de audio se realiza del siguiente modo: 

- Azure Percept Audio: captura y convierte el audio y lo envía al DK y a la toma de audio.

- Azure Percept DK: la pila de voz se ocupa de conformar los haces y cancelar el eco, y procesa el audio entrante para optimizar la voz. Después del procesamiento, realiza la búsqueda de palabras clave.

- Nube: procesa comandos y frases de lenguaje natural, comprueba las palabras clave y repite el entrenamiento. 

- Sin conexión: si el dispositivo está sin conexión, detectará la palabra clave y capturará la telemetría del estado de conexión a Internet. Se puede observar un aumento falso de la tasa de aceptación en la detección de palabras clave, ya que no se puede realizar la comprobación de palabras clave en la nube. 

## <a name="getting-started"></a>Introducción

- [Ensamblaje del kit de desarrollo Azure Percept DK](./quickstart-percept-dk-unboxing.md)
- [Conexión del dispositivo Azure Percept Audio al kit de desarrollo](./quickstart-percept-audio-setup.md)
- [Finalización de la experiencia de instalación de Azure Percept DK](./quickstart-percept-dk-set-up.md)

## <a name="build-a-no-code-prototype"></a>Creación de un prototipo sin código

Cree una [solución de voz sin código](./tutorial-no-code-speech.md) en [Azure Percept Studio](https://go.microsoft.com/fwlink/?linkid=2135819) mediante las plantillas del asistente para voz de Azure Percept para escenarios de hostelería, asistencia sanitaria, inventario y automoción.

### <a name="manage-your-no-code-speech-solution"></a>Administración de la solución de voz sin código

- [Configuración del asistente para voz en Azure Percept Studio](./how-to-manage-voice-assistant.md)
- [Configuración del asistente para voz en IoT Hub](./how-to-configure-voice-assistant.md)
- [Solución de problemas de Azure Percept Audio](./troubleshoot-audio-accessory-speech-module.md)

## <a name="additional-technical-information"></a>Información técnica adicional

- [Ficha técnica de Azure Percept Audio](./azure-percept-audio-datasheet.md)
- [Comportamiento del botón y los indicadores LED](./audio-button-led-behavior.md)

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Compre un dispositivo Azure Percept Audio en la tienda en línea de Microsoft](https://go.microsoft.com/fwlink/p/?LinkId=2155270)
