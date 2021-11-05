---
author: fvneerden
ms.service: azure-video-analyzer
ms.topic: include
ms.date: 11/04/2021
ms.author: faneerde
ms.openlocfilehash: 729a3d27bb64edabc8880753eeb0ac54f6c906a8
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/05/2021
ms.locfileid: "131861106"
---
1. En Visual Studio Code, vaya a *src/edge*. Vará el archivo *.env* y algunos archivos de plantilla de implementación.

    La plantilla de implementación hace referencia al manifiesto de implementación del dispositivo perimetral, en el que las variables se utilizan para algunas propiedades. El archivo *.env* contiene los valores de esas variables.
1. Vaya a la carpeta *src/cloud-to-device-console-app*. Aquí verá el archivo *appsettings.json* y algunos otros archivos:

    * ***operations.json***: una lista de las operaciones que desea que ejecute el programa.
    * **main.py**: código del programa de ejemplo. Este código:

      * Carga la configuración de la aplicación.
      * Invoca métodos directos que expone el módulo de Azure Video Analyzer. Puede usar el módulo para analizar secuencias de vídeo en directo mediante la invocación de sus [métodos directos](../../../direct-methods.md).
      * Se pone en pausa para que pueda examinar la salida del programa en la ventana **TERMINAL** y los eventos generados por el módulo en la ventana de **SALIDA**.
      * Invoca los métodos directos para limpiar los recursos.

