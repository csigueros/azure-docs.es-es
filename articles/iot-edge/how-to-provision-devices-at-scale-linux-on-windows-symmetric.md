---
title: Creación y aprovisionamiento de IoT Edge para dispositivos con claves simétricas en Linux en Windows - Azure IoT Edge | Microsoft Docs
description: Uso de la atestación de clave simétrica para probar el aprovisionamiento de Linux en dispositivos Windows a escala para Azure IoT Edge con el servicio Device Provisioning
author: v-tcassi
ms.author: v-tcassi
ms.reviewer: kgremban
ms.date: 08/17/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
monikerRange: =iotedge-2018-06
ms.openlocfilehash: d7d33b2f840b88226883bc9e6aff2823d7aa6cd2
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128699574"
---
# <a name="create-and-provision-iot-edge-for-linux-on-windows-devices-at-scale-using-symmetric-keys"></a>Creación y aprovisionamiento de IoT Edge para Linux en dispositivos Windows a escala mediante claves simétricas 

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

En este artículo se proporcionan instrucciones de un extremo a otro para el aprovisionamiento automático de una o varias [IoT Edge para Linux en dispositivos Windows](iot-edge-for-linux-on-windows.md) mediante claves simétricas. Puede aprovisionar los dispositivos Azure IoT Edge de forma automática con el [Azure IoT Hub Device Provisioning Service](../iot-dps/index.yml) (DPS). Si no está familiarizado con el proceso de aprovisionamiento automático, revise la [información general sobre el aprovisionamiento](../iot-dps/about-iot-dps.md#provisioning-process) antes de continuar.

Las tareas son las siguientes:

1. Crear una **inscripción individual** para un dispositivo o una **inscripción de grupo** para un conjunto de dispositivos.
1. Implemente una máquina virtual Linux con el entorno de ejecución de Azure IoT Edge instalado y conéctela al IoT Hub.

La atestación de clave simétrica es un enfoque sencillo para autenticar un dispositivo con una instancia del servicio Device Provisioning. Este método de atestación representa una experiencia de "Hola mundo" para los desarrolladores que no estén familiarizados con el aprovisionamiento de dispositivos, o no tengan estrictos requisitos de seguridad. La atestación de dispositivo mediante un [TPM](../iot-dps/concepts-tpm-attestation.md) o [certificado X.509](../iot-dps/concepts-x509-attestation.md) es más segura y se debe usar cuando los requisitos de seguridad son más estrictos.

## <a name="prerequisites"></a>Prerrequisitos

Recursos en la nube:

* Una instancia de IoT Hub activa.
* Una instancia de IoT Hub Device Provisioning Service en Azure que esté vinculada a IoT Hub.
  * Si no tiene una instancia de Device Provisioning Service, puede seguir las instrucciones de las secciones [Creación de un nuevo IoT Hub Device Provisioning Service](../iot-dps/quick-setup-auto-provision.md#create-a-new-iot-hub-device-provisioning-service) y [Vínculo al centro de IoT y el Device Provisioning Service](../iot-dps/quick-setup-auto-provision.md#link-the-iot-hub-and-your-device-provisioning-service) de la guía de inicio rápido IoT Hub Device Provisioning Service.
  * Cuando Device Provisioning Service esté en ejecución, copie el valor de **Ámbito de id.** de la página de información general. Use este valor cuando configure el entorno de ejecución de IoT Edge.

Una máquina de desarrollo:

* Un dispositivo Windows con los siguientes requisitos mínimos del sistema:
  * Windows 10, versión 1809 o posterior; compilación 17763 o posterior
  * Ediciones Pro, Enterprise o Server
  * Memoria libre mínima: 1 GB
  * Espacio libre en disco mínimo: 10 GB
  * Compatibilidad con la virtualización
    * En Windows 10, habilite Hyper-V. Para obtener más información, vea [Información de Hyper-V en Windows 10](/virtualization/hyper-v-on-windows/quick-start/enable-hyper-v).
    * En Windows Server, instale el rol de Hyper-V y cree un conmutador de red predeterminado. Para más información, consulte [Virtualización anidada en Azure IoT Edge para Linux en Windows](nested-virtualization.md).
    * En una máquina virtual, configure la virtualización anidada. Para obtener más información, consulte [Virtualización anidada](nested-virtualization.md).
  * Soporte de red
    * Windows Server no incluye un conmutador predeterminado. Para poder implementar EFLOW en un dispositivo Windows Server, debe crear un conmutador virtual.  Para obtener más información, consulte [Creación de un conmutador virtual para Linux en Windows](how-to-create-virtual-switch.md).
    * Las versiones de escritorio de Windows incluyen un conmutador predeterminado que se puede usar para la instalación de EFLOW. Si es necesario, puede crear su propio conmutador virtual personalizado.

   > [!TIP]
   > Si quiere usar **módulos de Linux con aceleración por GPU** en la implementación de Azure IoT Edge para Linux en Windows, debe tener en cuenta varias opciones de configuración. Tendrá que instalar los controladores correctos en función de la arquitectura de GPU y es posible que necesite acceder a una compilación del programa Windows Insider. Para determinar sus necesidades de configuración y cumplir estos requisitos previos, consulte [Aceleración por GPU para Azure IoT Edge para Linux en Windows](gpu-acceleration.md).

Puede usar tanto **PowerShell** como **Windows Admin Center** para gestionar el dispositivo IoT Edge. Cada utilidad tiene sus propios requisitos previos:

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Si desea usar PowerShell, siga estos pasos para preparar el dispositivo de destino para la instalación de Azure IoT Edge para Linux en Windows y la implementación de la máquina virtual Linux:

1. Establezca la directiva de ejecución en el dispositivo de destino en `AllSigned`. Puede comprobar la directiva de ejecución actual en un símbolo del sistema de PowerShell con privilegios elevados con el siguiente comando:

   ```powershell
   Get-ExecutionPolicy -List
   ```

   Si la directiva de ejecución de `local machine` no es `AllSigned`, puede establecer la directiva de ejecución con el siguiente comando:

   ```powershell
   Set-ExecutionPolicy -ExecutionPolicy AllSigned -Force
   ```

Para obtener más información sobre el módulo de Azure IoT Edge para Linux en Windows PowerShell, consulte la [referencia de funciones de PowerShell](reference-iot-edge-for-linux-on-windows-functions.md).

# <a name="windows-admin-center"></a>[Windows Admin Center](#tab/windowsadmincenter)

Si desea usar Windows Admin Center, siga estos pasos para descargar e instalar Windows Admin Center e instalar la extensión Windows Admin Center Azure IoT Edge:

   1. Descargue y ejecute el [instalador de Windows Admin Center](https://aka.ms/wacdownload). Siga las indicaciones del Asistente para la instalación a fin de instalar Windows Admin Center.

   1. Una vez instalado, use un explorador compatible para abrir Windows Admin Center. Entre los exploradores admitidos se incluyen Microsoft Edge (Windows 10, versión 1709 o posterior), Google Chrome y Microsoft Edge Insider.

   1. Al usar Windows Admin Center por primera vez, se le pedirá que seleccione un certificado para usarlo. Seleccione **Cliente de Windows Admin Center** como certificado.

   1. Instale la extensión de Azure IoT Edge. Seleccione el icono de engranaje en la parte superior derecha del panel de Windows Admin Center.

      ![Seleccione el icono de engranaje en la parte superior derecha del panel de información para acceder a la configuración, PNG.](./media/how-to-provision-devices-at-scale-linux-on-windows-x509/select-gear-icon.png)

   1. En el menú **Configuración**, en **Puerta de enlace**, seleccione **Extensiones**.

   1. En la pestaña **Extensiones disponibles**, busque **Azure IoT Edge** en la lista de extensiones. Esta dicha opción y seleccione la indicación **Instalar** sobre la lista de extensiones.

   1. Una vez finalizada la instalación, debería ver Azure IoT Edge en la lista de extensiones instaladas en la pestaña **Extensiones instaladas**.

---

## <a name="create-a-dps-enrollment"></a>Crear una inscripción de DPS

Cree una inscripción para aprovisionar uno o varios dispositivos mediante DPS.

Si desea aprovisionar un único dispositivo IoT Edge, cree una **inscripción individual**. Si necesita aprovisionar varios dispositivos, siga los pasos para crear una **inscripción de grupo** de DPS.

Al crear una inscripción en DPS, tiene la oportunidad de declarar un **estado inicial de dispositivo gemelo**. En el dispositivo gemelo, puede establecer etiquetas para agrupar dispositivos por cualquier métrica que necesite en su solución, como la región, el entorno, la ubicación o el tipo de dispositivo. Estas etiquetas se usan para crear [implementaciones automáticas](how-to-deploy-at-scale.md).

Para más información sobre las inscripciones en Device Provisioning Service, consulte [Administración de inscripciones de dispositivos](../iot-dps/how-to-manage-enrollments.md).

# <a name="individual-enrollment"></a>[Inscripción individual](#tab/individual-enrollment)

### <a name="create-a-dps-individual-enrollment"></a>Creación de una inscripción individual de DPS

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

# <a name="group-enrollment"></a>[Inscripción de grupo](#tab/group-enrollment)

### <a name="create-a-dps-group-enrollment"></a>Creación de una inscripción de grupo de DPS

> [!TIP]
> Los pasos de este artículo son para Azure Portal, pero también puede crear inscripciones de grupo mediante la CLI de Azure. Para más información, consulte [az iot dps enrollment-group](/cli/azure/iot/dps/enrollment-group). Como parte del comando de la CLI, use la marca **edge-enabled** para especificar que la inscripción es para dispositivos de IoT Edge. En el caso de una inscripción de grupo, todos los dispositivos deben ser dispositivos IoT Edge, o bien ninguno puede serlo.

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

Ahora que existe un grupo de inscripciones, el entorno de ejecución de Azure IoT Edge puede aprovisionar los dispositivos durante la instalación de forma automática.

#### <a name="derive-a-device-key"></a>Derivación de una clave de dispositivo

Cada dispositivo que se aprovisiona como parte de una inscripción de grupo necesita una clave de dispositivo derivada para realizar la atestación de clave simétrica con la inscripción durante el aprovisionamiento.

Para generar la clave del dispositivo, use la clave que copió del grupo de inscripciones de DPS para calcular un valor [HMAC-SHA256](https://wikipedia.org/wiki/HMAC) del identificador de registro único del dispositivo y convierta el resultado a formato Base64.

> [!IMPORTANT]
> No incluya la clave principal o secundaria de la inscripción en el código del dispositivo.

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

A continuación se muestra una salida de ejemplo de una clave de dispositivo derivada:

```powershell
Jsm0lyGpjaVYVP2g3FnmnmG9dI/9qU24wNoykUmermc=
```

---

## <a name="install-iot-edge"></a>Instalación de IoT Edge

Implemente Azure IoT Edge para Linux en Windows en el dispositivo de destino.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Instale IoT Edge para Linux en Windows en el dispositivo de destino.

> [!NOTE]
> En el siguiente proceso de PowerShell se muestra cómo implementar IoT Edge para Linux en Windows en el dispositivo local. Si quiere realizar la implementación en un dispositivo de destino remoto mediante PowerShell, puede usar [PowerShell remoto](/powershell/module/microsoft.powershell.core/about/about_remote) para establecer una conexión a un dispositivo remoto y ejecutar estos comandos de manera remota en ese dispositivo.

1. En una sesión de PowerShell con privilegios elevados, ejecute cada uno de los siguientes comandos para descargar IoT Edge para Linux en Windows.

   ```powershell
   $msiPath = $([io.Path]::Combine($env:TEMP, 'AzureIoTEdge.msi'))
   $ProgressPreference = 'SilentlyContinue'
   Invoke-WebRequest "https://aka.ms/AzEflowMSI" -OutFile $msiPath
   ```

1. Instale IoT Edge para Linux en Windows en el dispositivo.

   ```powershell
   Start-Process -Wait msiexec -ArgumentList "/i","$([io.Path]::Combine($env:TEMP, 'AzureIoTEdge.msi'))","/qn"
   ```

   Para especificar la instalación personalizada de IoT Edge para Linux en Windows y los directorios VHDX, agregue los parámetros `INSTALLDIR="<FULLY_QUALIFIED_PATH>"` y `VHDXDIR="<FULLY_QUALIFIED_PATH>"` al comando de instalación.

1. Establezca la directiva de ejecución en el dispositivo de destino en `AllSigned` si todavía no lo está. Consulte los requisitos previos de PowerShell para los comandos para comprobar la directiva de ejecución actual y establecer la directiva de ejecución en `AllSigned`.

1. Creación de la implementación de IoT Edge para Linux en Windows. La implementación crea la máquina virtual Linux e instala el entorno de ejecución de IoT Edge de forma automática.

   ```powershell
   Deploy-Eflow
   ```

   >[!TIP]
   >De forma predeterminada, el comando `Deploy-Eflow` crea la máquina virtual Linux con 1 GB de RAM, 1 núcleo de vCPU y 16 GB de espacio en disco. Sin embargo, los recursos que necesita la máquina virtual dependen en gran medida de las cargas de trabajo que implemente. Si la máquina virtual no tiene memoria suficiente para dar servicio a las cargas de trabajo, no se iniciará.
   >
   >Puede personalizar los recursos disponibles de la máquina virtual mediante los parámetros opcionales del comando `Deploy-Eflow`.
   >
   >Por ejemplo, el comando siguiente crea una máquina virtual con 4 núcleos de vCPU, 4 GB de RAM y 20 GB de espacio en disco:
   >
   >   ```powershell
   >   Deploy-Eflow -cpuCount 4 -memoryInMB 4096 -vmDiskSize 20
   >   ```
   >
   >Para obtener información sobre todos los parámetros opcionales disponibles, vea [Funciones de PowerShell para IoT Edge para Linux en Windows](reference-iot-edge-for-linux-on-windows-functions.md#deploy-eflow).

   Puede asignar una GPU a la implementación para habilitar módulos de Linux con aceleración por GPU. Para obtener acceso a estas características, deberá instalar los requisitos previos detallados en [Aceleración por GPU para Azure IoT Edge para Linux en Windows](gpu-acceleration.md).

   Para usar un acceso directo de GPU, agregue los parámetros **gpuName**, **gpuPassthroughType** y **gpuCount** a su comando `Deploy-Eflow`. Para obtener información sobre todos los parámetros opcionales disponibles, vea [Funciones de PowerShell para IoT Edge para Linux en Windows](reference-iot-edge-for-linux-on-windows-functions.md#deploy-eflow).

   >[!WARNING]
   >La habilitación del acceso directo de dispositivos de hardware puede aumentar los riesgos de seguridad. Microsoft recomienda usar un controlador de mitigación de dispositivos del proveedor de GPU, si procede. Para obtener más información, vea [Implementación de dispositivos gráficos mediante la asignación discreta de dispositivos](/windows-server/virtualization/hyper-v/deploy/deploying-graphics-devices-using-dda).

1. Escriba "Y" para aceptar los términos de licencia.

1. Escriba "O" o "R" para activar o desactivar la opción **Datos de diagnóstico opcionales** en función de sus preferencias.

1. Una vez completada la implementación, la ventana de PowerShell indica **Implementación correcta**.

   ![Una implementación correcta indicará "Implementación correcta" al final de los mensajes, PNG.](./media/how-to-provision-devices-at-scale-linux-on-windows-symmetric/successful-powershell-deployment.png)

Una vez completada la implementación, está listo para aprovisionar el dispositivo.

# <a name="windows-admin-center"></a>[Windows Admin Center](#tab/windowsadmincenter)

>[!NOTE]
>La extensión de Azure IoT Edge para Windows Admin Center está actualmente en [versión preliminar pública](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Los procesos de instalación y administración pueden ser diferentes de los de las características disponibles con carácter general.

Instale Azure IoT Edge para Linux en Windows en el dispositivo.

1. Descargue el [instalador de Azure IoT Edge para Linux en Windows](https://aka.ms/AzEflowMSI).

1. Una vez que haya instalado Azure IoT Edge para Linux en Windows, abra Windows Admin Center.

   En la página de inicio de Windows Admin Center, en la lista de conexiones, verá una conexión de host local que representa el equipo en el que se ejecuta Windows Admin Center. Los servidores, equipos o clústeres adicionales que administre también aparecerán aquí.

   Puede usar Windows Admin Center para instalar y administrar Azure IoT Edge para Linux en Windows tanto en el dispositivo local como en los dispositivos administrados remotos. En esta guía, la conexión de host local actuará como el dispositivo de destino para la implementación de Azure IoT Edge para Linux en Windows.

1. Confirme que el dispositivo local aparece en **Todas las conexiones**, como se muestra a continuación.

   ![Panel inicial de Windows Admin Center con el dispositivo de destino en la lista, PNG.](./media/how-to-provision-devices-at-scale-linux-on-windows-symmetric/windows-admin-center-initial-dashboard.png)

   Si quiere realizar la implementación en un dispositivo de destino remoto en lugar de en el dispositivo local y no ve el dispositivo de destino deseado en la lista, siga las [instrucciones para agregar el dispositivo](/windows-server/manage/windows-admin-center/use/get-started#connecting-to-managed-nodes-and-clusters).

1. Seleccione **+ Agregar** para empezar a crear la implementación. La implementación crea la máquina virtual Linux e instala el entorno de ejecución de IoT Edge de forma automática.

1. En el panel **Agregar o crear recursos**, busque el elemento **Azure IoT Edge**. Seleccione **Crear nuevo** para instalar una nueva instancia de Azure IoT Edge para Linux en Windows en un dispositivo.

   Si ya tiene una instancia de IoT Edge para Linux en Windows ejecutándose en un dispositivo, puede seleccionar **Agregar** para conectarse a ese dispositivo de IoT Edge existente y administrarlo con Windows Admin Center.

   ![Seleccione Crear nuevo en el elemento Azure IoT Edge de Windows Admin Center, PNG.](./media/how-to-provision-devices-at-scale-linux-on-windows-symmetric/resource-creation-tiles.png)

1. Se abrirá el panel **Crear una implementación de Azure IoT Edge para Linux en Windows**. En la pestaña **1. Getting Started** (1. Tareas iniciales), revise los requisitos mínimos y seleccione **Siguiente**.

1. Revise los términos de licencia, marque la casilla **Acepto** y seleccione **Siguiente**.

1. Puede activar o desactivar la opción **Datos de diagnóstico opcionales** en función de sus preferencias.

1. Seleccione **Siguiente: Implementación**.

   ![Seleccione el siguiente: Implementar botón después de activar o desactivar la opción datos de diagnóstico opcionales según sus preferencias, PNG.](./media/how-to-provision-devices-at-scale-linux-on-windows-symmetric/select-next-deploy.png)

1. En la pestaña **2. Implementar**, en **Seleccione un dispositivo de destino**, haga clic en su dispositivo para validar que cumple los requisitos mínimos. Una vez que su estado se confirme como compatible, seleccione **Siguiente**.

   ![Seleccione el dispositivo para comprobar que es compatible, PNG.](./media/how-to-provision-devices-at-scale-linux-on-windows-symmetric/evaluate-supported-device.png)

1. En la pestaña **Configuración 2.2**, revise los valores de configuración de la implementación.

   >[!NOTE]
   >IoT Edge para Linux en Windows usa un conmutador predeterminado, que asigna a la máquina virtual Linux una dirección IP interna. No se puede acceder a esta dirección IP interna desde fuera de la máquina virtual de Windows. Puede conectar localmente con la máquina virtual mientras tenga la sesión iniciada en la máquina virtual de Windows.
   >
   >Si usa Windows Server, [configure un conmutador predeterminado](how-to-create-virtual-switch.md) antes de implementar IoT Edge para Linux en Windows.

   Puede asignar una GPU a la implementación para habilitar módulos de Linux con aceleración por GPU. Para obtener acceso a estas características, deberá instalar los requisitos previos detallados en [Aceleración por GPU para Azure IoT Edge para Linux en Windows](gpu-acceleration.md). Si va a instalar estos requisitos previos en este momento del proceso de implementación, tendrá que empezar de nuevo desde el principio.

   Hay dos opciones disponibles para el acceso directo de GPU: **asignación directa de dispositivos (DDA)** y **paravirtualización de GPU (GPU-PV)** , en función del adaptador de GPU que asigne a la implementación. A continuación se muestran ejemplos de cada método.

   Para el método de asignación directa de dispositivos, seleccione el número de procesadores de GPU que se asignarán a la máquina virtual Linux.

   ![Opciones de configuración con la asignación directa de dispositivos de GPU habilitada, PNG.](./media/how-to-provision-devices-at-scale-linux-on-windows-symmetric/gpu-passthrough-direct-device-assignment.png)

   Para el método de paravirtualización, no se necesita ninguna configuración adicional.

   ![Opciones de configuración con la paravirtualización de GPU habilitada, PNG.](./media/how-to-provision-devices-at-scale-linux-on-windows-symmetric/gpu-passthrough-paravirtualization.png)

   >[!WARNING]
   >La habilitación del acceso directo de dispositivos de hardware puede aumentar los riesgos de seguridad. Microsoft recomienda usar un controlador de mitigación de dispositivos del proveedor de GPU, si procede. Para obtener más información, vea [Implementación de dispositivos gráficos mediante la asignación discreta de dispositivos](/windows-server/virtualization/hyper-v/deploy/deploying-graphics-devices-using-dda).

   Cuando esté satisfecho con la configuración, seleccione **Siguiente**.

1. En la pestaña **2.3 Implementación**, puede ver el progreso de la implementación. El proceso completo incluye la descarga del paquete de Azure IoT Edge para Linux en Windows, la instalación del paquete, la configuración del dispositivo host y la configuración de la máquina virtual Linux. Este proceso puede tardar varios minutos en completarse. A continuación se muestra una implementación correcta.

   ![Una implementación correcta mostrará todos los pasos con una marca de verificación verde y la etiqueta "Completado", PNG.](./media/how-to-provision-devices-at-scale-linux-on-windows-symmetric/successful-deployment.png)

Una vez completada la implementación, está listo para aprovisionar el dispositivo. Seleccione **Siguiente: Conectar** para ir a la pestaña **3. Conectar**, que controla el aprovisionamiento de dispositivos de Azure IoT Edge.

---

## <a name="configure-the-device-with-provisioning-information"></a>Configuración del dispositivo con la información de aprovisionamiento

Una vez que el entorno de ejecución está instalado en el dispositivo, configure el dispositivo con la información que usa para conectarse al servicio Device Provisioning y a IoT Hub.

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

## <a name="verify-successful-configuration"></a>Comprobación de configuración correcta

Compruebe que IoT Edge para Linux en Windows se haya instalado y configurado correctamente en el dispositivo de IoT Edge.

# <a name="individual-enrollment"></a>[Inscripción individual](#tab/individual-enrollment)

Puede comprobar que la inscripción individual que ha creado se ha utilizado en el servicio Device Provisioning. En Azure Portal, vaya a la instancia del servicio Device Provisioning. Abra los detalles de la inscripción para la inscripción individual que ha creado. Tenga en cuenta que el estado de la inscripción está **asignado** y se muestra el id. de dispositivo.

# <a name="group-enrollment"></a>[Inscripción de grupo](#tab/group-enrollment)

Puede comprobar que la inscripción de grupo que ha creado se ha utilizado en el servicio Device Provisioning. En Azure Portal, vaya a la instancia del servicio Device Provisioning. Abra los detalles de la inscripción para la inscripción de grupo que ha creado. Vaya a la pestaña **Entradas de registro** para ver todos los dispositivos registrados en ese grupo.

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

El proceso de inscripción en Device Provisioning Service permite establecer el id. de dispositivo y las etiquetas del dispositivo gemelo al mismo tiempo que aprovisiona el nuevo dispositivo. Puede usar esos valores para dirigirse a dispositivos individuales o grupos de dispositivos con la administración automática de dispositivos. Aprenda a [implementar y supervisar los módulos de IoT Edge a escala mediante Azure Portal](how-to-deploy-at-scale.md) o la [CLI de Azure](how-to-deploy-cli-at-scale.md).

También puede:

* Siga [implementando módulos de IoT Edge](how-to-deploy-modules-portal.md) para información sobre cómo implementar módulos en el dispositivo.
* Obtenga información acerca de cómo [administrar certificados en IoT Edge para Linux en máquinas virtuales de Windows](how-to-manage-device-certificates.md) y transferir archivos desde el sistema operativo host a la máquina virtual de Linux.
* Aprenda a [configurar los dispositivos de IoT Edge para que se comuniquen a través de un servidor proxy](how-to-configure-proxy-support.md).
