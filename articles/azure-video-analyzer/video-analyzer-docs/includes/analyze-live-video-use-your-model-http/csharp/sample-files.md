---
author: Juliako
ms.service: azure-video-analyzer
ms.topic: include
ms.date: 04/07/2021
ms.author: juliako
ms.openlocfilehash: 48168a651cb87af56b114821e029e1bba3b3eb4b
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/22/2021
ms.locfileid: "114465697"
---
Como parte de los requisitos previos, ha descargado el código de ejemplo en una carpeta. Siga estos pasos para examinar y editar los archivos de ejemplo.

1. En Visual Studio Code, vaya a *src/edge*. Puede ver el archivo *.env* y algunos archivos de plantilla de implementación.
La plantilla de implementación hace referencia al manifiesto de implementación del dispositivo perimetral. Incluye algunos valores de marcador de posición. El archivo .env incluye los valores de esas variables.
1. Vaya a la carpeta *src/cloud-to-device-console-app*. Aquí verá tanto su archivo *appsettings.json* como otros archivos:
    
    * c2d-console-app.csproj: el archivo de proyecto de Visual Studio Code.
    * operations.json: una lista de las operaciones que desea que ejecute el programa.
    * Program.cs: el código del programa de ejemplo. Este código:
    
        * Carga la configuración de la aplicación.
        * Invoca métodos directos que expone el módulo de Azure Video Analyzer. Puede usar el módulo para analizar secuencias de vídeo en directo mediante la invocación de sus métodos directos.
        * Se pone en pausa para que pueda examinar la salida del programa en la ventana **TERMINAL** y los eventos generados por el módulo en la ventana **SALIDA**.
        * Invoca los métodos directos para limpiar los recursos.
1. Edite el archivo operations.json:

    * Cambio del vínculo a la topología de la canalización<br/>`"pipelineTopologyUrl" : "https://raw.githubusercontent.com/Azure/video-analyzer/main/pipelines/live/topologies/motion-with-httpExtension/topology.json"`
    * En livePipelineSet, edite el nombre de la topología de la canalización para que coincida con el valor del vínculo anterior:<br/>`"pipelineTopologyName" : "EVROnMotionPlusHttpExtension"`
    * En PipelineTopologyDelete, edite el nombre:<br/>`"name": "EVROnMotionPlusHttpExtension"`
    
