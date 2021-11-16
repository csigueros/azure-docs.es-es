---
title: 'Aprovisionamiento de dispositivos con un TPM virtual en Windows: Azure IoT Edge | Microsoft Docs'
description: Uso de un TPM simulado en un dispositivo Windows para probar el servicio de aprovisionamiento de dispositivos de Azure en Azure IoT Edge
author: kgremban
ms.author: kgremban
ms.date: 10/06/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
monikerRange: =iotedge-2018-06
ms.openlocfilehash: 0b3d6bba1e99131b5a03b8ba5acfb760566437b1
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/22/2021
ms.locfileid: "130234762"
---
# <a name="create-and-provision-iot-edge-devices-at-scale-with-a-tpm-on-windows"></a>Creación y aprovisionamiento de dispositivos IoT Edge a gran escala con un TPM en Windows

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

En este artículo se proporcionan instrucciones para aprovisionar automáticamente Azure IoT Edge para dispositivos Windows mediante un módulo de plataforma segura (TPM). Puede aprovisionar dispositivos IoT Edge de forma automática con el [Azure IoT Hub Device Provisioning Service](../iot-dps/index.yml). Si no está familiarizado con el proceso de aprovisionamiento automático, revise la [información general sobre el aprovisionamiento](../iot-dps/about-iot-dps.md#provisioning-process) antes de continuar.

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

## <a name="prerequisites"></a>Prerrequisitos

Los requisitos previos son los mismos para las soluciones de TPM físico y TPM virtual.

* Una máquina de desarrollo Windows. En este artículo se usa Windows 10.
* Un centro de IoT activo.
* Una instancia de IoT Hub Device Provisioning Service en Azure que esté vinculada a su centro de IoT.
  * Si no tiene dicha instancia, siga las instrucciones de dos secciones del inicio rápido sobre IoT Hub Device Provisioning Service:
    - [Creación de una instancia de IoT Hub Device Provisioning Service](../iot-dps/quick-setup-auto-provision.md#create-a-new-iot-hub-device-provisioning-service)
    - [Vínculo al centro de IoT y a Device Provisioning Service](../iot-dps/quick-setup-auto-provision.md#link-the-iot-hub-and-your-device-provisioning-service)
  * Cuando tenga la instancia del servicio de aprovisionamiento de dispositivo en ejecución, copie el valor de **Ámbito de id.** de la página de información general. Use este valor cuando configure el entorno de ejecución de IoT Edge.

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

## <a name="create-a-device-provisioning-service-enrollment"></a>Creación de una inscripción del servicio de aprovisionamiento de dispositivos

Use la información de aprovisionamiento del TPM para crear una inscripción individual en servicio de aprovisionamiento de dispositivos.

Cuando hace esto, tiene la oportunidad de declarar un **estado inicial de dispositivo gemelo**. En el dispositivo gemelo, puede establecer etiquetas para agrupar dispositivos por cualquier métrica que necesite en su solución, como la región, el entorno, la ubicación o el tipo de dispositivo. Estas etiquetas se usan para crear [implementaciones automáticas](how-to-deploy-at-scale.md).

> [!TIP]
> Los pasos de este artículo son para Azure Portal, pero también puede crear inscripciones individuales mediante la CLI de Azure. Para más información, consulte [az iot dps enrollment](/cli/azure/iot/dps/enrollment). Como parte del comando de la CLI, use la marca **edge-enabled** para especificar que la inscripción es para un dispositivo de IoT Edge.

1. En [Azure Portal](https://portal.azure.com), vaya a la instancia de IoT Hub Device Provisioning Service.

1. En **Configuración**, seleccione **Administrar inscripciones**.

1. Seleccione **Agregar inscripción individual** y, luego, complete los pasos siguientes para configurar la inscripción:

   1. En **Mecanismo**, seleccione **TPM**.

   1. Proporcione la **clave de aprobación** y el **identificador de registro** que ha copiado de la máquina virtual o del dispositivo físico.

   1. Proporcione un identificador para el dispositivo, si así lo desea. Si no proporciona un identificador de dispositivo, se usará el **identificador de registro**.

   1. Seleccione **Verdadero** para declarar que esta máquina virtual o el dispositivo físico es un dispositivo IoT Edge.

   1. Elija el centro de IoT vinculado al que quiere conectar el dispositivo o seleccione **Link to new IoT Hub** (Vincular a un nuevo centro de IoT). Puede elegir varios centros y el dispositivo se asignará a uno de ellos según la directiva de asignación seleccionada.

   1. Agregue un valor de etiqueta a **Estado inicial de dispositivo gemelo**, si así lo desea. Puede usar etiquetas para los grupos de dispositivos de destino para la implementación del módulo. Para más información, consulte [Implementación de módulos IoT Edge a escala](how-to-deploy-at-scale.md).

   1. Seleccione **Guardar**.

Ahora que existe una inscripción para este dispositivo, el entorno de ejecución de Azure IoT Edge puede aprovisionar automáticamente el dispositivo durante la instalación.

## <a name="install-the-iot-edge-runtime"></a>Instalación del entorno de ejecución de IoT Edge

En esta sección, preparará la máquina virtual o el dispositivo físico Windows para IoT Edge. Luego, instalará IoT Edge.

Hay un paso que debe completar en el dispositivo antes de que esté listo para instalar el entorno de ejecución de IoT Edge. El dispositivo necesita un motor de contenedor instalado.

### <a name="install-iot-edge"></a>Instalación de IoT Edge

El demonio de seguridad de IoT Edge proporciona y mantiene los estándares de seguridad en el dispositivo IoT Edge. El demonio se inicia en cada arranque e inicia el resto del entorno de ejecución de IoT Edge para arrancar el dispositivo.

Los pasos de esta sección representan el proceso habitual para instalar la versión más reciente en un dispositivo que tenga conexión a Internet. Si necesita instalar una versión específica, como una versión preliminar, o si necesita realizar la instalación sin conexión, siga los pasos correspondientes en cada caso.

1. Ejecute PowerShell como administrador.

   Use una sesión AMD64 de PowerShell, no PowerShell (x86). Si no está seguro del tipo de sesión que usa, ejecute el comando siguiente:

   ```powershell
   (Get-Process -Id $PID).StartInfo.EnvironmentVariables["PROCESSOR_ARCHITECTURE"]
   ```

2. Ejecute el comando [Deploy-IoTEdge](reference-windows-scripts.md#deploy-iotedge), que realiza las tareas siguientes:

   * Comprueba que la máquina con Windows se encuentra en una versión compatible.
   * Activa la característica de los contenedores.
   * Descarga el motor de Moby y el entorno de ejecución de Azure IoT Edge.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Deploy-IoTEdge
   ```

3. Si se le solicita, reinicie el dispositivo.

Al instalar IoT Edge en un dispositivo, puede usar otros parámetros para modificar el proceso con el fin de:

* Dirigir el tráfico hacia un servidor proxy.
* Haga que el instalador apunte a un directorio local, en el caso de una instalación sin conexión.

Para más información sobre estos otros parámetros, consulte [Scripts de PowerShell para IoT Edge con contenedores Windows](reference-windows-scripts.md).

## <a name="configure-the-device-with-provisioning-information"></a>Configuración del dispositivo con la información de aprovisionamiento

Una vez instalado el entorno de ejecución en el dispositivo, configure este con la información que se usa para conectarse al servicio de aprovisionamiento de dispositivos e IoT Hub.

1. Conozca los valores de **Ámbito de id.** e **Id. de registro** del dispositivo que se recopilaron en las secciones anteriores.

1. Abra una ventana de Azure PowerShell en modo de administrador. Asegúrese de usar una sesión AMD64 de PowerShell al instalar IoT Edge, no PowerShell (x86).

1. El comando `Initialize-IoTEdge` configura el entorno de ejecución de IoT Edge en la máquina. El comportamiento predeterminado del comando es el aprovisionamiento manual con contenedores de Windows. Use la marca `-Dps` para utilizar el servicio de aprovisionamiento de dispositivos, en lugar del aprovisionamiento manual.

   Reemplace los valores de marcador de posición para `{scope_id}` y `{registration_id}` con los datos que ha recopilado antes.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -Dps -ScopeId {scope ID} -RegistrationId {registration ID}
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