---
title: 'Creación y aprovisionamiento de dispositivos IoT Edge mediante certificados X.509 en Linux en Windows: Azure IoT Edge | Microsoft Docs'
description: Uso de la atestación de certificados X.509 para probar el aprovisionamiento de dispositivos a gran escala para Azure IoT Edge con Device Provisioning Service
author: kgremban
ms.author: kgremban
ms.reviewer: v-tcassi
ms.date: 10/28/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
monikerRange: =iotedge-2018-06
ms.openlocfilehash: 59c29f3ce91f5ee1e1e5ee65710cc34ce1572907
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/05/2021
ms.locfileid: "131846012"
---
# <a name="create-and-provision-iot-edge-for-linux-on-windows-devices-at-scale-using-x509-certificates"></a>Creación y aprovisionamiento de IoT Edge para Linux en dispositivos Windows a gran escala mediante certificados X.509

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

En este artículo se proporcionan instrucciones completas para el aprovisionamiento automático de uno o varios dispositivos [IoT Edge para Linux en Windows](iot-edge-for-linux-on-windows.md) mediante certificados X.509. Puede aprovisionar automáticamente los dispositivos Azure IoT Edge con [Azure IoT Hub Device Provisioning Service](../iot-dps/index.yml) (DPS). Si no está familiarizado con el proceso de aprovisionamiento automático, revise la [información general sobre el aprovisionamiento](../iot-dps/about-iot-dps.md#provisioning-process) antes de continuar.

Las tareas son las siguientes:

1. Generar certificados y claves.
1. Crear una **inscripción individual** para un único dispositivo o una **inscripción de grupo** para un conjunto de dispositivos.
1. Implementar una máquina virtual Linux con el entorno de ejecución de Azure IoT Edge instalado y conectarla a IoT Hub.

El uso de certificados X.509 como un mecanismo de atestación es una manera excelente para escalar la producción y simplificar el aprovisionamiento de dispositivos. Normalmente, los certificados X.509 están organizados en una cadena de certificados de confianza. Comenzando por un certificado raíz de confianza o autofirmado, cada certificado de la cadena firma al certificado inmediatamente inferior. Este patrón crea una cadena delegada de confianza desde el certificado raíz a través de todos los certificados intermedios hasta el certificado "hoja" final que está instalado en un dispositivo.

## <a name="prerequisites"></a>Prerrequisitos

<!-- Cloud resources prerequisites H3 and content -->
[!INCLUDE [iot-edge-prerequisites-at-scale-cloud-resources.md](../../includes/iot-edge-prerequisites-at-scale-cloud-resources.md)]

<!-- IoT Edge for Linux on Windows installation prerequisites H3 and content -->
[!INCLUDE [iot-edge-prerequisites-linux-on-windows.md](../../includes/iot-edge-prerequisites-linux-on-windows.md)]

## <a name="generate-device-identity-certificates"></a>Generación de los certificados de identidad del dispositivo

El certificado de identidad del dispositivo es un certificado de hoja que se conecta a través de una cadena de certificados de confianza con el certificado de la entidad de certificación X.509 (CA) superior. El nombre común (CN) del certificado de identidad del dispositivo debe estar establecido en el id. de dispositivo que quiere que tenga el dispositivo en el centro de IoT.

Los certificados de identidad del dispositivo solo se usan para aprovisionar al dispositivo IoT Edge y autenticarlo con Azure IoT Hub. No son certificados de firma, a diferencia de los certificados de CA que el dispositivo IoT Edge presenta a los módulos o los dispositivos hoja para la comprobación. Para obtener más información, consulte los [detalles de uso de los certificados de Azure IoT Edge](iot-edge-certs.md).

Después de crear el certificado de identidad del dispositivo, debe tener dos archivos: un archivo. cer o. pem que contiene la parte pública del certificado y un archivo. cer o. pem con la clave privada del certificado. Si planea usar una inscripción de grupo en DPS, también necesitará la parte pública de un certificado de CA raíz o intermedio en la misma cadena de certificados de confianza.

Necesita los siguientes archivos para configurar el aprovisionamiento automático con X.509:

* El certificado de identidad del dispositivo y su certificado de clave privada. El certificado de identidad de dispositivo se carga en DPS si crea una inscripción individual. La clave privada se pasa al entorno de ejecución de Azure IoT Edge.
* Una cadena de certificados completa, que debe contener al menos la identidad del dispositivo y los certificados intermedios. La cadena de certificados completa se pasa al entorno de ejecución de Azure IoT Edge.
* Un certificado intermedio o raíz de entidad de certificación de la cadena de certificados de confianza. Este certificado se carga en DPS si crea una inscripción de grupo.

> [!NOTE]
> Actualmente, una limitación en libiothsm impide el uso de certificados que expiran el 1 de enero de 2038 o en una fecha posterior.

### <a name="use-test-certificates-optional"></a>Uso de certificados de prueba (opcional)

Si no tiene una entidad de certificación disponible para crear nuevos certificados de identidad y quiere probar este escenario, el repositorio de Git de Azure IoT Edge contiene scripts que puede usar para generar certificados de prueba. Estos certificados están diseñados solo para pruebas de desarrollo y no deben usarse en producción.

Para crear certificados de prueba, siga los pasos descritos en [Creación de certificados de demostración para probar las características de dispositivo IoT Edge](how-to-create-test-certificates.md). Complete las dos secciones necesarias para configurar los scripts de generación de certificados y crear un certificado de CA raíz. A continuación, siga los pasos para crear un certificado de identidad del dispositivo. Cuando haya terminado, debe tener la cadena de certificados y el par de claves siguientes:

* `<WRKDIR>\certs\iot-edge-device-identity-<name>-full-chain.cert.pem`
* `<WRKDIR>\private\iot-edge-device-identity-<name>.key.pem`

Necesita ambos certificados en el dispositivo IoT Edge. Si va a usar la inscripción individual en DPS, deberá cargar el archivo. cert.pem. Si va a usar la inscripción de grupos en DPS, también necesitará un certificado de CA raíz o intermedio en la misma cadena de certificados de confianza que se va a cargar. Si usa certificados de demostración, use el certificado `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem` para la inscripción de grupos.

<!-- Create a DPS enrollment using X.509 certificates H2 and content -->
[!INCLUDE [iot-edge-create-dps-enrollment-x509.md](../../includes/iot-edge-create-dps-enrollment-x509.md)]

<!-- Install IoT Edge for Linux on Windows H2 and content -->
[!INCLUDE [install-iot-edge-linux-on-windows.md](../../includes/iot-edge-install-linux-on-windows.md)]

## <a name="provision-the-device-with-its-cloud-identity"></a>Aprovisionamiento del dispositivo con su identidad de nube

Una vez que el entorno de ejecución esté instalado en el dispositivo, configure el dispositivo con la información que usa para conectarse a Device Provisioning Service y a IoT Hub.

Tenga lista la siguiente información:

* El valor de **Ámbito de id.** del DPS. Puede recuperar este valor de la página Información general de la instancia de DPS en Azure Portal.
* El archivo de cadena de certificados de identidad del dispositivo en el dispositivo.
* El archivo de clave de identidad del dispositivo en el dispositivo.

Puede usar PowerShell o Windows Admin Center para aprovisionar el dispositivo de IoT Edge.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Para PowerShell, ejecute el siguiente comando con los valores de marcador de posición actualizados con sus propios valores:

```powershell
Provision-EflowVm -provisioningType DpsX509 -scopeId PASTE_YOUR_ID_SCOPE_HERE -registrationId PASTE_YOUR_REGISTRATION_ID_HERE -identityCertPath PASTE_ABSOLUTE_PATH_TO_IDENTITY_CERTIFICATE_HERE -identityPrivateKey PASTE_ABSOLUTE_PATH_TO_IDENTITY_PRIVATE_KEY_HERE
```

# <a name="windows-admin-center"></a>[Windows Admin Center](#tab/windowsadmincenter)

Para Windows Admin Center, siga los pasos a continuación:

1. En el panel **Azure IoT Edge device provisioning** (Aprovisionamiento de dispositivos Azure IoT Edge), seleccione **X509 Certificate (DPS)** (Certificado X509 [DPS]) en el menú desplegable de método de aprovisionamiento.

1. Proporcione el identificador de ámbito de DPS, el identificador de registro del dispositivo y la clave principal de inscripción o la clave derivada en los campos de Windows Admin Center.

1. Elija **Provisioning with the selected method** (aprovisionar con el método seleccionado).

   ![Elija aprovisionar con el método seleccionado después de rellenar los campos obligatorios para el aprovisionamiento de claves simétricas, PNG.](./media/how-to-provision-devices-at-scale-linux-on-windows-x509/provisioning-with-selected-method-x509-certs.png)

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
