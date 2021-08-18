---
title: Instalación de Azure IoT Edge para Windows | Microsoft Docs
description: Instalación de contenedores de Azure IoT Edge para Windows en dispositivos Windows
author: kgremban
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 12/18/2020
ms.author: kgremban
monikerRange: iotedge-2018-06
ms.openlocfilehash: 676bd23dce7ca79f3640bdfa8f9b55e427390e91
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121722019"
---
# <a name="install-and-manage-azure-iot-edge-with-windows-containers"></a>Instalación y administración de Azure IoT Edge con contenedores Windows

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

El entorno de ejecución de Azure IoT Edge es lo que convierte a un dispositivo en un dispositivo IoT Edge. Una vez que un dispositivo está configurado con el entorno de ejecución de IoT Edge, puede empezar a implementar lógica de negocios en él desde la nube. Para obtener más información, consulte [Información del entorno de ejecución de Azure IoT Edge y su arquitectura](iot-edge-runtime.md).

Hay dos pasos para configurar un dispositivo IoT Edge. El primer paso consiste en instalar el entorno de ejecución y sus dependencias. El segundo paso es conectar el dispositivo a su identidad en la nube y configurar la autenticación con IoT Hub.

En este artículo se indican los pasos necesarios para instalar el runtime de Azure IoT Edge con dispositivos Windows. Si desea usar contenedores Linux en un dispositivo Windows, consulte el artículo [Azure IoT Edge para Linux en Windows](how-to-install-iot-edge-on-windows.md).

>[!NOTE]
>Azure IoT Edge con contenedores Windows no se admitirá a partir de la versión 1.2 de Azure IoT Edge.
>
>Considere la posibilidad de usar el nuevo método para ejecutar IoT Edge en dispositivos Windows, [Azure IoT Edge para Linux en Windows](iot-edge-for-linux-on-windows.md).

## <a name="prerequisites"></a>Requisitos previos

* Un dispositivo Windows

  IoT Edge para contenedores de Windows requiere la versión 1809/compilación 17763 de Windows, que es la [compilación de compatibilidad con Windows a largo plazo](/windows/release-information/) más reciente. Asegúrese de examinar la [lista de sistemas compatibles](support.md#operating-systems) para obtener una lista de las SKU admitidas.

* Un [identificador de dispositivo registrado](how-to-register-device.md)

  Si registró el dispositivo con la autenticación de clave simétrica, tenga lista la cadena de conexión del dispositivo.

  Si registró el dispositivo con la autenticación de certificado autofirmado X.509, debe tener al menos uno de los certificados de identidad que usó para registrar el dispositivo y su clave privada correspondiente en el dispositivo.

## <a name="install-a-container-engine"></a>Instalación de un motor del contenedor

Azure IoT Edge utiliza un runtime de contenedor compatible con OCI como [Moby](https://github.com/moby/moby). Un motor basado en Moby que se incluye en el script de instalación. No hay pasos adicionales para instalar el motor.

## <a name="install-the-iot-edge-security-daemon"></a>Instalación del demonio de seguridad de IoT Edge

1. Ejecute PowerShell como administrador.

   Use una sesión de AMD64 de PowerShell, no PowerShell (x86). Si no está seguro del tipo de sesión que usa, ejecute el comando siguiente:

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

Al instalar IoT Edge en un dispositivo, puede usar parámetros adicionales para modificar el proceso, entre los que se incluyen los siguientes:

* Dirigir el tráfico para que pase por un servidor proxy
* Haga que el instalador apunte a un directorio local, en el caso de una instalación sin conexión.

Para más información sobre estos parámetros adicionales, consulte [Scripts de PowerShell para IoT Edge con contenedores Windows](reference-windows-scripts.md).

## <a name="provision-the-device-with-its-cloud-identity"></a>Aprovisionamiento del dispositivo con su identidad de nube

Ahora que el motor del contenedor y el entorno de ejecución de IoT Edge están instalados en el dispositivo, está preparado para el paso siguiente, que consiste en configurar el dispositivo con su información de identidad de nube y autenticación.

Elija la siguiente sección en función del tipo de autenticación que quiera usar:

* [Opción 1: Autenticación con claves simétricas](#option-1-authenticate-with-symmetric-keys)
* [Opción 2: Autenticación con certificados X.509](#option-2-authenticate-with-x509-certificates)

### <a name="option-1-authenticate-with-symmetric-keys"></a>Opción 1: Autenticación con claves simétricas

En este momento, el entorno de ejecución de IoT Edge se instala en el dispositivo Windows y debe aprovisionar el dispositivo con su información de identidad de nube y autenticación.

En esta sección se explican los pasos necesarios para aprovisionar un dispositivo con la autenticación de clave simétrica. Debe haber registrado el dispositivo en IoT Hub y recuperar la cadena de conexión de la información del dispositivo. Si no es así, siga los pasos que se describen en [Registro de un dispositivo IoT Edge en IoT Hub](how-to-register-device.md).

1. En el dispositivo IoT Edge, ejecute PowerShell como administrador.

2. Use el comando [Initialize-IoTEdge](reference-windows-scripts.md#initialize-iotedge) para configurar el entorno de ejecución de IoT Edge en la máquina. El comportamiento predeterminado del comando es el aprovisionamiento manual con contenedores de Windows.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -ManualConnectionString -ContainerOs Windows
   ```

   * Si ha descargado el script IoTEdgeSecurityDaemon.ps1 en el dispositivo para la instalación sin conexión o de una versión específica, asegúrese de hacer referencia a la copia local del script.

      ```powershell
      . <path>/IoTEdgeSecurityDaemon.ps1
      Initialize-IoTEdge -ManualX509
      ```

3. Cuando se le pida, especifique la cadena de conexión del dispositivo que ha recuperado en la sección anterior. La cadena de conexión del dispositivo asocia el dispositivo físico a un identificador de dispositivo de IoT Hub y proporciona información de autenticación.

   La cadena de conexión del dispositivo adopta el formato siguiente y no debe incluir comillas: `HostName={IoT hub name}.azure-devices.net;DeviceId={device name};SharedAccessKey={key}`

Al aprovisionar un dispositivo manualmente, puede usar parámetros adicionales para modificar el proceso, lo que incluye:

* Dirigir el tráfico para que pase por un servidor proxy
* Declarar una imagen de contenedor de edgeAgent específica y proporcionar las credenciales si se encuentra en un registro privado

Para más información sobre estos parámetros adicionales, consulte [Scripts de PowerShell para IoT Edge con contenedores Windows](reference-windows-scripts.md).

### <a name="option-2-authenticate-with-x509-certificates"></a>Opción 2: Autenticación con certificados X.509

En este momento, el entorno de ejecución de IoT Edge se instala en el dispositivo Windows y debe aprovisionar el dispositivo con su información de identidad de nube y autenticación.

En esta sección se explican los pasos necesarios para aprovisionar un dispositivo con la autenticación de certificados X.509. Debe haber registrado el dispositivo en IoT Hub y proporcionado huellas digitales que coincidan con el certificado y la clave privada que se encuentran en el dispositivo IoT Edge. Si no es así, siga los pasos que se describen en [Registro de un dispositivo IoT Edge en IoT Hub](how-to-register-device.md).

1. En el dispositivo IoT Edge, ejecute PowerShell como administrador.

2. Use el comando [Initialize-IoTEdge](reference-windows-scripts.md#initialize-iotedge) para configurar el entorno de ejecución de Azure IoT Edge en el equipo.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -ManualX509
   ```

   * Si descargó el script IoTEdgeSecurityDaemon.ps1 en el dispositivo para la instalación sin conexión o de una versión específica, asegúrese de hacer referencia a la copia local del script.

      ```powershell
      . <path>/IoTEdgeSecurityDaemon.ps1
      Initialize-IoTEdge -ManualX509
      ```

3. Cuando se le solicite, proporcione la siguiente información:

   * **IotHubHostName**: nombre de host del centro de IoT al que se conectará el dispositivo. Por ejemplo, `{IoT hub name}.azure-devices.net`.
   * **DeviceId**: el id. que proporcionó al registrar el dispositivo.
   * **X509IdentityCertificate**: ruta de acceso absoluta a un certificado de identidad en el dispositivo. Por ejemplo, `C:\path\identity_certificate.pem`.
   * **X509IdentityPrivateKey**: ruta de acceso absoluta al archivo de clave privada para el certificado de identidad proporcionado. Por ejemplo, `C:\path\identity_key.pem`.

Al aprovisionar un dispositivo manualmente, puede usar parámetros adicionales para modificar el proceso, incluyendo:

* Dirigir el tráfico para que pase por un servidor proxy
* Declarar una imagen de contenedor de edgeAgent específica y proporcionar las credenciales si se encuentra en un registro privado

Para más información sobre estos parámetros adicionales, consulte [Scripts de PowerShell para IoT Edge con contenedores Windows](reference-windows-scripts.md).

## <a name="offline-or-specific-version-installation-optional"></a>Instalación sin conexión o de una versión específica (opcional)

Los pasos de esta sección corresponden a escenarios que no se tratan en los pasos de instalación estándar. Estos pueden incluir lo siguiente:

* Instalación de IoT Edge sin conexión
* Instalación de una versión candidata para lanzamiento
* Instalación de una versión que no sea la más reciente

Durante la instalación, se descargan tres archivos:

* Un script de PowerShell, que contiene las instrucciones de instalación.
* Un archivo .cab de Microsoft Azure IoT Edge, que contiene el demonio de seguridad de IoT Edge (iotedged), el motor del contenedor Moby y la CLI de Moby.
* El instalador del paquete redistribuible de Visual C++ (VC Runtime).

Si el dispositivo va a estar sin conexión durante la instalación o si desea instalar una versión específica de IoT Edge, puede descargar anticipadamente estos archivos en el dispositivo. En el momento de la instalación, haga que el script de instalación apunte hacia el directorio que contiene los archivos descargados. El instalador comprueba en primer lugar ese directorio y luego descarga solo los componentes que no se encuentren en él. Si los archivos están disponibles sin conexión, la instalación se puede realizar sin conexión a Internet.

1. Para obtener los archivos de instalación de IoT Edge más recientes, junto con las versiones anteriores, vea las [versiones de Azure IoT Edge](https://github.com/Azure/azure-iotedge/releases).

2. Busque la versión que desea instalar y descargue los siguientes archivos de la sección **Recursos** de las notas de la versión en el dispositivo IoT:

   * IoTEdgeSecurityDaemon.ps1
   * Microsoft-Azure-IoTEdge-amd64.cab del canal de versión 1.1.

   Es importante usar el script de PowerShell de la misma versión que el archivo .cab que se usa, porque la funcionalidad cambia para admitir las características de cada versión.

3. Si el archivo .cab que descargó tiene un sufijo de arquitectura, cambie el nombre del archivo a **Microsoft-Azure-IoTEdge.cab**.

4. Opcionalmente, descargue un instalador para Visual C++ Redistributable. Por ejemplo, el script de PowerShell usa esta versión: [vc_redist.x64.exe](https://download.microsoft.com/download/0/6/4/064F84EA-D1DB-4EAA-9A5C-CC2F0FF6A638/vc_redist.x64.exe). Guarde el instalador en la misma carpeta del dispositivo IoT que los archivos IoT Edge.

5. Para instalar con componentes sin conexión, [use el operador punto](/powershell/module/microsoft.powershell.core/about/about_scripts#script-scope-and-dot-sourcing) en la copia local del script de PowerShell.

6. Ejecute el comando [Deploy-IoTEdge](reference-windows-scripts.md#deploy-iotedge) con el parámetro `-OfflineInstallationPath`. Especifique la ruta de acceso absoluta al directorio de archivos. Por ejemplo,

   ```powershell
   . <path>\IoTEdgeSecurityDaemon.ps1
   Deploy-IoTEdge -OfflineInstallationPath <path>
   ```

   El comando de implementación usará cualquier componente que se encuentre en el directorio de archivos local proporcionado. Si falta el archivo .cab o el instalador de Visual C++, intentará descargarlos.

## <a name="update-iot-edge"></a>Actualización de IoT Edge

Use el comando `Update-IoTEdge` para actualizar el demonio de seguridad. El script extrae automáticamente la versión más reciente del demonio de seguridad.

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Update-IoTEdge
```

Al ejecutar el comando Update-IoTEdge se quita y actualiza el demonio de seguridad del dispositivo, junto con las dos imágenes de contenedor del entorno de ejecución. El archivo config.yaml se mantiene en el dispositivo, así como los datos del motor de contenedor Moby. Si conserva la información de configuración, no tendrá que volver a proporcionar la cadena de conexión o información de Device Provisioning Service para el dispositivo durante el proceso de actualización.

Si desea actualizar el demonio de seguridad a una versión específica, búsquela en el canal de versión 1.1 que desea que sea el objetivo desde las [versiones de IoT Edge](https://github.com/Azure/azure-iotedge/releases). En esa versión, descargue el archivo **Microsoft-Azure-IoTEdge.cab**. A continuación, use el parámetro `-OfflineInstallationPath` para que apunte a la ubicación del archivo local. Por ejemplo:

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Update-IoTEdge -OfflineInstallationPath <absolute path to directory>
```

>[!NOTE]
>El parámetro `-OfflineInstallationPath` busca un archivo llamado **Microsoft-Azure-IoTEdge.cab** en el directorio proporcionado. Cambie el nombre del archivo para quitar el sufijo de la arquitectura, en caso de que tenga.

Si quiere actualizar un dispositivo sin conexión, busque la versión de destino en las [versiones de Azure IoT Edge](https://github.com/Azure/azure-iotedge/releases). En esa versión, descargue los archivos *IoTEdgeSecurityDaemon.ps1* y *Microsoft-Azure-IoTEdge.cab*. Es importante usar el script de PowerShell de la misma versión que el archivo .cab que se usa, porque la funcionalidad cambia para admitir las características de cada versión.

Si el archivo .cab que descargó tiene un sufijo de arquitectura, cambie el nombre del archivo a **Microsoft-Azure-IoTEdge.cab**.

Para actualizar con componentes sin conexión, [use el operador punto](/powershell/module/microsoft.powershell.core/about/about_scripts#script-scope-and-dot-sourcing) en la copia local del script de PowerShell. A continuación, use el parámetro `-OfflineInstallationPath` como parte del comando `Update-IoTEdge` y proporcione la ruta de acceso absoluta al directorio de archivos. Por ejemplo,

```powershell
. <path>\IoTEdgeSecurityDaemon.ps1
Update-IoTEdge -OfflineInstallationPath <path>
```

Para más información sobre las opciones de autenticación, use el comando `Get-Help Update-IoTEdge -full` o consulte los [scripts de PowerShell para IoT Edge con contenedores Windows](reference-windows-scripts.md).

## <a name="uninstall-iot-edge"></a>Desinstalación de IoT Edge

Si quiere quitar la instalación de IoT Edge de su dispositivo, use los siguientes comandos.

Si quiere quitar la instalación de IoT Edge del dispositivo Windows, use el comando [Uninstall-IoTEdge](reference-windows-scripts.md#uninstall-iotedge) en una ventana administrativa de PowerShell. Este comando quita el entorno de ejecución de IoT Edge, junto con la configuración existente y los datos de motor de Moby.

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
Uninstall-IoTEdge
```

Para más información acerca de las opciones de desinstalación, use el comando `Get-Help Uninstall-IoTEdge -full`.

## <a name="next-steps"></a>Pasos siguientes

Siga [implementando módulos de IoT Edge](how-to-deploy-modules-portal.md) para información sobre cómo implementar módulos en el dispositivo.
