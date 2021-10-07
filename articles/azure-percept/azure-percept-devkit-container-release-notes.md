---
title: Notas de la versión del contenedor de Azure Percept DK
description: Información sobre los cambios y las correcciones de versiones de contenedor de Azure Percept DK.
author: amiyouss
ms.author: amiyouss
ms.service: azure-percept
ms.topic: conceptual
ms.date: 09/17/2021
ms.openlocfilehash: adfde8ee9ef73afdbbb175fd5ecbfc476e019a08
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128699511"
---
# <a name="azure-percept-dk-container-release-notes"></a>Notas de la versión del contenedor de Azure Percept DK

En esta página se proporciona información sobre los cambios y las correcciones de versiones de contenedor de Azure Percept DK.

Para descargar las actualizaciones de contenedor, vaya a [Azure Percept Studio](https://ms.portal.azure.com/#blade/AzureEdgeDevices/main/overview), seleccione "Dispositivos" en el panel de navegación izquierdo, elija el dispositivo específico y, a continuación, seleccione las pestañas "Vision" y "Voz" para iniciar las descargas de contenedor. 

## <a name="august-2108-release"></a>Versión de agosto (2108)

- Azureyemodule
    - Se ha actualizado a la versión más reciente de Intel (mayo) para la actualización del firmware de la cámara Myriad X. 
    - Se ha habilitado la cámara UVC (clase de vídeo USB) como origen de entrada. Consulte el [GitHub de desarrollo avanzado](https://github.com/microsoft/azure-percept-advanced-development/tree/main/azureeyemodule#using-uvcusb-video-class-camera-as-input-source) para obtener información sobre cómo usar una cámara UVC como origen de entrada. 
    - Se ha corregido el bloqueo del módulo al usar la transmisión RTSP sin formato H.264.

## <a name="june-2106-release"></a>Versión de junio (2106)

- Azureyemodule
    - Esta versión incorpora la compatibilidad con la alineación temporal de la inferencias de redes neuronales lentas con las secuencias de vídeo. Agregará una latencia en la secuencia de vídeo equivalente aproximadamente a la latencia de la red neuronal, pero provocará que las inferencias (por ejemplo, cuadros de límite) se dibujen sobre el vídeo en las ubicaciones adecuadas. 
    - Para habilitar esta característica, agregue `TimeAlignRTSP: true` a su módulo gemelo en el portal de Azure IoT Hub.
- Azureearspeechclientmodule
    - Se ha integrado el [SDK de voz 1.16](../cognitive-services/speech-service/devices-sdk-release-notes.md) en el módulo de voz, que incluye correcciones de la compatibilidad con tokens de voz e integra EAR SOM como dispositivo compatible predeterminado en la biblioteca de nivel bajo.
    - Se ha corregido un problema de detección de PnP mientras se quitaba el MCU de seguridad, pero no el códec.
    - Se han solucionado los tiempos de espera del servicio de voz con funciones de botón PTT/PTS.
    - Revisiones de seguridad
        - Lectura fuera de los límites al recibir datos TLS en el módulo de voz.
        - Los puertos USB de MCU y códec se exponen de nuevo al ejecutar la DFU.
        - Control de excepciones al analizar JSON.
        - Habilitación de todas las marcas de seguridad de compilador con protección.
        - Lectura fuera de los límites al recibir datos TLS en el módulo de voz.
        - Control de versiones de las dependencias SSL y libcurl y prevención de versiones vulnerables.
        - Aplicación de HTTPS y asignación de un CA TLS en conexiones cURL.

## <a name="april-2104-release"></a>Versión de abril (2104)

- Azureyemodule
    - Se ha corregido el formato de los mensajes de IoT Hub para que sea JSON con codificación UTF-8 (anteriormente era un formato codificado de 64 bits).
    - Se ha corregido un error con un clasificador de Custom Vision. Anteriormente, los modelos de clasificador de Custom Vision no funcionaban correctamente: interpretaban la dimensión incorrecta del tensor de salida como confianzas de clase, lo que provocaba que se predijera siempre una sola clase, independientemente de la confianza.
    - Se ha actualizado H.264 para que use TCP en lugar de UDP para la integración del analizador de vídeo de Azure.

## <a name="next-steps"></a>Pasos siguientes

- [Procedimiento para determinar una estrategia de actualización](./how-to-determine-your-update-strategy.md)