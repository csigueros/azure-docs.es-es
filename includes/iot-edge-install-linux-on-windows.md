---
ms.topic: include
ms.date: 10/29/2021
author: kgremban
ms.author: kgremban
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 890ec787bfc7a4316df9d27271460a90d5047175
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/05/2021
ms.locfileid: "131842719"
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
   >Por ejemplo, el comando siguiente crea una máquina virtual con 4 núcleos de vCPU, 4 GB de RAM (representados en MB) y 20 GB de espacio en disco:
   >
   >   ```powershell
   >   Deploy-Eflow -cpuCount 4 -memoryInMB 4096 -vmDiskSize 20
   >   ```
   >
   >Para obtener información sobre todos los parámetros opcionales disponibles, vea [Funciones de PowerShell para IoT Edge para Linux en Windows](../articles/iot-edge/reference-iot-edge-for-linux-on-windows-functions.md#deploy-eflow).

   Puede asignar una GPU a la implementación para habilitar módulos de Linux con aceleración por GPU. Para obtener acceso a estas características, deberá instalar los requisitos previos detallados en [Aceleración por GPU para Azure IoT Edge para Linux en Windows](../articles/iot-edge/gpu-acceleration.md).

   Para usar un acceso directo de GPU, agregue los parámetros **gpuName**, **gpuPassthroughType** y **gpuCount** a su comando `Deploy-Eflow`. Para obtener información sobre todos los parámetros opcionales disponibles, vea [Funciones de PowerShell para IoT Edge para Linux en Windows](../articles/iot-edge/reference-iot-edge-for-linux-on-windows-functions.md#deploy-eflow).

   >[!WARNING]
   >La habilitación del acceso directo de dispositivos de hardware puede aumentar los riesgos de seguridad. Microsoft recomienda usar un controlador de mitigación de dispositivos del proveedor de GPU, si procede. Para obtener más información, vea [Implementación de dispositivos gráficos mediante la asignación discreta de dispositivos](/windows-server/virtualization/hyper-v/deploy/deploying-graphics-devices-using-dda).

1. Escriba "Y" para aceptar los términos de licencia.

1. Escriba "O" o "R" para activar o desactivar la opción **Datos de diagnóstico opcionales** en función de sus preferencias.

1. Una vez completada la implementación, la ventana de PowerShell indica **Implementación correcta**.

   ![Una implementación correcta indicará "Implementación correcta" al final de los mensajes, PNG.](./media/iot-edge-install-linux-on-windows/successful-powershell-deployment.png)

   Después de una implementación correcta, está listo para aprovisionar el dispositivo.

# <a name="windows-admin-center"></a>[Windows Admin Center](#tab/windowsadmincenter)

>[!NOTE]
>La extensión de Azure IoT Edge para Windows Admin Center está actualmente en [versión preliminar pública](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Los procesos de instalación y administración pueden ser diferentes de los de las características disponibles con carácter general.

Instale Azure IoT Edge para Linux en Windows en el dispositivo.

1. Descargue el [instalador de Azure IoT Edge para Linux en Windows](https://aka.ms/AzEflowMSI).

1. Una vez que haya instalado Azure IoT Edge para Linux en Windows, abra Windows Admin Center.

   En la página de inicio de Windows Admin Center, en la lista de conexiones, verá una conexión de host local que representa el equipo en el que se ejecuta Windows Admin Center. Los servidores, equipos o clústeres adicionales que administre también aparecerán aquí.

   Puede usar Windows Admin Center para instalar y administrar Azure IoT Edge para Linux en Windows tanto en el dispositivo local como en los dispositivos administrados remotos. En esta guía, la conexión de host local actuará como el dispositivo de destino para la implementación de Azure IoT Edge para Linux en Windows.

1. Confirme que el dispositivo local aparece en **Todas las conexiones**, como se muestra a continuación.

   ![Panel inicial de Windows Admin Center con el dispositivo de destino en la lista, PNG.](./media/iot-edge-install-linux-on-windows/windows-admin-center-initial-dashboard.png)

   Si quiere realizar la implementación en un dispositivo de destino remoto en lugar de en el dispositivo local y no ve el dispositivo de destino deseado en la lista, siga las [instrucciones para agregar el dispositivo](/windows-server/manage/windows-admin-center/use/get-started#connecting-to-managed-nodes-and-clusters).

1. Seleccione **+ Agregar** para empezar a crear la implementación. La implementación crea la máquina virtual Linux e instala el entorno de ejecución de IoT Edge de forma automática.

1. En el panel **Agregar o crear recursos**, busque el elemento **Azure IoT Edge**. Seleccione **Crear nuevo** para instalar una nueva instancia de Azure IoT Edge para Linux en Windows en un dispositivo.

   Si ya tiene una instancia de IoT Edge para Linux en Windows ejecutándose en un dispositivo, puede seleccionar **Agregar** para conectarse a ese dispositivo de IoT Edge existente y administrarlo con Windows Admin Center.

   ![Seleccione "Crear nuevo" en el elemento Azure IoT Edge de Windows Admin Center, PNG.](./media/iot-edge-install-linux-on-windows/resource-creation-tiles.png)

1. Se abrirá el panel **Crear una implementación de Azure IoT Edge para Linux en Windows**. En la pestaña **1. Getting Started** (1. Tareas iniciales), revise los requisitos mínimos y seleccione **Siguiente**.

1. Revise los términos de licencia, marque la casilla **Acepto** y seleccione **Siguiente**.

1. Puede activar o desactivar la opción **Datos de diagnóstico opcionales** en función de sus preferencias.

1. Seleccione **Siguiente: Implementación**.

   ![Seleccione el botón "Siguiente: Implementación" después de activar o desactivar los datos de diagnóstico opcionales según sus preferencias, PNG.](./media/iot-edge-install-linux-on-windows/select-next-deploy.png)

1. En la pestaña **2. Implementar**, en **Seleccione un dispositivo de destino**, haga clic en su dispositivo para validar que cumple los requisitos mínimos. Una vez que su estado se confirme como compatible, seleccione **Siguiente**.

   ![Seleccione el dispositivo para comprobar si es compatible, PNG.](./media/iot-edge-install-linux-on-windows/evaluate-supported-device.png)

1. En la pestaña **Configuración 2.2**, revise los valores de configuración de la implementación. En la tabla siguiente se muestra la configuración y sus valores predeterminados.

   | Configuración | Valor predeterminado |
   | ------- | ------------- |
   | Tamaño del disco virtual en GB | 16 |
   | Memoria en MB | 1024 |
   | Número de núcleos | 2 |
   | Versión de Azure IoT Edge | 1.1 (LTS) |

   >[!NOTE]
   >IoT Edge para Linux en Windows usa un conmutador predeterminado, que asigna a la máquina virtual Linux una dirección IP interna. No se puede acceder a esta dirección IP interna desde fuera de la máquina virtual de Windows. Puede conectar localmente con la máquina virtual mientras tenga la sesión iniciada en la máquina virtual de Windows.
   >
   >Si usa Windows Server, [configure un conmutador predeterminado](../articles/iot-edge/how-to-create-virtual-switch.md) antes de implementar IoT Edge para Linux en Windows.

   Puede asignar una GPU a la implementación para habilitar módulos de Linux con aceleración por GPU. Para obtener acceso a estas características, deberá instalar los requisitos previos detallados en [Aceleración por GPU para Azure IoT Edge para Linux en Windows](../articles/iot-edge/gpu-acceleration.md). Si va a instalar estos requisitos previos en este momento del proceso de implementación, tendrá que empezar de nuevo desde el principio.

   Hay dos opciones disponibles para el acceso directo de GPU: **asignación directa de dispositivos (DDA)** y **paravirtualización de GPU (GPU-PV)** , en función del adaptador de GPU que asigne a la implementación.

   Para el método de asignación directa de dispositivos, seleccione el número de procesadores de GPU que se asignarán a la máquina virtual Linux.

   Para el método de paravirtualización, no se necesita ninguna configuración adicional.

   >[!WARNING]
   >La habilitación del acceso directo de dispositivos de hardware puede aumentar los riesgos de seguridad. Microsoft recomienda usar un controlador de mitigación de dispositivos del proveedor de GPU, si procede. Para obtener más información, vea [Implementación de dispositivos gráficos mediante la asignación discreta de dispositivos](/windows-server/virtualization/hyper-v/deploy/deploying-graphics-devices-using-dda).

   Cuando esté satisfecho con la configuración, seleccione **Siguiente**.

1. En la pestaña **2.3 Implementación**, puede ver el progreso de la implementación. El proceso completo incluye la descarga del paquete de Azure IoT Edge para Linux en Windows, la instalación del paquete, la configuración del dispositivo host y la configuración de la máquina virtual Linux. Este proceso puede tardar varios minutos en completarse. A continuación se muestra una implementación correcta.

   ![Una implementación correcta mostrará todos los pasos con una marca de verificación verde y la etiqueta "Completado", PNG.](./media/iot-edge-install-linux-on-windows/successful-deployment.png)

1. Una vez completada la implementación, está listo para aprovisionar el dispositivo. Seleccione **Siguiente: Conectar** para ir a la pestaña **3. Conectar**, que controla el aprovisionamiento de dispositivos de Azure IoT Edge.

---
