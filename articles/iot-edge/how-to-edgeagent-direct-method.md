---
title: 'Métodos directos integrados de edgeAgent: Azure IoT Edge'
description: Supervise y administre una implementación de IoT Edge mediante métodos directos integrados en el módulo de tiempo de ejecución del agente de IoT Edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 03/02/2020
ms.topic: conceptual
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: cc580bd1e7b33507f25fdb0ebec3ba38904db8bb
ms.sourcegitcommit: bd65925eb409d0c516c48494c5b97960949aee05
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/06/2021
ms.locfileid: "111541847"
---
# <a name="communicate-with-edgeagent-using-built-in-direct-methods"></a>Comunicarse con edgeAgent mediante métodos directos integrados

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Supervise y administre las implementaciones de IoT Edge con los métodos directos incluidos en el módulo IoT Edge Agent. Los métodos directos se implementan en el dispositivo y, a continuación, se pueden invocar desde la nube. El agente de IoT Edge incluye métodos directos que le ayudan a supervisar y administrar los dispositivos IoT Edge de forma remota.

Para obtener más información acerca de los métodos directos, cómo usarlos y cómo implementarlos en sus propios módulos, consulte [Descripción e invocación de los métodos directos de IoT Hub](../iot-hub/iot-hub-devguide-direct-methods.md).

Los nombres de estos métodos directos no distinguen mayúsculas de minúsculas.

## <a name="ping"></a>Ping

El método **ping** es útil para comprobar si se está ejecutando IoT Edge en un dispositivo o si el dispositivo tiene una conexión abierta a IoT Hub. Use un método directo para hacer ping al agente de IoT Edge y obtener su estado. Un ping correcto devuelve una carga vacía y **"status": 200**.

Por ejemplo:

```azurecli
az iot hub invoke-module-method --method-name 'ping' -n <hub name> -d <device name> -m '$edgeAgent'
```

En el Azure Portal, invoque el método con el nombre de método `ping` y una `{}`de carga JSON vacía.

![Invoque el método directo ' Ping ' en Azure Portal](./media/how-to-edgeagent-direct-method/ping-direct-method.png)

## <a name="restart-module"></a>Reinicie el módulo

El método **RestartModule** permite la administración remota de los módulos que se ejecutan en un dispositivo IoT Edge. Si un módulo informa un estado de error u otro comportamiento incorrecto, puede desencadenar el agente de IoT Edge para reiniciarlo. Un comando de reiniciar correcto devuelve una carga vacía y **"status": 200**.

El método RestartModule está disponible en IoT Edge versión 1.0.9 y versiones posteriores.

>[!TIP]
>La página de solución de problemas de IoT Edge en Azure Portal proporciona una experiencia simplificada para reiniciar los módulos. Para obtener más información, consulte [Supervisión y solución de problemas de dispositivos IoT Edge desde Azure Portal](troubleshoot-in-portal.md).

Puede usar el método directo RestartModule en cualquier módulo que se ejecute en un dispositivo IoT Edge, incluido el propio módulo edgeAgent. Sin embargo, si usa este método directo para cerrar el edgeAgent, no recibirá un resultado correcto, ya que la conexión se interrumpe mientras se reinicia el módulo.

Por ejemplo:

```azurecli
az iot hub invoke-module-method --method-name 'RestartModule' -n <hub name> -d <device name> -m '$edgeAgent' --method-payload \
'
    {
        "schemaVersion": "1.0",
        "id": "<module name>"
    }
'
```

En el Azure Portal, invoque el método con el nombre de método `RestartModule` y la siguiente carga útil de JSON:

```json
{
    "schemaVersion": "1.0",
    "id": "<module name>"
}
```

![Invoque un método directo ' RestartModule ' en Azure Portal](./media/how-to-edgeagent-direct-method/restartmodule-direct-method.png)

## <a name="diagnostic-direct-methods"></a>Métodos directos de diagnóstico

* [GetModuleLogs](how-to-retrieve-iot-edge-logs.md#retrieve-module-logs): Recupere los registros de módulo insertados en la respuesta del método directo.
* [UploadModuleLogs](how-to-retrieve-iot-edge-logs.md#upload-module-logs): Recupere los registros de módulo y cárguelos en Azure Blob Storage.
* [UploadSupportBundle](how-to-retrieve-iot-edge-logs.md#upload-support-bundle-diagnostics): Recupere los registros de módulo mediante un paquete de soporte y cargue un archivo zip en Azure Blob Storage.
* [GetTaskStatus](how-to-retrieve-iot-edge-logs.md#get-upload-request-status): Compruebe el estado de una solicitud de registros de carga o paquete de soporte.

Estos métodos directos de diagnóstico están disponibles a partir de la versión 1.0.10.

## <a name="next-steps"></a>Pasos siguientes

[Propiedades de los módulos gemelos del agente de IoT Edge y del centro de IoT Edge](module-edgeagent-edgehub.md)
