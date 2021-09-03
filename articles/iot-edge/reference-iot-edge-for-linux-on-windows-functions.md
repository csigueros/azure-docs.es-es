---
title: Funciones de PowerShell para Azure IoT Edge para Linux en Windows | Microsoft Docs
description: Información de referencia de funciones de PowerShell para Azure IoT Edge para Linux en Windows para implementar, aprovisionar y obtener el estado de máquinas virtuales de IoT Edge para Linux en Windows.
author: v-tcassi
ms.author: fcabrera
ms.date: 06/18/2021
ms.topic: reference
ms.service: iot-edge
services: iot-edge
monikerRange: =iotedge-2018-06
ms.openlocfilehash: a5af992bdfe3bb97383d3c8ed1ccd0e57dd1efb7
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121732859"
---
# <a name="powershell-functions-for-iot-edge-for-linux-on-windows"></a>Funciones de PowerShell para IoT Edge para Linux en Windows

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

Conozca las funciones de PowerShell que implementan, aprovisionan y obtienen el estado de su máquina virtual de IoT Edge para Linux en Windows (EFLOW).

## <a name="prerequisites"></a>Requisitos previos

Los comandos descritos en este artículo proceden del archivo `AzureEFLOW.psm1`, que puede encontrar en su sistema en el directorio `WindowsPowerShell`, en `C:\Program Files\WindowsPowerShell\Modules\AzureEFLOW`.

Si no tiene la carpeta **AzureEflow** en el directorio de PowerShell, siga estos pasos para descargar e instalar Azure IoT Edge para Linux en Windows: 

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

   Puede especificar la instalación personalizada y los directorios VHDX agregando los parámetros `INSTALLDIR="<FULLY_QUALIFIED_PATH>"` y `VHDXDIR="<FULLY_QUALIFIED_PATH>"` al comando install.

1. Establezca la directiva de ejecución en el dispositivo de destino en `AllSigned` si todavía no lo está.

   ```powershell
   Set-ExecutionPolicy -ExecutionPolicy AllSigned -Force
   ```

## <a name="connect-eflowvm"></a>Connect-EflowVM

El comando **Connect-EflowVM** se conecta a la máquina virtual mediante SSH. La única cuenta que se permite a SSH en la máquina virtual es la del usuario que la creó.

Este comando solo funciona en una sesión de PowerShell que se ejecuta en el dispositivo host. No funcionará al usar Windows Admin Center o PowerShell ISE.

Para obtener más información, use el comando `Get-Help Connect-EflowVM -full`.

## <a name="copy-eflowvmfile"></a>Copy-EflowVmFile

El comando **Copy-EflowVmFile** copia el archivo hacia o desde la máquina virtual mediante SCP. Use los parámetros opcionales para especificar las rutas de acceso del archivo de origen y de destino, así como la dirección de la copia.

El usuario **iotedge-user** debe tener permiso de lectura para los directorios de origen o permiso de escritura para los directorios de destino en la máquina virtual.

| Parámetro | Valores aceptados | Comentarios |
| --------- | --------------- | -------- |
| fromFile | Cadena que represente la ruta de acceso al archivo | Define el archivo del que se va a leer. |
| toFile | Cadena que represente la ruta de acceso al archivo |  Define el archivo en el que se va a escribir. |
| pushFile | Ninguno | Esta marca indica la dirección de copia. Si está presente, el comando inserta el archivo en la máquina virtual. Si no está presente, el comando extrae el archivo de la máquina virtual. |

Para obtener más información, use el comando `Get-Help Copy-EflowVMFile -full`.

## <a name="deploy-eflow"></a>Deploy-Eflow

El comando **Deploy-Eflow** es el método de implementación principal. El comando de implementación crea la máquina virtual, aprovisiona archivos e implementa el módulo de agente de IoT Edge. Aunque ninguno de los parámetros es obligatorio, se pueden usar para aprovisionar el dispositivo IoT Edge durante la implementación y para modificar la configuración de la máquina virtual durante la creación.

| Parámetro | Valores aceptados | Comentarios |
| --------- | --------------- | -------- |
| acceptEula | **Sí** o **No**. | Acceso directo para aceptar o denegar el CLUF y omitir el aviso del CLUF. |
| acceptOptionalTelemetry | **Sí** o **No**. |  Acceso directo para aceptar o denegar la telemetría opcional y omitir el mensaje de telemetría. |
| cpuCount | Valor entero entre 1 y el número de núcleos de CPU del dispositivo |  Número de núcleos de CPU para la VM.<br><br>**Valor predeterminado**: 1 núcleo virtual. |
| memoryInMB | Valor entero entre 1024 y la cantidad máxima de memoria libre del dispositivo |Memoria asignada para la VM.<br><br>**Valor predeterminado**: 1024 MB. |
| vmDiskSize | Entre 8 y 256 GB. | Tamaño de disco máximo del disco duro virtual de expansión dinámica.<br><br>**Valor predeterminado**: 16 GB. |
| vswitchName | Nombre del conmutador virtual |  Nombre del conmutador virtual asignado a la VM EFLOW. |
| vswitchType | **Interno** o **externo** | Tipo del conmutador virtual asignado a la VM EFLOW. |
| ip4Address | Dirección IPv4 en el intervalo del ámbito del servidor DCHP | Dirección IPv4 estática de la VM EFLOW. _NOTA: Solo se admite con el conmutador de ICS predeterminado_. |
| ip4PrefixLength | Longitud del prefijo IPv4 de la subred | Longitud del prefijo de subred IPv4, solo válida cuando se especifica una dirección IPv4 estática.  _NOTA: Solo se admite con el conmutador de ICS predeterminado_. |
| ip4GatewayAddress | Dirección IPv4 de la puerta de enlace de subred | Dirección IPv4 de puerta de enlace, solo válida cuando se especifica una dirección IPv4 estática.  _NOTA: Solo se admite con el conmutador de ICS predeterminado_. |
| gpuName | Nombre de dispositivo GPU |  Nombre del dispositivo GPU que se va a usar para el tránsito. |
| gpuPassthroughType | **DirectDeviceAssignment**, **ParaVirtualization** o ninguno (solo CPU) |  Tipo de tránsito de GPU |
| gpuCount | Valor entero entre 1 y el número de núcleos de la GPU del dispositivo | Número de dispositivos GPU para la VM. <br><br>**Nota**: Si usa ParaVirtualization, asegúrese de establecer gpuCount = 1. |

Para obtener más información, use el comando `Get-Help Deploy-Eflow -full`.  

## <a name="get-eflowhostconfiguration"></a>Get-EflowHostConfiguration

El comando **Get-EflowHostConfiguration** devuelve la configuración del host. Este comando no toma parámetros. Devuelve un objeto que contiene cuatro propiedades:

* FreePhysicalMemoryInMB
* NumberOfLogicalProcessors
* DiskInfo
* GpuInfo

Para obtener más información, use el comando `Get-Help Get-EflowHostConfiguration -full`.


## <a name="get-eflowlogs"></a>Get-EflowLogs

El comando **Get-EflowLogs** recopila y agrupa los registros de la implementación e instalación de IoT Edge para Linux en Windows. Genera los registros agrupados en forma de una carpeta `.zip`.

Para obtener más información, use el comando `Get-Help Get-EflowLogs -full`.

## <a name="get-eflowvm"></a>Get-EflowVm

El comando **Get-EflowVm** devuelve la configuración actual de la máquina virtual. Este comando no toma parámetros. Devuelve un objeto que contiene cuatro propiedades:

* VmConfiguration
* EdgeRuntimeVersion
* EdgeRuntimeStatus
* SystemStatistics

Para ver una propiedad específica en una lista legible, ejecute el comando `Get-EflowVM` con la propiedad expandida. Por ejemplo:

```powershell
Get-EflowVM | Select -ExpandProperty VmConfiguration | Format-List
```

Para obtener más información, use el comando `Get-Help Get-EflowVm -full`.

## <a name="get-eflowvmaddr"></a>Get-EflowVmAddr

El comando **Get-EflowVmAddr** se usa para consultar las direcciones IP y MAC actuales de la máquina virtual. Este comando existe para tener en cuenta el hecho de que las direcciones IP y MAC pueden cambiar con el tiempo.

Para obtener información adicional, use el comando `Get-Help Get-EflowVmAddr -full`.

## <a name="get-eflowvmfeature"></a>Get-EflowVmFeature

El comando **Get-EflowVmFeature** devuelve el estado de la habilitación de las características de IoT Edge para Linux en Windows.

| Parámetro | Valores aceptados | Comentarios |
| --------- | --------------- | -------- |
| feature | **DpsTpm** | Nombre de la característica que se va a alternar. |

Para obtener más información, use el comando `Get-Help Get-EflowVmFeature -full`.

## <a name="get-eflowvmname"></a>Get-EflowVmName

El comando **Get-EflowVmName** devuelve el nombre de host actual de la máquina virtual. Este comando existe para tener en cuenta el hecho de que el nombre de host de Windows puede cambiar con el tiempo.

Para obtener más información, use el comando `Get-Help Get-EflowVmName -full`.

## <a name="get-eflowvmtelemetryoption"></a>Get-EflowVmTelemetryOption

El comando **Get-EflowVmTelemetryOption** muestra el estado de la telemetría (**Opcional** u **Obligatorio**) dentro de la máquina virtual.

Para obtener más información, use el comando `Get-Help Get-EflowVmTelemetryOption -full`.


## <a name="get-eflowvmtpmprovisioninginfo"></a>Get-EflowVmTpmProvisioningInfo

El comando **Get-EflowVmTpmProvisioningInfo** devuelve la información de aprovisionamiento de TPM. Este comando no toma parámetros. Devuelve un objeto que contiene dos propiedades:

* Clave de aprobación
* Id. de registro 

Para obtener más información, use el comando `Get-Help Get-EflowVmTpmProvisioningInfo -full`.



## <a name="invoke-eflowvmcommand"></a>Invoke-EflowVmCommand

El comando **Invoke-EflowVMCommand** ejecuta un comando de Linux dentro de la máquina virtual y devuelve la salida. Este comando solo funciona para los comandos de Linux que devuelven una salida finita. No se puede usar para los comandos de Linux que requieren interacción del usuario o que se ejecutan indefinidamente.

Los siguientes parámetros opcionales se pueden usar para especificar el comando de antemano.

| Parámetro | Valores aceptados | Comentarios |
| --------- | --------------- | -------- |
| . | String | Comando que se va a ejecutar en la VM. |
| ignoreError | Ninguno |  Si esta marca está presente, ignore los errores del comando. |

Para obtener más información, use el comando `Get-Help Invoke-EflowVmCommand -full`.

## <a name="provision-eflowvm"></a>Provision-EflowVm

El comando **Provision-EflowVm** agrega la información de aprovisionamiento del dispositivo IoT Edge al archivo `config.yaml` de IOT Edge de la máquina virtual.

| Parámetro | Valores aceptados | Comentarios |
| --------- | --------------- | -------- |
| provisioningType | **ManualConnectionString**, **ManualX509**, **DpsTPM**, **DpsX509** o **DpsSymmetricKey** |  Define el tipo de aprovisionamiento que quiere usar para el dispositivo IoT Edge. |
| devConnString | Cadena de conexión de dispositivo de un dispositivo IoT Edge existente. | Cadena de conexión de dispositivo para el aprovisionamiento manual de un dispositivo IoT Edge (**ManualConnectionString**). |
| iotHubHostname | Nombre de host de un centro de IoT existente | Nombre de host de Azure IoT Hub para el aprovisionamiento de un dispositivo IoT Edge (**ManualX509**). |
| deviceId | Id. de dispositivo de un dispositivo IoT Edge existente | Id. de dispositivo para el aprovisionamiento de un dispositivo IoT Edge (**ManualX509**). |
| scopeId | Identificador de ámbito de una instancia de DPS existente. | Identificador de ámbito para el aprovisionamiento de un dispositivo IoT Edge (**DpsTPM**, **DpsX509** o **DpsSymmetricKey**). |
| symmKey | Clave principal de una inscripción de DPS existente o clave principal de un dispositivo IoT Edge existente registrado mediante claves simétricas. | Clave simétrica para el aprovisionamiento de un dispositivo IoT Edge (**DpsSymmetricKey**). |
| registrationId | Identificador de registro de un dispositivo de IoT Edge existente. | Identificador de registro para el aprovisionamiento de un dispositivo IoT Edge (**DpsSymmetricKey**). |
| identityCertPath | Ruta de acceso del directorio | Ruta de acceso de destino absoluta del certificado de identidad en la máquina host Windows (**ManualX509**, **DpsX509**). |
| identityPrivKeyPath | Ruta de acceso del directorio | Ruta de acceso de origen absoluta de la clave privada de identidad en la máquina host Windows (**ManualX509**, **DpsX509**). |

Para obtener más información, use el comando `Get-Help Provision-EflowVm -full`.

## <a name="set-eflowvm"></a>Set-EflowVM

El comando **Set-EflowVM** actualiza la configuración de la máquina virtual con las propiedades solicitadas. Use los parámetros opcionales para definir una configuración específica para la máquina virtual.

| Parámetro | Valores aceptados | Comentarios |
| --------- | --------------- | -------- |
| cpuCount | Valor entero entre 1 y los núcleos de GPU del dispositivo | Número de núcleos de CPU para la VM. |
| memoryInMB | Valor entero entre 1024 y la cantidad máxima de memoria libre del dispositivo | Memoria asignada para la VM. |
| gpuName | Nombre de dispositivo GPU |  Nombre del dispositivo GPU que se va a usar para el tránsito. |
| gpuPassthroughType | **DirectDeviceAssignment**, **ParaVirtualization** o ninguno (sin tránsito) |  Tipo de tránsito de GPU |
| gpuCount | Valor entero entre 1 y los núcleos de GPU del dispositivo | Número de dispositivos GPU para la VM **Nota**: Solo es válido cuando se usa DirectDeviceAssignment |
| headless | Ninguno | Si esta marca está presente, determina si se requiere la confirmación del usuario en caso de que se emita una advertencia de seguridad. |

Para obtener más información, use el comando `Get-Help Set-EflowVM -full`.

## <a name="set-eflowvmfeature"></a>Set-EflowVmFeature

El comando **Set-EflowVmFeature** habilita o deshabilita el estado de las características de IoT Edge para Linux en Windows.

| Parámetro | Valores aceptados | Comentarios |
| --------- | --------------- | -------- |
| feature | **DpsTpm** | Nombre de la característica que se va a alternar. |
| enable | Ninguno | Si esta marca está presente, el comando habilita la característica. |

Para obtener más información, use el comando `Get-Help Set-EflowVmFeature -full`.

## <a name="set-eflowvmtelemetryoption"></a>Set-EflowVmTelemetryOption

El comando **Set-EflowVmTelemetryOption** habilita o deshabilita la telemetría opcional dentro de la máquina virtual.

| Parámetro | Valores aceptados | Comentarios |
| --------- | --------------- | -------- |
| optionalTelemetry | **True** o **False** | Determina si la telemetría opcional está seleccionada. |

Para obtener más información, use el comando `Get-Help Set-EflowVmTelemetryOption -full`.

## <a name="start-eflowvm"></a>Start-EflowVm

El comando **Start-EflowVm** inicia la máquina virtual. Si la máquina virtual ya está iniciada, no se lleva a cabo ninguna acción.

Para obtener más información, use el comando `Get-Help Start-EflowVm -full`.

## <a name="stop-eflowvm"></a>Stop-EflowVm

El comando **Stop-EflowVm** detiene la máquina virtual. Si la máquina virtual ya está detenida, no se lleva a cabo ninguna acción.

Para obtener más información, use el comando `Get-Help Stop-EflowVm -full`.

## <a name="verify-eflowvm"></a>Verify-EflowVm

El comando **Verify-EflowVm** es una función expuesta que comprueba si se ha creado la máquina virtual IoT Edge para Linux en Windows. Solo toma parámetros comunes. Devuelve **true** si se creó la máquina virtual y **false** en caso contrario.

Para obtener más información, use el comando `Get-Help Verify-EflowVm -full`.

## <a name="next-steps"></a>Pasos siguientes

Obtenga información sobre cómo usar estos comandos para instalar y aprovisionar IoT Edge para Linux en Windows en el siguiente artículo:

* [Instalación de Azure IoT Edge para Linux en Windows](./how-to-install-iot-edge-windows-on-windows.md)