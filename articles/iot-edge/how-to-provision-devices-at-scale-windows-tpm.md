---
title: 'Creación y aprovisionamiento de dispositivos con un TPM virtual en Windows: Azure IoT Edge | Microsoft Docs'
description: Uso de un TPM simulado en un dispositivo Windows para probar el servicio de aprovisionamiento de dispositivos de Azure en Azure IoT Edge
author: kgremban
ms.author: kgremban
ms.date: 10/28/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
monikerRange: =iotedge-2018-06
ms.openlocfilehash: 4211cd69cddfea77ccd3f6e2a095ced6ce5effc3
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/05/2021
ms.locfileid: "131853878"
---
# <a name="create-and-provision-iot-edge-devices-at-scale-with-a-tpm-on-windows"></a>Creación y aprovisionamiento de dispositivos IoT Edge a gran escala con un TPM en Windows

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

En este artículo se proporcionan instrucciones para aprovisionar automáticamente Azure IoT Edge para dispositivos Windows mediante un módulo de plataforma segura (TPM). Puede aprovisionar dispositivos IoT Edge de forma automática con el [Azure IoT Hub Device Provisioning Service](../iot-dps/index.yml). Si no está familiarizado con el proceso de aprovisionamiento automático, revise la [información general sobre el aprovisionamiento](../iot-dps/about-iot-dps.md#provisioning-process) antes de continuar.

>[!NOTE]
>Azure IoT Edge con contenedores Windows no se admitirá a partir de la versión 1.2 de Azure IoT Edge.
>
>Considere la posibilidad de usar el nuevo método para ejecutar IoT Edge en dispositivos Windows, [Azure IoT Edge para Linux en Windows](iot-edge-for-linux-on-windows.md).
>
>Si desea usar Azure IoT Edge para Linux en Windows, puede seguir los pasos de la [guía de procedimientos equivalente](how-to-provision-devices-at-scale-linux-on-windows-tpm.md).

En este artículo, se describen dos metodologías. Seleccione sus preferencias en función de la arquitectura de la solución:

- Aprovisionar automáticamente un dispositivo Windows con hardware de TPM físico.
- Aprovisionar automáticamente un dispositivo Windows que ejecuta un TPM simulado. Se recomienda esta metodología solo como escenario de prueba. Un TPM simulado no ofrece la misma seguridad que un TPM físico.

Las instrucciones varían en función de la metodología, por lo que debe asegurarse de que se encuentra en la pestaña correcta en el futuro.

Las tareas son las siguientes:

# <a name="physical-tpm"></a>[TPM físico](#tab/physical-tpm)

* Recupere la información de aprovisionamiento del dispositivo.
* Cree una inscripción individual para el dispositivo.
* Instale el entorno de ejecución de IoT Edge y conecte el dispositivo a IoT Hub.

# <a name="simulated-tpm"></a>[TPM simulado](#tab/simulated-tpm)

* Configure el TPM simulado y recupere su información de aprovisionamiento.
* Cree una inscripción individual para el dispositivo.
* Instale el entorno de ejecución de IoT Edge y conecte el dispositivo a IoT Hub.

---

## <a name="prerequisites"></a>Requisitos previos

Los requisitos previos son los mismos para las soluciones de TPM físico y TPM virtual.

<!-- Cloud resources prerequisites H3 and content -->
[!INCLUDE [iot-edge-prerequisites-at-scale-cloud-resources.md](../../includes/iot-edge-prerequisites-at-scale-cloud-resources.md)]

### <a name="iot-edge-installation"></a>Instalación de IoT Edge

Una máquina de desarrollo Windows. En este artículo se usa Windows 10.

> [!NOTE]
> TPM 2.0 es necesario cuando se usa la atestación de TPM con el servicio de aprovisionamiento de dispositivos.
>
> Solo puede crear inscripciones individuales, no de grupo, del servicio de aprovisionamiento de dispositivos cuando se usa un TPM.

## <a name="set-up-your-tpm"></a>Configuración de TPM

# <a name="physical-tpm"></a>[TPM físico](#tab/physical-tpm)

En esta sección, creará una herramienta que podrá usar para recuperar el identificador de registro y la clave de aprobación del TPM.

1. Siga los pasos de [Configuración de un entorno de desarrollo en Windows](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#set-up-a-windows-development-environment) para instalar y compilar el SDK de dispositivo IoT de Azure para C.

1. Ejecute los comandos siguientes en una sesión de PowerShell con privilegios elevados para compilar la herramienta del SDK que recupera la información de aprovisionamiento de dispositivos desde el TPM.

   ```powershell
   cd azure-iot-sdk-c\cmake
   cmake -Duse_prov_client:BOOL=ON ..
   cd provisioning_client\tools\tpm_device_provision
   make
   .\tpm_device_provision
   ```

1. En la ventana de salida se muestra el **Id. de registro** y la **Clave de aprobación**. Copie estos valores; los usará más adelante cuando cree una inscripción individual para el dispositivo en el servicio de aprovisionamiento de dispositivos.

> [!TIP]
> Si no quiere usar la herramienta SDK para recuperar la información, debe encontrar otra manera de obtener la información de aprovisionamiento. La clave de aprobación, que es única para cada chip de TPM, se obtiene del fabricante del chip de TPM asociado a ella. Puede derivar un identificador de registro único para el dispositivo TPM. Por ejemplo, puede crear un hash SHA-256 de la clave de aprobación.

Cuando tenga el identificador de registro y la clave de aprobación, estará listo para continuar.

# <a name="simulated-tpm"></a>[TPM simulado](#tab/simulated-tpm)

Si no dispone de un TPM físico y desea probar este método de aprovisionamiento, puede simular uno en su dispositivo.

IoT Hub Device Provisioning Service proporciona ejemplos que simulan un TPM y devuelven la clave de aprobación y el identificador de registro.

1. Elija uno de los ejemplos de la siguiente lista en función del lenguaje que prefiera.
1. Una vez que el TPM simulado esté en ejecución y usted haya recopilado la **clave de aprobación** y el **identificador de registro**, deje de seguir los pasos de ejemplo del servicio de aprovisionamiento de dispositivos. No presione **Entrar** para ejecutar el registro en la aplicación de ejemplo.
1. Mantenga en ejecución la ventana que hospeda el TPM simulado hasta que haya terminado de probar este escenario.
1. Vuelva a este artículo para crear una inscripción del servicio de aprovisionamiento de dispositivos y configurar el dispositivo.

Ejemplos de TPM simulado:

* [C](../iot-dps/quick-create-simulated-device-tpm.md)
* [Java](../iot-dps/quick-create-simulated-device-tpm.md)
* [C#](../iot-dps/quick-create-simulated-device-tpm.md)
* [Node.js](../iot-dps/quick-create-simulated-device-tpm.md)
* [Python](../iot-dps/quick-create-simulated-device-tpm.md)

---

<!-- Create an enrollment for your device using TPM provisioning information H2 and content -->
[!INCLUDE [tpm-create-a-device-provision-service-enrollment.md](../../includes/tpm-create-a-device-provision-service-enrollment.md)]

<!-- Install IoT Edge on Windows H2 and content -->
[!INCLUDE [install-iot-edge-windows.md](../../includes/iot-edge-install-windows.md)]

## <a name="provision-the-device-with-its-cloud-identity"></a>Aprovisionamiento del dispositivo con su identidad de nube

Una vez instalado el entorno de ejecución en el dispositivo, configure este con la información que se usa para conectarse al servicio de aprovisionamiento de dispositivos e IoT Hub.

1. Conozca los valores de **Ámbito de id.** e **Id. de registro** del dispositivo que se recopilaron en las secciones anteriores.

1. Abra una ventana de Azure PowerShell en modo de administrador. Asegúrese de usar una sesión AMD64 de PowerShell al instalar IoT Edge, no PowerShell (x86).

1. El comando `Initialize-IoTEdge` configura el entorno de ejecución de IoT Edge en la máquina. El comportamiento predeterminado del comando es el aprovisionamiento manual con contenedores de Windows. Use la marca `-Dps` para utilizar el servicio de aprovisionamiento de dispositivos, en lugar del aprovisionamiento manual.

   Reemplace los valores de marcador de posición para `paste_scope_id_here` y `paste_registration_id_here` con los datos que ha recopilado antes.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -Dps -ScopeId paste_scope_id_here -RegistrationId paste_registration_id_here
   ```

## <a name="verify-successful-installation"></a>Comprobación de instalación correcta

Si el entorno de ejecución se inició correctamente, vaya a IoT Hub y empiece a implementar módulos de IoT Edge en el dispositivo. Use los siguientes comandos en el dispositivo para comprobar que el entorno de ejecución está instalado e iniciado correctamente.

1. Compruebe el estado del servicio IoT Edge.

    ```powershell
    Get-Service iotedge
    ```

1. Examine los registros de servicio de los últimos 5 minutos.

    ```powershell
    . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
    ```

1. Enumere los módulos en ejecución.

    ```powershell
    iotedge list
    ```

## <a name="next-steps"></a>Pasos siguientes

El proceso de inscripción del servicio de aprovisionamiento de dispositivos le permite establecer las etiquetas de identificador de dispositivo y de dispositivo gemelo al mismo tiempo que se aprovisiona el nuevo dispositivo. Puede usar esos valores para dirigirse a dispositivos individuales o grupos de dispositivos mediante la administración automática de dispositivos.

Aprenda a [implementar y supervisar los módulos de IoT Edge a gran escala mediante Azure Portal](how-to-deploy-at-scale.md) o la [CLI de Azure](how-to-deploy-cli-at-scale.md).
