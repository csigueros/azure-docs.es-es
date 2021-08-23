---
author: fvneerden
ms.service: azure-video-analyzer
ms.topic: include
ms.date: 05/05/2021
ms.author: faneerde
ms.openlocfilehash: 9544a69da1c8ba0e3c2fd248a0bbbc7cbc13206f
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/22/2021
ms.locfileid: "114594340"
---
Para ejecutar el código de ejemplo, siga estos pasos:

1. En Visual Studio Code, abra la pestaña **Extensiones** (o presione Ctrl + Mayús + X) y busque Azure IoT Hub.
1. Haga clic con el botón derecho y seleccione la **Configuración de la extensión**.

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="../../../media/vscode-common-screenshots/extension-settings.png" alt-text="Configuración de la extensión":::

1. Busque y habilite "Show Verbose Message" (Mostrar mensaje detallado).

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="../../../media/vscode-common-screenshots/verbose-message.png" alt-text="Show Verbose Message"::: (Mostrar mensaje detallado)

1. En Visual Studio Code, vaya a _src/cloud-to-device-console-app/operations.json_.
1. En el nodo `pipelineTopologySet`, asegúrese de que se muestra el valor siguiente:

   ```
   "pipelineTopologyUrl" : "https://raw.githubusercontent.com/Azure/video-analyzer/main/pipelines/live/topologies/motion-detection/topology.json"
   ```

1. En los nodos `livePipelineSet` y `livePipelineDelete`, asegúrese de que el valor de **topologyName** coincida con el valor de la propiedad **name** en la topología de la canalización:

   `"topologyName" : "MotionDetection"`

1. * Desplazamiento a la ventana `TERMINAL` en VS Code
   * Use el comando cd para ir al directorio /video-analyzer-iot-edge-python-main/src/cloud-to-device-console-app
   * Ejecute "python main.py" y verá los mensajes impresos en la ventana `TERMINAL`
1. El archivo _operations.json_ se inicia con llamadas a `pipelineTopologyList` y `livePipelineList`. Si limpió los recursos al finalizar los inicios rápidos anteriores, este proceso devolverá listas vacías.

   ```
   -----------------------  Request: pipelineTopologyList  --------------------------------------------------

   {
   "@apiVersion": "1.0"
   }

   ---------------  Response: pipelineTopologyList - Status: 200  ---------------

   {
     "value": []
   }

   --------------------------------------------------------------------------

   ```

   La ventana **TERMINAL** muestra el siguiente conjunto de llamadas al método directo:

   - Una llamada a `pipelineTopologySet` que utiliza el nodo pipelineTopologyUrl anterior.
   - Una llamada a `livePipelineSet` que usa el cuerpo siguiente:

   ```json
   {
     "@apiVersion": "1.0",
     "name": "Sample-Pipeline-1",
     "properties": {
       "topologyName": "MotionDetection",
       "description": "Sample pipeline description",
       "parameters": [
         {
           "name": "rtspUrl",
           "value": "rtsp://rtspsim:554/media/camera-300s.mkv"
         },
         {
           "name": "rtspUserName",
           "value": "testuser"
         },
         {
           "name": "rtspPassword",
           "value": "testpassword"
         }
       ]
     }
   }
   ```

   - Una llamada a `livePipelineActivate` que activa la canalización en directo y el flujo de vídeo.
   - Una segunda llamada a `livePipelineList` que muestra que la canalización en directo está en estado de ejecución.

1. La salida de la ventana **TERMINAL** se pone en pausa en `Press Enter to continue`. No seleccione Entrar todavía. Desplácese hacia arriba para ver las cargas de la respuesta JSON para los métodos directos que ha invocado.
1. Cambie a la ventana **SALIDA** de Visual Studio Code. Verá los mensajes que el módulo de Azure Video Analyzer está enviando al centro de IoT. En la siguiente sección de este inicio rápido se analizan estos mensajes.
1. Prosigue la canalización en directo y se imprimen los resultados. El simulador RTSP sigue recorriendo el vídeo de origen. Para detener la canalización en directo, vuelva a la ventana **TERMINAL** y seleccione Entrar.

   La siguiente serie de llamadas limpia los recursos:

   - Una llamada a `livePipelineDeactivate` desactiva la canalización.
   - Una llamada a `livePipelineDelete` elimina la canalización.
   - Una llamada a `pipelineTopologyDelete` elimina la topología.
   - Una llamada final a `pipelineTopologyList` muestra que la lista está vacía.
