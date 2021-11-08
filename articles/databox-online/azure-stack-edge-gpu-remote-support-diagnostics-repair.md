---
title: Habilitación del soporte técnico, el diagnóstico y la corrección de forma remota en dispositivos de Azure Stack Edge
description: Se describe cómo habilitar el soporte técnico remoto en los dispositivos de Azure Stack Edge.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: troubleshooting
ms.date: 10/26/2021
ms.author: alkohli
ms.openlocfilehash: 364845dc046664a7af4d9f7ac6fbb965a818430a
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131093793"
---
# <a name="remote-support-and-diagnostics-for-azure-stack-edge"></a>Soporte técnico y diagnóstico de forma remota en Azure Stack Edge 

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]


> [!IMPORTANT]
> El soporte técnico remoto se encuentra en versión preliminar pública y se aplica a Azure Stack Edge, versión 2110 o posterior.

En el dispositivo de Azure Stack Edge, puede habilitar el soporte técnico remoto para que los ingenieros de Microsoft puedan acceder a él de forma remota con el fin de diagnosticar y corregir problemas. Al habilitar esta característica, da su consentimiento con respecto al nivel de acceso y la duración de dicho acceso. 

En este artículo se describe la característica de soporte técnico remoto, cuándo usarla y cómo se puede habilitar, y se proporciona una lista de las operaciones que los ingenieros de Microsoft pueden ejecutar en el dispositivo de forma remota.


## <a name="about-remote-support"></a>Acerca del soporte técnico remoto

Si experimenta problemas con Azure Stack Edge, normalmente debe recopilar un paquete de soporte técnico y enviárselo al equipo de Soporte técnico de Microsoft. En algunos casos, los registros no contienen suficiente información para diagnosticar y corregir el problema. En este caso, el equipo de Soporte técnico de Microsoft se pone en contacto con usted para obtener su consentimiento para el soporte técnico remoto.

Estas son algunas de las ventajas del soporte técnico remoto:

- Puede resolver el problema con mayor rapidez, ya que el equipo de Soporte técnico de Microsoft no tiene que organizar una reunión en persona.
- Puede ver la transcripción de la sesión de soporte técnico remoto, incluidas todas las operaciones que se ejecutaron en el dispositivo.
- Tiene el control total de los datos y puede revocar el consentimiento en cualquier momento. Si olvida salir de la sesión, el acceso a esta se deshabilita automáticamente una vez que expira la duración establecida.

## <a name="how-remote-support-works"></a>Cómo funciona el soporte técnico remoto

En la ilustración siguiente se muestra cómo funciona el soporte técnico remoto.

![Ilustración del soporte técnico remoto en Azure Stack Edge](media/azure-stack-edge-gpu-remote-support-diagnostics-repair/remote-support-flow-1.png)

1. Puede dar su consentimiento al soporte técnico remoto de su dispositivo a través de Azure Portal. También puede configurar el nivel y la duración del acceso al dispositivo.
1. El consentimiento y la solicitud de soporte técnico remoto se envían desde Azure Portal al proveedor de recursos (RP) de Azure Stack Edge que, a su vez, lo envía al dispositivo. El agente de dispositivo solicita al RP la credencial de conexión híbrida a la que se puede conectar.
1. Se crea una credencial de conexión híbrida para establecer una conexión híbrida de Azure. Esta conexión tiene las siguientes características:
    
    - Es específica del dispositivo. Cada dispositivo tiene su propia conexión y la conexión no se comparte.
    - Permite que el equipo de Soporte técnico de Microsoft tenga acceso Just-In-Time (JIT) al dispositivo a través de un canal seguro, auditado y compatible.  
    - Esta conexión usa un protocolo *https* a través del puerto 443 y el tráfico se cifra con TLS 1.2.
     
1. El agente de dispositivo comienza a escuchar en esa conexión híbrida las solicitudes que llegan a través de la interfaz del explorador al Centro de soporte técnico de Azure. 
1. El explorador solicita conectarse a la conexión híbrida y la solicitud se envía al dispositivo para abrir una sesión de PowerShell restringida. Si existe el consentimiento, se acepta la solicitud. Si el dispositivo no tiene el consentimiento, se rechaza esa conexión. 

Una vez establecida la conexión, toda la comunicación se produce a través de esta conexión segura. 

Las operaciones que puede realizar el equipo de Soporte técnico de Microsoft están *restringidas* en función del nivel de acceso concedido mediante [Just Enough Administration (JEA)](/powershell/scripting/learn/remoting/jea/overview). Para más información sobre los cmdlets que puede ejecutar el equipo de Soporte técnico de Microsoft durante una sesión, consulte en este artículo la [lista de operaciones de Soporte técnico de Microsoft permitidas](#operations-allowed-in-remote-support).


## <a name="enable-remote-support"></a>Habilitación del soporte técnico remoto

Para configurar el soporte técnico remoto para su dispositivo de Azure Stack Edge, siga estos pasos:

1. En Azure Portal, vaya al recurso de Azure Stack Edge de su dispositivo y, luego, acceda a **Diagnóstico**.
1. De forma predeterminada, Microsoft no tiene acceso remoto al dispositivo y el estado de soporte técnico remoto se muestra como **No habilitado**. Para habilitar esta característica, seleccione **Configure remote support** (Configurar el soporte técnico remoto).

    ![Captura de pantalla de la habilitación del soporte técnico remoto en Azure Stack Edge con la opción Configure remote support (Configurar el soporte técnico remoto) resaltada](media/azure-stack-edge-gpu-remote-support-diagnostics-repair/enable-remote-support-1.png)

1. En **Nivel de acceso**, seleccione **Diagnóstico** para proporcionar acceso JIT al equipo de soporte técnico de Microsoft y que pueda recopilar información de diagnóstico de forma remota. Si es necesario, el equipo de soporte técnico le recomendará que seleccione **Diagnostics and repair** (Diagnóstico y reparación) para permitir la acción de corrección remota. 

    Cada nivel de acceso permite un conjunto diferente de comandos remotos en el dispositivo y, por tanto, un conjunto diferente de operaciones. 

    - El diagnóstico permite principalmente operaciones de lectura y, por lo tanto, la mayoría de los cmdlets `Get` están disponibles.
    - La opción de diagnóstico y reparación permite acceso de lectura y escritura y, por tanto, además de los cmdlets `Get`, también están disponibles los cmdlets `Set`, `Add`, `Start`, `Restart`, `Stop`, `Invoke` y `Remove`.
    
    Para más información, consulte [Lista de todas las operaciones de soporte técnico permitidas en cada nivel de acceso](#operations-allowed-in-remote-support).

1. Seleccione la duración del acceso remoto. Puede proporcionarse durante 7, 15, 21 o 30 días. Al final de este período, el acceso remoto al dispositivo se deshabilita automáticamente. 

1. Para revocar el acceso en cualquier momento, establezca **Do not allow access** (No permitir el acceso). Esta acción finaliza las sesiones existentes y no permite establecer otras nuevas.

1. Cuando haya configurado el soporte técnico remoto, seleccione **Aplicar** para que la configuración surta efecto. Al seleccionar **Aplicar**, da su consentimiento a que Microsoft recopile información de diagnóstico de forma remota en un entorno seguro y controlado.

    ![Captura de pantalla de la configuración del soporte técnico remoto en Azure Stack Edge con el nivel y la duración del acceso resaltados](media/azure-stack-edge-gpu-remote-support-diagnostics-repair/enable-remote-support-2.png)    

## <a name="remote-support-examples"></a>Ejemplos de soporte técnico remoto

En los siguientes escenarios de ejemplo se muestra cómo realizar varias operaciones cuando el soporte técnico remoto está habilitado en el dispositivo. 

Para realizar las operaciones de soporte técnico remoto, primero debe [conectarse a la interfaz de PowerShell](azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface) del dispositivo y, luego, ejecutar los cmdlets.

### <a name="list-existing-remote-sessions"></a>Lista de sesiones remotas existentes

Use el cmdlet `Get-HcsRemoteSupportSession` para enumerar todas las sesiones remotas que se realizaron en el dispositivo en el número de días especificado.

```powershell
Get-HcsRemoteSupportSession -IncludeTranscriptContents $false -NumberOfDays <Number of days>
```
Esta es una salida de ejemplo de todas las sesiones de soporte técnico remoto configuradas en los últimos 10 días. 

```output
[10.126.76.20]: PS>Get-HcsRemoteSupportSession -IncludeTranscriptContents $false -NumberOfDays 10

SessionId                : 3c135cba-f479-4fef-8dbb-a2b52b744504
RemoteApplication        : Powershell
AccessLevel              : ReadWrite
ControlSession           : False
SessionStartTime         : 8/19/2021 10:41:03 PM +00:00
SessionEndTime           : 8/19/2021 10:44:31 PM +00:00
SessionTranscriptPath    : C:\ProgramData\JEAConfiguration\Transcripts\RemoteRepair\PowerShell_transcript.HW4J1T2.Lp+Myohb.20210
                           819154101.txt
SessionTranscriptContent :

SessionId                : c0f2d002-66a0-4d54-87e4-4b1b949ad686
RemoteApplication        : Powershell
AccessLevel              : ReadWrite
ControlSession           : False
SessionStartTime         : 8/19/2021 7:41:19 PM +00:00
SessionEndTime           : 8/19/2021 7:58:20 PM +00:00
SessionTranscriptPath    : C:\ProgramData\JEAConfiguration\Transcripts\RemoteRepair\PowerShell_transcript.HW4J1T2.j0lCd5Tm.20210
                           819124117.txt
SessionTranscriptContent :

SessionId                : ca038e58-5344-4377-ab9c-c857a27c8b73
RemoteApplication        : Powershell
AccessLevel              : ReadOnly
ControlSession           : False
SessionStartTime         : 8/19/2021 10:49:39 PM +00:00
SessionEndTime           : 8/20/2021 12:49:40 AM +00:00
SessionTranscriptPath    : C:\ProgramData\JEAConfiguration\Transcripts\RemoteDiagnostics\PowerShell_transcript.HW4J1T2.YHmes94q.
                           20210819154937.txt
SessionTranscriptContent :

[10.126.76.20]: PS>
```


### <a name="get-details-on-a-specific-remote-session"></a>Obtención de detalles sobre una sesión remota específica

Use el cmdlet `Get-HcsRemoteSupportSession` para obtener los detalles de la sesión remota con el identificador *SessionID*.

```powershell
Get-HcsRemoteSupportSession -SessionId <SessionId> -IncludeSessionTranscript $true
```

Esta es una salida de ejemplo de una sesión específica en la que el soporte técnico remoto se configuró con la opción **Diagnóstico**. En este caso, `AccessLevel` es `ReadOnly`.

```output
[10.126.76.20]: PS>Get-HcsRemoteSupportSession -SessionId 360706a2-c530-419f-932b-55403e19502e -IncludeTranscriptContents $true

SessionId                : 360706a2-c530-419f-932b-55403e19502e
RemoteApplication        : Powershell
AccessLevel              : ReadOnly
ControlSession           : False
SessionStartTime         : 8/26/2021 2:35:37 PM +00:00
SessionEndTime           :
SessionTranscriptPath    : C:\ProgramData\JEAConfiguration\Transcripts\RemoteDiagnostics\PowerShell_transcript.HW4J1T2.u7qF2J2d.
                           20210826073536.txt
SessionTranscriptContent : **********************
                           Windows PowerShell transcript start
                           Start time: 20210826073536
                           Username: WORKGROUP\HW4J1T2$
                           RunAs User: WORKGROUP\SYSTEM
                           Configuration Name: RemoteDiagnostics
                           Machine: HW4J1T2 (Microsoft Windows NT 10.0.17763.0)
                           Host Application: C:\Windows\system32\wsmprovhost.exe -Embedding
                           Process ID: 10976
                           PSVersion: 5.1.17763.10520
                           PSEdition: Desktop
                           PSCompatibleVersions: 1.0, 2.0, 3.0, 4.0, 5.0, 5.1.17763.10520
                           BuildVersion: 10.0.17763.10520
                           CLRVersion: 4.0.30319.42000
                           WSManStackVersion: 3.0
                           PSRemotingProtocolVersion: 2.3
                           SerializationVersion: 1.1.0.1
                           **********************
                           PS>CommandInvocation(Get-Command): "Get-Command"
                           >> ParameterBinding(Get-Command): name="Name"; value="Out-Default, Exit-PSSession"
                           >> ParameterBinding(Get-Command): name="CommandType"; value="Alias, Function, Filter, Cmdlet,
                           Configuration"
                           >> ParameterBinding(Get-Command): name="Module"; value=""
                           >> ParameterBinding(Get-Command): name="ListImported"; value="True"
                           >> ParameterBinding(Get-Command): name="ErrorAction"; value="SilentlyContinue"
                           >> CommandInvocation(Measure-Object): "Measure-Object"
                           >> CommandInvocation(Select-Object): "Select-Object"
                           >> ParameterBinding(Select-Object): name="Property"; value="Count"
                           >> ParameterBinding(Measure-Object): name="InputObject"; value="Out-Default"
                           >> ParameterBinding(Measure-Object): name="InputObject"; value="Exit-PSSession"
                           PS>ParameterBinding(Select-Object): name="InputObject";
                           value="Microsoft.PowerShell.Commands.GenericMeasureInfo"

                           Count
                           -----
                               2

                           PS>CommandInvocation(Get-Command): "Get-Command"
                           >> ParameterBinding(Get-Command): name="Name"; value="Out-Default, Exit-PSSession"
                           >> ParameterBinding(Get-Command): name="CommandType"; value="Alias, Function, Filter, Cmdlet,
                           Configuration"
                           >> ParameterBinding(Get-Command): name="Module"; value=""
                           >> ParameterBinding(Get-Command): name="ListImported"; value="True"
                           >> CommandInvocation(Select-Object): "Select-Object"
                           >> ParameterBinding(Select-Object): name="Property"; value="Name, Namespace, HelpUri, CommandType,
                           ResolvedCommandName, OutputType, Parameters"
                           >> ParameterBinding(Select-Object): name="InputObject"; value="Out-Default"

                           Name                : Out-Default
                           Namespace           : Microsoft.PowerShell.Core
                           HelpUri             : https://go.microsoft.com/fwlink/?LinkID=113362
                           CommandType         : Cmdlet
                           ResolvedCommandName :
                           OutputType          : {}
                           Parameters          : {[Transcript, System.Management.Automation.ParameterMetadata], [InputObject,
                                                 System.Management.Automation.ParameterMetadata], [Verbose,
                                                 System.Management.Automation.ParameterMetadata], [Debug,
                                                 System.Management.Automation.ParameterMetadata]...}

                           >> ParameterBinding(Select-Object): name="InputObject"; value="Exit-PSSession"
                           Name                : Exit-PSSession
                           Namespace           :
                           HelpUri             :
                           CommandType         : Function
                           ResolvedCommandName :
                           OutputType          : {}
                           Parameters          : {}



                           PS>CommandInvocation(Get-Command): "Get-Command"
                           CommandInvocation(Get-HcsApplianceInfo): "Get-HcsApplianceInfo"

[10.126.76.20]: PS>
```
Esta es una salida de ejemplo de cuando se configuró la opción **Diagnostics and repair** (Diagnóstico y reparación) para el soporte técnico remoto. El valor de `AccessLevel` de la sesión de soporte técnico remoto es `ReadWrite`.

```output
[10.126.76.20]: PS>Get-HcsRemoteSupportSession -SessionId add360db-4593-4026-93d5-6d6d05d39046 -IncludeTranscriptContents $true

SessionId                : add360db-4593-4026-93d5-6d6d05d39046
RemoteApplication        : Powershell
AccessLevel              : ReadWrite
ControlSession           : False
SessionStartTime         : 8/26/2021 2:57:08 PM +00:00
SessionEndTime           :
SessionTranscriptPath    : C:\ProgramData\JEAConfiguration\Transcripts\RemoteRepair\PowerShell_transcript.HW4J1T2.ZroHb8Un.20210
                           826075705.txt
SessionTranscriptContent : **********************
                           Windows PowerShell transcript start
                           Start time: 20210826075705
                           Username: WORKGROUP\HW4J1T2$
                           RunAs User: WORKGROUP\SYSTEM
                           Configuration Name: RemoteRepair
                           Machine: HW4J1T2 (Microsoft Windows NT 10.0.17763.0)
                           Host Application: C:\Windows\system32\wsmprovhost.exe -Embedding
                           Process ID: 21832
                           PSVersion: 5.1.17763.10520
                           PSEdition: Desktop
                           PSCompatibleVersions: 1.0, 2.0, 3.0, 4.0, 5.0, 5.1.17763.10520
                           BuildVersion: 10.0.17763.10520
                           CLRVersion: 4.0.30319.42000
                           WSManStackVersion: 3.0
                           PSRemotingProtocolVersion: 2.3
                           SerializationVersion: 1.1.0.1
                           **********************
                           PS>CommandInvocation(Get-Command): "Get-Command"
                           >> ParameterBinding(Get-Command): name="Name"; value="Out-Default, Exit-PSSession"
                           >> ParameterBinding(Get-Command): name="CommandType"; value="Alias, Function, Filter, Cmdlet,
                           Configuration"
                           >> ParameterBinding(Get-Command): name="Module"; value=""
                           >> ParameterBinding(Get-Command): name="ListImported"; value="True"
                           >> ParameterBinding(Get-Command): name="ErrorAction"; value="SilentlyContinue"
                           >> CommandInvocation(Measure-Object): "Measure-Object"
                           >> CommandInvocation(Select-Object): "Select-Object"
                           >> ParameterBinding(Select-Object): name="Property"; value="Count"
                           >> ParameterBinding(Measure-Object): name="InputObject"; value="Out-Default"
                           >> ParameterBinding(Measure-Object): name="InputObject"; value="Exit-PSSession"
                           PS>ParameterBinding(Select-Object): name="InputObject";
                           value="Microsoft.PowerShell.Commands.GenericMeasureInfo"

                           Count
                           -----
                               2

                           PS>CommandInvocation(Get-Command): "Get-Command"
                           >> ParameterBinding(Get-Command): name="Name"; value="Out-Default, Exit-PSSession"
                           >> ParameterBinding(Get-Command): name="CommandType"; value="Alias, Function, Filter, Cmdlet,
                           Configuration"
                           >> ParameterBinding(Get-Command): name="Module"; value=""
                           >> ParameterBinding(Get-Command): name="ListImported"; value="True"
                           >> CommandInvocation(Select-Object): "Select-Object"
                           >> ParameterBinding(Select-Object): name="Property"; value="Name, Namespace, HelpUri, CommandType,
                           ResolvedCommandName, OutputType, Parameters"
                           >> ParameterBinding(Select-Object): name="InputObject"; value="Out-Default"

                           Name                : Out-Default
                           Namespace           : Microsoft.PowerShell.Core
                           HelpUri             : https://go.microsoft.com/fwlink/?LinkID=113362
                           CommandType         : Cmdlet
                           ResolvedCommandName :
                           OutputType          : {}
                           Parameters          : {[Transcript, System.Management.Automation.ParameterMetadata], [InputObject,
                                                 System.Management.Automation.ParameterMetadata], [Verbose,
                                                 System.Management.Automation.ParameterMetadata], [Debug,
                                                 System.Management.Automation.ParameterMetadata]...}

                           >> ParameterBinding(Select-Object): name="InputObject"; value="Exit-PSSession"
                           Name                : Exit-PSSession
                           Namespace           :
                           HelpUri             :
                           CommandType         : Function
                           ResolvedCommandName :
                           OutputType          : {}
                           Parameters          : {}

                           PS>CommandInvocation(Get-Command): "Get-Command"
[10.126.76.20]: PS>
```
 
### <a name="collect-remote-session-transcripts"></a>Recopilación de transcripciones de sesiones remotas

En función de los requisitos de auditoría, es posible que tenga que ver las transcripciones. Siga estos pasos para recopilar transcripciones de sesiones remotas:

1. En la interfaz de usuario local, vaya a **Solución de problemas > Soporte técnico**. Recopilación de paquetes de soporte técnico
1. Una vez recopilado el paquete de soporte técnico, descárguelo. Extraiga la carpeta comprimida. Las transcripciones se encuentran en la carpeta **SupportTranscripts** del paquete de soporte técnico. 

    ![Captura de pantalla de la carpeta SupportTranscripts resaltada en el paquete de soporte técnico](media/azure-stack-edge-gpu-remote-support-diagnostics-repair/transcript-folder-support-package-1.png)


 
## <a name="operations-allowed-in-remote-support"></a>Operaciones permitidas de soporte técnico remoto

En las secciones siguientes se enumeran los cmdlets que puede ejecutar el equipo de Soporte técnico de Microsoft durante una sesión de soporte técnico remoto. La disponibilidad del cmdlet se desglosa por el nivel de acceso establecido en **Diagnóstico** o **Diagnostics and repair** (Diagnóstico y reparación).

#### <a name="azure-stack-edge-cmdlets"></a>Cmdlets de Azure Stack Edge

| Cmdlets                      | Diagnóstico | Diagnóstico y reparación | Descripción |
|-----------------------------------------------|-------------|---------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Add-HcsExternalVirtualSwitch                  |             | Y                   | Crea un conmutador virtual externo para configurar Kubernetes en el dispositivo.  |
| Add-HcsVirtualNetwork                         |             | Y                   | Crea un conmutador virtual en una interfaz de red especificada.|
| Get-AcsHealthStatus                           | Y           | Y                   | Obtiene el estado de mantenimiento de los proveedores de servicios coherentes de Azure. |
| Get-AzureDataBoxEdgeRole                      | Y           | Y                   | Obtiene los registros de proceso a través de la interfaz de PowerShell si el rol de proceso está configurado en el dispositivo.|
| Get-HcsApplianceInfo                          | Y           | Y                   | Obtiene información del dispositivo, como el identificador, el nombre descriptivo, la versión de software o el estado del sistema.|
| Get-HcsApplianceSupportPackage                | Y           | Y                   | Recopila el paquete de soporte técnico del dispositivo.     |
| Get-HcsArpResponse                            | Y           | Y                   |   |
| Get-HcsControllerSetupInformation             | Y           | Y                   | Obtiene el objeto Microsoft.Hcs.Setup.ControllerInfo. |
| Get-HcsDataBoxAccount                         | Y           | Y                   ||
| Get-HcsExternalVirtualSwitch                  | Y           | Y                   | Obtiene el conmutador en el que Kubernetes debería estar configurado.   |
| Get-HcsGpuNvidiaSmi                           | Y           | Y                   | Obtiene la información del controlador de GPU del dispositivo.  |
| Get-HcsIPAddress                              | Y           | Y                   | Captura la configuración de adaptadores de red del almacén de datos o del sistema.   |
| Get-HcsIPAddressPool                          |             | Y                   | |
| Get-HcsKubeClusterInfo                        | Y           | Y                   | Obtiene información de configuración del clúster de Kubernetes.|
| Get-HcsKubeClusterNetworkInfo                 | Y           | Y                   | Obtiene las subredes de servicio y pod de Kubernetes.                                                                                                                                                   |
| Get-HcsKubernetesAzureMonitorConfiguration    | Y           | Y                   | Obtiene información sobre la ejecución de Azure Monitor en el clúster de Kubernetes del dispositivo.                                                                                                        |
| Get-HcsKubernetesContainerRegistryInfo        | Y           | Y                   | Obtiene los detalles del registro de contenedor perimetral del dispositivo.                                                                                                                               |
| Get-HcsKubernetesDashboardToken               | Y           | Y                   | Obtiene el token del panel de Kubernetes para ver el panel (puede hacer eso mismo a través de la interfaz de usuario local).                                                                                               |
| Get-HcsKubernetesNamespaces                   | Y           | Y                   | Obtiene el espacio de nombres de Kubernetes que ha configurado.  |
| Get-HcsKubernetesUserConfig                   | Y           | Y                   | Obtiene el parámetro `kubeconfig` correspondiente a un espacio de nombres específico que ha configurado.  |
| Get-HcsLocalWebUICertificateHash              | Y           | Y                   | Obtiene la huella digital del certificado de interfaz de usuario web local configurado. |
| Get-HcsMacAddressPool                         |             | Y                   | Obtiene el grupo de direcciones Mac de máquina virtual de Kubernetes.     |
| Get-HcsNetBmcInterface                        | Y           | Y                   | Obtiene las propiedades de configuración de red del controlador de administración de placa base (BMC), como la dirección IPv4, la puerta de enlace y la máscara de subred IPv4 y si DHCP está habilitado o no en el dispositivo. |
| Get-HcsNetInterface                           | Y           | Y                   | Captura la configuración de red deseada del dispositivo.    |
| Get-HcsNetRoute                               | Y           | Y                   | Comprueba todas las configuraciones de ruta personalizadas que agregó en el dispositivo. Estas rutas no incluyen todas las rutas del sistema ni las rutas predeterminadas que ya existen en el dispositivo.            |
| Get-HcsNodeSecureEraseLogs                    | Y           | Y                   | Recupera los registros que confirman que las unidades del dispositivo se borraron de forma segura durante el restablecimiento anterior a los valores predeterminados de fábrica.     |
| Get-HcsNodeSupportPackage                     | Y           | Y                   | Recopila registros del dispositivo y los copia en una red o un recurso compartido local especificados en forma de paquete de soporte técnico.   |
| Get-HcsRemoteSupportConsent                   | Y           | Y                   | Obtiene el consentimiento del cliente para permitir el soporte técnico remoto en el dispositivo.   |
| Get-HcsRestEndPoint                           | Y           | Y                   |  |
| Get-HcsSetupDesiredStateResult                | Y           | Y                   | Obtiene los objetos de resultado de Desired State Configuration (DSC) de algunas ejecuciones de DSC en el dispositivo. |
| Get-HcsSmbConfiguration                       | Y           | Y                   | <!-- to be removed--> |
| Get-HcsSupportedVpnRegions                    | Y           | Y                   |   |
| Get-HcsSupportPackageUploadJob                | Y           | Y                   | Obtiene el estado de un trabajo de carga de paquetes de soporte técnico que se está ejecutando.  |
| Get-HcsUpdateConfiguration                    | Y           | Y                   | Obtiene la configuración del servidor de actualización (como el tipo de servidor, la ruta de acceso del URI al servidor) realizada para el dispositivo de Azure Stack Edge.  |
| Get-HcsUpdateJob                              | Y           | Y                   | Captura todos los trabajos de actualización que se ejecutan en el dispositivo o el estado de un trabajo de actualización determinado cuando se pasa el identificador de trabajo. |
| Get-HcsVirtualNetwork                         |             | Y                   | Identifica la red virtual y la subred asociada a un conmutador que se creó en el dispositivo.   |
| Get-HcsVirtualSwitch                          | Y           | Y                   | Obtiene el conmutador virtual asociado a una interfaz de red especificada.  |
| Get-VMInGuestLogs                             |             | Y                   | Recopila los registros de invitado de las máquinas virtuales con errores en el dispositivo.  |
| Invoke-AzureDataBoxEdgeRoleReconcile          |             | Y                   | Se usa para actualizar la configuración del clúster de Kubernetes. |
| Invoke-AzureDataBoxEdgeRoleReconfigure        |             | Y                   | Se usa para cambiar la configuración del clúster de Kubernetes.  |
| Remove-HcsIPAddressPool                       |             |                     | Quita el grupo de direcciones Mac de la máquina virtual de Kubernetes. |
| Remove-HcsKubeClusterNetworkInfo              |             | Y                   | Cambia las subredes o pods del servicio Kubernetes.|
| Remove-HcsKubernetesAzureArcAgent             |             | Y                   | Quita el agente de Azure Arc del clúster de Kubernetes en el dispositivo.  |
| Remove-HcsKubernetesAzureMonitorConfiguration |             | Y                   | Quita Azure Monitor del clúster de Kubernetes en el dispositivo y le permite recopilar registros de contenedor y métricas de procesador del clúster de Kubernetes.  |
| Remove-HcsKubernetesContainerRegistry         |             | Y                   | Elimina el registro de contenedor perimetral del dispositivo.   |
| Remove-HcsKubernetesNamespace                 |             | Y                   | Elimina un espacio de nombres de Kubernetes especificado. |
| Remove-HcsMacAddressPool                      |             | Y                   | Quita el grupo de direcciones Mac de la máquina virtual de Kubernetes.|
| Remove-HcsNetRoute                            |             | Y                   | Quita una configuración de ruta que ha agregado en el dispositivo. |
| Remove-HcsVirtualNetwork                      |             | Y                   | Quita una red virtual y la subred asociada a un conmutador que se creó en el dispositivo. |
| Remove-HcsVirtualSwitch                       |             | Y                   | Quita un conmutador virtual asociado a un puerto en el dispositivo.  |
| Restart-HcsNode                               |             | Y                   | Reinicia un nodo del dispositivo. En el caso de un dispositivo de un solo nodo, se reinicia el dispositivo y se produce un tiempo de inactividad. |
| Set-AzureDataBoxEdgeRoleCompute               |             | Y                   | Se usa para configurar el proceso mediante Azure Portal y crea y configura el clúster de Kubernetes.   |
| Set-HcsCertificate                            |             | Y                   | Permite que traiga sus propios certificados. |
| Set-HcsClusterLevelSecurity                   |             | Y                   | Configura el nivel de seguridad del tráfico del clúster. Esto incluye el tráfico del clúster dentro del nodo y el tráfico procedente de volúmenes compartidos de clúster (CSV).    |
| Set-HcsClusterWitness                         |             | Y                   | Crea o configura un testigo de clúster de Windows. Un testigo de clúster ayuda a establecer un cuórum cuando uno de los nodos de un dispositivo de 2 nodos se vuelve inactivo.     |
| Set-HcsEastWestCsvEncryption                  |             | Y                   | <!--can be removed, confirmed with Vibha/Deepan. Talk to Phani. -->  |
| Set-HcsExternalVirtualSwitch                  |             | Y                   | Configura un conmutador virtual externo en el puerto para el que se ha habilitado el proceso en el dispositivo.  |
| Set-HcsIPAddress                              |             | Y                   | Actualiza las propiedades de la interfaz de red.                 |
| Set-HcsIPAddressPool                          |             |                     | Establece el grupo de direcciones Mac de la máquina virtual de Kubernetes.       |
| Set-HcsKubeClusterNetworkInfo                 |             | Y                   | Cambia las subredes de pod y servicio de Kubernetes antes de configurar el proceso desde Azure Portal en el dispositivo.                                                                                  |
| Set-HcsKubernetesAzureArcAgent                |             | Y                   | Habilita Azure Arc en el clúster de Kubernetes mediante la instalación del agente de Arc en el dispositivo. |
| Set-HcsKubernetesAzureArcDataController       |             | Y                   | Crea un controlador de datos en el clúster de Kubernetes en el dispositivo. |
| Set-HcsKubernetesAzureMonitorConfiguration    |             | Y                   | Habilita Azure Monitor en el clúster de Kubernetes en el dispositivo y permite recopilar registros de contenedor y métricas de procesador del clúster de Kubernetes.|
| Set-HcsKubernetesContainerRegistry            |             | Y                   | Habilita el registro de contenedor perimetral como un complemento para el dispositivo.                                                                                                                                  |
| Set-HcsMacAddressPool                         |             | Y                   | Establece el grupo de direcciones Mac de la máquina virtual de Kubernetes. |
| Set-HcsNetBmcInterface                        |             | Y                   | Habilita o deshabilita la configuración DHCP de BMC. |
| Set-HcsNetInterface                           |             | Y                   | Configura la dirección IP, la máscara de subred y la puerta de enlace de una interfaz de red en el dispositivo de Azure Stack Edge.|
| Set-HcsSmbServerEncryptionConfiguration       |             | Y                   | <!--to be removed, Ernie confirmed--> |
| Set-HcsSmbSigningConfiguration                |             | Y                   | <!--to be removed, Ernie confirmed--> |
| Set-HcsUpdateConfiguration                    |             | Y                   | Establece la configuración del servidor de actualización del dispositivo, incluidos el tipo de servidor y la ruta de acceso del URI al servidor. Puede optar por instalar actualizaciones desde un servidor de Microsoft Update o desde un servidor de Windows Server Update Services (WSUS).   |
| Set-HcsVirtualNetwork                         |             | Y                   |Crea una red virtual y la subred asociada a un conmutador que se creó en el dispositivo. |
| Set-HcsVpnS2SInterface                        |             | Y                   | <!--VPN should be P2S-->  |
| Start-HcsGpuMPS                               |             | Y                   | Habilita el servicio multiprocesador (MPS) en el dispositivo.|
| Start-HcsSupportPackageUploadJob              | Y           | Y                   | Recopila un paquete de soporte técnico con todos los registros y lo carga para que Microsoft pueda usarlo para depurar cualquier problema con el dispositivo.|
| Start-HcsUpdateJob                            |             | Y                   | Inicia el trabajo de actualización.|
| Stop-HcsGpuMPS                                |             | Y                   | Deshabilita el servicio multiprocesador (MPS) en el dispositivo. |
| Test-HcsKubernetesStatus                      | Y           | Y                   | Ejecuta el contenedor de diagnóstico de Kubernetes. |

#### <a name="generic-network-cmdlets"></a>Cmdlets de red genéricos

| Cmdlets   | Diagnóstico | Diagnóstico y reparación | Descripción                                                                                                                            |
|---------------------------|-------------|----------------------|----------------------------------------------------------------------------------------------------------------------------------------|
| Find-NetRoute             | Y           | Y                    | Busca la dirección IP local y la ruta mejores para llegar a una dirección remota.                                                          |
| Get-NetAdapter            | Y           | Y                    | Obtiene las propiedades básicas del adaptador de red.                                                                                             |
| Get-NetIPAddress          | Y           | Y                    | Obtiene toda la configuración de direcciones IP del equipo.                                                                             |
| Get-NetNat                | Y           | Y                    | Obtiene los objetos de traducción de direcciones de red (NAT) configurados en un equipo.                                                               |
| Get-NetNatExternalAddress | Y           | Y                    | Obtiene una lista de direcciones externas configuradas en una instancia de traducción de direcciones de red (NAT).                                          |
| Get-NetRoute              | Y           | Y                    | Obtiene la información de ruta IP de la tabla de enrutamiento IP, incluidos los prefijos de red de destino, las direcciones IP de próximo salto y las métricas de ruta. |
| Get-NetCompartment        | Y           | Y                    | Obtiene los compartimientos de red de la pila de protocolos.                                                                                        |
| Get-NetNeighbor           | Y           | Y                    | Obtiene las entradas de caché vecinas.                                                                                                           |
| Get-NetAdapterSriov       | Y           | Y                    | Obtiene las propiedades SR-IOV del adaptador de red.                                                                                     |
| Resolve-DnsName           |             | Y                    | Realiza una resolución de consultas de nombres DNS con respecto al nombre especificado.                                                                           |


####  <a name="multi-access-edge-computing-mec-cmdlets"></a>Cmdlets de Multi-Access Edge Computing (MEC)

| Cmdlets | Diagnóstico | Diagnóstico y reparación | Descripción                                                                                                       |
|-------------------------------------------|-------------|----------------------|-------------------------------------------------------------------------------------------------------------------|
| Get-MecVnf                                | Y           | Y                    | Obtiene una lista de todas las máquinas virtuales de función de red virtual de proceso perimetral de acceso múltiple implementadas en Azure Stack Edge.       |
| Get-MecVirtualMachine                     | Y           | Y                    | Obtiene una lista de máquinas virtuales creadas mediante funciones de red virtual de proceso perimetral de acceso múltiple .                    |
| Get-MecServiceConfig                      | Y           | Y                    | Obtiene la configuración del servicio de proceso perimetral de acceso múltiple que afecta al flujo de trabajo del ciclo de vida de las funciones de red virtual. |
| Get-MecInformation                        | Y           | Y                    | Obtiene información de proceso perimetral de acceso múltiple. Por ejemplo, si el dispositivo de Azure Stack Edge se ha registrado con Azure Network Function Manager.

####  <a name="general-purpose-os-cmdlets"></a>Cmdlets de sistema operativo de uso general

| Cmdlets | Diagnóstico | Diagnóstico y reparación | Descripción                                                                                                                  |
|----------------------------|-------------|----------------------|------------------------------------------------------------------------------------------------------------------------------|
| Select-String              | Y           | Y                    | Busca texto en cadenas y archivos.                                                                                             |
| Write-Progress             | Y           | Y                    | Muestra una barra de progreso dentro de una ventana de comandos de PowerShell.                                                                  |
| Get-Command                | Y           | Y                    | Obtiene una lista de comandos que se pueden ejecutar en una sesión.                                                                        |
| Measure-Object             | Y           | Y                    | Calcula las propiedades numéricas de objetos y los caracteres, palabras y líneas en objetos de cadena (como archivos de texto). |
| Select-Object              | Y           | Y                    | Selecciona objetos o propiedades de objeto.                                                                                        |
| Out-Default                |             | Y                    | Envía resultados al formateador predeterminado y al cmdlet de salida predeterminado.                                                  |
| Get-WinEvent               | Y           | Y                    | Obtiene eventos de registros de eventos y archivos de registro de seguimiento de eventos.                                                                     |
| Get-Counter                | Y           | Y                    | Obtiene datos del contador de rendimiento.                                                                                               |
| Get-Volume                 | Y           | Y                    | Obtiene el objeto de volumen especificado o todos los objetos de volumen si no se proporciona ningún filtro.                                            |
| Get-Service                | Y           | Y                    | Obtiene objetos que representan los servicios.                                                                                    |

#### <a name="cluster-cmdlets"></a>Cmdlets de clúster 

| Cmdlets       | Diagnóstico | Diagnóstico y reparación | Descripción                                                                            |
|-----------------------|-------------|----------------------|----------------------------------------------------------------------------------------|
| Get-ClusterResource   | Y           | Y                    | Obtiene información sobre uno o varios recursos de un clúster de conmutación por error.                    |
| Get-Cluster           | Y           | Y                    | Obtiene información sobre el clúster de conmutación por error.                                               |
| Get-ClusterNode       | Y           | Y                    | Obtiene información sobre uno o varios nodos, o servidores, de un clúster de conmutación por error.           |
| Start-Cluster         |             | Y                    | Inicia el servicio de clúster en todos los nodos del clúster en los que aún no se ha iniciado. |
| Start-ClusterResource |             | Y                    | Pone un recurso en línea en un clúster de conmutación por error.                                        |
| Stop-ClusterResource  |             | Y                    | Desconecta un recurso en un clúster de conmutación por error.                                        |


#### <a name="hyper-v-cmdlets"></a>Cmdlets de Hyper-V

| Cmdlets      | Diagnóstico | Diagnóstico y reparación | Descripción                                                                                                                                             |
|----------------------|-------------|----------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------|
| Get-Vm               | Y           | Y                    | Obtiene las máquinas virtuales del equipo.                                                                                                              |
| Get-VMNetworkAdapter | Y           | Y                    | Obtiene los adaptadores de red virtual de una máquina virtual, una instantánea o un sistema operativo de administración, o de una máquina virtual y un sistema operativo de administración. |
| Get-VMHardDiskDrive  | Y           | Y                    | Obtiene las unidades de disco duro virtual conectadas a una o más máquinas virtuales.                                                                             |
| Get-VMSwitch         | Y           | Y                    |  Obtiene la lista de conmutadores virtuales.                                                                                                                     |

## <a name="next-steps"></a>Pasos siguientes

[Póngase en contacto con Soporte técnico de Microsoft](azure-stack-edge-contact-microsoft-support.md).
