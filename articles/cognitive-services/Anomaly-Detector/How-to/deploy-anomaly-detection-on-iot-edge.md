---
title: Ejecución de Anomaly Detector en IoT Edge
titleSuffix: Azure Cognitive Services
description: Implemente el módulo Anomaly Detector en IoT Edge.
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: conceptual
ms.date: 12/03/2020
ms.author: mbullwin
ms.openlocfilehash: 0fd02fbdd8f6c02ce86a4e0c1ee3eabec69e56c3
ms.sourcegitcommit: 6ea4d4d1cfc913aef3927bef9e10b8443450e663
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/05/2021
ms.locfileid: "113296728"
---
# <a name="deploy-an-anomaly-detector-univariate-module-to-iot-edge"></a>Implementación del módulo univariante de Anomaly Detector en IoT Edge

Aprenda a implementar el módulo [Anomaly Detector](../anomaly-detector-container-howto.md) de Cognitive Services en un dispositivo de IoT Edge. Una vez que implementa el módulo en IoT Edge, se ejecuta en IoT Edge junto con otros módulos como instancias de contenedor. Expone exactamente las mismas API que una instancia de contenedor de Anomaly Detector que se ejecute en un entorno estándar de contenedor de Docker. 

## <a name="prerequisites"></a>Requisitos previos

* Use una suscripción de Azure. Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free) antes de empezar.
* Instale la [CLI de Azure](/cli/azure/install-azure-cli).
* Una instancia de [IoT Hub](../../../iot-hub/iot-hub-create-through-portal.md) y un dispositivo [IoT Edge](../../../iot-edge/quickstart-linux.md).

[!INCLUDE [Create a Cognitive Services Anomaly Detector resource](../includes/create-anomaly-detector-resource.md)]

## <a name="deploy-the-anomaly-detection-module-to-the-edge"></a>Implementación del módulo Anomaly Detector en el borde

1. En Azure Portal, escriba **Anomaly Detector en IoT Edge** en la búsqueda y abra el resultado de Azure Marketplace.
2. Le llevará a la página [Dispositivos de destino para el módulo de IoT Edge](https://portal.azure.com/#create/azure-cognitive-service.edge-anomaly-detector) de Azure Portal. Proporcione la siguiente información requerida:

    1. Seleccione su suscripción.

    1. Seleccione su instancia de IoT Hub.

    1. Seleccione **Buscar dispositivo** y busque un dispositivo IoT Edge.

3. Seleccione el botón **Crear**.

4. Seleccione el módulo **AnomalyDetectoronIoTEdge**.

    :::image type="content" source="../media/deploy-anomaly-detection-on-iot-edge/iot-edge-modules.png" alt-text="Imagen de la interfaz de usuario de Módulos de IoT Edge con el vínculo AnomalyDetectoronIoTEdge resaltado con un cuadro rojo para indicar que es el elemento que se debe seleccionar.":::

5. Vaya a **Variables de entorno** e indique la siguiente información.

    1.  Mantenga el valor accept para **Eula**.

    1. Rellene la parte correspondiente a **Billing** con el punto de conexión de Cognitive Services.

    1. Rellene la parte correspondiente a **ApiKey** con la clave de API de Cognitive Services.

    :::image type="content" source="../media/deploy-anomaly-detection-on-iot-edge/environment-variables.png" alt-text="Variables de entorno con cuadros rojos alrededor de las áreas donde deben rellenarse los valores para el punto de conexión y la clave de API.":::

6. Seleccione **Update** (Actualizar).

7. Seleccione **Siguiente: Rutas** para definir la ruta. Especifique que todos los mensajes de todos los módulos vayan a Azure IoT Hub.

8. Seleccione **Siguiente: Revisar y crear**. Podemos obtener una vista previa del archivo JSON que define todos los módulos que se van a implementar en nuestro dispositivo IoT Edge.
    
9. Seleccione **Crear** para iniciar la implementación de módulos.

10. Una vez completada la implementación del módulo, volveremos a la página IoT Edge de nuestra instancia de IoT Hub. Seleccione el dispositivo en la lista de dispositivos IoT Edge para ver los detalles correspondientes.

11. Desplácese hacia abajo y vea los módulos que aparecen. Compruebe que el estado de tiempo de ejecución esté en ejecución para el nuevo módulo. 

Para solucionar los problemas de estado de tiempo de ejecución del dispositivo IoT Edge, consulte la [guía de solución de problemas](../../../iot-edge/troubleshoot.md).

## <a name="test-anomaly-detector-on-an-iot-edge-device"></a>Prueba de Anomaly Detector en un dispositivo IoT Edge

Haremos una llamada HTTP al dispositivo Azure IoT Edge que tenga el contenedor de Azure Cognitive Services en ejecución. El contenedor proporciona API de punto de conexión basadas en REST. Use el host, `http://<your-edge-device-ipaddress>:5000`, para las API del módulo.

Si el dispositivo perimetral no permite ya la comunicación entrante en el puerto 5000, tendrá que crear una nueva **regla de puerto de entrada**. 

En el caso de una VM de Azure, esto puede establecerse en **Máquina virtual** > **Configuración** > **Redes** > **Regla de puerto de entrada** > **Agregar regla de puerto de entrada**.

Hay varias maneras de validar que el módulo esté en ejecución. Busque la dirección *IP externa* y el puerto expuesto del dispositivo perimetral en cuestión y abra el explorador web que prefiera. Use las distintas direcciones URL de solicitud para validar que el contenedor se está ejecutando. Las direcciones URL de solicitud de ejemplo que se enumeran a continuación son `http://<your-edge-device-ipaddress:5000`, pero el contenedor específico puede variar. Tenga en cuenta que debe usar la dirección *IP externa* del dispositivo perimetral.

| URL de la solicitud | Propósito |
|:-------------|:---------|
| `http://<your-edge-device-ipaddress>:5000/` | El contenedor proporciona una página principal. |
| `http://<your-edge-device-ipaddress>:5000/status` | También se solicita con GET y comprueba si el valor de api-key usado para iniciar el contenedor es válido sin generar una consulta de punto de conexión. Esta solicitud se puede usar con los [sondeos de ejecución y preparación](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/) de Kubernetes. |
| `http://<your-edge-device-ipaddress>:5000/swagger` | El contenedor cuenta con un completo conjunto de documentación sobre los puntos de conexión y una característica de **prueba**. Esta característica le permite especificar la configuración en un formulario HTML basado en web y realizar la consulta sin necesidad de escribir código. Una vez que la consulta devuelve resultados, se proporciona un ejemplo del comando CURL para mostrar los encabezados HTTP y el formato de cuerpo requeridos. |

![Página principal del contenedor](../../../../includes/media/cognitive-services-containers-api-documentation/container-webpage.png)

## <a name="next-steps"></a>Pasos siguientes

* Revise [Instalación y ejecución de contenedores](../anomaly-detector-container-configuration.md) para extraer la imagen del contenedor y ejecutarlo.
* Revise [Configure containers](../anomaly-detector-container-configuration.md) (Configuración de contenedores) para ver las opciones de configuración.
* [Más información sobre el servicio de API Anomaly Detector](https://go.microsoft.com/fwlink/?linkid=2080698&clcid=0x409)
