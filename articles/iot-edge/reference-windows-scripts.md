---
title: Scripts para Azure IoT Edge con contenedores de Windows | Microsoft Docs
description: Información de referencia sobre los scripts de PowerShell para instalar, desinstalar o actualizar IoT Edge en dispositivos Windows
author: kgremban
ms.author: kgremban
ms.date: 10/06/2020
ms.topic: reference
ms.service: iot-edge
services: iot-edge
monikerRange: =iotedge-2018-06
ms.openlocfilehash: 912d13e5a9c05916e209ee42cf5629b8a363e686
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121749529"
---
# <a name="powershell-scripts-for-iot-edge-with-windows-containers"></a>Scripts de PowerShell para IoT Edge con contenedores de Windows

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

Conozca los scripts de PowerShell que permiten instalar, actualizar o desinstalar IoT Edge en dispositivos Windows.

Los comandos descritos en este artículo proceden del archivo `IoTEdgeSecurityDaemon.ps1` que se publica con cada [versión de IoT Edge](https://github.com/Azure/azure-iotedge/releases). La versión más reciente del script siempre está disponible en aka.ms/iotedge-win.

Puede ejecutar cualquiera de los comandos con el cmdlet `Invoke-WebRequest` para acceder a la última versión del script. Por ejemplo:

```powershell
. {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
Deploy-IoTEdge
```

También puede descargar este script, o uno de una versión específica, para ejecutar los comandos. Por ejemplo:

```powershell
. <path>\IoTEdgeSecurityDaemon.ps1
Deploy-IoTEdge
```

El script proporcionado se firma para aumentar la seguridad. Puede comprobar la firma si descarga el script en el dispositivo y luego ejecuta el siguiente comando de PowerShell:

```powershell
Get-AuthenticodeSignature "C:\<path>\IotEdgeSecurityDaemon.ps1"
```

El estado de salida es **Válido** si se comprueba la firma.

## <a name="deploy-iotedge"></a>Deploy-IoTEdge

El comando Deploy-IoTEdge descarga e implementa el demonio de seguridad de IoT Edge y sus dependencias. El comando de implementación acepta estos parámetros comunes, entre otros. Para obtener la lista completa, use el comando `Get-Help Deploy-IoTEdge -full`.  

| Parámetro | Valores aceptados | Comentarios |
| --------- | --------------- | -------- |
| **ContainerOs** | **Windows** o **Linux** | Si no se especifica el sistema operativo del contenedor, el valor predeterminado es Windows.<br><br>En el caso de los contenedores de Windows, IoT Edge usa el motor del contenedor Moby que se incluye en la instalación. Para los contenedores de Linux, deberá instalar un motor de contenedor antes de iniciar la instalación. |
| **Proxy** | Dirección URL del proxy | Incluya este parámetro si el dispositivo debe pasar por un servidor proxy para conectarse a Internet. Para más información, consulte [Configuración de un dispositivo de IoT Edge para que se comunique a través de un servidor proxy](how-to-configure-proxy-support.md). |
| **OfflineInstallationPath** | Ruta de acceso del directorio | Si se incluye este parámetro, el instalador buscará en el directorio enumerado el archivo CAB de IoT Edge y los archivos MSI del runtime de VC necesarios para la instalación. Se descargan los archivos que no se encuentren en el directorio. Si ambos archivos se encuentran en el directorio, IoT Edge se puede instalar sin una conexión a Internet. Este parámetro también se puede usar para utilizar una versión concreta. |
| **InvokeWebRequestParameters** | Tabla de hash de parámetros y valores | Durante la instalación se realizan varias solicitudes web. Utilice este campo para definir parámetros para esas solicitudes web. Este parámetro es útil para configurar las credenciales para los servidores proxy. Para más información, consulte [Configuración de un dispositivo de IoT Edge para que se comunique a través de un servidor proxy](how-to-configure-proxy-support.md). |
| **RestartIfNeeded** | None | Esta marca permite al script de implementación reiniciar el equipo sin preguntar, en caso de que sea necesario. |

## <a name="initialize-iotedge"></a>Initialize-IoTEdge

El comando Initialize-IoTEdge configura IoT Edge con su cadena de conexión del dispositivo y los detalles operativos. Gran parte de la información que genera este comando se almacena en el archivo iotedge\config.yaml. El comando de inicialización acepta estos parámetros comunes, entre otros. Para obtener la lista completa, use el comando `Get-Help Initialize-IoTEdge -full`.

| Parámetro | Valores aceptados | Comentarios |
| --------- | --------------- | -------- |
| **ManualConnectionString** | None | **Parámetro de modificador**. **Valor predeterminado**. Si no se especifica el tipo de aprovisionamiento, el valor predeterminado es el manual con una cadena de conexión.<br><br>Declara que se proporcionará una cadena de conexión del dispositivo para aprovisionarlo manualmente. |
| **ManualX509** | None | **Parámetro de modificador**. Si no se especifica el tipo de aprovisionamiento, el valor predeterminado es el manual con una cadena de conexión.<br><br>Declara que se proporcionarán un certificado de identidad y una clave privada para aprovisionar el dispositivo manualmente.
| **DpsTpm** | None | **Parámetro de modificador**. Si no se especifica el tipo de aprovisionamiento, el valor predeterminado es el manual con una cadena de conexión.<br><br>Declara que usted proporcionará un Id. de ámbito de Device Provisioning Service (DPS) y el Id. de registro del dispositivo para aprovisionar a través de DPS.  |
| **DpsSymmetricKey** | None | **Parámetro de modificador**. Si no se especifica el tipo de aprovisionamiento, el valor predeterminado es el manual con una cadena de conexión.<br><br>Declara que se proporcionarán un identificador de ámbito de Device Provisioning Service (DPS) y el identificador de registro del dispositivo para realizar el aprovisionamiento por medio de DPS, junto con una clave simétrica para la atestación. |
| **DpsX509** | None | **Parámetro de modificador**. Si no se especifica el tipo de aprovisionamiento, el valor predeterminado es el manual con una cadena de conexión.<br><br>Declara que se proporcionarán un identificador de ámbito de Device Provisioning Service (DPS) y el identificador de registro del dispositivo para realizar el aprovisionamiento por medio de DPS, junto con un certificado de identidad X.509 y una clave privada para la atestación.  |
| **DeviceConnectionString** | Una cadena de conexión desde un dispositivo de IoT Edge registrado en IoT Hub, entre comillas simples | **Obligatorio** para el aprovisionamiento manual con una cadena de conexión. Si no proporciona una cadena de conexión en los parámetros del script, se le pedirá una. |
| **IotHubHostName** | Nombre de host del centro de IoT al que se conecta un dispositivo. | **Obligatorio** para el aprovisionamiento manual con certificados X.509. El formato es *{nombre del centro}.azure-devices.net*. |
| **DeviceId** | Id. de dispositivo de una identidad de dispositivo registrada en IoT Hub. | **Obligatorio** para el aprovisionamiento manual con certificados X.509. |
| **ScopeId** | Un Id. de ámbito de una instancia de Device Provisioning Service asociada con IoT Hub. | **Obligatorio** para aprovisionamiento de DPS. Si no proporciona un identificador de ámbito en los parámetros del script, se le pedirá uno. |
| **RegistrationId** | Un identificador de registro generado por el dispositivo | **Obligatorio** para aprovisionamiento de DPS si se usa TPM o la atestación de clave simétrica. **Opcional**  si se usa la atestación de certificados X. 509. |
| **X509IdentityCertificate** | La ruta de acceso del URI al certificado de identidad del dispositivo X. 509 en el dispositivo. | **Obligatorio** para el aprovisionamiento manual o por medio de DPS si se usa la atestación de certificados X.509. |
| **X509IdentityPrivateKey** | La ruta de acceso del URI a la clave del certificado de identidad del dispositivo X. 509 en el dispositivo. | **Obligatorio** para el aprovisionamiento manual o por medio de DPS si se usa la atestación de certificados X.509. |
| **SymmetricKey** | La clave simétrica usada para aprovisionar la identidad del dispositivo de IoT Edge al usar DPS | **Obligatorio** para aprovisionamiento de DPS si se usa atestación de clave simétrica. |
| **ContainerOs** | **Windows** o **Linux** | Si no se especifica el sistema operativo del contenedor, el valor predeterminado es Windows.<br><br>En el caso de los contenedores de Windows, IoT Edge usa el motor del contenedor Moby que se incluye en la instalación. Para los contenedores de Linux, deberá instalar un motor de contenedor antes de iniciar la instalación. |
| **DeviceCACertificate** | Ruta de acceso del URI al certificado de la entidad de certificación del dispositivo (X.509) en dicho dispositivo. | También puede configurarse en el archivo `C:\ProgramData\iotedge\config.yaml`. Para obtener más información, consulte [Administración de certificados en un dispositivo IoT Edge](how-to-manage-device-certificates.md). |
| **DeviceCAPrivateKey** | Ruta de acceso del URI a la clave privada de la entidad de certificación (X.509) del dispositivo en dicho dispositivo. | También puede configurarse en el archivo `C:\ProgramData\iotedge\config.yaml`. Para obtener más información, consulte [Administración de certificados en un dispositivo IoT Edge](how-to-manage-device-certificates.md). |
| **InvokeWebRequestParameters** | Tabla de hash de parámetros y valores | Durante la instalación se realizan varias solicitudes web. Utilice este campo para definir parámetros para esas solicitudes web. Este parámetro es útil para configurar las credenciales para los servidores proxy. Para más información, consulte [Configuración de un dispositivo de IoT Edge para que se comunique a través de un servidor proxy](how-to-configure-proxy-support.md). |
| **AgentImage** | URI de la imagen del agente de IoT Edge | De forma predeterminada, una nueva instalación de IoT Edge utiliza la etiqueta gradual más reciente de la imagen del agente de IoT Edge. Utilice este parámetro para establecer una etiqueta específica para la versión de la imagen o para proporcionar su propia imagen de agente. Para obtener más información, vea [Información sobre las etiquetas de IoT Edge](how-to-update-iot-edge.md#understand-iot-edge-tags). |
| **Nombre de usuario** | Nombre de usuario de registro de contenedor | Utilice este parámetro solo si establece el parámetro -AgentImage en un contenedor en un registro privado. Proporcione un nombre de usuario con acceso al registro. |
| **Contraseña** | Cadena de contraseña segura | Utilice este parámetro solo si establece el parámetro -AgentImage en un contenedor en un registro privado. Proporcione la contraseña para acceder al registro. |

## <a name="update-iotedge"></a>Update-IoTEdge

| Parámetro | Valores aceptados | Comentarios |
| --------- | --------------- | -------- |
| **ContainerOs** | **Windows** o **Linux** | Si no se especifica el sistema operativo del contenedor, el valor predeterminado es Windows. Para los contenedores de Windows, se incluirá un motor de contenedor en la instalación. Para los contenedores de Linux, deberá instalar un motor de contenedor antes de iniciar la instalación. |
| **Proxy** | Dirección URL del proxy | Incluya este parámetro si el dispositivo debe pasar por un servidor proxy para conectarse a Internet. Para más información, consulte [Configuración de un dispositivo de IoT Edge para que se comunique a través de un servidor proxy](how-to-configure-proxy-support.md). |
| **InvokeWebRequestParameters** | Tabla de hash de parámetros y valores | Durante la instalación se realizan varias solicitudes web. Utilice este campo para definir parámetros para esas solicitudes web. Este parámetro es útil para configurar las credenciales para los servidores proxy. Para más información, consulte [Configuración de un dispositivo de IoT Edge para que se comunique a través de un servidor proxy](how-to-configure-proxy-support.md). |
| **OfflineInstallationPath** | Ruta de acceso del directorio | Si se incluye este parámetro, el instalador buscará en el directorio enumerado el archivo CAB de IoT Edge y los archivos MSI del runtime de VC necesarios para la instalación. Se descargan los archivos que no se encuentren en el directorio. Si ambos archivos se encuentran en el directorio, IoT Edge se puede instalar sin una conexión a Internet. Este parámetro también se puede usar para utilizar una versión concreta. |
| **RestartIfNeeded** | None | Esta marca permite al script de implementación reiniciar el equipo sin preguntar, en caso de que sea necesario. |

## <a name="uninstall-iotedge"></a>Uninstall-IoTEdge

| Parámetro | Valores aceptados | Comentarios |
| --------- | --------------- | -------- |
| **Force** | None | Esta marca fuerza la desinstalación en caso de que el intento anterior de desinstalación no diera unos resultados satisfactorio.
| **RestartIfNeeded** | None | Esta marca permite al script de desinstalación reiniciar el equipo sin preguntar, en caso de que sea necesario. |

## <a name="next-steps"></a>Pasos siguientes

Obtenga más información sobre cómo usar estos comandos en el artículo siguiente:

* [Instalación o desinstalación de Azure IoT Edge para Windows](how-to-install-iot-edge-windows-on-windows.md)
