---
title: Instalación de Azure IoT Edge para Linux en Windows | Microsoft Docs
description: Instrucciones de instalación de Azure IoT Edge en dispositivos Windows
author: kgremban
ms.reviewer: fcabrera
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 06/10/2021
ms.author: v-tcassi
monikerRange: =iotedge-2018-06
ms.openlocfilehash: 8cc39325f8c993afb6901e9700376fc73ecbefe7
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128602866"
---
# <a name="install-and-provision-azure-iot-edge-for-linux-on-a-windows-device"></a>Instalación y aprovisionamiento de Azure IoT Edge para Linux en un dispositivo Windows (versión preliminar)

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

El entorno de ejecución de Azure IoT Edge es lo que convierte a un dispositivo en un dispositivo IoT Edge. El entorno de ejecución se puede implementar en dispositivos desde la clase PC hasta servidores industriales. Una vez que un dispositivo está configurado con el entorno de ejecución de IoT Edge, puede empezar a implementar lógica de negocios en él desde la nube. Para obtener más información, consulte [Información del entorno de ejecución de Azure IoT Edge y su arquitectura](iot-edge-runtime.md).

Azure IoT Edge para Linux en Windows le permite instalar IoT Edge en máquinas virtuales Linux que se ejecutan en dispositivos Windows. La versión de Linux de Azure IoT Edge y todos los módulos de Linux que se implementen con ella se ejecutan en la máquina virtual. Desde allí, el código y las aplicaciones Windows, así como el entorno de ejecución de Azure IoT Edge y los módulos pueden libremente entre sí.

En este artículo se enumeran los pasos para configurar una instancia de IoT Edge en un dispositivo Windows. En estos pasos se implementa una máquina virtual Linux que contiene el entorno de ejecución de Azure IoT Edge para que se ejecute en el dispositivo Windows y, a continuación, se aprovisiona el dispositivo con su identidad de dispositivo de IoT Hub.

>[!NOTE]
>Aunque IoT Edge para Linux en Windows es la experiencia recomendada para usar Azure IoT Edge en un entorno de Windows. Aun así, los contenedores de Windows siguen estando disponibles. Si prefiere usar contenedores Windows, consulte [Instalación y administración de Azure IoT Edge con contenedores de Windows](how-to-install-iot-edge-windows-on-windows.md).

## <a name="prerequisites"></a>Requisitos previos

* Una cuenta de Azure con una suscripción válida. Si no tiene una [suscripción a Azure](../guides/developer/azure-developer-guide.md#understanding-accounts-subscriptions-and-billing), cree una [cuenta gratuita](https://azure.microsoft.com/free/) antes de empezar.

* Una instancia de [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) de nivel estándar o gratis en Azure.

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

* Si quiere instalar y administrar un dispositivo IoT Edge mediante Windows Admin Center, asegúrese de que dispone de acceso a este y que tiene instalada la extensión de Azure IoT Edge:

   1. Descargue y ejecute el [instalador de Windows Admin Center](https://aka.ms/wacdownload). Siga las indicaciones del Asistente para la instalación a fin de instalar Windows Admin Center.

   1. Una vez instalado, use un explorador compatible para abrir Windows Admin Center. Entre los exploradores admitidos se incluyen Microsoft Edge (Windows 10, versión 1709 o posterior), Google Chrome y Microsoft Edge Insider.

   1. Al usar Windows Admin Center por primera vez, se le pedirá que seleccione un certificado para usarlo. Seleccione **Cliente de Windows Admin Center** como certificado.

   1. Instale la extensión de Azure IoT Edge. Seleccione el icono de engranaje en la parte superior derecha del panel de Windows Admin Center.

      ![Seleccione el icono de engranaje en la parte superior derecha del panel de Windows Admin Center para acceder a la configuración.](./media/how-to-install-iot-edge-on-windows/select-gear-icon.png)

   1. En el menú **Configuración**, en **Puerta de enlace**, seleccione **Extensiones**.

   1. En la pestaña **Extensiones disponibles**, busque **Azure IoT Edge** en la lista de extensiones. Esta dicha opción y seleccione la indicación **Instalar** sobre la lista de extensiones.

   1. Una vez finalizada la instalación, debería ver Azure IoT Edge en la lista de extensiones instaladas en la pestaña **Extensiones instaladas**.

* Si quiere usar **módulos de Linux con aceleración por GPU** en la implementación de Azure IoT Edge para Linux en Windows, debe tener en cuenta varias opciones de configuración. Tendrá que instalar los controladores correctos en función de la arquitectura de GPU y es posible que necesite acceder a una compilación del programa Windows Insider. Para determinar sus necesidades de configuración y cumplir estos requisitos previos, consulte [Aceleración por GPU para Azure IoT Edge para Linux en Windows](gpu-acceleration.md).

## <a name="choose-your-provisioning-method"></a>Elección del método de aprovisionamiento

Azure IoT Edge para Linux en Windows admite los siguientes métodos de aprovisionamiento:

* **Aprovisionamiento manual** para un solo dispositivo.

  * A fin de prepararse para el aprovisionamiento manual, siga los pasos descritos en [Registro de un dispositivo IoT Edge en IoT Hub](how-to-register-device.md). Elija la autenticación de clave simétrica o la autenticación de certificado X.509 y vuelva a este artículo para instalar y aprovisionar IoT Edge.

* **Aprovisionamiento automático** mediante IoT Hub Device Provisioning Service (DPS) para uno o varios dispositivos.

  * Elija el método de autenticación que quiere usar y, luego, siga los pasos descritos en el artículo correspondiente para configurar una instancia de DPS y crear una inscripción para aprovisionar los dispositivos. Para obtener más información sobre los tipos de inscripción, visite los [conceptos de Azure IoT Hub Device Provisioning Service](../iot-dps/concepts-service.md#enrollment).

    * [Aprovisionamiento de un dispositivo IoT Edge con DPS y claves simétricas.](how-to-provision-devices-at-scale-linux-on-windows-symmetric.md)
    * [Aprovisionamiento de un dispositivo IoT Edge con DPS y certificados X.509.](how-to-provision-devices-at-scale-linux-on-windows-x509.md)
    * [Aprovisionamiento de un dispositivo IoT Edge con DPS y una atestación de TPM.](how-to-auto-provision-tpm-linux-on-windows.md)

## <a name="create-a-new-deployment"></a>Crear una nueva implementación

Implemente Azure IoT Edge para Linux en Windows en el dispositivo de destino.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Instale IoT Edge para Linux en Windows en el dispositivo de destino si todavía no lo ha hecho.

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

1. Establezca la directiva de ejecución en el dispositivo de destino en `AllSigned` si todavía no lo está. Puede comprobar la directiva de ejecución actual en un símbolo del sistema de PowerShell con privilegios elevados con el siguiente comando:

   ```powershell
   Get-ExecutionPolicy -List
   ```

   Si la directiva de ejecución de `local machine` no es `AllSigned`, puede establecer la directiva de ejecución con el siguiente comando:

   ```powershell
   Set-ExecutionPolicy -ExecutionPolicy AllSigned -Force
   ```

1. Creación de la implementación de IoT Edge para Linux en Windows.

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

   Para usar un acceso directo de GPU, deberá agregar los parámetros **gpuName**, **gpuPassthroughType** y **gpuCount** en el comando `Deploy-Eflow`. Para obtener información sobre todos los parámetros opcionales disponibles, vea [Funciones de PowerShell para IoT Edge para Linux en Windows](reference-iot-edge-for-linux-on-windows-functions.md#deploy-eflow).

   >[!WARNING]
   >La habilitación del acceso directo de dispositivos de hardware puede aumentar los riesgos de seguridad. Microsoft recomienda usar un controlador de mitigación de dispositivos del proveedor de GPU, si procede. Para obtener más información, vea [Implementación de dispositivos gráficos mediante la asignación discreta de dispositivos](/windows-server/virtualization/hyper-v/deploy/deploying-graphics-devices-using-dda).

1. Escriba "Y" para aceptar los términos de licencia.

1. Escriba "O" o "R" para activar o desactivar la opción **Datos de diagnóstico opcionales** en función de sus preferencias.

1. Una vez completada la implementación, la ventana de PowerShell indica **Implementación correcta**.

   ![Una implementación correcta indicará "implementación correcta" al final de los mensajes.](./media/how-to-install-iot-edge-on-windows/successful-powershell-deployment-2.png)

Una vez completada la implementación, está listo para aprovisionar el dispositivo.

# <a name="windows-admin-center"></a>[Windows Admin Center](#tab/windowsadmincenter)

>[!NOTE]
>La extensión de Azure IoT Edge para Windows Admin Center está actualmente en [versión preliminar pública](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Los procesos de instalación y administración pueden ser diferentes de los de las características disponibles con carácter general.

En la página de inicio de Windows Admin Center, en la lista de conexiones, verá una conexión de host local que representa el equipo en el que se ejecuta Windows Admin Center. Los servidores, equipos o clústeres adicionales que administre también aparecerán aquí.

Puede usar Windows Admin Center para instalar y administrar Azure IoT Edge para Linux en Windows tanto en el dispositivo local como en los dispositivos administrados remotos. En esta guía, la conexión de host local actuará como el dispositivo de destino para la implementación de Azure IoT Edge para Linux en Windows.

Si quiere realizar la implementación en un dispositivo de destino remoto en lugar de en el dispositivo local y no ve el dispositivo de destino deseado en la lista, siga las [instrucciones para agregar el dispositivo](/windows-server/manage/windows-admin-center/use/get-started#connecting-to-managed-nodes-and-clusters).

   ![Panel inicial de Windows Admin Center con el dispositivo de destino en la lista](./media/how-to-install-iot-edge-on-windows/windows-admin-center-initial-dashboard.png)

1. Seleccione **Agregar**.

1. En el panel **Agregar o crear recursos**, busque el elemento **Azure IoT Edge**. Seleccione **Crear nuevo** para instalar una nueva instancia de Azure IoT Edge para Linux en Windows en un dispositivo.

   Si ya tiene una instancia de IoT Edge para Linux en Windows ejecutándose en un dispositivo, puede seleccionar **Agregar** para conectarse a ese dispositivo de IoT Edge existente y administrarlo con Windows Admin Center.

   ![Seleccione Crear nuevo en el elemento Azure IoT Edge de Windows Admin Center](./media/how-to-install-iot-edge-on-windows/resource-creation-tiles.png)

1. Se abrirá el panel **Crear una implementación de Azure IoT Edge para Linux en Windows**. En la pestaña **1. Getting Started** (1. Tareas iniciales), revise los requisitos mínimos y seleccione **Siguiente**.

1. Revise los términos de licencia, marque la casilla **Acepto** y seleccione **Siguiente**.

1. Puede activar o desactivar la opción **Datos de diagnóstico opcionales** en función de sus preferencias.

1. Seleccione **Siguiente: Implementación**.

   ![Seleccione el botón Siguiente: Implementar después de activar o desactivar la opción Datos de diagnóstico opcionales según sus preferencias.](./media/how-to-install-iot-edge-on-windows/select-next-deploy.png)

1. En la pestaña **2. Implementar**, en **Seleccione un dispositivo de destino**, haga clic en su dispositivo para validar que cumple los requisitos mínimos. Una vez que su estado se confirme como compatible, seleccione **Siguiente**.

   ![Seleccione el dispositivo para comprobar que es compatible](./media/how-to-install-iot-edge-on-windows/evaluate-supported-device.png)

1. En la pestaña **Configuración 2.2**, revise los valores de configuración de la implementación.

   >[!NOTE]
   >IoT Edge para Linux en Windows usa un conmutador predeterminado, que asigna a la máquina virtual Linux una dirección IP interna. No se puede acceder a esta dirección IP interna desde fuera de la máquina virtual de Windows. Puede conectar localmente con la máquina virtual mientras tenga la sesión iniciada en la máquina virtual de Windows.
   >
   >Si usa Windows Server, configure un conmutador predeterminado antes de implementar IoT Edge para Linux en Windows.

   Puede asignar una GPU a la implementación para habilitar módulos de Linux con aceleración por GPU. Para obtener acceso a estas características, deberá instalar los requisitos previos detallados en [Aceleración por GPU para Azure IoT Edge para Linux en Windows](gpu-acceleration.md). Si va a instalar estos requisitos previos en este momento del proceso de implementación, tendrá que empezar de nuevo desde el principio.

   Hay dos opciones disponibles para el acceso directo de GPU: **asignación directa de dispositivos (DDA)** y **paravirtualización de GPU (GPU-PV)** , en función del adaptador de GPU que asigne a la implementación. A continuación se muestran ejemplos de cada método.

   Para el método de asignación directa de dispositivos, seleccione el número de procesadores de GPU que se asignarán a la máquina virtual Linux.

   ![Opciones de configuración con la asignación directa de dispositivos de GPU habilitada.](./media/how-to-install-iot-edge-on-windows/gpu-passthrough-direct-device-assignment.png)

   Para el método de paravirtualización, no se necesita ninguna configuración adicional.

   ![Opciones de configuración con la paravirtualización de GPU habilitada.](./media/how-to-install-iot-edge-on-windows/gpu-passthrough-paravirtualization.png)

   >[!WARNING]
   >La habilitación del acceso directo de dispositivos de hardware puede aumentar los riesgos de seguridad. Microsoft recomienda usar un controlador de mitigación de dispositivos del proveedor de GPU, si procede. Para obtener más información, vea [Implementación de dispositivos gráficos mediante la asignación discreta de dispositivos](/windows-server/virtualization/hyper-v/deploy/deploying-graphics-devices-using-dda).

   Cuando esté satisfecho con la configuración, seleccione **Siguiente**.

1. En la pestaña **2.3 Implementación**, puede ver el progreso de la implementación. El proceso completo incluye la descarga del paquete de Azure IoT Edge para Linux en Windows, la instalación del paquete, la configuración del dispositivo host y la configuración de la máquina virtual Linux. Este proceso puede tardar varios minutos en completarse. A continuación se muestra una implementación correcta.

   ![Una implementación correcta mostrará todos los pasos con una marca de verificación verde y la etiqueta "Completado".](./media/how-to-install-iot-edge-on-windows/successful-deployment.png)

Una vez completada la implementación, está listo para aprovisionar el dispositivo. Seleccione **Siguiente: Conectar** para ir a la pestaña **3. Conectar**, que controla el aprovisionamiento de dispositivos de Azure IoT Edge.

---

## <a name="provision-your-device"></a>Aprovisionamiento del dispositivo

Elija un método para aprovisionar el dispositivo y siga las instrucciones de la sección correspondiente. En este artículo se proporcionan los pasos para aprovisionar manualmente el dispositivo con claves simétricas o certificados X.509. Si usa el aprovisionamiento automático con DPS, siga los vínculos adecuados para completar el aprovisionamiento.

Puede usar Windows Admin Center o una sesión de PowerShell con privilegios elevados para aprovisionar los dispositivos.

* Aprovisionamiento manual:

  * [Aprovisionamiento manual mediante la cadena de conexión del dispositivo IoT Edge](#manual-provisioning-using-the-connection-string)
  * [Aprovisionamiento manual mediante certificados X.509](#manual-provisioning-using-x509-certificates)

* Aprovisionamiento automático:

  * [Aprovisionamiento automático mediante Device Provisioning Service (DPS) y claves simétricas](how-to-provision-devices-at-scale-linux-on-windows-symmetric.md#configure-the-device-with-provisioning-information)
  * [Aprovisionamiento automático mediante DPS y certificados X.509](how-to-provision-devices-at-scale-linux-on-windows-x509.md#configure-the-device-with-provisioning-information)
  * [Aprovisionamiento automático mediante DPS y la atestación de TPM](how-to-auto-provision-tpm-linux-on-windows.md#configure-the-device-with-provisioning-information)

### <a name="manual-provisioning-using-the-connection-string"></a>Aprovisionamiento manual mediante la cadena de conexión

Esta sección se centra en el aprovisionamiento manual del dispositivo mediante la cadena de conexión del dispositivo IoT Edge.

Si aún no lo ha hecho, siga los pasos indicados en [Registro de un dispositivo IoT Edge en IoT Hub](how-to-register-device.md) para registrar el dispositivo y recuperar la cadena de conexión.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Ejecute el siguiente comando en una sesión de PowerShell con privilegios elevados en el dispositivo de destino. Reemplace el texto de marcador de posición por sus valores propios.

```powershell
Provision-EflowVm -provisioningType ManualConnectionString -devConnString "<CONNECTION_STRING_HERE>"
```

Para obtener más información sobre el comando `Provision-EflowVM`, vea [Funciones de PowerShell para IoT Edge para Linux en Windows](reference-iot-edge-for-linux-on-windows-functions.md#provision-eflowvm).

# <a name="windows-admin-center"></a>[Windows Admin Center](#tab/windowsadmincenter)

1. En el panel **Azure IoT Edge device provisioning** (Aprovisionamiento de dispositivos de Azure IoT Edge), seleccione **Connection String (Manual)** (cadena de conexión [manual]) en el menú desplegable Método de aprovisionamiento.

1. En [Azure Portal](https://ms.portal.azure.com/), vaya a la pestaña **IoT Edge** de IoT Hub.

1. Haga clic en el id. de dispositivo de su dispositivo. Copie el campo **Cadena de conexión principal**.

1. Proporcione la **cadena de conexión de dispositivo** que recuperó de IoT Hub después de registrar el dispositivo.

1. Seleccione **Provisioning with the selected method** (Aprovisionar con el método seleccionado).

   ![Elija Aprovisionar con el método seleccionado después de pegar la cadena de conexión del dispositivo.](./media/how-to-install-iot-edge-on-windows/provisioning-with-selected-method-connection-string.png)

1. Una vez completado el aprovisionamiento, seleccione **Finalizar**. Volverá al panel principal. Ahora debería mostrarse un nuevo dispositivo con el tipo `IoT Edge Devices`. Puede seleccionar el dispositivo de IoT Edge para conectarse a él. Una vez en la página **Información general** del dispositivo, puede ver los valores de **IoT Edge Module List** (lista de módulos de IoT Edge) y **IoT Edge Status** (estado de IoT Edge) del dispositivo.

---

### <a name="manual-provisioning-using-x509-certificates"></a>Aprovisionamiento manual mediante certificados X.509

Esta sección se centra en el aprovisionamiento manual del dispositivo mediante certificados X.509 en el dispositivo IoT Edge.

Si aún no lo ha hecho, siga los pasos indicados en [Registro de un dispositivo IoT Edge en IoT Hub](how-to-register-device.md) para preparar los certificados necesarios y registrar el dispositivo. 

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Tenga listo el certificado de identidad del dispositivo y su clave privada correspondiente en el dispositivo de destino. Disponga de la ruta de acceso absoluta a ambos archivos.

Ejecute el siguiente comando en una sesión de PowerShell con privilegios elevados en el dispositivo de destino. Reemplace el texto de marcador de posición por sus valores propios.

```powershell
Provision-EflowVm -provisioningType ManualX509 -iotHubHostname "<HUB HOSTNAME>" -deviceId "<DEVICE ID>" -identityCertPath "<ABSOLUTE PATH TO IDENTITY CERT>" -identityPrivKeyPath "<ABSOLUTE PATH TO PRIVATE KEY>"
```

Para obtener más información sobre el comando `Provision-EflowVM`, vea [Funciones de PowerShell para IoT Edge para Linux en Windows](reference-iot-edge-for-linux-on-windows-functions.md#provision-eflowvm).

# <a name="windows-admin-center"></a>[Windows Admin Center](#tab/windowsadmincenter)

1. En el panel **Azure IoT Edge device provisioning** (Aprovisionamiento de dispositivos Azure IoT Edge), seleccione **ManualX509** en el menú desplegable de métodos de aprovisionamiento.

   ![Selección del aprovisionamiento manual con certificados X.509.](./media/how-to-install-iot-edge-on-windows/provisioning-with-selected-method-manual-x509.png)

1. Proporcione los parámetros obligatorios:

   * **Nombre de host de IoT Hub**: nombre del centro de IoT en el que está registrado este dispositivo.
   * **Id. de dispositivo**: nombre con el que está registrado este dispositivo.
   * **Archivo de certificado**: cargue el certificado de identidad del dispositivo, que se trasladará a la máquina virtual y se usará para aprovisionar el dispositivo.
   * **Archivo de clave privada**: cargue el archivo de clave privada correspondiente, que se trasladará a la máquina virtual y se usará para aprovisionar el dispositivo.

1. Seleccione **Provisioning with the selected method** (Aprovisionar con el método seleccionado).

1. Una vez completado el aprovisionamiento, seleccione **Finalizar**. Volverá al panel principal. Ahora debería mostrarse un nuevo dispositivo con el tipo `IoT Edge Devices`. Puede seleccionar el dispositivo de IoT Edge para conectarse a él. Una vez en la página **Información general** del dispositivo, puede ver los valores de **IoT Edge Module List** (lista de módulos de IoT Edge) y **IoT Edge Status** (estado de IoT Edge) del dispositivo.

---

## <a name="verify-successful-configuration"></a>Comprobación de configuración correcta

Compruebe que IoT Edge para Linux en Windows se haya instalado y configurado correctamente en el dispositivo de IoT Edge.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

> [!IMPORTANT]
> Si usa funciones públicas de PowerShell en IoT Edge para Linux en Windows, asegúrese de establecer la directiva de ejecución en el dispositivo de destino en `AllSigned`. Asegúrese de que se cumplen todos los requisitos previos de las [funciones de PowerShell en IoT Edge para Linux en Windows](reference-iot-edge-for-linux-on-windows-functions.md).

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

* Siga [implementando módulos de IoT Edge](how-to-deploy-modules-portal.md) para información sobre cómo implementar módulos en el dispositivo.
* Obtenga información acerca de cómo [administrar certificados en IoT Edge para Linux en máquinas virtuales de Windows](how-to-manage-device-certificates.md) y transferir archivos desde el sistema operativo host a la máquina virtual de Linux.
* Aprenda a [configurar los dispositivos de IoT Edge para que se comuniquen a través de un servidor proxy](how-to-configure-proxy-support.md).
