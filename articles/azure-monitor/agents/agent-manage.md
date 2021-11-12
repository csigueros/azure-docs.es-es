---
title: Administración del agente de Azure Log Analytics
description: En este artículo se describen las diferentes tareas de administración que normalmente realizará durante el ciclo de vida del agente de Windows o Linux para Log Analytics que está implementado en una máquina.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 06/14/2019
ms.openlocfilehash: 0fa173769e50b8d3d06199e166d2507281abc12e
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/22/2021
ms.locfileid: "130240174"
---
# <a name="managing-and-maintaining-the-log-analytics-agent-for-windows-and-linux"></a>Administrar y mantener el agente de Log Analytics para Windows y Linux

Después de la implementación inicial del agente de Windows o Linux para Log Analytics en Azure Monitor, debe volver a configurar el agente, actualizarlo o quitarlo del equipo si alcanzó la fase de retirada de su ciclo de vida. Puede administrar fácilmente estas tareas de mantenimiento rutinarias manualmente o de manera automática, lo que reduce los errores de funcionamiento y los gastos.

## <a name="upgrading-agent"></a>Actualizar el agente

El agente de Log Analytics para Windows y Linux puede actualizarse a la versión más reciente de forma manual o automáticamente según el escenario de implementación y la VM que se está ejecutando en el entorno. Los métodos siguientes pueden usarse para actualizar el agente.

| Entorno | Método de instalación | Método de actualización |
|--------|----------|-------------|
| Azure VM | Extensión de VM del agente de Log Analytics para Windows o Linux | El agente se actualiza automáticamente de forma predeterminada [después de que cambie el modelo de VM](../../virtual-machines/extensions/features-linux.md#how-do-agents-and-extensions-get-updated), a menos que configure la plantilla de Azure Resource Manager para no participar mediante el establecimiento de la propiedad *autoUpgradeMinorVersion* en **false**. |
| Imágenes personalizadas de VM de Azure | Instalación manual del agente de Log Analytics para Windows o Linux | La actualización de las VM a la versión más reciente del agente debe realizarse desde la línea de comandos que ejecuta el paquete del instalador de Windows o el paquete de scripts de shell instalable y autoextraíble de Linux.|
| VM ajenas a Azure | Instalación manual del agente de Log Analytics para Windows o Linux | La actualización de las VM a la versión más reciente del agente debe realizarse desde la línea de comandos que ejecuta el paquete del instalador de Windows o el paquete de scripts de shell instalable y autoextraíble de Linux. |

### <a name="upgrade-windows-agent"></a>Actualización del agente de Windows 

Para actualizar el agente de una máquina virtual Windows a la última versión que no se instaló con la extensión de máquina virtual de Log Analytics, realice el proceso desde el símbolo del sistema, un script u otra solución de automatización, o mediante el Asistente de instalación MMASetup-\<platform\>.msi.  

Puede descargar la versión más reciente del agente de Windows desde el área de trabajo de Log Analytics, realizando los pasos siguientes.

1. Inicie sesión en [Azure Portal](https://portal.azure.com).

2. En Azure Portal, haga clic en **Todos los servicios**. En la lista de recursos, escriba **Log Analytics**. Cuando comience a escribir, la lista se filtrará en función de la entrada. Seleccione **Áreas de trabajo de Log Analytics**.

3. En la lista de áreas de trabajo de Log Analytics, seleccione un área de trabajo.

4. En el área de trabajo de Log Analytics, seleccione **Configuración avanzada**, a continuación, seleccione **Orígenes conectados** y, finalmente, **Servidores Windows**.

5. En la página **Servidores de Windows**, seleccione la versión correcta en **Descargar el agente de Windows** que quiera descargar según la arquitectura del procesador del sistema operativo Windows.

>[!NOTE]
>Durante la actualización del agente de Log Analytics para Windows, no se admitirá la configuración o volver a configurar un área de trabajo para notificaciones. Para configurar el agente, deberá seguir uno de los métodos admitidos que se enumeran en [Agregar o quitar un área de trabajo](#adding-or-removing-a-workspace).
>

#### <a name="to-upgrade-using-the-setup-wizard"></a>Para actualizar mediante el Asistente de instalación

1. Inicie sesión en el equipo con una cuenta con derechos administrativos.

2. Ejecute **MMASetup-\<platform\>.exe** para iniciar el Asistente de instalación.

3. En la primera página del Asistente, haga clic en **Siguiente**.

4. En el cuadro de diálogo **Configuración de Microsoft Monitoring Agent**, haga clic en **Aceptar** para aceptar el contrato de licencia.

5. En el cuadro de diálogo **Configuración de Microsoft Monitoring Agent**, haga clic en **Actualizar**. La página de estado muestra el progreso de la actualización.

6. Cuando aparezca la página **Se completó correctamente la configuración de Microsoft Monitoring Agent**, haga clic en **Finalizar**.

#### <a name="to-upgrade-from-the-command-line"></a>Actualizar desde la línea de comandos

1. Inicie sesión en el equipo con una cuenta con derechos administrativos.

2. Para extraer los archivos de instalación del agente, desde un símbolo del sistema con privilegios elevados ejecute `MMASetup-<platform>.exe /c` y se le solicitará la ruta en la que extraer los archivos. Como alternativa, puede especificar la ruta de acceso pasando los argumentos `MMASetup-<platform>.exe /c /t:<Full Path>`.

3. Ejecute el siguiente comando, donde D:\ es la ubicación del archivo de registro de actualización.

    ```dos
    setup.exe /qn /l*v D:\logs\AgentUpgrade.log AcceptEndUserLicenseAgreement=1
    ```

### <a name="upgrade-linux-agent"></a>Actualización del agente de Linux 

Es posible actualizar desde versiones anteriores (> 1.0.0-47). Si realizas la instalación con el comando `--upgrade`, se actualizarán todos los componentes del agente a la versión más reciente.

Para actualizar al agente, ejecute el siguiente comando.

`sudo sh ./omsagent-*.universal.x64.sh --upgrade`

## <a name="adding-or-removing-a-workspace"></a>Adición o eliminación de un área de trabajo

### <a name="windows-agent"></a>Agente de Windows
Los pasos descritos en esta sección son necesarios cuando no solo quiere reconfigurar el agente de Windows para informar a un área de trabajo diferente o eliminar un área de trabajo de su configuración, sino también cuando quiere configurar el agente para que informe a más de un área de trabajo (comúnmente referido como hospedaje múltiple). La configuración del agente de Windows para informar a múltiples áreas de trabajo solo se puede realizar después de la configuración inicial del agente y mediante los métodos que se describen a continuación.    

#### <a name="update-settings-from-control-panel"></a>Actualizar la configuración del Panel de control

1. Inicie sesión en el equipo con una cuenta con derechos administrativos.

2. Abra el **Panel de control**.

3. Seleccione **Microsoft Monitoring Agent** y haga clic en la pestaña **Azure Log Analytics**.

4. Si quiere eliminar un área de trabajo, selecciónela y, después, haga clic en **Quitar**. Repita este paso para cualquier otra área de trabajo de la que quiera que el agente deje de informar.

5. Si agrega un área de trabajo, haga clic en **Agregar** y en el cuadro de diálogo **Agregar área de trabajo de Log Analytics**, pegue el identificador del área de trabajo y la clave del área de trabajo (clave principal). Si el equipo tiene que notificar a un área de trabajo de Log Analytics en Azure Government Cloud, seleccione Azure US Gov de la lista desplegable Azure Cloud.

6. Haga clic en **Aceptar** para guardar los cambios.

#### <a name="remove-a-workspace-using-powershell"></a>Quitar un área de trabajo con PowerShell

```powershell
$workspaceId = "<Your workspace Id>"
$mma = New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg'
$mma.RemoveCloudWorkspace($workspaceId)
$mma.ReloadConfiguration()
```

#### <a name="add-a-workspace-in-azure-commercial-using-powershell"></a>Agregar un área de trabajo en Azure Commercial con PowerShell

```powershell
$workspaceId = "<Your workspace Id>"
$workspaceKey = "<Your workspace Key>"
$mma = New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg'
$mma.AddCloudWorkspace($workspaceId, $workspaceKey)
$mma.ReloadConfiguration()
```

#### <a name="add-a-workspace-in-azure-for-us-government-using-powershell"></a>Agregar un área de trabajo en Azure for US Government con PowerShell

```powershell
$workspaceId = "<Your workspace Id>"
$workspaceKey = "<Your workspace Key>"
$mma = New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg'
$mma.AddCloudWorkspace($workspaceId, $workspaceKey, 1)
$mma.ReloadConfiguration()
```

>[!NOTE]
>Si ha usado la línea de comandos o el script anteriormente para instalar o configurar el agente, `EnableAzureOperationalInsights` se ha reemplazado por `AddCloudWorkspace` y `RemoveCloudWorkspace`.
>

### <a name="linux-agent"></a>Agente Linux
En los pasos siguientes se muestra cómo volver a configurar el agente de Linux si decide registrarlo en un área de trabajo diferente o si quiere quitar un área de trabajo de la configuración.

1. Para comprobar que está registrado en un área de trabajo, ejecute el siguiente comando:

    `/opt/microsoft/omsagent/bin/omsadmin.sh -l`

    Debe devolver un estado similar al del ejemplo siguiente:

    `Primary Workspace: <workspaceId>   Status: Onboarded(OMSAgent Running)`

    Es importante que el estado también muestre que el agente se está ejecutando, de lo contrario los siguientes pasos para volver a configurar el agente no se completarán correctamente.

2. Si ya está registrado en un área de trabajo, quite el área de trabajo registrada mediante al ejecución del siguiente comando. En caso contrario, si no está registrado, continúe con el paso siguiente.

    `/opt/microsoft/omsagent/bin/omsadmin.sh -X`

3. Para registrarse con otra área de trabajo, ejecute el comando siguiente:

    `/opt/microsoft/omsagent/bin/omsadmin.sh -w <workspace id> -s <shared key> [-d <top level domain>]`
    
4. Para comprobar que los cambios surten efecto, ejecute el siguiente comando:

    `/opt/microsoft/omsagent/bin/omsadmin.sh -l`

    Debe devolver un estado similar al del ejemplo siguiente:

    `Primary Workspace: <workspaceId>   Status: Onboarded(OMSAgent Running)`

El servicio del agente no tiene que reiniciarse para que los cambios surtan efecto.

## <a name="update-proxy-settings"></a>Actualizar la configuración de proxy
Si quiere configurar el agente para comunicarse con el servicio a través de un servidor proxy o la [puerta de enlace de Log Analytics](./gateway.md) después de la implementación, use uno de los métodos siguientes para completar esta tarea.

### <a name="windows-agent"></a>Agente de Windows

#### <a name="update-settings-using-control-panel"></a>Actualizar la configuración con Panel de control

1. Inicie sesión en el equipo con una cuenta con derechos administrativos.

2. Abra el **Panel de control**.

3. Seleccione **Microsoft Monitoring Agent** y haga clic en la pestaña **Configuración de proxy**.

4. Haga clic en **Usar un servidor proxy** y proporcione la dirección URL y el número de puerto del servidor proxy o puerta de enlace. Si el servidor proxy o la puerta de enlace de Log Analytics requiere autenticación, escriba el nombre de usuario y la contraseña para autenticarse y luego haga clic en **Aceptar**.

#### <a name="update-settings-using-powershell"></a>Actualizar la configuración con PowerShell

Copie el código de PowerShell de ejemplo siguiente, actualícelo con información específica de su entorno y guárdelo con una extensión de nombre de archivo PS1. Ejecute el script en cada uno de los equipos que se conecta directamente con el área de trabajo de Log Analytics en Azure Monitor.

```powershell
param($ProxyDomainName="https://proxy.contoso.com:30443", $cred=(Get-Credential))

# First we get the Health Service configuration object. We need to determine if we
#have the right update rollup with the API we need. If not, no need to run the rest of the script.
$healthServiceSettings = New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg'

$proxyMethod = $healthServiceSettings | Get-Member -Name 'SetProxyInfo'

if (!$proxyMethod)
{
    Write-Output 'Health Service proxy API not present, will not update settings.'
    return
}

Write-Output "Clearing proxy settings."
$healthServiceSettings.SetProxyInfo('', '', '')

$ProxyUserName = $cred.username

Write-Output "Setting proxy to $ProxyDomainName with proxy username $ProxyUserName."
$healthServiceSettings.SetProxyInfo($ProxyDomainName, $ProxyUserName, $cred.GetNetworkCredential().password)
```

### <a name="linux-agent"></a>Agente Linux
Siga estos pasos si los equipos Linux necesitan comunicarse a través de un servidor proxy o la puerta de enlace de Log Analytics. El valor de configuración de proxy tiene la siguiente sintaxis: `[protocol://][user:password@]proxyhost[:port]`. La propiedad *proxyhost* acepta un nombre de dominio completo o la dirección IP del servidor proxy.

1. Edite el archivo `/etc/opt/microsoft/omsagent/proxy.conf`. Para ello, ejecute los comandos siguientes y cambie los valores según su configuración específica.

    ```
    proxyconf="https://proxyuser:proxypassword@proxyserver01:30443"
    sudo echo $proxyconf >>/etc/opt/microsoft/omsagent/proxy.conf
    sudo chown omsagent:omiusers /etc/opt/microsoft/omsagent/proxy.conf
    ```

2. Ejecute el comando siguiente para reiniciar el agente:

    ```
    sudo /opt/microsoft/omsagent/bin/service_control restart [<workspace id>]
    ```

## <a name="uninstall-agent"></a>Desinstalar agente
Use uno de los procedimientos siguientes para desinstalar al agente de Windows o Linux mediante el asistente para instalación o la línea de comandos.

### <a name="windows-agent"></a>Agente de Windows

#### <a name="uninstall-from-control-panel"></a>Desinstalar desde el Panel de control
1. Inicie sesión en el equipo con una cuenta con derechos administrativos.

2. En **Panel de control**, haga clic en **Programas y características**.

3. En **Programas y características**, haga clic en **Microsoft Monitoring Agent**, haga clic en **Desinstalar** y, después, haga clic en **Sí**.

>[!NOTE]
>También se puede ejecutar el asistente para instalación del agente haciendo doble clic en **MMASetup-\<platform\>.exe**, que está disponible para su descarga desde un área de trabajo en Azure Portal.

#### <a name="uninstall-from-the-command-line"></a>Desinstalar desde la línea de comandos
El archivo descargado del agente es un paquete de instalación independiente creado con IExpress. El programa de instalación para el agente y los archivos auxiliares se encuentran en el paquete y deben extraerse para realizar la desinstalación correctamente con la línea de comandos que se muestra en el ejemplo siguiente.

1. Inicie sesión en el equipo con una cuenta con derechos administrativos.

2. Para extraer los archivos de instalación del agente, desde un símbolo del sistema con privilegios elevados ejecute `extract MMASetup-<platform>.exe` y se le solicitará la ruta en la que extraer los archivos. Como alternativa, puede especificar la ruta de acceso pasando los argumentos `extract MMASetup-<platform>.exe /c:<Path> /t:<Path>`. Para más información acerca de los modificadores de la línea de comandos compatibles con IExpress, consulte [Command-line switches for IExpress](https://www.betaarchive.com/wiki/index.php?title=Microsoft_KB_Archive/197147) (Modificadores de la línea de comandos para IExpress) y, después, actualice el ejemplo para adaptarlo a sus necesidades.

3. En el símbolo del sistema, escriba:`%WinDir%\System32\msiexec.exe /x <Path>:\MOMAgent.msi /qb`.

### <a name="linux-agent"></a>Agente Linux
Para quitar el agente, ejecute el siguiente comando en el equipo Linux. El argumento *--purge* quita completamente el agente y su configuración.

   `wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh --purge`

## <a name="configure-agent-to-report-to-an-operations-manager-management-group"></a>Configurar el agente para que envíe notificaciones a un grupo de administración de Operations Manager

### <a name="windows-agent"></a>Agente de Windows
Siga estos pasos para configurar al agente de Log Analytics para Windows para informar a un grupo de administración de System Center Operations Manager.

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

1. Inicie sesión en el equipo con una cuenta con derechos administrativos.

2. Abra el **Panel de control**.

3. Haga clic en **Microsoft Monitoring Agent** y, después, en la pestaña **Operations Manager**.

4. Si los servidores de Operations Manager tienen integración con Active Directory, haga clic en **Actualizar automáticamente asignaciones de grupos de administración desde AD DS**.

5. Haga clic en **Agregar** para abrir el cuadro de diálogo **Agregar un grupo de administración**.

6. En el campo **Nombre del grupo de administración**, especifique un nombre para el grupo de administración.

7. En el campo **Servidor de administración principal**, escriba el nombre de equipo del servidor de administración principal.

8. En el campo **Puerto de servidor de administración**, escriba el número de puerto TCP.

9. En **Cuenta de acción del agente**, seleccione la cuenta de sistema local o una cuenta de dominio local.

10. Haga clic en **Aceptar** para cerrar el cuadro de diálogo **Agregar un grupo de administración** y después haga clic en **Aceptar** para cerrar el cuadro de diálogo **Propiedades de Microsoft Monitoring Agent**.

### <a name="linux-agent"></a>Agente Linux
Siga estos pasos para configurar al agente de Log Analytics para Linux para informar a un grupo de administración de System Center Operations Manager.

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

1. Edite el archivo `/etc/opt/omi/conf/omiserver.conf`

2. Asegúrese de que la línea que comienza con `httpsport=` define el puerto 1270. Por ejemplo: `httpsport=1270`

3. Reinicie el servidor OMI: `sudo /opt/omi/bin/service_control restart`

## <a name="next-steps"></a>Pasos siguientes

- Consulte [Solución de problemas del agente Linux](agent-linux-troubleshoot.md) si encuentra incidencias durante la instalación o la administración del agente.

- Consulte [Solución de problemas del agente Windows](agent-windows-troubleshoot.md) si encuentra incidencias durante la instalación o la administración del agente.
