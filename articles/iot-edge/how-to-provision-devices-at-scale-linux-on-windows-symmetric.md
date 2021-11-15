---
title: Creación y aprovisionamiento de IoT Edge para dispositivos con claves simétricas en Linux en Windows - Azure IoT Edge | Microsoft Docs
description: Uso de la atestación de clave simétrica para probar el aprovisionamiento de Linux en dispositivos Windows a gran escala para Azure IoT Edge con Device Provisioning Service
author: kgremban
ms.author: kgremban
ms.reviewer: v-tcassi
ms.date: 10/29/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
monikerRange: =iotedge-2018-06
ms.openlocfilehash: 8b11313c7ff443226352558ffb172c3bbf59964b
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/05/2021
ms.locfileid: "131852130"
---
# <a name="create-and-provision-iot-edge-for-linux-on-windows-devices-at-scale-using-symmetric-keys"></a>Creación y aprovisionamiento de IoT Edge para Linux en dispositivos Windows a escala mediante claves simétricas 

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

En este artículo se proporcionan instrucciones completas para el aprovisionamiento automático de uno o varios dispositivos [IoT Edge para Linux en Windows](iot-edge-for-linux-on-windows.md) mediante claves simétricas. Puede aprovisionar automáticamente los dispositivos Azure IoT Edge con [Azure IoT Hub Device Provisioning Service](../iot-dps/index.yml) (DPS). Si no está familiarizado con el proceso de aprovisionamiento automático, revise la [información general sobre el aprovisionamiento](../iot-dps/about-iot-dps.md#provisioning-process) antes de continuar.

Las tareas son las siguientes:

1. Crear una **inscripción individual** para un dispositivo o una **inscripción de grupo** para un conjunto de dispositivos.
1. Implementar una máquina virtual Linux con el entorno de ejecución de Azure IoT Edge instalado y conectarla a IoT Hub.

La atestación de clave simétrica es un enfoque sencillo para autenticar un dispositivo con una instancia de Device Provisioning Service. Este método de atestación representa una experiencia de "Hola mundo" para los desarrolladores que no estén familiarizados con el aprovisionamiento de dispositivos, o no tengan estrictos requisitos de seguridad. La atestación de dispositivo mediante un [TPM](../iot-dps/concepts-tpm-attestation.md) o [certificado X.509](../iot-dps/concepts-x509-attestation.md) es más segura y se debe usar cuando los requisitos de seguridad son más estrictos.

## <a name="prerequisites"></a>Prerrequisitos

<!-- Cloud resources prerequisites H3 and content -->
[!INCLUDE [iot-edge-prerequisites-at-scale-cloud-resources.md](../../includes/iot-edge-prerequisites-at-scale-cloud-resources.md)]

<!-- IoT Edge for Linux on Windows installation prerequisites H3 and content -->
[!INCLUDE [iot-edge-prerequisites-linux-on-windows.md](../../includes/iot-edge-prerequisites-linux-on-windows.md)]

<!-- Create a DPS enrollment using symmetric keys H2 and content -->
[!INCLUDE [iot-edge-create-dps-enrollment-symmetric.md](../../includes/iot-edge-create-dps-enrollment-symmetric.md)]

<!-- Install IoT Edge for Linux on Windows H2 and content -->
[!INCLUDE [install-iot-edge-linux-on-windows.md](../../includes/iot-edge-install-linux-on-windows.md)]

## <a name="provision-the-device-with-its-cloud-identity"></a>Aprovisionamiento del dispositivo con su identidad de nube

Una vez que el entorno de ejecución esté instalado en el dispositivo, configure el dispositivo con la información que usa para conectarse a Device Provisioning Service y a IoT Hub.

Tenga lista la siguiente información:

* El valor **Ámbito de id.** del DPS
* El **Id. de registro** del dispositivo que ha creado
* La **Clave principal** de una inscripción individual o una [clave derivada](#derive-a-device-key) para dispositivos que usan una inscripción de grupo.

Puede usar PowerShell o Windows Admin Center para aprovisionar el dispositivo de IoT Edge.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Para PowerShell, ejecute el siguiente comando con los valores de marcador de posición actualizados con sus propios valores:

```powershell
Provision-EflowVm -provisioningType DpsSymmetricKey -scopeId PASTE_YOUR_ID_SCOPE_HERE -registrationId PASTE_YOUR_REGISTRATION_ID_HERE -symmKey PASTE_YOUR_PRIMARY_KEY_OR_DERIVED_KEY_HERE
```

# <a name="windows-admin-center"></a>[Windows Admin Center](#tab/windowsadmincenter)

Para Windows Admin Center, siga los pasos a continuación:

1. En el panel **Azure IoT Edge device provisioning** (Aprovisionamiento de dispositivos de Azure IoT Edge), seleccione **Symmetric Key (DPS)** (clave simétrica [DPS]) en el menú desplegable Método de aprovisionamiento.

1. En [Azure Portal](https://ms.portal.azure.com/), vaya a su instancia de DPS.

1. Proporcione el identificador de ámbito de DPS, el identificador de registro del dispositivo y la clave principal de inscripción o la clave derivada en los campos de Windows Admin Center.

1. Elija **Provisioning with the selected method** (aprovisionar con el método seleccionado).

   ![Elija aprovisionar con el método seleccionado después de rellenar los campos obligatorios para el aprovisionamiento de claves simétricas, PNG.](./media/how-to-provision-devices-at-scale-linux-on-windows-symmetric/provisioning-with-selected-method-symmetric-key.png)

1. Una vez completado el aprovisionamiento, seleccione **Finalizar**. Volverá al panel principal. Ahora debería mostrarse un nuevo dispositivo, cuyo tipo es `IoT Edge Devices`. Puede seleccionar el dispositivo de IoT Edge para conectarse a él. Una vez en la página **Información general** del dispositivo, puede ver los valores de **IoT Edge Module List** (lista de módulos de IoT Edge) y **IoT Edge Status** (estado de IoT Edge) del dispositivo.

---

## <a name="verify-successful-installation"></a>Comprobación de instalación correcta

Compruebe que IoT Edge para Linux en Windows se haya instalado y configurado correctamente en el dispositivo de IoT Edge.

# <a name="individual-enrollment"></a>[Inscripción individual](#tab/individual-enrollment)

Puede comprobar que la inscripción individual que ha creado en Device Provisioning Service se ha usado. En Azure Portal, vaya a la instancia de Device Provisioning Service. Abra los detalles de la inscripción para la inscripción individual que ha creado. Tenga en cuenta que el estado de la inscripción está **asignado** y se muestra el id. de dispositivo.

# <a name="group-enrollment"></a>[Inscripción de grupo](#tab/group-enrollment)

Puede comprobar que la inscripción de grupo que ha creado en Device Provisioning Service se ha usado. En Azure Portal, vaya a la instancia de Device Provisioning Service. Abra los detalles de la inscripción para la inscripción de grupo que ha creado. Vaya a la pestaña **Entradas de registro** para ver todos los dispositivos registrados en ese grupo.

---

# <a name="powershell"></a>[PowerShell](#tab/powershell)

1. Inicie sesión en la instancia de IoT Edge para Linux en la máquina virtual Windows con el siguiente comando en la sesión de PowerShell:

   ```powershell
   Connect-EflowVm
   ```

   >[!NOTE]
   >La única cuenta que se permite a SSH en la máquina virtual es la del usuario que la creó.

1. Una vez que haya iniciado sesión, puede comprobar la lista de módulos de IoT Edge en ejecución con el comando de Linux siguiente:

   ```bash
   sudo iotedge list
   ```

1. Si tiene que solucionar problemas del servicio IoT Edge, use los siguientes comandos de Linux.

    1. Recupere los registros de servicio.

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

   * En la sección **IoT Edge Module List** (lista de módulos de IoT Edge) se muestran los módulos en ejecución en el dispositivo. Cuando el servicio de IoT Edge se inicia por primera vez, solo verá la ejecución del módulo **edgeAgent**. El módulo edgeAgent se ejecuta de forma predeterminada y le ayuda a instalar e iniciar todos los módulos adicionales que implemente en el dispositivo.

   * En la sección **IoT Edge Status** (estado de IoT Edge) se muestra el estado del servicio y debe aparecer como **activo (en ejecución)** .

---

Al crear un nuevo dispositivo IoT Edge, se mostrará el código de estado `417 -- The device's deployment configuration is not set` en Azure Portal. Este estado es normal y significa que el dispositivo está listo para recibir una implementación de módulo.

## <a name="next-steps"></a>Pasos siguientes

El proceso de inscripción en Device Provisioning Service permite establecer el id. de dispositivo y las etiquetas del dispositivo gemelo al mismo tiempo que se aprovisiona el nuevo dispositivo. Puede usar esos valores para dirigirse a dispositivos individuales o grupos de dispositivos con la administración automática de dispositivos. Aprenda a [implementar y supervisar los módulos de IoT Edge a escala mediante Azure Portal](how-to-deploy-at-scale.md) o la [CLI de Azure](how-to-deploy-cli-at-scale.md).

También puede:

* Siga [implementando módulos de IoT Edge](how-to-deploy-modules-portal.md) para información sobre cómo implementar módulos en el dispositivo.
* Obtenga información acerca de cómo [administrar certificados en IoT Edge para Linux en máquinas virtuales de Windows](how-to-manage-device-certificates.md) y transferir archivos desde el sistema operativo host a la máquina virtual de Linux.
* Aprenda a [configurar los dispositivos de IoT Edge para que se comuniquen a través de un servidor proxy](how-to-configure-proxy-support.md).
