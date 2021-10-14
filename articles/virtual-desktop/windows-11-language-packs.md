---
title: 'Instalación de paquetes de idioma en máquinas virtuales con Windows 11 Enterprise en Azure Virtual Desktop: Azure'
description: Procedimiento para instalar paquetes de idioma para máquinas virtuales con Windows 11 Enterprise en Azure Virtual Desktop.
author: Heidilohr
ms.topic: how-to
ms.date: 10/04/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 365d2821ce0fec8fdf901ad4dc663e8325267803
ms.sourcegitcommit: c27f71f890ecba96b42d58604c556505897a34f3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/05/2021
ms.locfileid: "129536616"
---
# <a name="add-languages-to-a-windows-11-enterprise-image"></a>Adición de idiomas a una imagen de Windows 11 Enterprise

Es importante asegurarse de que los usuarios de su organización en cualquier parte del mundo puedan usar la implementación de Azure Virtual Desktop. Por eso puede personalizar la imagen de Windows 11 Enterprise que usa para que las máquinas virtuales (VM) tengan paquetes de idioma diferentes. A partir Windows 11 las cuentas de usuario que no son de administrador pueden agregar el idioma de visualización y sus características de idioma correspondientes. Esta característica significa que no necesitará instalar previamente paquetes de idioma para los usuarios de un grupo de hosts personal. En el caso de los grupos de hosts agrupados, se recomienda agregar a una imagen personalizada los idiomas que tiene pensado a agregar. Puede usar las instrucciones de este artículo para las versiones de sesión única y sesión múltiple de Windows 11 Enterprise.

Cuando su organización incluye usuarios con varios idiomas diferentes, tiene dos opciones:

- Crear un grupo de hosts dedicado con una imagen personalizada por idioma.
- Incluir en el mismo grupo de hosts a varios usuarios con distintos idiomas.

La segunda opción es más eficaz en lo que se refiere a recursos y costes, pero requiere algunos pasos adicionales. Este artículo le ayudará a crear una imagen que pueda adaptarse a usuarios de todos los idiomas y necesidades de localización.

## <a name="requirements"></a>Requisitos

Para poder agregar idiomas a una máquina virtual con Windows 11 Enterprise, deberá tener listo lo siguiente:

-   Una máquina virtual con Windows 11 Enterprise instalado
-   ISO de idioma y características opcionales (LoF). Puede descargar la ISO en [Windows 11 Language and Optional Features LoF ISO](https://software-download.microsoft.com/download/sg/22000.1.210604-1628.co_release_amd64fre_CLIENT_LOF_PACKAGES_OEM.iso) (ISO de idioma y características opcionales)
-   Un recurso compartido de Azure Files o un recurso compartido de archivos en una máquina virtual del servidor de archivos de Windows

>[!NOTE]
>El repositorio del recurso compartido de archivos debe ser accesible desde la máquina virtual de Azure que va a usar para crear la imagen personalizada.

## <a name="create-a-content-repository-for-language-packages-and-features-on-demand"></a>Creación de un repositorio de contenido para paquetes de idioma y características previa petición

Para crear el repositorio de contenidos que usará para agregar idiomas y características a la máquina virtual:

1. Abra la máquina virtual a la que quiere agregar idiomas en Azure.

2. Abra y monte el archivo ISO que descargó en [Requisitos](#requirements) en la máquina virtual.

3. Cree una carpeta en el recurso compartido de archivos.

4. Copie todo el contenido de la carpeta **LanguagesAndOptionalFeatures** de la ISO en la carpeta que ha creado.

     >[!NOTE]
     > Si está trabajando con almacenamiento limitado, puede usar la ISO "Languages and Optional Features" (Idiomas y características opcionales) montada como repositorio. Para obtener información sobre cómo crear un repositorio, consulte [Build a custom FOD and language pack repository](/windows-hardware/manufacture/desktop/languages-overview#build-a-custom-fod-and-language-pack-repository) (Compilación de un repositorio personalizado de paquetes de idioma y característica previa petición).

     >[!IMPORTANT]
     > Algunos idiomas requieren fuentes adicionales incluidas en los paquetes satélite que siguen convenciones de nomenclatura distintas. Por ejemplo, los nombres de archivo de las fuentes del japonés incluyen "Jpan".
     >
     > [!div class="mx-imgBorder"]
     > ![Un ejemplo de los paquetes de idioma del japonés con la etiqueta de idioma "Jpan" en el nombre de los archivos.](media/language-pack-example.png)

5. Establezca los permisos en el recurso compartido del repositorio de contenido de idioma para que tenga acceso de lectura desde la máquina virtual que usará para crear la imagen personalizada.

## <a name="create-a-custom-windows-11-enterprise-image-manually"></a>Creación manual de una imagen de Windows 11 Enterprise personalizada

Siga estos pasos para crear una imagen personalizada:

1. Implemente una máquina virtual de Azure y, luego, vaya a la galería de Azure y seleccione la versión actual de Windows 11 Enterprise que está usando.

2. Una vez que haya implementado la máquina virtual, conéctese a ella mediante RDP como administrador local.

3. Conéctese al repositorio de recurso compartido de archivos que ha creado en [Creación de un repositorio de contenido para paquetes de idioma y características previa petición](#create-a-content-repository-for-language-packages-and-features-on-demand) y móntelo en una unidad de letra (por ejemplo, unidad E).

4. Ejecute el siguiente script de PowerShell desde una sesión de PowerShell con privilegios elevados para instalar paquetes de idioma y paquetes satélite en Windows 11 Enterprise:
   
   ```powershell
   ########################################################
   ## Add Languages to running Windows Image for Capture##
   ########################################################
   ##Disable Language Pack Cleanup##
   Disable-ScheduledTask -TaskPath "\Microsoft\Windows\AppxDeploymentClient\" -TaskName "Pre-staged app cleanup"
   Disable-ScheduledTask -TaskPath "\Microsoft\Windows\MUI\" -TaskName "LPRemove"
   Disable-ScheduledTask -TaskPath "\Microsoft\Windows\LanguageComponentsInstaller" -TaskName "Uninstallation"
   reg add "HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Control Panel\International" /v "BlockCleanupOfUnusedPreinstalledLangPacks" /t REG_DWORD /d 1 /f

   ##Set Language Pack Content Stores##
   $LIPContent = "E:"

   ##Set Path of CSV File##
   $CSVFile = "Windows-10-1809-FOD-to-LP-Mapping-Table.csv"
   $filePath = (Get-Location).Path + "/$CSVFile"

   ##Import Necesarry CSV File##
   $FODList = Import-Csv -Path $filePath -Delimiter ";"

   ##Set Language (Target)##
   $targetLanguage = "es-es"

   $sourceLanguage = (($FODList | Where-Object {$_.'Target Lang' -eq $targetLanguage}) | Where-Object {$_.'Source Lang' -ne $targetLanguage} | Select-Object -Property 'Source Lang' -Unique).'Source Lang'
   if(!($sourceLanguage)){
       $sourceLanguage = $targetLanguage
   }

   $langGroup = (($FODList | Where-Object {$_.'Target Lang' -eq $targetLanguage}) | Where-Object {$_.'Lang Group:' -ne ""} | Select-Object -Property 'Lang Group:' -Unique).'Lang Group:'

   ##List of additional features to be installed##
   $additionalFODList = @(
       "$LIPContent\Microsoft-Windows-NetFx3-OnDemand-Package~31bf3856ad364e35~amd64~~.cab", 
       "$LIPContent\Microsoft-Windows-MSPaint-FoD-Package~31bf3856ad364e35~amd64~$sourceLanguage~.cab",
       "$LIPContent\Microsoft-Windows-SnippingTool-FoD-Package~31bf3856ad364e35~amd64~$sourceLanguage~.cab",
       "$LIPContent\Microsoft-Windows-Lip-Language_x64_$sourceLanguage.cab" ##only if applicable##
   )
   
   $additionalCapabilityList = @(
    "Language.Basic~~~$sourceLanguage~0.0.1.0",
    "Language.Handwriting~~~$sourceLanguage~0.0.1.0",
    "Language.OCR~~~$sourceLanguage~0.0.1.0",
    "Language.Speech~~~$sourceLanguage~0.0.1.0",
    "Language.TextToSpeech~~~$sourceLanguage~0.0.1.0"
    )

    ##Install all FODs or fonts from the CSV file###
    Dism /Online /Add-Package /PackagePath:$LIPContent\Microsoft-Windows-Client-Language-Pack_x64_$sourceLanguage.cab
    Dism /Online /Add-Package /PackagePath:$LIPContent\Microsoft-Windows-Lip-Language-Pack_x64_$sourceLanguage.cab
    foreach($capability in $additionalCapabilityList){
       Dism /Online /Add-Capability /CapabilityName:$capability /Source:$LIPContent
    }

    foreach($feature in $additionalFODList){
    Dism /Online /Add-Package /PackagePath:$feature
    }

    if($langGroup){
    Dism /Online /Add-Capability /CapabilityName:Language.Fonts.$langGroup~~~und-$langGroup~0.0.1.0 
    }

    ##Add installed language to language list##
    $LanguageList = Get-WinUserLanguageList
    $LanguageList.Add("$targetlanguage")
    Set-WinUserLanguageList $LanguageList -force
    ```

    >[!NOTE]
    >Este script de ejemplo usa el código de idioma español (es-es). Para instalar automáticamente los archivos adecuados para otro idioma, cambie el parámetro *$targetLanguage* al código de idioma correcto. Para obtener una lista de códigos de idioma, consulte [Available language packs for Windows](/windows-hardware/manufacture/desktop/available-language-packs-for-windows) (Paquetes de idioma disponibles en Windows).

    El script puede tardar unos minutos en completarse, en función del número de idiomas que necesite instalar. También puede instalar idiomas adicionales después de la instalación inicial ejecutando el script de nuevo con un parámetro *$targetLanguage* diferente.

5. Para seleccionar automáticamente los archivos de instalación adecuados, descargue y guarde la [tabla de idiomas y características a petición disponibles en Windows 10 1809](https://download.microsoft.com/download/7/6/0/7600F9DC-C296-4CF8-B92A-2D85BAFBD5D2/Windows-10-1809-FOD-to-LP-Mapping-Table.xlsx ) como un archivo CSV y guárdela en la misma carpeta que el script de PowerShell.

6. Una vez finalizada la ejecución del script, asegúrese de que los paquetes de idioma se hayan instalado correctamente; para ello, vaya a **Inicio** > **Configuración** > **Hora e idioma** > **Idioma**. Si los archivos de idioma se encuentran ahí, todo está listo.

7. Por último, si la máquina virtual está conectada a Internet durante la instalación de idiomas, deberá ejecutar un proceso de limpieza para quitar los paquetes de idioma que no sean necesarios. Para limpiar los archivos, ejecute estos comandos:

    ```powershell
    ##Cleanup to prepare sysprep##
    Remove-AppxPackage -Package Microsoft.LanguageExperiencePackes-ES_22000.8.13.0_neutral__8wekyb3d8bbwe

    Remove-AppxPackage -Package Microsoft.OneDriveSync_22000.8.13.0_neutral__8wekyb3d8bbwe
    ```

    Para limpiar distintos paquetes de idioma, reemplace "es-ES" por un código de idioma distinto.

8. Una vez que haya terminado con la limpieza, desconecte el recurso compartido. 

## <a name="finish-customizing-your-image"></a>Finalización de la personalización de la imagen

Después de instalar los paquetes de idioma, puede instalar cualquier otro software que quiera agregar a la imagen personalizada.

Una vez que haya terminado de personalizar la imagen, deberá ejecutar la herramienta de preparación del sistema (sysprep).

Para ejecutar sysprep, haga lo siguiente:

1. Abra un símbolo del sistema con privilegios elevados y ejecute el siguiente comando para generalizar la imagen:  
   
     ```cmd
     C:\Windows\System32\Sysprep\sysprep.exe /oobe /generalize /shutdown
     ```

2. Si tiene algún problema, compruebe el archivo **SetupErr.log** en la unidad C en **Windows** > **System32** > **Sysprep** > **Panther**. Después de eso, siga las instrucciones que encontrará en [Sysprep fails with Microsoft Store apps](/troubleshoot/windows-client/deployment/sysprep-fails-remove-or-update-store-apps) (Sysprep falla con las aplicaciones de Microsoft Store) para solucionar problemas de configuración.

3. Si la configuración es correcta, detenga la máquina virtual y captúrela en una imagen administrada siguiendo las instrucciones que se indican en [Creación de una imagen administrada de una máquina virtual generalizada en Azure](../virtual-machines/windows/capture-image-resource.md).

4. Ahora puede usar la imagen personalizada para implementar un grupo de hosts de Azure Virtual Desktop. Para aprender a implementar un grupo de hosts, consulte [Tutorial: Creación de un grupo de hosts con Azure Portal](create-host-pools-azure-marketplace.md).

>[!NOTE]
>Cuando un usuario cambia su idioma de visualización, debe cerrar sesión en Azure Virtual Desktop y, a continuación, volver a iniciar sesión. Debe cerrar sesión desde el menú Inicio.

## <a name="next-steps"></a>Pasos siguientes

Obtenga información sobre cómo instalar paquetes de idioma para máquinas virtuales multisesión con Windows 10 en [Adición de paquetes de idioma a una imagen multisesión de Windows 10](language-packs.md).

Para obtener una lista de problemas conocidos, consulte [Adding languages in Windows 10: Known issues](/windows-hardware/manufacture/desktop/language-packs-known-issue) (Adición de idiomas en Windows 10: problemas conocidos).
