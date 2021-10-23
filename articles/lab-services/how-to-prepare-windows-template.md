---
title: Guía para configurar una máquina de plantillas de Windows | Microsoft Docs
description: Pasos genéricos para preparar una máquina de plantillas de Windows en Lab Services.  Estos pasos incluyen la configuración de la programación de Windows Update, la instalación de OneDrive y la instalación de Office.
author: EMaher
ms.topic: how-to
ms.date: 06/26/2020
ms.author: enewman
ms.openlocfilehash: 9b84823e356f59cda00b80052add584153a4aad6
ms.sourcegitcommit: 92889674b93087ab7d573622e9587d0937233aa2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/19/2021
ms.locfileid: "130180122"
---
# <a name="guide-to-setting-up-a-windows-template-machine-in-azure-lab-services"></a>Guía para configurar una máquina de plantillas de Windows en Azure Lab Services

Si va a configurar una máquina de plantilla con Windows 10 para Azure Lab Services, aquí encontrará algunos procedimientos recomendados y sugerencias para tener en cuenta. Los pasos de configuración siguientes son todos opcionales.  Sin embargo, estos pasos preparatorias pueden ayudar a que sus alumnos sean más productivos, a minimizar las interrupciones del tiempo de clase y a asegurar que usen las tecnologías más recientes.

>[!IMPORTANT]
>Este artículo contiene fragmentos de código de PowerShell para simplificar el proceso de modificación de plantillas de máquina.  Para todos los scripts de PowerShell que se muestran, querrá ejecutarlos en Windows PowerShell con privilegios de administrador. En Windows 10, una forma rápida de hacerlo es hacer clic con el botón derecho en el menú Inicio y elegir "Windows PowerShell (Administrador)".

## <a name="install-and-configure-onedrive"></a>Instalación y configuración de OneDrive

Para evitar que se pierdan los datos de los alumnos si se restablece una máquina virtual, se recomienda que los alumnos hagan una copia de seguridad de sus datos en la nube.  Microsoft OneDrive puede ayudar a los alumnos a proteger sus datos.  

### <a name="install-onedrive"></a>Instalación de OneDrive

Para descargar e instalar manualmente OneDrive, consulte las páginas de descarga [OneDrive](https://onedrive.live.com/about/download/) o [OneDrive para la Empresa](https://onedrive.live.com/about/business/).

También puede usar el script de PowerShell siguiente.  La versión más reciente de OneDrive se descargará e instalará automáticamente.  Una vez que el cliente de OneDrive esté instalado, ejecute el instalador.  En nuestro ejemplo, usamos el modificador `/allUsers` para instalar OneDrive para todos los usuarios de la máquina. También usamos el modificador `/silent` para instalar OneDrive de forma silenciosa.

```powershell
Write-Host "Downloading OneDrive Client..."
$DownloadPath = "$env:USERPROFILE/Downloads/OneDriveSetup.exe"
if((Test-Path $DownloadPath) -eq $False )
{
    Write-Host "Downloading OneDrive..."
    $web = new-object System.Net.WebClient
    $web.DownloadFile("https://go.microsoft.com/fwlink/p/?LinkId=248256",$DownloadPath)
} else {
    Write-Host "OneDrive installer already exists at " $DownloadPath
}

Write-Host "Installing OneDrive..."
& $env:USERPROFILE/Downloads/OneDriveSetup.exe /allUsers /silent
```

### <a name="onedrive-customizations"></a>Personalizaciones de OneDrive

Hay muchas [personalizaciones que se pueden aplicar en OneDrive](/onedrive/use-group-policy). Vamos a abordar algunas de las personalizaciones más comunes.

#### <a name="silently-move-windows-known-folders-to-onedrive"></a>Traslado silencioso de carpetas conocidas de Windows a OneDrive

Las carpetas como Documentos, Descargas e Imágenes se suelen usar para almacenar los archivos de los alumnos. Para asegurarse de que se haga una copia de seguridad de estas carpetas en OneDrive, se recomienda mover estas carpetas a OneDrive.

Si se encuentra en una máquina que no usa Active Directory, los usuarios pueden mover manualmente esas carpetas a OneDrive una vez que se autentiquen en OneDrive.

1. Abra el Explorador de archivos.
2. Haga clic con el botón derecho en la carpeta Documentos, Descargas o Imágenes.
3. Vaya a Propiedades > Ubicación.  Mueva la carpeta a una nueva carpeta en el directorio OneDrive.

Si la máquina virtual está conectada a Active Directory, puede establecer la máquina de plantillas para que solicite automáticamente a los alumnos que muevan las carpetas conocidas a OneDrive.  

Primero deberá recuperar el identificador de la organización.  Para más instrucciones, consulte [Búsqueda del ID. de la organización de Microsoft 365](/onedrive/find-your-office-365-tenant-id).  También puede obtener el identificador de la organización mediante el siguiente código de PowerShell.

```powershell
Install-Module MSOnline -Confirm
Connect-MsolService
$officeTenantID = Get-MSOLCompanyInformation |
    Select-Object -expand objectID |
    Select-Object -expand Guid
```

Una vez que tenga el identificador de la organización, establezca OneDrive para que pregunte si se deben mover las carpetas conocidas a OneDrive con el siguiente código de PowerShell.

```powershell
if ($officeTenantID -eq $null)
{
        Write-Error "Variable `$officeTenantId must be set to your Office Tenant Id before continuing."
}
New-Item -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive"
New-ItemProperty -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive"
    -Name "KFMSilentOptIn" -Value $officeTenantID -PropertyType STRING
```

### <a name="use-onedrive-files-on-demand"></a>Uso de archivos de OneDrive a petición

Los alumnos pueden tener muchos archivos en sus cuentas de OneDrive. Para ayudar a ahorrar espacio en la máquina y reducir el tiempo de descarga, se recomienda que todos los archivos almacenados en la cuenta de OneDrive del alumno estén disponibles a petición.  Los archivos a petición solo se descargan una vez que un usuario accede al archivo.

```powershell
New-Item -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive" -Force
New-ItemProperty -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive"
    -Name "FilesOnDemandEnabled" -Value "00000001" -PropertyType DWORD
```

### <a name="silently-sign-in-users-to-onedrive"></a>Inicio de sesión silencioso de usuarios en OneDrive

OneDrive puede establecerse para que inicie sesión automáticamente con las credenciales de Windows del usuario que ha iniciado sesión.  El inicio de sesión automático es útil para las clases en las que el alumno inicia sesión con sus credenciales de la cuenta educativa.

```powershell
New-Item -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive"
New-ItemProperty -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive"
    -Name "SilentAccountConfig" -Value "00000001" -PropertyType DWORD
```

### <a name="disable-the-tutorial-that-appears-at-the-end-of-onedrive-setup"></a>Deshabilitación del tutorial que aparece al final de la instalación de OneDrive

Esta configuración permite evitar que el tutorial se inicie en un explorador web al final de la instalación de OneDrive.

```powershell
New-Item -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive" -Force
New-ItemProperty -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive"
    -Name "DisableTutorial" -Value "00000001" -PropertyType DWORD -Force
```

### <a name="set-the-maximum-size-of-a-file-that-to-be-download-automatically"></a>Definición del tamaño máximo de un archivo que se va a descargar automáticamente

Esta configuración se usa junto con los usuarios que inician sesión de forma silenciosa en el cliente de sincronización de OneDrive con sus credenciales de Windows en dispositivos que no tienen habilitada la opción a petición de archivos de OneDrive. A todo usuario que tenga una instancia de OneDrive que sea mayor que el umbral especificado (en MB) se le pedirá que elija las carpetas que desea sincronizar antes de que el cliente de sincronización de OneDrive (OneDrive.exe) descargue los archivos.  En nuestro ejemplo, "1111-2222-3333-4444" es el identificador de la organización y 0005000 establece un umbral de 5 GB.

```powershell
New-Item -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive"
New-Item -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive\DiskSpaceCheckThresholdMB"
New-ItemProperty -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive\DiskSpaceCheckThresholdMB"
    -Name "1111-2222-3333-4444" -Value "0005000" -PropertyType DWORD
```

## <a name="install-and-configure-microsoft-365"></a>Instalación y configuración de Microsoft 365

### <a name="install-microsoft-365"></a>Instalación de Microsoft 365

Si la máquina de plantillas necesita Office, se recomienda la instalación de Office a través de la [Herramienta de implementación de Office (ODT)](https://www.microsoft.com/download/details.aspx?id=49117). Tendrá que crear un archivo de configuración reutilizable mediante [Apps Admin Center de Microsoft 365](https://config.office.com/) para elegir la arquitectura, las características que necesitará de Office y la frecuencia de actualización.

1. Vaya a [Apps Admin Center de Microsoft 365](https://config.office.com/) y descargue su propio archivo de configuración.
2. Descargue la [Herramienta de implementación de Office](https://www.microsoft.com/download/details.aspx?id=49117).  El archivo descargado será `setup.exe`.
3. Ejecute `setup.exe /download configuration.xml` para descargar los componentes de Office.
4. Ejecute `setup.exe /configure configuration.xml` para instalar los componentes de Office.

### <a name="change-the-microsoft-365-update-channel"></a>Cambio de canal de actualización de Microsoft 365

Con la Herramienta de configuración de Office, puede establecer la frecuencia con la que Office recibe actualizaciones. Sin embargo, si necesita modificar la frecuencia con la que Office recibe actualizaciones después de la instalación, puede cambiar la dirección URL del canal de actualización. Las direcciones URL del canal de actualización se encuentran en [Cambio del canal de actualización de Aplicaciones de Microsoft 365 en los dispositivos de la organización](/deployoffice/change-update-channels). En el ejemplo siguiente se muestra cómo configurar Microsoft 365 para usar el canal de actualización mensual.

```powershell
# Update to the Microsoft 365 Monthly Channel
Set-ItemProperty
    -Path "HKLM:\SOFTWARE\Microsoft\Office\ClickToRun\Configuration\CDNBaseUrl"
    -Name "CDNBaseUrl"
    -Value "http://officecdn.microsoft.com/pr/492350f6-3a01-4f97-b9c0-c7c6ddf67d60"
```

## <a name="install-and-configure-updates"></a>Instalación y configuración de actualizaciones

### <a name="install-the-latest-windows-updates"></a>Instalación de las actualizaciones de Windows más recientes

Se recomienda instalar las actualizaciones de Microsoft más recientes en la máquina de plantillas por motivos de seguridad antes de publicar la VM de plantillas.  También permite evitar que los alumnos se vean interrumpidos en su trabajo cuando las actualizaciones se ejecuten en momentos inesperados.

1. En el menú Inicio, inicie **Configuración**.
2. Haga clic en **Actualizar** y Seguridad.
3. Haga clic en **Buscar actualizaciones**.
4. Las actualizaciones se descargarán e instalarán.

También puede usar PowerShell para actualizar la máquina de plantillas.

```powershell
Set-ExecutionPolicy Bypass -Scope Process -Force
Install-PackageProvider -Name NuGet -MinimumVersion 2.8.5.201 -Confirm
Install-Module PSWindowsUpdate -Confirm
Install-WindowsUpdate -MicrosoftUpdate
Set-ExecutionPolicy default -Force
```

>[!NOTE]
>Algunas actualizaciones pueden requerir que se reinicie la máquina.  Se le solicitará si se requiere un reinicio.

### <a name="install-the-latest-updates-for-microsoft-store-apps"></a>Instalación de las actualizaciones más recientes para aplicaciones de Microsoft Store

Se recomienda que todas las aplicaciones de Microsoft Store se actualicen a sus versiones más recientes.  Estas son las instrucciones para actualizar manualmente las aplicaciones desde Microsoft Store.  

1. Inicie la aplicación de **Microsoft Store**.
2. Haga clic en los puntos suspensivos (…) junto a la foto de usuario en la esquina superior de la aplicación.
3. Seleccione **Descargar** y actualizaciones en el menú desplegable.
4. Haga clic en el botón **Obtener actualización**.

También puede usar PowerShell para actualizar las aplicaciones de Microsoft Store que ya están instaladas.

```powershell
(Get-WmiObject -Namespace "root\cimv2\mdm\dmmap" -Class "MDM_EnterpriseModernAppManagement_AppManagement01").UpdateScanMethod()
```

### <a name="stop-automatic-windows-updates"></a>Detener actualizaciones automáticas de Windows

Después de actualizar Windows a la versión más reciente, es posible que considere la posibilidad de detener las actualizaciones de Windows.  Las actualizaciones automáticas podrían interferir con el tiempo de clase programado.  Si el curso es de larga duración, piense en la posibilidad de solicitar a los alumnos que comprueben manualmente si hay actualizaciones o establezca las actualizaciones automáticas para una hora fuera del horario clase programado.  Para obtener más información sobre las opciones de personalización para Windows Update, consulte [Administrar la configuración de Windows Update adicional](/windows/deployment/update/waas-wu-settings).

Las actualizaciones automáticas de Windows se pueden detener con el siguiente script de PowerShell.

```powershell
New-Item -Path "HKLM:\SOFTWARE\Policies\Microsoft\Windows\AU"
New-ItemProperty -Path "HKLM:\SOFTWARE\Policies\Microsoft\Windows\AU"
    -Name "NoAutoUpdate" -Value "1" -PropertyType DWORD
```

## <a name="install-foreign-language-packs"></a>Instalación de paquetes de idioma extranjeros

Si necesita instalar idiomas adicionales en la máquina virtual, puede agregarlos a través de Microsoft Store.

1. Inicie Microsoft Store.
2. Busque "paquete de idioma".
3. Elegir el idioma que se va a instalar.

Si ya ha iniciado sesión en la VM de plantillas, use el acceso directo "instalar paquete de idioma" (`ms-settings:regionlanguage?activationSource=SMC-IA-4027670`) para ir directamente a la página de configuración adecuada.

## <a name="remove-unneeded-built-in-apps"></a>Quitar las aplicaciones integradas innecesarias

Windows 10 incluye muchas aplicaciones integradas que podrían no ser necesarias para su clase concreta. Para simplificar la imagen de la máquina para los alumnos, es posible que desee desinstalar algunas aplicaciones de la máquina de plantillas.  Para ver una lista de las aplicaciones instaladas, use el cmdlet `Get-AppxPackage` de PowerShell.  En el ejemplo siguiente se muestran todas las aplicaciones instaladas que se pueden quitar.

```powershell
Get-AppxPackage | Where {$_.NonRemovable -eq $false} | select Name
```

Para quitar una aplicación, use el cmdlet Remove-Appx.  En el ejemplo siguiente se muestra cómo quitar todo lo relacionado con XBox.

```powershell
Get-AppxPackage -Name *xbox* | foreach { if (-not $_.NonRemovable) { Remove-AppxPackage $_} }
```

## <a name="install-common-teaching-related-applications"></a>Instalación de aplicaciones comunes relacionadas con la enseñanza

Instale otras aplicaciones que se usan habitualmente para la enseñanza a través de la aplicación de Microsoft Store. Entre las sugerencias se incluyen aplicaciones como la [aplicación Microsoft Whiteboard](https://www.microsoft.com/store/productId/9MSPC6MP8FM4), [Microsoft Teams](https://www.microsoft.com/store/productId/9MSPC6MP8FM4) y [Minecraft Education Edition](https://education.minecraft.net/). Estas aplicaciones se deben instalar manualmente a través de Microsoft Store o a través de sus respectivos sitios web en la VM de plantillas.

## <a name="conclusion"></a>Conclusión

En este artículo se han mostrado pasos opcionales para preparar la VM de plantillas de Windows para una clase eficaz.  Los pasos incluyen la instalación de OneDrive y de Microsoft 365, de actualizaciones para Windows y de actualizaciones para aplicaciones de Microsoft Store.  También se ha explicado cómo establecer actualizaciones en un cronograma que funcione mejor para su clase.  

## <a name="next-steps"></a>Pasos siguientes
Consulte el artículo sobre cómo controlar el comportamiento de apagado de Windows para ayudar a administrar los costos: [Guía para controlar el comportamiento de apagado de Windows](how-to-windows-shutdown.md)