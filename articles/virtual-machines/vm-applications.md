---
title: Introducción a las aplicaciones de máquina virtual en Azure Compute Gallery (versión preliminar)
description: Aprenda más sobre los paquetes de aplicación de máquina virtual de una instancia de Azure Compute Gallery.
author: cynthn
ms.service: virtual-machines
ms.subservice: gallery
ms.topic: conceptual
ms.workload: infrastructure
ms.date: 11/02/2021
ms.author: cynthn
ms.reviewer: amjads
ms.custom: ''
ms.openlocfilehash: b2d5c3c1d204771b8380a06ac9c103d4473e0d8f
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/17/2021
ms.locfileid: "132708995"
---
# <a name="vm-applications-overview-preview"></a>Introducción a las aplicaciones de máquina virtual (versión preliminar)

Las aplicaciones de máquina virtual son un tipo de recurso de Azure Compute Gallery (anteriormente conocido como Shared Image Gallery) que simplifica la administración, el uso compartido y la distribución global de aplicaciones para las máquinas virtuales.

> [!IMPORTANT]
> Las **aplicaciones de máquina virtual de Azure Compute Gallery** se encuentran actualmente en versión preliminar pública.
> Esta versión preliminar se ofrece sin un Acuerdo de Nivel de Servicio y no se recomienda para cargas de trabajo de producción. Es posible que algunas características no sean compatibles o que tengan sus funcionalidades limitadas. Para más información, consulte [Términos de uso complementarios de las Versiones Preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


Aunque puede crear una imagen de una máquina virtual con aplicaciones preinstaladas, deberá actualizar la imagen cada vez que tenga cambios en la aplicación. Separar la instalación de la aplicación de las imágenes de máquina virtual significa que no es necesario publicar una nueva imagen con cada cambio de línea de código.

Los paquetes de aplicación proporcionan ventajas con respecto a otros métodos de implementación y empaquetado:

- Agrupación y control de versiones de los paquetes

- Las aplicaciones de máquina virtual se pueden replicar globalmente para estar más cerca de la infraestructura, por lo que no es necesario usar AzCopy u otros mecanismos de copia de almacenamiento para copiar los bits entre regiones de Azure.

- Uso compartido con otros usuarios mediante el control de acceso basado en rol (RBAC) de Azure

- Compatibilidad con máquinas virtuales y conjuntos de escalado flexibles y uniformes

- Si tiene ha aplicado reglas de grupo de seguridad de red (NSG) a la máquina virtual o al conjunto de escalado, puede que no sea posible descargar los paquetes de un repositorio de Internet. Además, con las cuentas de almacenamiento, para descargar paquetes en máquinas virtuales bloqueadas sería necesario configurar vínculos privados.
- Las aplicaciones de máquina virtual se pueden usar con la directiva [DeployIfNotExists](../governance/policy/concepts/effects.md).


## <a name="what-are-vm-app-packages"></a>¿Qué son los paquetes de aplicaciones de máquina virtual?

Los paquetes de aplicación de máquina virtual usan varios tipos de recursos:

| Resource | Descripción|
|----------|------------|
| **Azure Compute Gallery** | Una galería es un repositorio para administrar y compartir paquetes de aplicaciones. Los usuarios pueden compartir el recurso de la galería y todos los recursos secundarios se compartirán automáticamente. El nombre de la galería debe ser único en cada suscripción. Por ejemplo, puede tener una galería para almacenar todas las imágenes del sistema operativo y otra para almacenar todas las aplicaciones de máquina virtual.|
| **Aplicación de máquina virtual** | Esta es la definición de la aplicación de máquina virtual. Se trata de un recurso *lógico* que almacena los metadatos comunes de todas las versiones que hay en él. Por ejemplo, puede tener una definición de aplicación para Apache Tomcat y tener varias versiones dentro de ella. |
| **Versión de aplicación de máquina virtual** | Este es el recurso que se puede implementar. Puede replicar globalmente las versiones de la aplicación de máquina virtual en regiones de destino próximas a la infraestructura de la máquina virtual. La versión de aplicación de máquina virtual se debe replicar en una región para que se pueda implementar en una máquina virtual de esa región. |


## <a name="limitations"></a>Limitaciones

- **No más de tres réplicas por región**: al crear una versión de aplicación de máquina virtual, el número máximo de réplicas por región es tres. 

- **Reintento de instalaciones con errores**: actualmente, la única manera de reintentar una instalación con errores es quitar la aplicación del perfil y, luego, volver a agregarla.

- **Solo cinco aplicaciones por máquina virtual**: no se pueden implementar más de cinco aplicaciones en una máquina virtual en cualquier momento.

- **Tamaño de aplicación de 1 GB**: el tamaño máximo de archivo de una versión de aplicación es de 1 GB. 

- **Sin garantías de reinicios en el script**: si el script requiere un reinicio, se recomienda colocar la aplicación en último lugar durante la implementación. Aunque el código intenta controlar los reinicios, se puede producir un error.

- **Se requiere un agente de máquina virtual**: el agente de máquina virtual debe existir en la máquina virtual y ser capaz de recibir los estados objetivo.

- **Varias versiones de la misma aplicación en la misma máquina virtual**: no puede tener varias versiones de la misma aplicación en una máquina virtual.


## <a name="cost"></a>Coste

No hay ningún cargo adicional por el uso de paquetes de aplicación de máquina virtual, pero se le cobrará por los siguientes recursos:

- Costos de almacenamiento de cada paquete y de las réplicas. 
- Cargos de salida de red para la replicación de la primera versión de la imagen desde la región de origen a las regiones replicadas. Las réplicas subsiguientes se tratan dentro de la región, por lo que no habrá ningún cargo adicional. 

Para más información sobre la salida de la red, consulte [Detalles de precios de ancho de banda](https://azure.microsoft.com/pricing/details/bandwidth/).


## <a name="vm-applications"></a>Aplicaciones de máquina virtual

El recurso de aplicación de máquina virtual define lo siguiente sobre la aplicación de máquina virtual:

- La instancia de Azure Compute Gallery donde se almacena la aplicación de máquina virtual
- Nombre de la aplicación
- Tipo de sistema operativo compatible, como Linux o Windows
- Descripción de la aplicación de máquina virtual

## <a name="vm-application-versions"></a>Versiones de aplicación de máquina virtual

Las versiones de la aplicación de máquina virtual son el recurso que se puede implementar. Las versiones se definen con las siguientes propiedades:
- Número de la versión
- Vínculo al archivo de paquete de aplicación en una cuenta de almacenamiento
- Cadena de instalación de la aplicación
- Cadena de eliminación para mostrar cómo quitar correctamente la aplicación
- Nombre de archivo del paquete que se usará cuando se descargue en la máquina virtual
- Nombre del archivo de configuración que se usará para configurar la aplicación en la máquina virtual
- Vínculo al archivo de configuración de la aplicación de máquina virtual
- Cadena de actualización para saber cómo actualizar la aplicación de máquina virtual a una versión más reciente
- Fecha final del ciclo de vida. Las fechas de fin de ciclo de vida son informativas; podrá seguir implementando versiones de aplicación de máquina virtual pasada la fecha de fin de ciclo de vida.
- Excluir de la versión más reciente. Puede evitar que se use una versión como la versión más reciente de la aplicación. 
- Regiones de destino de la replicación
- Recuento de réplicas por región

## <a name="download-directory"></a>Directorio de descarga 
 
La ubicación de descarga del paquete de aplicación y los archivos de configuración son:
  
- Linux: `/var/lib/waagent/Microsoft.CPlat.Core.VMApplicationManagerLinux/<appname>/<app version> `
- Windows: `C:\Packages\Plugins\Microsoft.CPlat.Core.VMApplicationManagerWindows\1.0.4\Downloads\<appname>\<app version> `


Los comandos de instalación, actualización y eliminación deben escribirse suponiendo que el paquete de aplicación y el archivo de configuración se encuentran en el directorio actual.

## <a name="file-naming"></a>Nomenclatura de los archivos

Durante la versión preliminar, cuando el archivo de aplicación se descarga en la máquina virtual, el nombre de archivo es el mismo que el que se usa al crear la aplicación de máquina virtual. Por ejemplo, si asigno a la aplicación de máquina virtual el nombre `myApp`, el archivo que se descarga en la máquina virtual también se llamará `myApp`, con independencia del nombre de archivo que se utilice en la cuenta de almacenamiento. Si la aplicación de máquina virtual también tiene un archivo de configuración, ese archivo es el nombre de la aplicación con `_config` anexado. Si `myApp` tiene un archivo de configuración, se llamará `myApp_config`.

Por ejemplo, si asigno a la aplicación de máquina virtual el nombre `myApp` al crearla en la galería, pero se almacena como `myApplication.exe` en la cuenta de almacenamiento, cuando se descargue en la máquina virtual, el nombre de archivo será `myApp`. Mi cadena de instalación debería empezar por renombrar el archivo para que sea lo que tenga que ser para ejecutarse en la máquina virtual (como myApp.exe).

Los comandos de instalación, actualización y eliminación deben escribirse teniendo en cuenta esta circunstancia. 
 
## <a name="command-interpreter"></a>Intérprete de comandos  

Los intérpretes de comandos predeterminados son:
- Linux: `/bin/sh` 
- Windows: `cmd.exe`

Es posible usar un intérprete diferente, siempre y cuando esté instalado en el equipo, llamando al ejecutable y pasándole el comando. Por ejemplo, para que el comando se ejecute en PowerShell en Windows en lugar de en el símbolo del sistema, puede pasar `powershell.exe -Command '<powershell commmand>'`.
  

## <a name="how-updates-are-handled"></a>Control de las actualizaciones

Al actualizar una versión de la aplicación, se usará el comando de actualización que proporcionó durante la implementación. Si la versión actualizada no tiene un comando de actualización, se quitará la versión actual y se instalará la nueva versión. 

Los comandos de actualización deben escribirse con la expectativa de que se podrían estar actualizando desde cualquier versión anterior de la aplicación de máquina virtual.


## <a name="tips-for-creating-vm-applications-on-linux"></a>Sugerencias para crear aplicaciones de máquina virtual en Linux 

Las aplicaciones de terceros para Linux se pueden empaquetar de varias maneras. Vamos a examinar cómo controlar la creación de algunos de los comandos de instalación más comunes.

### <a name="tar-and-gz-files"></a>Archivos .tar y .gz 

Son archivos comprimidos y simplemente se pueden extraer en la ubicación deseada. Compruebe las instrucciones de instalación del paquete original en caso de que deban extraerse en una ubicación específica. Si el archivo .tar. o .gz contiene código fuente, consulte las instrucciones del paquete para ver cómo realizar la instalación desde dicho código. 

Ejemplo de comando de instalación para instalar `golang` en una máquina Linux:

```bash
tar -C /usr/local -xzf go_linux
```

Comando de eliminación de ejemplo:

```bash
rm -rf /usr/local/go
```

### <a name="deb-rpm-and-other-platform-specific-packages"></a>.deb, .rpm y otros paquetes específicos de la plataforma 
Puede descargar paquetes individuales de administradores de paquetes específicos de la plataforma, pero normalmente no contienen todas las dependencias. Con estos archivos, también debe incluir todas las dependencias del paquete de aplicación o indicar al administrador de paquetes del sistema que descargue las dependencias a través de los repositorios que están disponibles para la máquina virtual. Si trabaja con una máquina virtual con acceso restringido a Internet, debe empaquetar todas las dependencias por su cuenta.


Solucionar las dependencias puede ser un poco complicado. Existen herramientas de terceros que pueden mostrar todo el árbol de dependencias. 

En Ubuntu, puede ejecutar `apt-get install <name> --simulate` para mostrar todos los paquetes que se instalarán con el comando `apt-get install <name>`. Luego, puede usar esa salida para descargar todos los archivos .deb y crear un archivo que se pueda usar como paquete de aplicación. La desventaja de este método es que no se muestran las dependencias que ya están instaladas en la máquina virtual.
 
Por ejemplo, para crear un paquete de aplicación de máquina virtual para instalar PowerShell para Ubuntu, ejecute el comando `apt-get install powershell --simulate` en una nueva máquina virtual Ubuntu. Compruebe la salida de la línea **The following NEW packages will be installed** (Se instalarán los siguientes paquetes NUEVOS), que muestra los siguientes paquetes:
- `liblttng-ust-ctl4` 
- `liblttng-ust0` 
- `liburcu6` 
- `powershell`. 

Descargue estos archivos mediante `apt-get download` y cree un archivo .tar con todos los archivos en el nivel raíz. Este archivo .tar será el archivo de paquete de aplicación. En este caso, el comando de instalación es:

```bash
tar -xf powershell && dpkg -i ./liblttng-ust-ctl4_2.10.1-1_amd64.deb ./liburcu6_0.10.1-1ubuntu1_amd64.deb ./liblttng-ust0_2.10.1-1_amd64.deb ./powershell_7.1.2-1.ubuntu.18.04_amd64.deb
```

Y el comando de eliminación es:

```bash
dpkg -r powershell && apt autoremove
```

Use `apt autoremove` en lugar de intentar quitar explícitamente todas las dependencias. Es posible que haya instalado otras aplicaciones con dependencias superpuestas y, en ese caso, se producirá un error en un comando de eliminación explícito.


Si no quiere resolver las dependencias por su cuenta y apt/rpm puede conectarse a los repositorios, puede instalar una aplicación con un solo archivo .deb/.rpm y dejar que apt/rpm controle las dependencias.

Comando de instalación de ejemplo:

```bash
dpkg -i <appname> || apt --fix-broken install -y
```
 
## <a name="tips-for-creating-vm-applications-on-windows"></a>Sugerencias para crear aplicaciones de máquina virtual en Windows 

La mayoría de las aplicaciones de terceros en Windows están disponibles como instaladores .exe o .msi. Algunas también están disponibles como archivos ZIP de extracción y ejecución. Echemos un vistazo a los procedimientos recomendados para cada uno de ellos.


### <a name="exe-installer"></a>Instalador .exe 

Normalmente, los ejecutables del instalador inician una interfaz de usuario (UI) en la que alguien debe hacer clic. Si el instalador admite un parámetro de modo silencioso, debe incluirse en la cadena de instalación. 

Cmd.exe también espera que los archivos ejecutables tengan la extensión .exe, por lo que debe cambiar el nombre del archivo para que tenga esta extensión.  

Si quisiera crear un paquete de aplicación de máquina virtual para myApp.exe, que se distribuye como ejecutable, como mi aplicación de máquina virtual se denomina "myApp", escribo el comando suponiendo que el paquete de aplicación está en el directorio actual:

```
"move .\\myApp .\\myApp.exe & myApp.exe /S -config myApp_config" 
```
 
Si el archivo ejecutable del instalador no admite un parámetro de desinstalación, a veces se puede buscar en el Registro de una máquina de prueba para saber dónde se encuentra el desinstalador. 

En el Registro, la cadena de desinstalación se almacena en `Computer\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Uninstall\<installed application name>\UninstallString`, por lo que usaría el contenido como mi comando de eliminación:

```
'\"C:\\Program Files\\myApp\\uninstall\\helper.exe\" /S'
```

### <a name="msi-installer"></a>Instalador MSI 

Para la ejecución de la línea de comandos de los instaladores `.msi`, los comandos para instalar o quitar una aplicación deben usar `msiexec`. Normalmente, `msiexec` se ejecuta como su propio proceso independiente y `cmd` no espera a que se complete, lo que puede provocar problemas al instalar más de una aplicación de máquina virtual.  El comando `start` se puede usar con `msiexec` para asegurarse de que la instalación se completa antes de que el comando devuelva resultados. Por ejemplo:

```
start /wait %windir%\\system32\\msiexec.exe /i myapp /quiet /forcerestart /log myapp_install.log
```

Comando de eliminación de ejemplo:

```
start /wait %windir%\\system32\\msiexec.exe /x $appname /quiet /forcerestart /log ${appname}_uninstall.log
```

### <a name="zipped-files"></a>Archivos comprimidos 

En el caso de archivos ZIP u otros archivos comprimidos, descomprima el contenido del paquete de aplicación en el destino deseado. 

Comando de instalación de ejemplo:

```
mkdir C:\\myapp && powershell.exe -Command \"Expand-Archive -Path myapp -DestinationPath C:\\myapp\" 
```

Comando de eliminación de ejemplo:

```
rmdir /S /Q C:\\myapp
```


## <a name="troubleshooting-during-preview"></a>Solución de problemas durante la versión preliminar

Durante la versión preliminar, la extensión de aplicación de máquina virtual siempre devuelve un resultado correcto, independientemente de si alguna aplicación de máquina virtual no se pudo instalar, actualizar o quitar. La extensión de aplicación de máquina virtual solo informa del estado de la extensión como error cuando hay un problema con la extensión o con la infraestructura subyacente. Para saber si una aplicación de máquina virtual determinada se agregó correctamente a la instancia de máquina virtual, compruebe el mensaje de la extensión VMApplication.

Para más información sobre cómo obtener el estado de las extensiones de máquina virtual, consulte [Características y extensiones de máquina virtual para Windows](extensions/features-windows.md#view-extension-status).

Para obtener el estado de las extensiones de máquina virtual, use [Get-AzVM](/powershell/module/az.compute/get-azvm):

```azurepowershell-interactive
Get-AzVM -name <VMSS name> -ResourceGroupName <resource group name> -InstanceView | convertto-json  
```

Para obtener el estado de las extensiones de VMSS, use [Get-AzVMSS](/powershell/module/az.compute/get-azvmss):

```azurepowershell-interactive
Get-AzVmss -name <VMSS name> -ResourceGroupName <resource group name> -InstanceView | convertto-json  
```

## <a name="error-messages"></a>Mensajes de error

| Message | Descripción |
|--|--|
| La versión actual de la aplicación de máquina virtual {name} se dejó de usar el {date}. | Ha intentado implementar una versión de aplicación de máquina virtual que ya está en desuso. Pruebe a usar `latest` en lugar de especificar una versión concreta. |
| La versión actual de la aplicación de máquina virtual {name} admite el sistema operativo OS {OS}, pero el sistema operativo actual de OSDisk es {OS}. | Ha intentado implementar una aplicación de Linux en una instancia de Windows o viceversa. |
| Se ha superado el número máximo de aplicaciones de máquina virtual (max=5, current={count}). Use menos aplicaciones y vuelva a intentar la solicitud. | Actualmente solo se admiten cinco aplicaciones de máquina virtual por máquina virtual o VMSS. |
| Se especificó más de un elemento VMApplication con el mismo valor de packageReferenceId. | Se especificó la misma aplicación más de una vez. |
| Suscripción no autorizada para acceder a esta imagen. | La suscripción no tiene acceso a esta versión de la aplicación. |
| La cuenta de almacenamiento de los argumentos no existe. | No hay aplicaciones para esta suscripción. |
| La imagen de plataforma {image} no está disponible. Compruebe que todos los campos del perfil de almacenamiento son correctos. Para más detalles sobre la información del perfil de almacenamiento, consulte https://aka.ms/storageprofile. | La aplicación no existe. |
| La imagen de la galería {image} no está disponible en la región {region}. Póngase en contacto con el propietario de la imagen para replicarla en esta región o cambie la región solicitada. | La versión de la aplicación de la galería existe, pero no se replicó en esta región. |
| La SAS no es válida para el URI de origen {uri}. | Se recibió un error `Forbidden` del almacenamiento al intentar recuperar información sobre la dirección URL (mediaLink o defaultConfigurationLink). |
| El blob al que hace referencia el URI de origen {uri} no existe. | El blob proporcionado para las propiedades mediaLink o defaultConfigurationLink no existe. |
| No se puede acceder a la dirección URL de la versión de la aplicación de la galería {url} debido al siguiente error: no se encontró el nombre remoto. Asegúrese de que el blob existe y que es accesible públicamente o es una dirección URL de SAS con privilegios de lectura. | El caso más probable es que no se haya proporcionado un URI de SAS con privilegios de lectura. |
| No se puede acceder a la dirección URL de la versión de la aplicación de la galería {url} debido al siguiente error: {error description}. Asegúrese de que el blob existe y que es accesible públicamente o es una dirección URL de SAS con privilegios de lectura. | Hubo un problema con el blob de almacenamiento proporcionado. En la descripción del error se proporcionará más información. |
| No se permite la operación {operationName} en {application}, ya que está marcada para su eliminación. Solo puede volver a intentar realizar la operación de eliminación (o esperar hasta que se complete una en curso). | Intente actualizar una aplicación que se está eliminando actualmente. |
| El valor {value} del parámetro "galleryApplicationVersion.properties.publishingProfile.replicaCount" está fuera del intervalo. El valor debe encontrarse entre 1 y 3, ambos inclusive. | Solo se permiten entre 1 y 3 réplicas para las versiones de VMApplication. |
| No se permite cambiar la propiedad "galleryApplicationVersion.properties.publishingProfile.manageActions.install". (puede actualizarla o eliminarla) | No es posible cambiar ninguna de las acciones de administración en una instancia de VmApplication existente. Se debe crear otra versión de VmApplication. |
| No se permite cambiar la propiedad "galleryApplicationVersion.properties.publishingProfile.settings.packageFileName". (o configFileName) | No es posible cambiar ninguna de las opciones, como el nombre del archivo de paquete o el nombre del archivo de configuración. Se debe crear otra versión de VmApplication. |
| El blob al que hace referencia el URI de origen {uri} es demasiado grande: size = {size}. El tamaño máximo de blob permitido es "1 GB". | El tamaño máximo de un blob al que hace referencia mediaLink o defaultConfigurationLink es actualmente de 1 GB. |
| El blob al que hace referencia el URI de origen {uri} está vacío. | Se hizo referencia a un blob vacío. |
| El tipo de blob {type} no se admite para la operación {operation}. Solo se admiten blobs en páginas y blobs en bloques. | VmApplications solo admite blobs en páginas y blobs en bloques. |
| La SAS no es válida para el URI de origen {uri}. | El URI de SAS proporcionado para mediaLink o defaultConfigurationLink no es un URI de SAS válido. |
| No se puede especificar {region} en las regiones de destino porque falta la característica necesaria {featureName}. Registre la suscripción con la característica necesaria o quite la región de la lista de regiones de destino. | Para usar VmApplications en determinadas regiones restringidas, es necesario tener registrada la marca de características para esa suscripción. |
| Las regiones del perfil de publicación de la versión de imagen de la galería {regions} deben contener la ubicación de la versión de imagen {location}. | La lista de regiones para la replicación debe contener la ubicación donde se encuentra la versión de la aplicación. |
| No se permiten regiones duplicadas en las regiones de publicación de destino. | Es posible que las regiones de publicación no tengan duplicados. |
| Actualmente, los recursos de la versión de la aplicación de la galería no admiten el cifrado. | La propiedad de cifrado para las regiones de destino no se admite en las aplicaciones de máquina virtual |
| El nombre de entidad no coincide con el nombre de la dirección URL de la solicitud. | La versión de la aplicación de la galería especificada en la dirección URL de solicitud no coincide con la especificada en el cuerpo de la solicitud. |
| El nombre de la versión de la aplicación de la galería no es válido. El nombre de la versión de la aplicación debe seguir el formato Major(int32).Minor(int32).Patch(int32), donde int está entre 0 y 2 147 483 647 (ambos inclusive). Por ejemplo, 1.0.0, 2018.12.1, etc. | La versión de la aplicación de la galería debe seguir el formato especificado. |



## <a name="next-steps"></a>Pasos siguientes

- Descubra cómo [crear e implementar paquetes de aplicación de máquina virtual](vm-applications-how-to.md).