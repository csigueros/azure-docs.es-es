---
ms.openlocfilehash: 7b173a5fe639ff21cc8a475edd16d0e3885dacef
ms.sourcegitcommit: 6bd31ec35ac44d79debfe98a3ef32fb3522e3934
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/02/2021
ms.locfileid: "113280191"
---
Para ejecutar el código de ejemplo, siga estos pasos:

1. En Visual Studio Code, abra la pestaña **Extensiones** (o presione Ctrl + Mayús + X) y busque Azure IoT Hub.
1. Haga clic con el botón derecho y seleccione la **Configuración de la extensión**.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="../../../media/run-program/extensions-tab.png" alt-text="Configuración de la extensión":::
1. Busque y habilite "Show Verbose Message" (Mostrar mensaje detallado).

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="../../../media/run-program/show-verbose-message.png" alt-text="Show Verbose Message"::: (Mostrar mensaje detallado)
1. En Visual Studio Code, vaya a *src/cloud-to-device-console-app/operations.json*.
1. En el nodo **GraphTopologySet**, asegúrese de que vea el siguiente valor:

    `"topologyUrl" : "https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/motion-detection/2.0/topology.json"`
1. En los nodos **GraphInstanceSet** y **GraphTopologyDelete**, asegúrese de que el valor de `topologyName` coincida con el valor de la propiedad `name` en la topología del grafo:

    `"topologyName" : "MotionDetection"`
    
1. Para iniciar una sesión de depuración, seleccione la tecla F5. La ventana **TERMINAL** mostrará algunos mensajes.

    Al iniciar una sesión de depuración con F5, la primera vez se le pedirá el tipo de entorno y el proyecto. Esto crea y configura el archivo launch.json la carpeta. Use lo siguiente para los fines de esta demostración:
    * Entorno: .Net Core
    * Proyecto: c2d-console-app
    
    Edite el archivo launch.json después de crearlo. Cambie la configuración de la consola a "integratedTeminal".
    
    `"console": "integratedTerminal"`
1. El archivo *operations.json* se inicia con llamadas a `GraphTopologyList` y `GraphInstanceList`. Si ha limpiado los recursos tras haber finalizado los inicios rápidos anteriores, este proceso devolverá listas vacías y, después, se pausará. Para continuar, seleccione la tecla Entrar.

    ```
    --------------------------------------------------------------------------
    Executing operation GraphTopologyList
    -----------------------  Request: GraphTopologyList  --------------------------------------------------
    {
        "@apiVersion": "2.0"
    }
    ---------------  Response: GraphTopologyList - Status: 200  ---------------
    {
        "value": []
    }
    --------------------------------------------------------------------------
    Executing operation WaitForInput
    Press Enter to continue
    ```
    
    La ventana **TERMINAL** muestra el siguiente conjunto de llamadas al método directo:
     * Una llamada a `GraphTopologySet` que utiliza la instancia anterior de `topologyUrl`.
     * Una llamada a `GraphInstanceSet` que usa el cuerpo siguiente:
         
    ```
    {
      "@apiVersion": "2.0",
      "name": "Sample-Graph",
      "properties": {
        "topologyName": "MotionDetection",
        "description": "Sample graph description",
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
     
    * Una llamada a `GraphInstanceActivate` que inicia la instancia del grafo y el flujo de vídeo.
    * Una segunda llamada a `GraphInstanceList` que muestra que la instancia del grafo está en ejecución.
1. La salida de la ventana **TERMINAL** se pone en pausa en `Press Enter to continue`. No seleccione Entrar todavía. Desplácese hacia arriba para ver las cargas de la respuesta JSON para los métodos directos que ha invocado.
1. Cambie a la ventana **SALIDA** de Visual Studio Code. Verá los mensajes que el módulo Live Video Analytics en IoT Edge envía al centro de IoT. En la siguiente sección de este inicio rápido se analizan estos mensajes.
1. El grafo multimedia continúa ejecutándose e imprimiendo los resultados. El simulador RTSP sigue recorriendo el vídeo de origen. Para detener el grafo multimedia, vuelva a la ventana **TERMINAL** y seleccione Entrar. 

    La siguiente serie de llamadas limpia los recursos:

    * Una llamada a `GraphInstanceDeactivate` desactiva la instancia del grafo.
    * Una llamada a `GraphInstanceDelete` elimina la instancia.
    * Una llamada a `GraphTopologyDelete` elimina la topología.
    * Una llamada final a `GraphTopologyList` muestra que la lista está vacía.
