---
title: 'Aprovisionamiento de IoT Edge para Linux en un dispositivo Windows mediante un TPM: Azure IoT Edge | Microsoft Docs'
description: Utilice un TPM simulado de Linux en un dispositivo Windows para probar Azure Device Provisioning Service para Azure IoT Edge
author: kgremban
manager: lizross
ms.author: kgremban
ms.reviewer: fcabrera
ms.date: 07/08/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
monikerRange: =iotedge-2018-06
ms.openlocfilehash: d6943fa78960760a665f0abad51f0839da9d63bb
ms.sourcegitcommit: e82ce0be68dabf98aa33052afb12f205a203d12d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/07/2021
ms.locfileid: "129661671"
---
# <a name="create-and-provision-an-iot-edge-for-linux-on-windows-device-at-scale-using-a-tpm"></a>Creación y aprovisionamiento de IoT Edge para Linux en un dispositivo Windows mediante TPM

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

En este artículo, se proporcionan instrucciones completas para el aprovisionamiento automático de IoT Edge para Linux en un dispositivo Windows mediante un Módulo de plataforma segura (TPM). Puede aprovisionar los dispositivos Azure IoT Edge de forma automática con [Azure IoT Hub Device Provisioning Service](../iot-dps/index.yml) (DPS). Si no está familiarizado con el proceso de aprovisionamiento automático, revise la [información general sobre el aprovisionamiento](../iot-dps/about-iot-dps.md#provisioning-process) antes de continuar.

En este artículo, se describen dos metodologías. Seleccione sus preferencias en función de la arquitectura de la solución:

1. Aprovisionamiento automático de Linux en un dispositivo Windows con hardware de TPM físico.
1. Aprovisionamiento automático de Linux en un dispositivo Windows mediante un TPM simulado. Esta metodología solo se recomienda como escenario de prueba, porque un TPM simulado no ofrece la misma seguridad que un TPM físico.

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

Recursos en la nube:

* Una instancia de IoT Hub activa.
* Una instancia de IoT Hub Device Provisioning Service en Azure que esté vinculada a IoT Hub.
  * Si no tiene una instancia de Device Provisioning Service, puede seguir las instrucciones de las secciones [Creación de una instancia de IoT Hub Device Provisioning Service](../iot-dps/quick-setup-auto-provision.md#create-a-new-iot-hub-device-provisioning-service) y [Vínculo al centro de IoT y a Device Provisioning Service](../iot-dps/quick-setup-auto-provision.md#link-the-iot-hub-and-your-device-provisioning-service) del inicio rápido de IoT Hub Device Provisioning Service.
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

Para obtener más información sobre el módulo de PowerShell de Azure IoT Edge para Linux en Windows, consulte la [referencia de funciones de PowerShell](reference-iot-edge-for-linux-on-windows-functions.md).

# <a name="windows-admin-center"></a>[Windows Admin Center](#tab/windowsadmincenter)

Si desea usar Windows Admin Center, siga estos pasos para descargar e instalar Windows Admin Center e instalar la extensión Windows Admin Center Azure IoT Edge:

   1. Descargue y ejecute el [instalador de Windows Admin Center](https://aka.ms/wacdownload). Siga las indicaciones del Asistente para la instalación a fin de instalar Windows Admin Center.

   1. Una vez instalado, use un explorador compatible para abrir Windows Admin Center. Entre los exploradores admitidos se incluyen Microsoft Edge (Windows 10, versión 1709 o posterior), Google Chrome y Microsoft Edge Insider.

   1. Al usar Windows Admin Center por primera vez, se le pedirá que seleccione un certificado para usarlo. Seleccione **Cliente de Windows Admin Center** como certificado.

   1. Instale la extensión de Azure IoT Edge. Seleccione el icono de engranaje en la parte superior derecha del panel de Windows Admin Center.

      ![Seleccione el icono de engranaje en la parte superior derecha del panel para acceder a la configuración, PNG.](./media/how-to-provision-devices-at-scale-linux-on-windows-tpm/select-gear-icon.png)

   1. En el menú **Configuración**, en **Puerta de enlace**, seleccione **Extensiones**.

   1. En la pestaña **Extensiones disponibles**, busque **Azure IoT Edge** en la lista de extensiones. Esta dicha opción y seleccione la indicación **Instalar** sobre la lista de extensiones.

   1. Una vez finalizada la instalación, debería ver Azure IoT Edge en la lista de extensiones instaladas en la pestaña **Extensiones instaladas**.

---

> [!NOTE]
> Es necesario TPM 2.0 cuando se usa la atestación de TPM con DPS.
>
> Solo puede crear inscripciones de DPS individuales, no de grupo, cuando se usa un TPM.

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

1. Establezca la directiva de ejecución en el dispositivo de destino en `AllSigned` si todavía no lo está. Consulte los requisitos previos de PowerShell para los comandos a fin de comprobar la directiva de ejecución actual y establecer la directiva de ejecución en `AllSigned`.

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

   ![Una implementación correcta indicará "Implementación correcta" al final de los mensajes, PNG.](./media/how-to-provision-devices-at-scale-linux-on-windows-tpm/successful-powershell-deployment.png)

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

   ![Panel inicial de Windows Admin Center con el dispositivo de destino en la lista, PNG.](./media/how-to-provision-devices-at-scale-linux-on-windows-tpm/windows-admin-center-initial-dashboard.png)

   Si quiere realizar la implementación en un dispositivo de destino remoto en lugar de en el dispositivo local y no ve el dispositivo de destino deseado en la lista, siga las [instrucciones para agregar el dispositivo](/windows-server/manage/windows-admin-center/use/get-started#connecting-to-managed-nodes-and-clusters).

1. Seleccione **+ Agregar** para empezar a crear la implementación. La implementación crea la máquina virtual Linux e instala el entorno de ejecución de IoT Edge de forma automática.

1. En el panel **Agregar o crear recursos**, busque el elemento **Azure IoT Edge**. Seleccione **Crear nuevo** para instalar una nueva instancia de Azure IoT Edge para Linux en Windows en un dispositivo.

   Si ya tiene una instancia de IoT Edge para Linux en Windows ejecutándose en un dispositivo, puede seleccionar **Agregar** para conectarse a ese dispositivo de IoT Edge existente y administrarlo con Windows Admin Center.

   ![Seleccione "Crear nuevo" en el elemento Azure IoT Edge de Windows Admin Center, PNG.](./media/how-to-provision-devices-at-scale-linux-on-windows-tpm/resource-creation-tiles.png)

1. Se abrirá el panel **Crear una implementación de Azure IoT Edge para Linux en Windows**. En la pestaña **1. Getting Started** (1. Tareas iniciales), revise los requisitos mínimos y seleccione **Siguiente**.

1. Revise los términos de licencia, marque la casilla **Acepto** y seleccione **Siguiente**.

1. Puede activar o desactivar la opción **Datos de diagnóstico opcionales** en función de sus preferencias.

1. Seleccione **Siguiente: Implementación**.

   ![Seleccione el botón "Siguiente: Implementación" después de activar o desactivar los datos de diagnóstico opcionales según sus preferencias, PNG.](./media/how-to-provision-devices-at-scale-linux-on-windows-tpm/select-next-deploy.png)

1. En la pestaña **2. Implementar**, en **Seleccione un dispositivo de destino**, haga clic en su dispositivo para validar que cumple los requisitos mínimos. Una vez que su estado se confirme como compatible, seleccione **Siguiente**.

   ![Seleccione el dispositivo para comprobar si es compatible, PNG.](./media/how-to-provision-devices-at-scale-linux-on-windows-tpm/evaluate-supported-device.png)

1. En la pestaña **Configuración 2.2**, revise los valores de configuración de la implementación.

   >[!NOTE]
   >IoT Edge para Linux en Windows usa un conmutador predeterminado, que asigna a la máquina virtual Linux una dirección IP interna. No se puede acceder a esta dirección IP interna desde fuera de la máquina virtual de Windows. Puede conectar localmente con la máquina virtual mientras tenga la sesión iniciada en la máquina virtual de Windows.
   >
   >Si usa Windows Server, [configure un conmutador predeterminado](how-to-create-virtual-switch.md) antes de implementar IoT Edge para Linux en Windows.

   Puede asignar una GPU a la implementación para habilitar módulos de Linux con aceleración por GPU. Para obtener acceso a estas características, deberá instalar los requisitos previos detallados en [Aceleración por GPU para Azure IoT Edge para Linux en Windows](gpu-acceleration.md). Si va a instalar estos requisitos previos en este momento del proceso de implementación, tendrá que empezar de nuevo desde el principio.

   Hay dos opciones disponibles para el acceso directo de GPU: **asignación directa de dispositivos (DDA)** y **paravirtualización de GPU (GPU-PV)** , en función del adaptador de GPU que asigne a la implementación. A continuación se muestran ejemplos de cada método.

   Para el método de asignación directa de dispositivos, seleccione el número de procesadores de GPU que se asignarán a la máquina virtual Linux.

   ![Opciones de configuración con la asignación directa de dispositivos de GPU habilitada, PNG.](./media/how-to-provision-devices-at-scale-linux-on-windows-tpm/gpu-passthrough-direct-device-assignment.png)

   Para el método de paravirtualización, no se necesita ninguna configuración adicional.

   ![Opciones de configuración con la paravirtualización de GPU habilitada, PNG.](./media/how-to-provision-devices-at-scale-linux-on-windows-tpm/gpu-passthrough-paravirtualization.png)

   >[!WARNING]
   >La habilitación del acceso directo de dispositivos de hardware puede aumentar los riesgos de seguridad. Microsoft recomienda usar un controlador de mitigación de dispositivos del proveedor de GPU, si procede. Para obtener más información, vea [Implementación de dispositivos gráficos mediante la asignación discreta de dispositivos](/windows-server/virtualization/hyper-v/deploy/deploying-graphics-devices-using-dda).

   Cuando esté satisfecho con la configuración, seleccione **Siguiente**.

1. En la pestaña **2.3 Implementación**, puede ver el progreso de la implementación. El proceso completo incluye la descarga del paquete de Azure IoT Edge para Linux en Windows, la instalación del paquete, la configuración del dispositivo host y la configuración de la máquina virtual Linux. Este proceso puede tardar varios minutos en completarse. A continuación se muestra una implementación correcta.

   ![Una implementación correcta mostrará todos los pasos con una marca de verificación verde y la etiqueta "Completado", PNG.](./media/how-to-provision-devices-at-scale-linux-on-windows-tpm/successful-deployment.png)

1. Una vez completada la implementación, está listo para aprovisionar el dispositivo. Seleccione **Siguiente: Conectar** para ir a la pestaña **3. Conectar**, que controla el aprovisionamiento de dispositivos de Azure IoT Edge.

Deje de seguir el Asistente para la implementación en la página **Aprovisionamiento**, ya que, antes de continuar, debe seguir varios pasos con el fin de preparar el dispositivo para el aprovisionamiento con el TPM. Deje abierta la ventana de Windows Admin Center, ya que volverá a ella al final de este artículo para completar los pasos de aprovisionamiento.

---

## <a name="enable-tpm-passthrough"></a>Habilitación del acceso directo del TPM

La máquina virtual IoT Edge para Linux en Windows tiene una característica de TPM que se puede habilitar o deshabilitar. (Está deshabilitada de manera predeterminada). Cuando esta característica está habilitada, la máquina virtual puede acceder al TPM de la máquina host.

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

Para aprovisionar su dispositivo, debe recopilar información del chip de TPM y proporcionarla a su instancia de Device Provisioning Service (DPS) para que el servicio pueda reconocer el dispositivo cuando este intente conectarse.

Primero, debe determinar la **clave de aprobación**, que es única para cada chip del TPM y se obtiene del fabricante asociado a este. A continuación, debe proporcionar un **Id. de registro** único para el dispositivo. Puede derivar un Id. de registro único para el dispositivo del TPM, por ejemplo, creando un código hash SHA-256 de la clave de aprobación.

IoT Edge para Linux en Windows proporciona un script de PowerShell para ayudar a recuperar esta información del TPM. Para usar el script, siga estos pasos en el dispositivo:

1. Abra PowerShell en una sesión con privilegios elevados.

1. Clone el repositorio [iotedge-eflow](https://github.com/Azure/iotedge-eflow).

   ```powershell
   git clone https://github.com/Azure/iotedge-eflow.git
   ```

1. Importe el archivo descargado.

   ```powershell
   Import-Module <path>\iotedge-eflow\samples\scripts\EflowTpmProvisioningInfo.ps1
   ```

1. Ejecute el comando.

   ```powershell
   Get-EflowVmTpmProvisioningInformation
   ```

# <a name="simulated-tpm"></a>[TPM simulado](#tab/simulated-tpm)

Si no dispone de un TPM físico y desea probar este método de aprovisionamiento, puede simular uno en su dispositivo.

IoT Hub Device Provisioning Service proporciona ejemplos que simulan un TPM y devuelven la **clave de aprobación** y el **Id. de registro**.

1. Elija uno de los ejemplos de la siguiente lista en función del idioma que prefiera.
1. Deje de seguir los pasos de ejemplo de DPS una vez que el TPM simulado esté en ejecución y usted haya recopilado la **clave de aprobación** y el **Id. de registro**. No presione *Entrar* para ejecutar el registro en la aplicación de ejemplo.
1. Mantenga en ejecución la ventana que hospeda el TPM simulado hasta que haya terminado de probar este escenario.
1. Vuelva a este artículo para crear una inscripción de DPS y configurar su dispositivo.

Ejemplos de TPM simulado:

* [C](../iot-dps/quick-create-simulated-device.md)
* [Java](../iot-dps/quick-create-simulated-device-tpm-java.md)
* [C#](../iot-dps/quick-create-simulated-device-tpm-csharp.md)
* [Node.js](../iot-dps/quick-create-simulated-device-tpm-node.md)
* [Python](../iot-dps/quick-create-simulated-device-tpm-python.md)

---

## <a name="create-a-dps-enrollment"></a>Crear una inscripción de DPS

Recupere la información de aprovisionamiento del TPM y úsela para crear una inscripción individual en Device Provisioning Service.

Al crear una inscripción en DPS, tiene la oportunidad de declarar un **Estado inicial de dispositivo gemelo**. En el dispositivo gemelo, puede establecer etiquetas para agrupar dispositivos por cualquier métrica que necesite en su solución, como la región, el entorno, la ubicación o el tipo de dispositivo. Estas etiquetas se usan para crear [implementaciones automáticas](how-to-deploy-at-scale.md).

> [!TIP]
> Los pasos de este artículo son para Azure Portal, pero también puede crear inscripciones individuales mediante la CLI de Azure. Para más información, consulte [az iot dps enrollment](/cli/azure/iot/dps/enrollment). Como parte del comando de la CLI, use la marca **edge-enabled** para especificar que la inscripción es para un dispositivo de IoT Edge.

1. En [Azure Portal](https://portal.azure.com), vaya a la instancia de IoT Hub Device Provisioning Service.

1. En **Configuración**, seleccione **Administrar inscripciones**.

1. Seleccione **Agregar inscripción individual** y, a continuación, complete los pasos siguientes para configurar la inscripción:

   1. En **Mecanismo**, seleccione **TPM**.

   1. Proporcione la **clave de aprobación** y el **identificador de registro** que ha copiado de la máquina virtual o el dispositivo físico.

   1. Si lo desea, proporcione un identificador para el dispositivo. Si no proporciona un id. de dispositivo, se usará el id. de registro.

   1. Seleccione **Verdadero** para declarar que esta máquina virtual o el dispositivo físico es un dispositivo IoT Edge.

   1. Elija el centro de IoT vinculado al que quiere conectar el dispositivo o seleccione **Link to new IoT Hub** (Vincular a un nuevo centro de IoT). Puede elegir varios centros y el dispositivo se asignará a uno de ellos según la directiva de asignación seleccionada.

   1. Agregue un valor de etiqueta a **Estado inicial de dispositivo gemelo**, si lo desea. Puede usar etiquetas para los grupos de dispositivos de destino para la implementación del módulo. Para más información, consulte [Implementación de módulos IoT Edge a escala](how-to-deploy-at-scale.md).

   1. Seleccione **Guardar**.

Ahora que existe una inscripción para este dispositivo, el entorno de ejecución de Azure IoT Edge puede aprovisionarlo.

## <a name="configure-the-device-with-provisioning-information"></a>Configuración del dispositivo con la información de aprovisionamiento

# <a name="powershell"></a>[PowerShell](#tab/powershell)

1. Abra una sesión de PowerShell con privilegios elevados en el dispositivo Windows.

1. Aprovisione el dispositivo usando el **Id. de ámbito** que ha recopilado de su instancia de Device Provisioning Service.

   ```powershell
   Provision-EflowVM -provisioningType "DpsTpm" -scopeId "<scope id>"
   ```

# <a name="windows-admin-center"></a>[Windows Admin Center](#tab/windowsadmincenter)

1. En el paso **Conexión**, aprovisione el dispositivo.

   1. Seleccione el método de aprovisionamiento **DpsTpm**.
   1. Proporcione el **Id. de ámbito** que recupera de su instancia de Device Provisioning Service.

   ![Aprovisionamiento de su dispositivo con DPS y atestación de TPM](./media/how-to-auto-provision-tpm-linux-on-windows/tpm-provision.png)

1. Seleccione **Aprovisionamiento con el método seleccionado**.

1. Una vez que IoT Edge se haya instalado y aprovisionado correctamente en el dispositivo, seleccione **Finalizar** para salir del Asistente para la implementación.

---

## <a name="verify-successful-configuration"></a>Comprobación de configuración correcta

Compruebe que IoT Edge para Linux en Windows se haya instalado y configurado correctamente en el dispositivo de IoT Edge.

Si el entorno de ejecución se inició correctamente, puede ir a IoT Hub y empezar a implementar módulos de IoT Edge en el dispositivo.

Puede comprobar que la inscripción individual que ha creado se ha utilizado en el servicio Device Provisioning. En Azure Portal, vaya a la instancia del servicio Device Provisioning. Abra los detalles de la inscripción para la inscripción individual que ha creado. Tenga en cuenta que el estado de la inscripción está **asignado** y se muestra el id. de dispositivo.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Use los siguientes comandos en el dispositivo para comprobar que la instancia de IoT Edge se haya instalado e iniciado correctamente.

1. Inicie sesión en la máquina virtual IoT Edge para Linux en Windows usando el siguiente comando en la sesión de PowerShell:

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

## <a name="next-steps"></a>Pasos siguientes

El proceso de inscripción en Device Provisioning Service permite establecer el id. de dispositivo y las etiquetas del dispositivo gemelo al mismo tiempo que aprovisiona el nuevo dispositivo. Puede usar esos valores para dirigirse a dispositivos individuales o grupos de dispositivos con la administración automática de dispositivos. Obtenga información sobre la [Implementación y supervisión de módulos de IoT Edge a escala mediante Azure Portal](how-to-deploy-at-scale.md) o [mediante la CLI de Azure](how-to-deploy-cli-at-scale.md)