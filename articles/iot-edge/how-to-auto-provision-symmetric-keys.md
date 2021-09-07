---
title: 'Aprovisionamiento de un dispositivo mediante la atestación de clave simétrica: Azure IoT Edge'
description: Uso de atestación de clave simétrica para probar el aprovisionamiento automático de dispositivos para Azure IoT Edge con Device Provisioning Service
author: kgremban
ms.author: kgremban
ms.reviewer: mrohera
ms.date: 07/21/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: d4aa7f1a02d8ab789810f06b38c95e9cfd76d5fb
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121742252"
---
# <a name="create-and-provision-an-iot-edge-device-using-symmetric-key-attestation"></a>Creación y aprovisionamiento de un dispositivo IoT Edge mediante la atestación de clave simétrica

[!INCLUDE [iot-edge-version-201806-or-202011](../../includes/iot-edge-version-201806-or-202011.md)]

Los dispositivos Azure IoT Edge pueden aprovisionarse automáticamente con [Device Provisioning Service](../iot-dps/index.yml), igual que los dispositivos que no están habilitados para Edge. Si no está familiarizado con el proceso de aprovisionamiento automático, revise la información general sobre el [aprovisionamiento](../iot-dps/about-iot-dps.md#provisioning-process) antes de continuar.

En este artículo se muestra cómo crear una inscripción individual o de grupo del servicio de aprovisionamiento de dispositivos mediante la atestación de clave simétrica en un dispositivo IoT Edge con los pasos siguientes:

* Cree una instancia de IoT Hub Device Provisioning Service (DPS).
* Cree una inscripción individual o grupal.
* Instale el entorno de ejecución de IoT Edge y conéctese a IoT Hub.

:::moniker range=">=iotedge-2020-11"
>[!TIP]
>Para una experiencia simplificada, pruebe la [herramienta de configuración de Azure IoT Edge](https://github.com/azure/iot-edge-config). Esta herramienta de línea de comandos, actualmente en versión preliminar pública, instala IoT Edge en el dispositivo y lo aprovisiona mediante DPS y la atestación de clave simétrica.
:::moniker-end

La atestación de clave simétrica es un enfoque sencillo para autenticar un dispositivo con una instancia del servicio Device Provisioning. Este método de atestación representa una experiencia de "Hola mundo" para los desarrolladores que no estén familiarizados con el aprovisionamiento de dispositivos, o no tengan estrictos requisitos de seguridad. La atestación de dispositivo mediante un [TPM](../iot-dps/concepts-tpm-attestation.md) o [certificado X.509](../iot-dps/concepts-x509-attestation.md) es más segura y se debe usar cuando los requisitos de seguridad son más estrictos.

## <a name="prerequisites"></a>Prerrequisitos

* Una instancia de IoT Hub activa
* Un dispositivo físico o virtual

## <a name="set-up-the-iot-hub-device-provisioning-service"></a>Configuración de IoT Hub Device Provisioning Service

Cree una nueva instancia de IoT Hub Device Provisioning Service en Azure y vincúlela a IoT Hub. Puede seguir las instrucciones de [Configuración de IoT Hub DPS](../iot-dps/quick-setup-auto-provision.md).

Cuando Device Provisioning Service esté en ejecución, copie el valor de **Ámbito de id.** de la página de información general. Use este valor cuando configure el entorno de ejecución de IoT Edge.

## <a name="choose-a-unique-device-registration-id"></a>Elección de un identificador de registro único para un dispositivo

Se debe definir un identificador de registro único para identificar cada dispositivo. Puede usar la dirección MAC, el número de serie o cualquier otra información única del dispositivo. Por ejemplo, podría usar una combinación de una dirección MAC y un número de serie que formen la siguiente cadena de un identificador de registro: `sn-007-888-abc-mac-a1-b2-c3-d4-e5-f6`. Solo se pueden usar caracteres alfanuméricos en minúsculas y guiones (`-`).

## <a name="option-1-create-a-dps-individual-enrollment"></a>Opción 1: Creación de una inscripción individual de DPS

Cree una inscripción individual para aprovisionar un único dispositivo a través de DPS.

Al crear una inscripción en DPS, tiene la oportunidad de declarar un **Estado inicial de dispositivo gemelo**. En el dispositivo gemelo, puede establecer etiquetas para agrupar dispositivos por cualquier métrica que necesite en su solución, como la región, el entorno, la ubicación o el tipo de dispositivo. Estas etiquetas se usan para crear [implementaciones automáticas](how-to-deploy-at-scale.md).

> [!TIP]
> Los pasos de este artículo son para Azure Portal, pero también puede crear inscripciones individuales mediante la CLI de Azure. Para más información, consulte [az iot dps enrollment](/cli/azure/iot/dps/enrollment). Como parte del comando de la CLI, use la marca **edge-enabled** para especificar que la inscripción es para un dispositivo de IoT Edge.

1. En [Azure Portal](https://portal.azure.com), navegue hasta la instancia de IoT Hub Device Provisioning Service.

1. En **Configuración**, seleccione **Administrar inscripciones**.

1. Seleccione **Add individual enrollment** (Agregar inscripción individual) y, a continuación, complete los pasos siguientes para configurar la inscripción:  

   1. En **Mecanismo**, seleccione **Clave simétrica**.

   1. Proporcione un **Id. de registro** único para el dispositivo.

   1. De manera opcional, proporcione un **Id. de dispositivo IoT Hub** para el dispositivo. Puede usar identificadores de dispositivo para dirigirse a un dispositivo individual para la implementación del módulo. Si no proporciona un id. de dispositivo, se usará el id. de registro.

   1. Seleccione **Verdadero** para declarar que la inscripción es para un dispositivo IoT Edge.

   1. De manera opcional, agregue un valor de etiqueta a **Estado inicial de dispositivo gemelo**. Puede usar etiquetas para los grupos de dispositivos de destino para la implementación del módulo. Por ejemplo:

      ```json
      {
         "tags": {
            "environment": "test"
         },
         "properties": {
            "desired": {}
         }
      }
      ```

   1. Seleccione **Guardar**.

1. Copie el valor de **Clave principal** de la inscripción individual para usarlo al instalar el entorno de ejecución de Azure IoT Edge.

Ahora que existe una inscripción para este dispositivo, el entorno de ejecución de Azure IoT Edge puede aprovisionar automáticamente el dispositivo durante la instalación.

## <a name="option-2-create-a-dps-enrollment-group"></a>Opción 2: Creación de un grupo de inscripción de DPS

Use el identificador de registro del dispositivo para crear una inscripción individual en DPS.

Al crear una inscripción en DPS, tiene la oportunidad de declarar un **Estado inicial de dispositivo gemelo**. En el dispositivo gemelo, puede establecer etiquetas para agrupar dispositivos por cualquier métrica que necesite en su solución, como la región, el entorno, la ubicación o el tipo de dispositivo. Estas etiquetas se usan para crear [implementaciones automáticas](how-to-deploy-at-scale.md).

> [!TIP]
> Los pasos de este artículo son para Azure Portal, pero también puede crear inscripciones individuales mediante la CLI de Azure. Para más información, consulte [az iot dps enrollment-group](/cli/azure/iot/dps/enrollment-group). Como parte del comando de la CLI, use la marca **edge-enabled** para especificar que la inscripción es para dispositivos de IoT Edge. En el caso de una inscripción de grupo, todos los dispositivos deben ser dispositivos IoT Edge, o bien ninguno puede serlo.

1. En [Azure Portal](https://portal.azure.com), navegue hasta la instancia de IoT Hub Device Provisioning Service.

1. En **Configuración**, seleccione **Administrar inscripciones**.

1. Seleccione **Add individual enrollment** (Agregar inscripción individual) y, a continuación, complete los pasos siguientes para configurar la inscripción:  

   1. Escriba un **Nombre de grupo**.

   1. Seleccione **Clave simétrica** como tipo de atestación.

   1. Seleccione **Verdadero** para declarar que la inscripción es para un dispositivo IoT Edge. En el caso de una inscripción de grupo, todos los dispositivos deben ser dispositivos IoT Edge, o bien ninguno puede serlo.

   1. De manera opcional, agregue un valor de etiqueta a **Estado inicial de dispositivo gemelo**. Puede usar etiquetas para los grupos de dispositivos de destino para la implementación del módulo. Por ejemplo:

      ```json
      {
         "tags": {
            "environment": "test"
         },
         "properties": {
            "desired": {}
         }
      }
      ```

   1. Seleccione **Guardar**.

1. Copie el valor de **Clave principal** del grupo de inscripción para usarlo al crear claves de dispositivo para usarlas con una inscripción del grupo.

Ahora que existe un grupo de inscripciones, el entorno de ejecución de Azure IoT Edge puede aprovisionar automáticamente los dispositivos durante la instalación.

### <a name="derive-a-device-key"></a>Derivación de una clave de dispositivo

Cada dispositivo que se aprovisiona como parte de una inscripción de grupo necesita una clave de dispositivo derivada para realizar la atestación de clave simétrica con la inscripción durante el aprovisionamiento.

Para generar la clave del dispositivo, use la clave que copió del grupo de inscripciones de DPS para calcular un valor [HMAC-SHA256](https://wikipedia.org/wiki/HMAC) del identificador de registro único del dispositivo y convierta el resultado a formato Base64.

No incluya la clave principal o secundaria de la inscripción en el código del dispositivo.

#### <a name="derive-a-key-on-linux"></a>Derivación de una clave en Linux

En Linux, puede usar openssl para generar la clave de dispositivo derivada tal y como se muestra en el ejemplo siguiente.

Reemplace el valor de **KEY** por el de la **clave principal** que ha apuntado anteriormente.

Reemplace el valor de **REG_ID** por el identificador de registro del dispositivo.

```bash
KEY=PASTE_YOUR_ENROLLMENT_KEY_HERE
REG_ID=PASTE_YOUR_REGISTRATION_ID_HERE

keybytes=$(echo $KEY | base64 --decode | xxd -p -u -c 1000)
echo -n $REG_ID | openssl sha256 -mac HMAC -macopt hexkey:$keybytes -binary | base64
```

```bash
Jsm0lyGpjaVYVP2g3FnmnmG9dI/9qU24wNoykUmermc=
```

#### <a name="derive-a-key-on-windows"></a>Derivación de una clave en Windows

En Windows, puede usar PowerShell para generar las claves de dispositivo derivadas tal y como se muestra en el ejemplo siguiente.

Reemplace el valor de **KEY** por el de la **clave principal** que ha apuntado anteriormente.

Reemplace el valor de **REG_ID** por el identificador de registro del dispositivo.

```powershell
$KEY='PASTE_YOUR_ENROLLMENT_KEY_HERE'
$REG_ID='PASTE_YOUR_REGISTRATION_ID_HERE'

$hmacsha256 = New-Object System.Security.Cryptography.HMACSHA256
$hmacsha256.key = [Convert]::FromBase64String($KEY)
$sig = $hmacsha256.ComputeHash([Text.Encoding]::ASCII.GetBytes($REG_ID))
$derivedkey = [Convert]::ToBase64String($sig)
echo "`n$derivedkey`n"
```

```powershell
Jsm0lyGpjaVYVP2g3FnmnmG9dI/9qU24wNoykUmermc=
```

## <a name="install-the-iot-edge-runtime"></a>Instalación del entorno de ejecución de IoT Edge

El runtime de IoT Edge se implementa en todos los dispositivos de IoT Edge. Sus componentes se ejecutan en contenedores y permiten implementar contenedores adicionales en el dispositivo para que pueda ejecutar código en Edge.

<!-- 1.1 -->
:::moniker range="=iotedge-2018-06"

Siga los pasos adecuados para instalar Azure IoT Edge en función del sistema operativo:

* [Instalación de IoT Edge para Linux](how-to-install-iot-edge.md)
* [Instalación de IoT Edge para Linux en dispositivos Windows](how-to-install-iot-edge-on-windows.md)
  * Este escenario es la manera recomendada de ejecutar IoT Edge en dispositivos Windows.
* [Instalación de IoT Edge con contenedores de Windows](how-to-install-iot-edge-windows-on-windows.md)

Una vez que IoT Edge esté instalado en el dispositivo, vuelva a este artículo para aprovisionar el dispositivo.

:::moniker-end
<!-- end 1.1 -->

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

Siga los pasos descritos en [Instalación del entorno de ejecución de Azure IoT Edge](how-to-install-iot-edge.md) y, luego, vuelva a este artículo para aprovisionar el dispositivo.

:::moniker-end
<!-- end 1.2 -->

## <a name="configure-the-device-with-provisioning-information"></a>Configuración del dispositivo con la información de aprovisionamiento

Una vez que el entorno de ejecución está instalado en el dispositivo, configure el dispositivo con la información que usa para conectarse al servicio Device Provisioning y a IoT Hub.

Tenga lista la siguiente información:

* El valor **Ámbito de id.** del DPS
* El **Id. de registro** del dispositivo que ha creado
* La **Clave principal** de una inscripción individual o una [clave derivada](#derive-a-device-key) para dispositivos que usan una inscripción de grupo.

# <a name="linux"></a>[Linux](#tab/linux)

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

# <a name="linux-on-windows"></a>[Linux en Windows](#tab/eflow)

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"

Puede usar PowerShell o Windows Admin Center para aprovisionar el dispositivo de IoT Edge.

### <a name="powershell"></a>PowerShell

Para PowerShell, ejecute el siguiente comando con los valores de marcador de posición actualizados con sus propios valores:

```powershell
Provision-EflowVm -provisioningType DpsSymmetricKey -scopeId PASTE_YOUR_ID_SCOPE_HERE -registrationId PASTE_YOUR_REGISTRATION_ID_HERE -symmKey PASTE_YOUR_PRIMARY_KEY_OR_DERIVED_KEY_HERE
```

### <a name="windows-admin-center"></a>Windows Admin Center

Para Windows Admin Center, siga los pasos a continuación:

1. En el panel **Azure IoT Edge device provisioning** (Aprovisionamiento de dispositivos de Azure IoT Edge), seleccione **Symmetric Key (DPS)** (clave simétrica [DPS]) en el menú desplegable Método de aprovisionamiento.

1. En [Azure Portal](https://ms.portal.azure.com/), vaya a su instancia de DPS.

1. Proporcione el identificador de ámbito de DPS, el identificador de registro del dispositivo y la clave principal de inscripción o la clave derivada en los campos de Windows Admin Center.

1. Elija **Provisioning with the selected method** (aprovisionar con el método seleccionado).

   ![Elija Aprovisionar con el método seleccionado después de rellenar los campos obligatorios para el aprovisionamiento de claves simétricas.](./media/how-to-install-iot-edge-on-windows/provisioning-with-selected-method-symmetric-key.png)

1. Una vez completado el aprovisionamiento, seleccione **Finalizar**. Volverá al panel principal. Ahora debería mostrarse un nuevo dispositivo, cuyo tipo es `IoT Edge Devices`. Puede seleccionar el dispositivo de IoT Edge para conectarse a él. Una vez en la página **Información general** del dispositivo, puede ver los valores de **IoT Edge Module List** (lista de módulos de IoT Edge) y **IoT Edge Status** (estado de IoT Edge) del dispositivo.

:::moniker-end
<!-- end 1.1. -->

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

>[!NOTE]
>Actualmente, no hay compatibilidad para IoT Edge, versión 1.2, en ejecución en IoT Edge para Linux en Windows.

:::moniker-end
<!-- end 1.2 -->

# <a name="windows"></a>[Windows](#tab/windows)

<!-- 1.1 -->
:::moniker range="=iotedge-2018-06"

1. Abra una ventana de Azure PowerShell en modo de administrador. Asegúrese de usar una sesión de AMD64 de PowerShell para instalar IoT Edge, no PowerShell (x86).

1. El comando **Initialize-IoTEdge** configura el entorno de ejecución de Azure IoT Edge en el equipo. De forma predeterminada, el comando realiza el aprovisionamiento manual con contenedores de Windows, a menos que se use la marca `-DpsSymmetricKey` para emplear el aprovisionamiento automático con la autenticación de clave simétrica.

   Reemplace los valores de marcador de posición para `{scope_id}`, `{registration_id}` y `{symmetric_key}` con los datos que ha recopilado antes.

   Si va a usar contenedores de Linux en Windows, agregue el parámetro `-ContainerOs Linux`.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -DpsSymmetricKey -ScopeId {scope ID} -RegistrationId {registration ID} -SymmetricKey {symmetric key}
   ```

:::moniker-end
<!-- end 1.1 -->

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

>[!NOTE]
>Actualmente, no hay compatibilidad para IoT Edge, versión 1.2, en ejecución en Windows.

:::moniker-end
<!-- end 1.2 -->

---

## <a name="verify-successful-installation"></a>Comprobación de instalación correcta

Si el entorno de ejecución se inició correctamente, puede ir a IoT Hub y empezar a implementar módulos de IoT Edge en el dispositivo.

Puede comprobar que la inscripción individual que ha creado se ha utilizado en el servicio Device Provisioning. En Azure Portal, vaya a la instancia del servicio Device Provisioning. Abra los detalles de la inscripción para la inscripción individual que ha creado. Tenga en cuenta que el estado de la inscripción está **asignado** y se muestra el id. de dispositivo.

Use los siguientes comandos en el dispositivo para comprobar que la instancia de IoT Edge se ha instalado e iniciado correctamente.

# <a name="linux"></a>[Linux](#tab/linux)

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

# <a name="linux-on-windows"></a>[Linux en Windows](#tab/eflow)

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"

Conéctese a la máquina virtual de IoT Edge para Linux en Windows:

```powershell
Connect-EflowVM
```

Compruebe el estado del servicio IoT Edge.

```cmd/sh
sudo systemctl status iotedge
```

Examine los registros del servicio.

```cmd/sh
sudo journalctl -u iotedge --no-pager --no-full
```

Enumere los módulos en ejecución.

```cmd/sh
sudo iotedge list
```

:::moniker-end

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

>[!NOTE]
>Actualmente, no hay compatibilidad para IoT Edge, versión 1.2, en ejecución en IoT Edge para Linux en Windows.

:::moniker-end
<!-- end 1.2 -->

# <a name="windows"></a>[Windows](#tab/windows)

<!-- 1.1 -->
:::moniker range="=iotedge-2018-06"

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

:::moniker-end

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

>[!NOTE]
>Actualmente, no hay compatibilidad para IoT Edge, versión 1.2, en ejecución en Windows.

:::moniker-end
<!-- end 1.2 -->

---

## <a name="next-steps"></a>Pasos siguientes

El proceso de inscripción en Device Provisioning Service permite establecer el id. de dispositivo y las etiquetas del dispositivo gemelo al mismo tiempo que aprovisiona el nuevo dispositivo. Puede usar esos valores para dirigirse a dispositivos individuales o grupos de dispositivos con la administración automática de dispositivos. Aprenda a [implementar y supervisar los módulos de IoT Edge a escala mediante Azure Portal](how-to-deploy-at-scale.md) o la [CLI de Azure](how-to-deploy-cli-at-scale.md).
