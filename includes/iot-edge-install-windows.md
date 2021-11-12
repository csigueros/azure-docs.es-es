---
ms.topic: include
ms.date: 10/29/2021
author: kgremban
ms.author: kgremban
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 94b115654d50fcc7924d86afcc9b78ddab05688d
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/05/2021
ms.locfileid: "131845118"
---
## <a name="install-iot-edge"></a>Instalación de IoT Edge

En esta sección, preparará la VM o el dispositivo físico Windows para IoT Edge. A continuación, instalará IoT Edge.

Azure IoT Edge utiliza un runtime de contenedor compatible con OCI. [Moby](https://github.com/moby/moby), un motor basado en Moby, se incluye en el script de instalación, lo que significa que no hay ningún paso adicional para instalar el motor.

Para instalar el entorno de ejecución de IoT Edge:

1. Ejecute PowerShell como administrador.

   Use una sesión de AMD64 de PowerShell, no PowerShell (x86). Si no está seguro del tipo de sesión que usa, ejecute el comando siguiente:

   ```powershell
   (Get-Process -Id $PID).StartInfo.EnvironmentVariables["PROCESSOR_ARCHITECTURE"]
   ```

2. Ejecute el comando [Deploy-IoTEdge](../articles/iot-edge/reference-windows-scripts.md#deploy-iotedge), que realiza las tareas siguientes:

   * Comprueba que la máquina Windows se encuentra en una versión compatible.
   * Activa la característica de los contenedores.
   * Descarga el motor de Moby y el entorno de ejecución de IoT Edge.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Deploy-IoTEdge
   ```

3. Si se le solicita, reinicie el dispositivo.

Al instalar IoT Edge en un dispositivo, puede usar parámetros adicionales para modificar el proceso, entre los que se incluyen los siguientes:

* Dirigir el tráfico para que pase por un servidor proxy
* Haga que el instalador apunte a un directorio local, en el caso de una instalación sin conexión.

Para más información sobre estos parámetros adicionales, consulte [Scripts de PowerShell para IoT Edge con contenedores Windows](../articles/iot-edge/reference-windows-scripts.md).
