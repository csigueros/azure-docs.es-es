---
title: Configuración de un laboratorio con RStudio en Windows mediante Azure Lab Services
description: Aprenda a configurar laboratorios para enseñar el lenguaje R mediante RStudio en Windows.
author: emaher
ms.topic: article
ms.date: 08/26/2021
ms.author: enewman
ms.openlocfilehash: 5d081e21723fa73d84beec12e8602234ad5e8074
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124757389"
---
# <a name="set-up-a-lab-to-teach-r-on-windows"></a>Configuración de un laboratorio para enseñar R en Windows

[R](https://www.r-project.org/about.html) es un lenguaje de código abierto que se usa para la computación y los gráficos estadísticos.  Se utiliza en el análisis estadístico de la genética, en el procesamiento del lenguaje natural y en el análisis de datos financieros.  R proporciona una experiencia de [línea de comandos interactiva](https://cran.r-project.org/doc/manuals/r-release/R-intro.html#Invoking-R-from-the-command-line).  [RStudio](https://www.rstudio.com/products/rstudio/) es un entorno de desarrollo interactivo (IDE) disponible para el lenguaje R.  La versión gratuita proporciona herramientas de edición de código, una experiencia de depuración integrada y herramientas de desarrollo de paquetes.

Este artículo se centrará únicamente en RStudio y R como bloque de creación para una clase que requiere el uso de computación estadística.  Los tipos de clase [aprendizaje profundo](class-type-deep-learning-natural-language-processing.md) y [Python y Jupyter Notebooks](class-type-jupyter-notebook.md) configuran RStudio de forma diferente.  En cada artículo se describe cómo usar la imagen de Marketplace [Data Science Virtual Machine para Linux (Ubuntu)](https://azuremarketplace.microsoft.com/en-US/marketplace/apps/microsoft-dsvm.ubuntu-1804), que tiene instaladas muchas [herramientas relacionadas con la ciencia de datos](../machine-learning/data-science-virtual-machine/tools-included.md), incluido RStudio.  

## <a name="lab-account-configuration"></a>Configuración de la cuenta de laboratorio

Para configurar este laboratorio, para empezar, necesita una suscripción a Azure y una cuenta de laboratorio. Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/) antes de empezar. Una vez que obtenga una suscripción a Azure, puede crear una nueva cuenta de laboratorio en Azure Lab Services. Para más información sobre la creación de una nueva cuenta de laboratorio, consulte el tutorial sobre la [configuración de una cuenta de laboratorio](./tutorial-setup-lab-account.md). También puede usar una cuenta de laboratorio existente.

### <a name="lab-account-settings"></a>Configuración de la cuenta de laboratorio

Habilite la configuración de la cuenta de laboratorio tal y como se describe en la tabla siguiente.

| Configuración de la cuenta de laboratorio | Instructions |
| -------------------- | ----- |
| [Habilitar red virtual del mismo nivel](how-to-connect-peer-virtual-network.md) | Use esta opción en los siguientes casos:<ul><li>La clase requiere una instancia compartida de R Server.</li><li>La clase requiere archivos de datos grandes que desea almacenar externamente y no en la máquina virtual del alumno.</li></ul> |

> [!IMPORTANT]
> Si decide habilitar la red virtual del mismo nivel, debe hacerlo antes de crear el laboratorio.

## <a name="lab-configuration"></a>Configuración del laboratorio

Para obtener instrucciones sobre cómo crear un laboratorio y aplicar la configuración necesaria, consulte [Tutorial: Configuración de un laboratorio de clase](tutorial-setup-classroom-lab.md).  Al crear el laboratorio, aplique la siguiente configuración:

| Configuración del laboratorio | Valor y descripción |
| ------------ | ------------------ |
| Tamaño de la máquina virtual | GPU pequeña (proceso)|
| Imagen de la máquina virtual | Windows 10 Pro. Versión 2004 |

## <a name="external-resource-configuration"></a>Configuración de recursos externos

Algunas clases requieren que determinados archivos (por ejemplo, los de datos grandes) se almacenen externamente.  Para ver opciones e instrucciones de configuración, consulte [Uso de almacenamiento de archivos externo en Lab Services](how-to-attach-external-storage.md).

Si decide tener una instancia compartida de RStudio Server para los alumnos, se debe configurar el servidor antes de crear el laboratorio.  Para más información sobre cómo configurar un servidor compartido, consulte [cómo crear un laboratorio con un recurso compartido en Azure Lab Services](how-to-create-a-lab-with-shared-resource.md).  Para obtener instrucciones sobre cómo crear una instancia de RStudio Server, consulte [Descarga de RStudio Server para Debian y Ubuntu](https://www.rstudio.com/products/rstudio/download-server/debian-ubuntu/) y [Acceso a RStudio Server de código abierto](https://support.rstudio.com/hc/en-us/articles/200552306-Getting-Started).

## <a name="template-configuration"></a>Configuración de plantilla

Una vez creada la plantilla de máquina, inicie la máquina y conéctese a ella para [instalar R](https://docs.rstudio.com/resources/install-r/) y [RStudio Desktop](https://www.rstudio.com/products/rstudio/download/).  

### <a name="install-r"></a>Instalar R

1. Descargue el [instalador más reciente de R para Windows](https://cran.r-project.org/bin/windows/base/release.html).  Para obtener una lista completa de las versiones disponibles, consulte la [página de descarga de R para Windows](https://cran.r-project.org/bin/windows/base/).
2. Ejecute el instalador.
    1. En el símbolo del sistema **Seleccionar idioma de instalación**, elija el idioma preferido y seleccione **Aceptar**.
    2. En la página **Información** del instalador, lea el contrato de licencia.  Seleccione **Siguiente** para aceptar el contrato y continuar.
    3. En la página **Seleccionar ubicación de destino**, acepte la ubicación de instalación predeterminada y seleccione **Siguiente**.
    4. En la página **Seleccionar componentes**, desactive opcionalmente la opción **Archivos de 32 bits**.  Para más información sobre cómo ejecutar versiones de R de 32 y 62 bits, consulte la pregunta frecuente [¿Se puede instalar R de 32 y 64 bits en la misma máquina?](https://cran.r-project.org/bin/windows/base/rw-FAQ.html#Can-both-32_002d-and-64_002dbit-R-be-installed-on-the-same-machine_003f)
    5. En la página **Opciones de inicio**, deje las opciones de inicio como **No (accept defaults)** (No [aceptar valores predeterminados]).  Si quiere que la interfaz gráfica de usuario (GUI) de R use ventanas independientes (SDI) o ayuda de texto sin formato, elija el botón de radio **Yes (customize startup)** (Sí [personalizar inicio]) y cambie las opciones de inicio de las páginas siguientes a las páginas del asistente.
    6. En la página **Seleccionar carpeta del menú Inicio**, seleccione **Siguiente**.
    7. En la página **Seleccionar tareas adicionales**, active la casilla **Crear un acceso directo en el escritorio**.  Seleccione **Siguiente**.
    8. En la página **Instalando**, espere a que finalice la operación.
    9. En la página **Completing the R for Windows** (Finalizando R para Windows), haga clic en **Finalizar**.

También puede ejecutar la instalación de R mediante PowerShell.  En el ejemplo de código se muestra cómo instalar R sin el componente de 32 bits y se agrega un icono de escritorio de la versión más reciente de R. Para ver una lista completa de las opciones de línea de comandos del instalador, consulte [Configuración de los parámetros de la línea de comandos](https://jrsoftware.org/ishelp/index.php?topic=setupcmdline).

```powershell
#Avoid prompt to setup Internet Explorer if we must parse download page
Set-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Internet Explorer\Main" -Name "DisableFirstRunCustomize" -Value 2

$outputfile = "R-win.exe"

$result = Invoke-WebRequest "https://cran.r-project.org/bin/windows/base/release.html" -OutFile $outputfile -PassThru

#Check if we need to parse the result ourselves, to find the latest version of R
if ($result.StatusCode -eq '200' -and $result.Headers["Content-Type"] -eq 'text/html')
{
    $metaTag = $result.ParsedHtml.Head.children | Where-Object {$_.nodeName -eq 'META'}
    if ($metaTag.content  -match "R-\d+\.\d+\.\d+-win.exe"){
        $outputfile = $Matches.0

        #Download latest version
        Invoke-WebRequest "https://cran.r-project.org/bin/windows/base/$outputfile" -OutFile $outputfile
    }else{
        Write-Error "Unable to find latest version of R installer.  Go to https://cran.r-project.org/bin/windows/base/release.html to download manually."
    }
}

#Install Silently
$installPath = Get-Item -Path $outputfile
Start-Process -FilePath $installPath.FullName -ArgumentList "/VERYSILENT /LOG=r-install.log /NORESTART /COMPONENTS=""main,x64,translations"" /MERGETASKS=""desktopicon"" /LANG=""en""" -NoNewWindow -Wait
```

### <a name="install-rstudio"></a>Instalación de RStudio

Ahora que R está instalado localmente, podemos instalar el IDE de RStudio.  Vamos a instalar la versión gratuita de RStudio Desktop.  Para todas las versiones disponibles, consulte [Descargas de RStudio](https://www.rstudio.com/products/rstudio/download/).

1. Descargue el [instalador de R Studio](https://www.rstudio.com/products/rstudio/download/#download) para Windows 10.  El archivo del instalador tendrá el formato `rstudio-{version}.exe`.  
2. Ejecute el instalador de RStudio.
    1. En la página **Welcome to RStudio Setup** (Instalación de RStudio) del asistente para la **instalación de RStudio**, seleccione **Siguiente**.
    2. En la página **Choose Install Location** (Elegir ubicación de instalación), seleccione **Siguiente**.
    3. En la página **Choose Start Menu Folder** (Elegir la carpeta del menú Inicio), seleccione **Instalar**.
    4. En la página **Instalando**, espere a que finalice la operación.
    5. En la página **Completing RStudio Setup** (Finalizando la instalación de RStudio), seleccione **Finalizar**.

Para ejecutar los pasos de instalación de RStudio mediante PowerShell, ejecute los siguientes comandos.  Consulte [Descargas de RStudio](https://www.rstudio.com/products/rstudio/download/) para comprobar que la versión de RStudio está disponible antes de ejecutar los comandos.

```powershell
$rstudiover="1.4.1717"
$outputfile = "RStudio-$rstudiover.exe"

#Download installer executable
Invoke-WebRequest "https://download1.rstudio.org/desktop/windows/RStudio-$rstudiover.exe" -OutFile $outputfile

#Install RStudio silently
$installPath = Get-Item -Path $outputfile
Start-Process -FilePath $installPath.FullName -ArgumentList "/S" -NoNewWindow -Wait
```

### <a name="cran-packages"></a>Paquetes CRAN

Use el comando `install.packages(“package name”)` en una sesión interactiva de R como se muestra en el artículo [Lista rápida de paquetes de R útiles](https://support.rstudio.com/hc/articles/201057987-Quick-list-of-useful-R-packages).  También puede usar el elemento de menú Tools -> Install Packages (Instalar > Instalar paquetes) de RStudio.

Si necesita ayuda para encontrar un paquete, use la [lista de paquetes por tarea](https://cran.r-project.org/web/views/) o la [lista alfabética de paquetes](https://cloud.r-project.org/web/packages/available_packages_by_name.html).

## <a name="cost"></a>Coste

Veamos un ejemplo de una posible estimación de costos para esta clase.  Supongamos que tiene una clase de 25 alumnos. Cada alumno tiene 20 horas de clase programadas.  Se asignan otras 10 horas de cuota para deberes o asignaciones fuera de las horas de clase programadas para cada alumno.  El tamaño de la máquina virtual que hemos elegido es el de **GPU pequeña (proceso)** , que tiene 139 unidades de laboratorio.

25 alumnos &times; (20 horas programadas + 10 horas de cuota) &times; 139 unidades de laboratorio &times; 0,01 USD por hora = 1042,5 USD

> [!IMPORTANT]
> La estimación de costos solo se utiliza con fines de ejemplo.  Para obtener información sobre los precios actuales, consulte [Precios de Azure Lab Services](https://azure.microsoft.com/pricing/details/lab-services/).

## <a name="next-steps"></a>Pasos siguientes

Ahora se puede publicar la imagen de plantilla en el laboratorio. Consulte [Publicación de la plantilla de máquina virtual](how-to-create-manage-template.md#publish-the-template-vm) para obtener más instrucciones.

Cuando configure el laboratorio, consulte los siguientes artículos:

- [Incorporación de usuarios](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Establecimiento de cuotas](how-to-configure-student-usage.md#set-quotas-for-users)
- [Establecimiento de la programación](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [Vínculos de registro por correo electrónico para los alumnos](how-to-configure-student-usage.md#send-invitations-to-users)