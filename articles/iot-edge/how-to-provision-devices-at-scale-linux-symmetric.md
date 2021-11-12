---
title: 'Creación y aprovisionamiento de IoT Edge para dispositivos con claves simétricas en Linux: Azure IoT Edge | Microsoft Docs'
description: Uso de la atestación de clave simétrica para probar el aprovisionamiento de dispositivos Linux a gran escala para Azure IoT Edge con Device Provisioning Service
author: kgremban
ms.author: kgremban
ms.reviewer: v-tcassi
ms.date: 10/29/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 6a0b1a73c45c5c08eaf3c172900ade531c6fedda
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2021
ms.locfileid: "131505958"
---
# <a name="create-and-provision-iot-edge-devices-at-scale-on-linux-using-symmetric-key"></a>Creación y aprovisionamiento de dispositivos IoT Edge a escala en Linux mediante claves simétricas

[!INCLUDE [iot-edge-version-201806-or-202011](../../includes/iot-edge-version-201806-or-202011.md)]

En este artículo se proporcionan instrucciones de un extremo a otro para el aprovisionamiento automático de uno o varios dispositivos Linux IoT Edge mediante claves simétricas. Puede aprovisionar los dispositivos Azure IoT Edge de forma automática con [Azure IoT Hub Device Provisioning Service](../iot-dps/index.yml) (DPS). Si no está familiarizado con el proceso de aprovisionamiento automático, revise la [información general sobre el aprovisionamiento](../iot-dps/about-iot-dps.md#provisioning-process) antes de continuar.

Las tareas son las siguientes:

1. Crear una **inscripción individual** para un dispositivo o una **inscripción de grupo** para un conjunto de dispositivos.
1. Instale el entorno de ejecución de IoT Edge y conéctese a IoT Hub.

:::moniker range=">=iotedge-2020-11"
>[!TIP]
>Para una experiencia simplificada, pruebe la [herramienta de configuración de Azure IoT Edge](https://github.com/azure/iot-edge-config). Esta herramienta de línea de comandos, actualmente en versión preliminar pública, instala IoT Edge en el dispositivo y lo aprovisiona mediante DPS y la atestación de clave simétrica.
:::moniker-end

La atestación de clave simétrica es un enfoque sencillo para autenticar un dispositivo con una instancia de Device Provisioning Service. Este método de atestación representa una experiencia de "Hola mundo" para los desarrolladores que no estén familiarizados con el aprovisionamiento de dispositivos, o no tengan estrictos requisitos de seguridad. La atestación de dispositivo mediante un [TPM](../iot-dps/concepts-tpm-attestation.md) o [certificado X.509](../iot-dps/concepts-x509-attestation.md) es más segura y se debe usar cuando los requisitos de seguridad son más estrictos.

## <a name="prerequisites"></a>Prerrequisitos

<!-- Cloud resources prerequisites H3 and content -->
[!INCLUDE [iot-edge-prerequisites-at-scale-cloud-resources.md](../../includes/iot-edge-prerequisites-at-scale-cloud-resources.md)]

### <a name="iot-edge-installation"></a>Instalación de IoT Edge

Un dispositivo Linux físico o virtual para que sea el dispositivo IoT Edge.

Se debe definir un **identificador de registro** *único* para identificar cada dispositivo. Puede usar la dirección MAC, el número de serie o cualquier otra información única del dispositivo. Por ejemplo, podría usar una combinación de una dirección MAC y un número de serie que formen la siguiente cadena de un identificador de registro: `sn-007-888-abc-mac-a1-b2-c3-d4-e5-f6`. Solo se pueden usar caracteres alfanuméricos en minúsculas y guiones (`-`).

<!-- Create a DPS enrollment using symmetric keys H2 and content -->
[!INCLUDE [iot-edge-create-dps-enrollment-symmetric.md](../../includes/iot-edge-create-dps-enrollment-symmetric.md)]

<!-- Install IoT Edge on Linux H2 and content -->
[!INCLUDE [install-iot-edge-linux.md](../../includes/iot-edge-install-linux.md)]

## <a name="provision-the-device-with-its-cloud-identity"></a>Aprovisionamiento del dispositivo con su identidad de nube

Una vez que el entorno de ejecución está instalado en el dispositivo, configure el dispositivo con la información que usa para conectarse a Device Provisioning Service y a IoT Hub.

Tenga lista la siguiente información:

* El valor **Ámbito de id.** del DPS
* El **Id. de registro** del dispositivo que ha creado
* La **Clave principal** de una inscripción individual o una [clave derivada](#derive-a-device-key) para dispositivos que usan una inscripción de grupo.

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"

1. Abra el archivo de configuración en el dispositivo IoT Edge.

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

1. Busque la sección configuraciones de aprovisionamiento del archivo. Quite las marcas de comentario de las líneas del aprovisionamiento de claves simétricas de DPS y asegúrese de que cualquier otra línea de aprovisionamiento esté comentada.

   La línea `provisioning:` no debe ir precedida por espacios en blanco y se debe aplicar una sangría de dos espacios a los elementos anidados.

   ```yml
   # DPS TPM provisioning configuration
   provisioning:
     source: "dps"
     global_endpoint: "https://global.azure-devices-provisioning.net"
     scope_id: "PASTE_YOUR_SCOPE_ID_HERE"
     attestation:
       method: "symmetric_key"
       registration_id: "PASTE_YOUR_REGISTRATION_ID_HERE"
       symmetric_key: "PASTE_YOUR_PRIMARY_KEY_OR_DERIVED_KEY_HERE"
   #  always_reprovision_on_startup: true
   #  dynamic_reprovisioning: false
   ```

1. Actualice los valores de `scope_id`, `registration_id` y `symmetric_key` con la información de DPS y del dispositivo.

1. También puede usar las líneas `always_reprovision_on_startup` o `dynamic_reprovisioning` para configurar el comportamiento de reaprovisionamiento del dispositivo. Si un dispositivo se establece para que se vuelva a aprovisionar en el inicio, siempre intentará aprovisionar con DPS primero y, a continuación, revertir a la copia de seguridad de aprovisionamiento si se produce un error. Si un dispositivo se establece para que se vuelva a aprovisionar dinámicamente, IoT Edge se reiniciará y volverá a aprovisionar si se detecta un evento de reaprovisionamiento. Para más información, consulte [Conceptos sobre el reaprovisionamiento de dispositivos de IoT Hub](../iot-dps/concepts-device-reprovision.md).

1. Reinicie el entorno de ejecución de IoT Edge para que aplique todos los cambios de configuración realizados en el dispositivo.

   ```bash
   sudo systemctl restart iotedge
   ```

:::moniker-end
<!-- end 1.1 -->

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

1. Cree un archivo de configuración para el dispositivo basándose en un archivo de plantilla que se proporciona como parte de la instalación de IoT Edge.

   ```bash
   sudo cp /etc/aziot/config.toml.edge.template /etc/aziot/config.toml
   ```

1. Abra el archivo de configuración en el dispositivo IoT Edge.

   ```bash
   sudo nano /etc/aziot/config.toml
   ```

1. Busque la sección **Provisioning** (Aprovisionamiento) del archivo. Quite las marcas de comentario de las líneas del aprovisionamiento a DPS de clave simétrica y asegúrese de que cualquier otra línea de aprovisionamiento esté comentada.

   ```toml
   # DPS provisioning with symmetric key
   [provisioning]
   source = "dps"
   global_endpoint = "https://global.azure-devices-provisioning.net"
   id_scope = "PASTE_YOUR_SCOPE_ID_HERE"
   
   [provisioning.attestation]
   method = "symmetric_key"
   registration_id = "PASTE_YOUR_REGISTRATION_ID_HERE"

   symmetric_key = "PASTE_YOUR_PRIMARY_KEY_OR_DERIVED_KEY_HERE"
   ```

1. Actualice los valores de `id_scope`, `registration_id` y `symmetric_key` con la información de DPS y del dispositivo.

   El parámetro de clave simétrica puede aceptar un valor de una clave insertada, un URI de archivo o un URI de PKCS#11. Quite la marca de comentario de una sola línea de clave simétrica, en función del formato que esté usando.

   Si usa cualquier URI de PKCS#11, busque la sección **PKCS#11** en el archivo de configuración y proporcione información sobre la configuración de PKCS#11.

1. Guarde y cierre el archivo config.toml.

1. Aplique los cambios de configuración que ha realizado a IoT Edge.

   ```bash
   sudo iotedge config apply
   ```

:::moniker-end
<!-- end 1.2 -->

## <a name="verify-successful-installation"></a>Comprobación de instalación correcta

Si el entorno de ejecución se inició correctamente, puede ir a IoT Hub y empezar a implementar módulos de IoT Edge en el dispositivo.

# <a name="individual-enrollment"></a>[Inscripción individual](#tab/individual-enrollment)

Puede comprobar que la inscripción individual que ha creado en Device Provisioning Service se ha usado. En Azure Portal, vaya a la instancia de Device Provisioning Service. Abra los detalles de la inscripción para la inscripción individual que ha creado. Tenga en cuenta que el estado de la inscripción está **asignado** y se muestra el id. de dispositivo.

# <a name="group-enrollment"></a>[Inscripción de grupo](#tab/group-enrollment)

Puede comprobar que la inscripción de grupo que ha creado en Device Provisioning Service se ha usado. En Azure Portal, vaya a la instancia de Device Provisioning Service. Abra los detalles de la inscripción para la inscripción de grupo que ha creado. Vaya a la pestaña **Entradas de registro** para ver todos los dispositivos registrados en ese grupo.

---

Use los siguientes comandos en el dispositivo para comprobar que la instancia de IoT Edge se haya instalado e iniciado correctamente.

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"

Compruebe el estado del servicio IoT Edge.

```cmd/sh
systemctl status iotedge
```

Examine los registros del servicio.

```cmd/sh
journalctl -u iotedge --no-pager --no-full
```

Enumere los módulos en ejecución.

```cmd/sh
iotedge list
```

:::moniker-end

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

Compruebe el estado del servicio IoT Edge.

```cmd/sh
sudo iotedge system status
```

Examine los registros del servicio.

```cmd/sh
sudo iotedge system logs
```

Enumere los módulos en ejecución.

```cmd/sh
sudo iotedge list
```

:::moniker-end

## <a name="next-steps"></a>Pasos siguientes

El proceso de inscripción en Device Provisioning Service permite establecer el id. de dispositivo y las etiquetas del dispositivo gemelo al mismo tiempo que se aprovisiona el nuevo dispositivo. Puede usar esos valores para dirigirse a dispositivos individuales o grupos de dispositivos con la administración automática de dispositivos. Aprenda a [implementar y supervisar los módulos de IoT Edge a escala mediante Azure Portal](how-to-deploy-at-scale.md) o la [CLI de Azure](how-to-deploy-cli-at-scale.md).
