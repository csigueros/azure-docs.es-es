---
title: Creación y aprovisionamiento de IoT Edge para dispositivos con claves simétricas en Windows - Azure IoT Edge | Microsoft Docs
description: Uso de la atestación de clave simétrica para probar el aprovisionamiento de dispositivos Windows a escala para Azure IoT Edge con Device Provisioning Service
author: kgremban
ms.author: kgremban
ms.reviewer: v-tcassi
ms.date: 10/27/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
monikerRange: =iotedge-2018-06
ms.openlocfilehash: a1abccf4a039a36f2969e6b32b8eca2478d07427
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/05/2021
ms.locfileid: "131845955"
---
# <a name="create-and-provision-iot-edge-devices-at-scale-on-windows-using-symmetric-keys"></a>Creación y aprovisionamiento de dispositivos IoT Edge a escala en Windows mediante claves simétricas

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

En este artículo se proporcionan instrucciones completas para el aprovisionamiento automático de uno o varios dispositivos Windows IoT Edge con claves simétricas. Puede aprovisionar automáticamente los dispositivos Azure IoT Edge con [Azure IoT Hub Device Provisioning Service](../iot-dps/index.yml) (DPS). Si no está familiarizado con el proceso de aprovisionamiento automático, revise la [información general sobre el aprovisionamiento](../iot-dps/about-iot-dps.md#provisioning-process) antes de continuar.

>[!NOTE]
>Azure IoT Edge con contenedores Windows no se admitirá a partir de la versión 1.2 de Azure IoT Edge.
>
>Considere la posibilidad de usar el nuevo método para ejecutar IoT Edge en dispositivos Windows, [Azure IoT Edge para Linux en Windows](iot-edge-for-linux-on-windows.md).
>
>Si desea usar Azure IoT Edge para Linux en Windows, puede seguir los pasos de la [guía de procedimientos equivalente](how-to-provision-devices-at-scale-linux-on-windows-symmetric.md).

Las tareas son las siguientes:

1. Crear una **inscripción individual** para un dispositivo o una **inscripción de grupo** para un conjunto de dispositivos.
1. Instale el entorno de ejecución de IoT Edge y conéctese a IoT Hub.

La atestación de clave simétrica es un enfoque sencillo para autenticar un dispositivo con una instancia de Device Provisioning Service. Este método de atestación representa una experiencia de "Hola mundo" para los desarrolladores que no estén familiarizados con el aprovisionamiento de dispositivos, o no tengan estrictos requisitos de seguridad. La atestación de dispositivo mediante un [TPM](../iot-dps/concepts-tpm-attestation.md) o [certificado X.509](../iot-dps/concepts-x509-attestation.md) es más segura y se debe usar cuando los requisitos de seguridad son más estrictos. <!-- note links here; they will break -->

## <a name="prerequisites"></a>Prerrequisitos

<!-- Cloud resources prerequisites H3 and content -->
[!INCLUDE [iot-edge-prerequisites-at-scale-cloud-resources.md](../../includes/iot-edge-prerequisites-at-scale-cloud-resources.md)]

### <a name="iot-edge-installation"></a>Instalación de IoT Edge

Un dispositivo Windows físico o virtual para que sea el dispositivo IoT Edge.

Se debe definir un **identificador de registro** *único* para identificar cada dispositivo. Puede usar la dirección MAC, el número de serie o cualquier otra información única del dispositivo. Por ejemplo, podría usar una combinación de una dirección MAC y un número de serie que formen la siguiente cadena de un identificador de registro: `sn-007-888-abc-mac-a1-b2-c3-d4-e5-f6`. Solo se pueden usar caracteres alfanuméricos en minúsculas y guiones (`-`).

<!-- Create a DPS enrollment using symmetric keys H2 and content -->
[!INCLUDE [iot-edge-create-dps-enrollment-symmetric.md](../../includes/iot-edge-create-dps-enrollment-symmetric.md)]

<!-- Install IoT Edge on Windows H2 and content -->
[!INCLUDE [install-iot-edge-windows.md](../../includes/iot-edge-install-windows.md)]

## <a name="provision-the-device-with-its-cloud-identity"></a>Aprovisionamiento del dispositivo con su identidad de nube

Una vez que el entorno de ejecución esté instalado en el dispositivo, configure el dispositivo con la información que usa para conectarse a Device Provisioning Service y a IoT Hub.

Tenga lista la siguiente información:

* El valor **Ámbito de id.** del DPS
* El **Id. de registro** del dispositivo que ha creado
* La **Clave principal** de una inscripción individual o una [clave derivada](#derive-a-device-key) para dispositivos que usan una inscripción de grupo.

1. Abra una ventana de Azure PowerShell en modo de administrador. Asegúrese de usar una sesión de AMD64 de PowerShell para instalar IoT Edge, no PowerShell (x86).

1. El comando **Initialize-IoTEdge** configura el entorno de ejecución de Azure IoT Edge en el equipo. De forma predeterminada, el comando realiza el aprovisionamiento manual con contenedores de Windows, a menos que se use la marca `-DpsSymmetricKey` para emplear el aprovisionamiento automático con la autenticación de clave simétrica.

   Reemplace los valores de marcador de posición para `paste_scope_id_here`, `paste_registration_id_here` y `paste_symmetric_key_here` con los datos que ha recopilado antes.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -DpsSymmetricKey -ScopeId paste_scope_id_here -RegistrationId paste_registration_id_here -SymmetricKey paste_symmetric key_here
   ```

## <a name="verify-successful-installation"></a>Comprobación de instalación correcta

Si el entorno de ejecución se inició correctamente, puede ir a IoT Hub y empezar a implementar módulos de IoT Edge en el dispositivo.

# <a name="individual-enrollment"></a>[Inscripción individual](#tab/individual-enrollment)

Puede comprobar que la inscripción individual que ha creado en Device Provisioning Service se ha usado. En Azure Portal, vaya a la instancia de Device Provisioning Service. Abra los detalles de la inscripción para la inscripción individual que ha creado. Tenga en cuenta que el estado de la inscripción está **asignado** y se muestra el id. de dispositivo.

# <a name="group-enrollment"></a>[Inscripción de grupo](#tab/group-enrollment)

Puede comprobar que la inscripción de grupo que ha creado en Device Provisioning Service se ha usado. En Azure Portal, vaya a la instancia de Device Provisioning Service. Abra los detalles de la inscripción para la inscripción de grupo que ha creado. Vaya a la pestaña **Entradas de registro** para ver todos los dispositivos registrados en ese grupo.

---

Use los siguientes comandos en el dispositivo para comprobar que la instancia de IoT Edge se haya instalado e iniciado correctamente.

Compruebe el estado del servicio IoT Edge.

```powershell
Get-Service iotedge
```

Examine los registros del servicio.

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
```

Enumere los módulos en ejecución.

```powershell
iotedge list
```

## <a name="next-steps"></a>Pasos siguientes

El proceso de inscripción en Device Provisioning Service permite establecer el id. de dispositivo y las etiquetas del dispositivo gemelo al mismo tiempo que se aprovisiona el nuevo dispositivo. Puede usar esos valores para dirigirse a dispositivos individuales o grupos de dispositivos con la administración automática de dispositivos. Aprenda a [implementar y supervisar los módulos de IoT Edge a escala mediante Azure Portal](how-to-deploy-at-scale.md) o la [CLI de Azure](how-to-deploy-cli-at-scale.md).
