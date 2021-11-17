---
title: 'Creación y aprovisionamiento de un dispositivo IoT Edge para Linux en Windows mediante un TPM: Azure IoT Edge | Microsoft Docs'
description: Utilice un TPM simulado para un dispositivo Linux en Windows para probar Azure Device Provisioning Service para Azure IoT Edge.
author: kgremban
manager: lizross
ms.author: kgremban
ms.reviewer: fcabrera
ms.date: 10/28/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
monikerRange: =iotedge-2018-06
ms.openlocfilehash: b286dfd6fd4e494427b1094fc38039e575af1e17
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/05/2021
ms.locfileid: "131846090"
---
# <a name="create-and-provision-an-iot-edge-for-linux-on-windows-device-at-scale-by-using-a-tpm"></a>Creación y aprovisionamiento de un dispositivo IoT Edge para Linux en Windows mediante TPM

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

En este artículo se proporcionan instrucciones para aprovisionar automáticamente un dispositivo Azure IoT Edge para Linux en Windows mediante un Módulo de plataforma segura (TPM). Puede aprovisionar automáticamente los dispositivos Azure IoT Edge con [Azure IoT Hub Device Provisioning Service](../iot-dps/index.yml). Si no está familiarizado con el proceso de aprovisionamiento automático, revise la [información general sobre el aprovisionamiento](../iot-dps/about-iot-dps.md#provisioning-process) antes de continuar.

En este artículo, se describen dos metodologías. Seleccione sus preferencias en función de la arquitectura de la solución:

- Aprovisionamiento automático de un dispositivo Linux en Windows con hardware de TPM físico.
- Aprovisionamiento automático de un dispositivo Linux en Windows mediante un TPM simulado. Se recomienda esta metodología solo como escenario de prueba. Un TPM simulado no ofrece la misma seguridad que un TPM físico.

Las tareas son las siguientes:

# <a name="physical-tpm"></a>[TPM físico](#tab/physical-tpm)

* Instalación de IoT Edge para Linux en Windows
* Recuperación de la información de TPM del dispositivo
* Cree una inscripción individual para el dispositivo.
* Aprovisionamiento del dispositivo con su información de TPM

# <a name="simulated-tpm"></a>[TPM simulado](#tab/simulated-tpm)

* Instalación de IoT Edge para Linux en Windows
* Configure el TPM simulado y recupere su información de aprovisionamiento.
* Cree una inscripción individual para el dispositivo.
* Aprovisionamiento del dispositivo con su información de TPM

---

## <a name="prerequisites"></a>Requisitos previos

<!-- Cloud resources prerequisites H3 and content -->
[!INCLUDE [iot-edge-prerequisites-at-scale-cloud-resources.md](../../includes/iot-edge-prerequisites-at-scale-cloud-resources.md)]

<!-- IoT Edge for Linux on Windows installation prerequisites H3 and content -->
[!INCLUDE [iot-edge-prerequisites-linux-on-windows.md](../../includes/iot-edge-prerequisites-linux-on-windows.md)]

> [!NOTE]
> Se necesita TPM 2.0 cuando se usa la atestación de TPM con Device Provisioning Service.
>
> Solo puede crear inscripciones individuales, no de grupo, de Device Provisioning Service cuando se usa un TPM.

<!-- Install IoT Edge for Linux on Windows H2 and content -->
[!INCLUDE [install-iot-edge-linux-on-windows.md](../../includes/iot-edge-install-linux-on-windows.md)]

Hay algunos pasos para preparar el dispositivo para el aprovisionamiento con TPM. Deje abierta la implementación mientras prepara el dispositivo. Volverá a la implementación más adelante en el artículo.

## <a name="enable-tpm-passthrough"></a>Habilitación del acceso directo del TPM

La máquina virtual IoT Edge para Linux en Windows tiene una característica de TPM que se puede habilitar o deshabilitar. (Está deshabilitada de manera predeterminada). Cuando esta característica está habilitada, la máquina virtual puede acceder al TPM de la máquina host.

1. Abra PowerShell en una sesión con privilegios elevados.

1. Si aún no lo ha hecho, establezca la directiva de ejecución de su dispositivo en `AllSigned` para que pueda ejecutar las funciones de PowerShell de IoT Edge para Linux en Windows.

   ```powershell
   Set-ExecutionPolicy -ExecutionPolicy AllSigned -Force
   ```

1. Active la característica de TPM.

   ```powershell
   Set-EflowVmFeature -feature 'DpsTpm' -enable
   ```

## <a name="retrieve-the-tpm-information-from-your-device"></a>Recuperación de la información de TPM del dispositivo

# <a name="physical-tpm"></a>[TPM físico](#tab/physical-tpm)

Para aprovisionar el dispositivo, necesita una **Clave de aprobación** para el chip TPM y el **Id. de registro** para el dispositivo. Proporcione esta información a la instancia de Device Provisioning Service para que el servicio pueda reconocer el dispositivo cuando este intente conectarse.

La clave de aprobación es única para cada chip TPM. Se obtiene del fabricante del chip TPM asociado. Puede derivar un Id. de registro único para el dispositivo del TPM, por ejemplo, creando un código hash SHA-256 de la clave de aprobación.

IoT Edge para Linux en Windows proporciona un script de PowerShell para ayudar a recuperar esta información del TPM. Para usar el script, siga estos pasos en el dispositivo:

1. Abra PowerShell en una sesión con privilegios elevados.

1. Ejecute el comando.

   ```powershell
   Get-EflowVmTpmProvisioningInfo
   ```

# <a name="simulated-tpm"></a>[TPM simulado](#tab/simulated-tpm)

Si no dispone de un TPM físico y desea probar este método de aprovisionamiento, puede simular uno en su dispositivo.

IoT Hub Device Provisioning Service proporciona ejemplos que simulan un TPM y devuelven la clave de aprobación y el identificador de registro.

1. Elija uno de los ejemplos de la siguiente lista en función del idioma que prefiera.
1. Una vez que el TPM simulado esté en ejecución y usted haya recopilado la **clave de aprobación** y el **identificador de registro**, deje de seguir los pasos de ejemplo de Device Provisioning Service. No presione **ENTRAR** para ejecutar el registro en la aplicación de ejemplo.
1. Mantenga en ejecución la ventana que hospeda el TPM simulado hasta que haya terminado de probar este escenario.
1. Vuelva a este artículo para crear una inscripción de Device Provisioning Service y configurar el dispositivo.

Ejemplos de TPM simulado:

* [C](../iot-dps/quick-create-simulated-device-tpm.md)
* [Java](../iot-dps/quick-create-simulated-device-tpm.md)
* [C#](../iot-dps/quick-create-simulated-device-tpm.md)
* [Node.js](../iot-dps/quick-create-simulated-device-tpm.md)
* [Python](../iot-dps/quick-create-simulated-device-tpm.md)

---

<!-- Create an enrollment for your device using TPM provisioning information H2 and content -->
[!INCLUDE [tpm-create-a-device-provision-service-enrollment.md](../../includes/tpm-create-a-device-provision-service-enrollment.md)]

## <a name="provision-the-device-with-its-cloud-identity"></a>Aprovisionamiento del dispositivo con su identidad de nube

# <a name="powershell"></a>[PowerShell](#tab/powershell)

1. Abra una sesión de PowerShell con privilegios elevados en el dispositivo Windows.

1. Aprovisione el dispositivo usando el **Id. de ámbito** que ha recopilado de su instancia de Device Provisioning Service.

   ```powershell
   Provision-EflowVM -provisioningType "DpsTpm" -scopeId "SCOPE_ID_HERE"
   ```

# <a name="windows-admin-center"></a>[Windows Admin Center](#tab/windowsadmincenter)

1. En el paso **Conexión**, aprovisione el dispositivo.

   1. Seleccione el método de aprovisionamiento **DpsTpm**.
   1. Proporcione el **Id. de ámbito** obtenido de su instancia de Device Provisioning Service.

      ![Captura de pantalla que muestra el aprovisionamiento del dispositivo con Device Provisioning Service y la atestación de TPM.](./media/how-to-auto-provision-tpm-linux-on-windows/tpm-provision.png)

1. Seleccione **Aprovisionamiento con el método seleccionado**.

1. Una vez que IoT Edge se haya instalado y aprovisionado correctamente en el dispositivo, seleccione **Finalizar** para salir del Asistente para la implementación.

---

## <a name="verify-successful-installation"></a>Comprobación de instalación correcta

Compruebe que IoT Edge para Linux en Windows se haya instalado y configurado correctamente en el dispositivo de IoT Edge.

Si el entorno de ejecución se inició correctamente, puede ir a IoT Hub y empezar a implementar módulos de IoT Edge en el dispositivo.

Puede comprobar que se ha utilizado la inscripción individual que ha creado en Device Provisioning Service. En Azure Portal, vaya a la instancia de Device Provisioning Service. Abra los detalles de la inscripción para la inscripción individual que ha creado. Tenga en cuenta que el estado de la inscripción está **asignado** y se muestra el id. de dispositivo.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Use los siguientes comandos en el dispositivo para comprobar que la instancia de IoT Edge se haya instalado e iniciado correctamente.

1. Inicie sesión en la máquina virtual IoT Edge para Linux en Windows usando el siguiente comando en la sesión de PowerShell:

   ```powershell
   Connect-EflowVm
   ```

   >[!NOTE]
   >La única cuenta que puede usar SSH en la máquina virtual es el usuario que la creó.

1. Una vez que haya iniciado sesión, puede comprobar la lista de módulos de IoT Edge en ejecución con el comando de Linux siguiente:

   ```bash
   sudo iotedge list
   ```

1. Si tiene que solucionar problemas del servicio IoT Edge, use los siguientes comandos de Linux.

    1. Si necesita solucionar problemas del servicio, recupere los registros del servicio.

       ```bash
       sudo journalctl -u iotedge
       ```

    2. Use la herramienta `check` para comprobar el estado de la configuración y la conexión del dispositivo.

       ```bash
       sudo iotedge check
       ```

# <a name="windows-admin-center"></a>[Windows Admin Center](#tab/windowsadmincenter)

1. Seleccione el dispositivo de IoT Edge en la lista de dispositivos conectados de Windows Admin Center para conectarse a él.

1. En la página Información general del dispositivo se muestran algunos datos sobre el dispositivo:

   * En la sección **IoT Edge Module List** (lista de módulos de IoT Edge) se muestran los módulos en ejecución en el dispositivo. Cuando el servicio de IoT Edge se inicia por primera vez, solo verá la ejecución del módulo **edgeAgent**. El módulo edgeAgent se ejecuta de forma predeterminada y le ayuda a instalar e iniciar cualquier otro módulo que implemente en el dispositivo.
   * En la sección **IoT Edge Status** (Estado de IoT Edge) se muestra el estado del servicio y debe aparecer como **activo (en ejecución)** .

---

## <a name="next-steps"></a>Pasos siguientes

El proceso de inscripción en Device Provisioning Service permite establecer el id. de dispositivo y las etiquetas del dispositivo gemelo al mismo tiempo que se aprovisiona el nuevo dispositivo. Puede usar esos valores para dirigirse a dispositivos individuales o grupos de dispositivos con la administración automática de dispositivos.

Aprenda a [implementar y supervisar módulos de IoT Edge a gran escala mediante Azure Portal](how-to-deploy-at-scale.md) o la [CLI de Azure](how-to-deploy-cli-at-scale.md).
