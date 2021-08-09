---
title: Procedimientos recomendados para el desarrollo de dispositivos en Azure IoT Central | Microsoft Docs
description: En este artículo se describen los procedimientos recomendados para la conectividad de dispositivos en Azure IoT Central.
author: dominicbetts
ms.author: dobett
ms.date: 03/03/2021
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.custom:
- device-developer
ms.openlocfilehash: a3cbfa17d3b063ddcef90820dc31a080a768cbcd
ms.sourcegitcommit: bb9a6c6e9e07e6011bb6c386003573db5c1a4810
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/26/2021
ms.locfileid: "110493769"
---
# <a name="best-practices-for-device-development"></a>Procedimientos recomendados para el desarrollo de dispositivos

Estas recomendaciones muestran cómo implementar dispositivos para aprovechar las ventajas de la recuperación ante desastres integrada y el escalado automático en IoT Central.

En la lista siguiente, se muestra el flujo general cuando un dispositivo se conecta a IoT Central:

1. Use DPS para aprovisionar el dispositivo y obtener una cadena de conexión de dispositivo.

1. Use la cadena de conexión para conectar el punto de conexión de IoT Hub interno de IoT Central. Envíe y reciba datos de la aplicación de IoT Central.

1. Si el dispositivo obtiene errores de conexión, en función del tipo de error, reintente la conexión o vuelva a aprovisionar el dispositivo.

## <a name="use-dps-to-provision-the-device"></a>Uso de DPS para aprovisionar el dispositivo

Para aprovisionar un dispositivo con DPS, use el id. de ámbito, las credenciales y el id. de dispositivo de la aplicación de IoT Central. Para obtener más información sobre los tipos de credenciales, vea [Inscripción de grupos X.509](concepts-get-connected.md#x509-group-enrollment) e [Inscripción de grupos SAS](concepts-get-connected.md#sas-group-enrollment). Para obtener más información sobre los identificadores de dispositivo, vea [Registro de dispositivos](concepts-get-connected.md#device-registration).

Si se ejecuta correctamente, DPS devuelve una cadena de conexión que el dispositivo puede usar para conectarse a la aplicación de IoT Central. Para solucionar los errores de aprovisionamiento, vea [Comprobación del estado de aprovisionamiento del dispositivo](troubleshoot-connection.md#check-the-provisioning-status-of-your-device).

El dispositivo puede almacenar en caché la cadena de conexión para usarla en conexiones posteriores. Pero el dispositivo debe estar preparado para [controlar los errores de conexión](#handle-connection-failures).

## <a name="connect-to-iot-central"></a>Conexión a IoT Central

Use la cadena de conexión para conectar el punto de conexión de IoT Hub interno de IoT Central. La conexión permite enviar telemetría a la aplicación de IoT Central, sincronizar los valores de propiedad con ella y responder a los comandos enviados que envía.

## <a name="handle-connection-failures"></a>Control de errores de conexión

Con fines de escalado o recuperación ante desastres, IoT Central puede actualizar su centro de IoT subyacente. Para mantener la conectividad, el código del dispositivo debe controlar los errores de conexión específicos mediante el establecimiento de una conexión con el nuevo punto de conexión de IoT Hub.

Si el dispositivo obtiene alguno de los errores siguientes al conectarse, debe rehacer el paso de aprovisionamiento con DPS para obtener una nueva cadena de conexión. Estos errores significan que la cadena de conexión que usa el dispositivo ya no es válida:

- Punto de conexión de IoT Hub inaccesible.
- Token de seguridad expirado.
- Dispositivo deshabilitado en IoT Hub.

Si el dispositivo obtiene alguno de los errores siguientes al conectarse, debe usar una estrategia de retroceso para volver a intentar la conexión. Estos errores significan que la cadena de conexión que usa el dispositivo sigue siendo válida, pero hay condiciones transitorias que impiden que el dispositivo se conecte:

- Dispositivo bloqueado por el operador.
- Error interno 500 del servicio.

Para obtener más información sobre los códigos de error de los dispositivos, vea [Solución de problemas de conexiones de dispositivos](troubleshoot-connection.md).

## <a name="test-failover-capabilities"></a>Prueba de las funcionalidades de conmutación por error

La CLI de Azure permite probar las funcionalidades de conmutación por error del código de cliente del dispositivo. El comando de la CLI funciona cambiando temporalmente un registro de dispositivo a un centro de IoT interno diferente. Puede comprobar que la conmutación por error del dispositivo funcionó comprobando que el dispositivo sigue enviando telemetría y respondiendo a los comandos de la aplicación IoT Central.

Para ejecutar la prueba de conmutación por error del dispositivo, ejecute el siguiente comando:

```azurecli
az iot central device manual-failover \
    --app-id {Application ID of your IoT Central application} \
    --device-id {Device ID of the device you're testing} \
    --ttl-minutes {How to wait before moving the device back to it's original IoT hub}
```

> [!TIP]
> Para buscar el **identificador de la aplicación**, vaya a **Administración > Su aplicación** en su aplicación IoT Central.

Si el comando se ejecuta correctamente, verá una salida similar a la siguiente:

```output
Command group 'iot central device' is in preview and under development. Reference and support levels: https://aka.ms/CLI_refstatus
{
  "hubIdentifier": "6bd4...bafa",
  "message": "Success! This device is now being failed over. You can check your device'’'s status using 'iot central device registration-info' command. The device will revert to its original hub at Tue, 18 May 2021 11:03:45 GMT. You can choose to failback earlier using device-manual-failback command. Learn more: https://aka.ms/iotc-device-test"
}
```

Para más información sobre el comando de la CLI, consulte [az iot central device manual-failover](/cli/azure/iot/central/device#az_iot_central_device_manual_failover).

Ahora puede comprobar que la telemetría del dispositivo sigue llegando a su aplicación IoT Central.

Para ver código de dispositivo de ejemplo que controla las conmutaciones por error en varios lenguajes de programación, consulte [Clientes de alta disponibilidad de IoT](https://github.com/iot-for-all/iot-central-high-availability-clients).

## <a name="next-steps"></a>Pasos siguientes

Algunos pasos siguientes sugeridos son:

- Revisar algún código de ejemplo que muestre cómo usar los tokens de SAS en [Tutorial: Creación y conexión de una aplicación cliente a una aplicación de Azure IoT Central](tutorial-connect-device.md)
- Aprender el [procedimiento para conectar dispositivos con certificados X.509 mediante el SDK de dispositivo de Node.js para la aplicación de IoT Central](how-to-connect-devices-x509.md)
- Aprender a [supervisar la conectividad de dispositivos mediante la CLI de Azure](./howto-monitor-devices-azure-cli.md)
- Aprender a [definir un nuevo tipo de dispositivo IoT en la aplicación de Azure IoT Central](./howto-set-up-template.md)
- Leer sobre los [dispositivos de Azure IoT Edge y Azure IoT Central](./concepts-iot-edge.md)
