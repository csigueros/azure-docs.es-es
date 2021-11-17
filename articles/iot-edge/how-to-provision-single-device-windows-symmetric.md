---
title: 'Creación y aprovisionamiento de un dispositivo IoT Edge en Windows con claves simétricas: Azure IoT Edge | Microsoft Docs'
description: Creación y aprovisionamiento de un dispositivo IoT Edge único de Windows en IoT Hub mediante el aprovisionamiento manual con claves simétricas
author: kgremban
ms.reviewer: v-tcassi
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 10/28/2021
ms.author: kgremban
monikerRange: iotedge-2018-06
ms.openlocfilehash: 4c831e12c7660727c966d1fdc9d0079d8b236fa5
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/05/2021
ms.locfileid: "131853365"
---
# <a name="create-and-provision-an-iot-edge-device-on-windows-using-symmetric-keys"></a>Creación y aprovisionamiento de un dispositivo IoT Edge en Windows con claves simétricas

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

En este artículo se proporcionan instrucciones completas para registrar y aprovisionar un dispositivo IoT Edge de Windows.

>[!NOTE]
>Azure IoT Edge con contenedores Windows no se admitirá a partir de la versión 1.2 de Azure IoT Edge.
>
>Considere la posibilidad de usar el nuevo método para ejecutar IoT Edge en dispositivos Windows, [Azure IoT Edge para Linux en Windows](iot-edge-for-linux-on-windows.md).
>
>Si desea usar Azure IoT Edge para Linux en Windows, puede seguir los pasos de la [guía de procedimientos equivalente](how-to-provision-single-device-linux-on-windows-symmetric.md).

Todos los dispositivos que se conectan a IoT Hub tienen un identificador de dispositivo que se usa para realizar un seguimiento de las comunicaciones de la nube al dispositivo o del dispositivo a la nube. Configure un dispositivo con su información de conexión, que incluye el nombre de host del centro de IoT, el identificador de dispositivo y la información que el dispositivo usa para autenticarse en IoT Hub.

Los pasos de este artículo le guían a través de un proceso denominado aprovisionamiento manual, donde conecta un único dispositivo a su centro de IoT. Para el aprovisionamiento manual, tiene dos opciones para autenticar dispositivos IoT Edge:

* **Claves simétricas**: cuando se crea una identidad de dispositivo en IoT Hub, el servicio crea dos claves. Debe colocar una de las claves en el dispositivo y este presenta la clave a IoT Hub al autenticarse.

  Este método de autenticación es más rápido para comenzar, pero no es tan seguro.

* **X.509 autofirmado**: cree dos certificados de identidad X.509 y colóquelos en el dispositivo. Cuando se crea una identidad de dispositivo nueva en IoT Hub, se proporcionan huellas digitales desde ambos certificados. Cuando el dispositivo se autentica en IoT Hub, presenta un certificado, y IoT Hub comprueba que el certificado coincide con su huella digital.

  Este método de autenticación es más seguro y se recomienda para los escenarios de producción.

En este artículo se describe el uso de claves simétricas como método de autenticación. Si desea usar certificados X.509, consulte [Creación y aprovisionamiento de un dispositivo IoT Edge en Windows mediante certificados X.509](how-to-provision-single-device-windows-x509.md).

> [!NOTE]
> Si tiene muchos dispositivos para configurar y no quiere aprovisionar manualmente cada uno, siga uno de los artículos a continuación para obtener información sobre cómo funciona IoT Edge con IoT Hub Device Provisioning Service:
>
> * [Creación y aprovisionamiento de dispositivos IoT Edge a gran escala mediante certificados X.509](how-to-provision-devices-at-scale-windows-x509.md)
> * [Creación y aprovisionamiento de dispositivos IoT Edge a gran escala con un TPM](how-to-provision-devices-at-scale-windows-tpm.md)
> * [Creación y aprovisionamiento de dispositivos IoT Edge a gran escala mediante claves simétricas](how-to-provision-devices-at-scale-windows-symmetric.md)

## <a name="prerequisites"></a>Prerrequisitos

En este artículo se explica cómo registrar un dispositivo IoT Edge e instalar IoT Edge en él. Estas tareas tienen requisitos previos y utilidades diferentes que se usan para realizarlas. Asegúrese de que cumple todos los requisitos previos antes de continuar.

<!-- Device registration prerequisites H3 and content -->
[!INCLUDE [iot-edge-prerequisites-register-device.md](../../includes/iot-edge-prerequisites-register-device.md)]

### <a name="iot-edge-installation"></a>Instalación de IoT Edge

Un dispositivo Windows.

IoT Edge para contenedores de Windows requiere la versión 1809/compilación 17763 de Windows, que es la [compilación de compatibilidad con Windows a largo plazo](/windows/release-information/) más reciente. Asegúrese de examinar la [lista de sistemas compatibles](support.md#operating-systems) para obtener una lista de las SKU admitidas.

<!-- Register your device and View provisioning information H2s and content -->
[!INCLUDE [iot-edge-register-device-symmetric.md](../../includes/iot-edge-register-device-symmetric.md)]

<!-- Install IoT Edge on Windows H2 and content -->
[!INCLUDE [install-iot-edge-windows.md](../../includes/iot-edge-install-windows.md)]

## <a name="provision-the-device-with-its-cloud-identity"></a>Aprovisionamiento del dispositivo con su identidad de nube

Ahora que el motor del contenedor y el entorno de ejecución de IoT Edge están instalados en el dispositivo, está preparado para el paso siguiente, que consiste en configurar el dispositivo con su información de identidad de nube y autenticación.

1. En el dispositivo IoT Edge, ejecute PowerShell como administrador.

2. Use el comando [Initialize-IoTEdge](reference-windows-scripts.md#initialize-iotedge) para configurar el entorno de ejecución de IoT Edge en la máquina. El comportamiento predeterminado del comando es el aprovisionamiento manual con contenedores de Windows.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -ManualConnectionString -ContainerOs Windows
   ```

   * Si ha descargado el script IoTEdgeSecurityDaemon.ps1 en el dispositivo para la instalación sin conexión o de una versión específica, asegúrese de hacer referencia a la copia local del script.

      ```powershell
      . <path>/IoTEdgeSecurityDaemon.ps1
      Initialize-IoTEdge -ManualConnectionString -ContainerOs Windows
      ```

3. Cuando se le pida, especifique la cadena de conexión del dispositivo que ha recuperado en la sección anterior. La cadena de conexión del dispositivo asocia el dispositivo físico a un identificador de dispositivo de IoT Hub y proporciona información de autenticación.

   La cadena de conexión del dispositivo adopta el formato siguiente y no debe incluir comillas: `HostName={IoT_hub_name}.azure-devices.net;DeviceId={device_name};SharedAccessKey={key}`

Al aprovisionar un dispositivo manualmente, puede usar parámetros adicionales para modificar el proceso, lo que incluye:

* Dirigir el tráfico para que pase por un servidor proxy
* Declarar una imagen de contenedor de edgeAgent específica y proporcionar las credenciales si se encuentra en un registro privado

Para más información sobre estos parámetros adicionales, consulte [Scripts de PowerShell para IoT Edge con contenedores Windows](reference-windows-scripts.md).

## <a name="verify-successful-configuration"></a>Comprobación de configuración correcta

Compruebe que el runtime se ha instalado y configurado correctamente en el dispositivo de IoT Edge.

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
   . path_to_powershell_module_here\IoTEdgeSecurityDaemon.ps1
   Deploy-IoTEdge -OfflineInstallationPath path_to_file_directory_here
   ```

   El comando de implementación usará cualquier componente que se encuentre en el directorio de archivos local proporcionado. Si falta el archivo .cab o el instalador de Visual C++, intentará descargarlos.

## <a name="uninstall-iot-edge"></a>Desinstalación de IoT Edge

Si quiere quitar la instalación de IoT Edge del dispositivo Windows, use el comando [Uninstall-IoTEdge](reference-windows-scripts.md#uninstall-iotedge) en una ventana administrativa de PowerShell. Este comando quita el entorno de ejecución de IoT Edge, junto con la configuración existente y los datos de motor de Moby.

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
Uninstall-IoTEdge
```

Para más información acerca de las opciones de desinstalación, use el comando `Get-Help Uninstall-IoTEdge -full`.

## <a name="next-steps"></a>Pasos siguientes

Siga [implementando módulos de IoT Edge](how-to-deploy-modules-portal.md) para información sobre cómo implementar módulos en el dispositivo.
