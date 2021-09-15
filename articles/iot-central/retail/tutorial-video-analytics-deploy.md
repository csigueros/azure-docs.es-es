---
title: 'Tutorial: Análisis de vídeo de Azure IoT: detección de movimiento y objetos | Microsoft Docs'
description: En este tutorial se muestra cómo implementar y usar la plantilla de aplicación de detección de objetos y movimiento en el análisis de vídeo para IoT Central.
services: iot-central
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: tutorial
ms.author: nandab
author: KishorIoT
ms.date: 09/02/2021
ms.openlocfilehash: 026c1794f678c5d194b0e5174986f9f962508647
ms.sourcegitcommit: e8b229b3ef22068c5e7cd294785532e144b7a45a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/04/2021
ms.locfileid: "123473755"
---
# <a name="tutorial-deploy-and-walk-through-the-video-analytics---object-and-motion-detection-application-template"></a>Tutorial: Implementación y tutorial de la plantilla de aplicación de detección de objetos y movimiento en el análisis de vídeo

Para obtener información general de la aplicación de *detección de objetos y movimiento para el análisis de vídeo*. La plantilla de aplicación de **detección de objetos y movimiento para el análisis de vídeo** permite compilar soluciones de IoT, entre las que se incluyen las funcionalidades de análisis de vídeo en directo.

:::image type="content" source="media/architecture-video-analytics/architecture.png" alt-text="Diagrama de información general sobre los componentes de detección de movimiento y objetos de análisis de vídeo.":::

Los componentes clave de la solución de análisis de vídeo incluyen:

### <a name="live-video-analytics-lva"></a>Live Video Analytics (LVA)

LVA ofrece una plataforma para compilar aplicaciones de vídeo inteligentes que abarquen el entorno perimetral y la nube. La plataforma permite compilar aplicaciones de vídeo inteligentes que abarquen el perímetro y la nube. La plataforma ofrece la funcionalidad de capturar, grabar, analizar vídeo en directo y publicar los resultados, que podrían ser análisis de vídeo o vídeo, en los servicios de Azure. Los servicios de Azure podrían ejecutarse en la nube o en el dispositivo perimetral. La plataforma se puede usar para mejorar las soluciones de IoT con análisis de vídeo.

Para más información, consulte [Live Video Analytics](https://github.com/Azure/live-video-analytics) en GitHub.

### <a name="iot-edge-lva-gateway-module"></a>Módulo de la puerta de enlace para LVA de IoT Edge

El módulo de la puerta de enlace para LVA de IoT Edge crea instancias de las cámaras como dispositivos nuevos y los conecta directamente a IoT Central mediante el SDK de cliente de dispositivo IoT.

En esta implementación de referencia, los dispositivos se conectan a la solución mediante claves simétricas del dispositivo perimetral. Para más información sobre la conectividad de los dispositivos, consulte [Conexión a Azure IoT Central](../core/concepts-get-connected.md).

### <a name="media-graph"></a>Grafo multimedia

El grafo multimedia permite definir dónde se debe capturar el contenido multimedia, cómo procesarlo y dónde se entregan los resultados. Conecte componentes o nodos de la manera deseada para configurar este grafo multimedia. Para más información, consulte [Grafos multimedia](https://github.com/Azure/live-video-analytics/tree/master/MediaGraph) en GitHub.

En el siguiente vídeo se ofrece un tutorial sobre cómo usar la _plantilla de aplicación de análisis de vídeo y detección de movimiento y objetos_ para implementar una solución IoT Central:

> [!VIDEO https://www.youtube.com/embed/Bo3FziU9bSA]

En este conjunto de tutoriales, aprenderá:

> [!div class="checklist"]
> * Implementación de la aplicación
> * Implementación de una instancia de IoT Edge que se conecta a la aplicación
> * Supervisión y administración de la aplicación

## <a name="prerequisites"></a>Prerrequisitos

* No se necesitan requisitos previos específicos para implementar esta aplicación.
* Puede usar el plan gratuito o una suscripción de Azure.

## <a name="deploy-the-application"></a>Implementación de la aplicación

Para implementar una aplicación de IoT Central mediante la plantilla de análisis de vídeo, complete los pasos siguientes:

1. Complete el tutorial [Creación de una aplicación de análisis de vídeo en Azure IoT Central (YOLO v3)](tutorial-video-analytics-create-app-yolo-v3.md) o [Creación de un análisis de vídeo en Azure IoT Central (OpenVINO&trade;)](tutorial-video-analytics-create-app-openvino.md) para:
    - Creación de una cuenta de Azure Media Services.
    - Crear la aplicación de IoT Central a partir de la plantilla de la aplicación Video analytics - object and motion detection.
    - Configurar un dispositivo de puerta de enlace a la aplicación de IoT Central. La puerta de enlace permite que los dispositivos de cámara se conecten a la aplicación.

1. Complete el tutorial [Creación de una instancia de IoT Edge para análisis de vídeo (máquina virtual Linux)](tutorial-video-analytics-iot-edge-vm.md) o [: Creación de una instancia de IoT Edge para análisis de vídeo (Intel NUC)](tutorial-video-analytics-iot-edge-nuc.md) para:
    - Crear una máquina virtual de Azure con el entorno de ejecución de Azure IoT Edge instalado. Prepare la instalación de IoT Edge para hospedar el módulo de análisis de vídeo.
    - Conecte el dispositivo de IoT Edge a la aplicación de IoT Central.

1. Complete el tutorial [Supervisión y administración de una aplicación de análisis de vídeo](tutorial-video-analytics-manage.md) para:
    - Agregar cámaras de detección de objetos y movimiento a la puerta de enlace de la aplicación de IoT Central.
    - Iniciar el procesamiento de la cámara.
    - Instalar un reproductor multimedia local para ver el vídeo capturado en AMS.
    - Ver el vídeo capturado que muestra los objetos detectados.
    - Ordenar.

## <a name="clean-up-resources"></a>Limpieza de recursos

Cuando haya terminado con la aplicación, puede eliminar todos los recursos que haya creado de la siguiente manera:

1. En la aplicación de IoT Central, vaya a la página **Your application** (Su aplicación) en la sección **Administration** (Administración). A continuación, seleccione **Eliminar**.
1. En Azure Portal, elimine el grupo de recursos **lva-rg**.
1. En el equipo local, detenga el contenedor de Docker **amp-viewer**.

## <a name="next-steps"></a>Pasos siguientes

Ahora tiene una visión general de los pasos necesarios para implementar y usar la plantilla de aplicación de análisis de vídeo. Consulte:

> [!div class="nextstepaction"]
> [Creación de una aplicación de análisis de vídeo en Azure IoT Central (YOLO V3)](tutorial-video-analytics-create-app-yolo-v3.md) o

> [!div class="nextstepaction"]
> [Creación de un análisis de vídeo en Azure IoT Central (OpenVINO&trade;)](tutorial-video-analytics-create-app-openvino.md) para comenzar.