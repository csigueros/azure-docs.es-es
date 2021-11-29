---
title: 'Configuración de Azure Virtual Desktop para Azure Stack HCI (versión preliminar): Azure'
description: Procedimiento para configurar Azure Virtual Desktop para Azure Stack HCI (versión preliminar).
author: Heidilohr
ms.topic: how-to
ms.date: 11/02/2021
ms.author: helohr
manager: femila
ms.custom: ignite-fall-2021
ms.openlocfilehash: ce89ec45e9c4f59107b205f04ce2c3d83afc8ce2
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/17/2021
ms.locfileid: "132704028"
---
# <a name="set-up-azure-virtual-desktop-for-azure-stack-hci-preview"></a>Configuración de Azure Virtual Desktop para Azure Stack HCI (versión preliminar)

> [!IMPORTANT]
> Azure Virtual Desktop para Azure Stack HCI está actualmente en versión preliminar.
> Consulte [Términos de uso complementarios para las versiones preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) para conocer los términos legales que se aplican a las características de Azure que se encuentran en la versión beta, en versión preliminar o que todavía no se han publicado para que estén disponibles con carácter general.

Con Azure Virtual Desktop para Azure Stack HCI (versión preliminar), puede usar hosts de sesión de Azure Virtual Desktop en la infraestructura local de Azure Stack HCI. Para más información, vea [Azure Virtual Desktop para Azure Stack HCI (versión preliminar)](azure-stack-hci-overview.md).

## <a name="requirements"></a>Requisitos

A fin de usar Azure Virtual Desktop para Azure Stack HCI, necesitará lo siguiente:

- Un [clúster de Azure Stack HCI registrado en Azure](/azure-stack/hci/deploy/register-with-azure).

- Una suscripción de Azure para la creación de grupos de hosts de sesión de Azure Virtual Desktop con todos los permisos de administrador necesarios.

- [Una instancia local de Active Directory (Azure AD) sincronizada con Azure Active Directory](/azure/architecture/reference-architectures/identity/azure-ad).

- Una conexión estable a Azure desde la red local.

- Acceso desde la red local a todas las direcciones URL necesarias enumeradas en la [lista de direcciones URL necesarias](safe-url-list.md) de Azure Virtual Desktop para máquinas virtuales.

## <a name="configure-azure-virtual-desktop-for-azure-stack-hci"></a>Configuración de Azure Virtual Desktop para Azure Stack HCI

Para configurar Azure Virtual Desktop para Azure Stack HCI:

1. Cree un grupo de hosts sin máquinas virtuales mediante las instrucciones de [Inicio del proceso de configuración del grupo de hosts](create-host-pools-azure-marketplace.md#begin-the-host-pool-setup-process). Al final de esa sección, vuelva a este artículo e inicie el paso 2.

2. Configure el grupo de hosts recién creado como grupo de hosts de validación siguiendo los pasos descritos en [Definición del grupo de hosts como grupo de hosts de validación](create-validation-host-pool.md#define-your-host-pool-as-a-validation-host-pool) para habilitar la propiedad de entorno de validación.

3. Siga las instrucciones de [Información del área de trabajo](create-host-pools-azure-marketplace.md#workspace-information) para crear un área de trabajo.

4. Implemente una nueva máquina virtual en la infraestructura de Azure Stack HCI mediante las instrucciones de [Creación de una máquina virtual](/azure-stack/hci/manage/vm#create-a-new-vm). Implemente una máquina virtual con un sistema operativo compatible y únala a un dominio.

   >[!NOTE]
   >Instale el rol de host de sesión de Escritorio remoto (RDSH) si en la máquina virtual se ejecuta un sistema operativo Windows Server.

5. Habilite Azure para administrar la nueva máquina virtual con Azure Arc mediante la instalación del agente de Connected Machine en ella. Siga las instrucciones de [Conexión de máquinas híbridas con servidores habilitados para Azure Arc](../azure-arc/servers/learn/quick-enable-hybrid-vm.md) a fin de instalar el agente Windows en la máquina virtual.

6. Agregue la máquina virtual al grupo de hosts de Azure Virtual Desktop que ha creado antes mediante la instalación del [agente de Azure Virtual Desktop](agent-overview.md). Después, siga las instrucciones de [Registro de las máquinas virtuales en el grupo de hosts de Azure Virtual Desktop](create-host-pools-powershell.md#register-the-virtual-machines-to-the-azure-virtual-desktop-host-pool) para registrar la máquina virtual en el servicio Azure Virtual Desktop.

7. Siga las instrucciones de [Creación de grupos de aplicaciones y administración de asignaciones de usuarios](manage-app-groups.md) a fin de crear un grupo de aplicaciones para probarlo y asignarle acceso de usuario.

8. Vaya al [cliente web](./user-documentation/connect-web.md) y conceda a los usuarios acceso a la nueva implementación.

## <a name="optional-configurations"></a>Configuraciones opcionales

Ahora que ha configurado Azure Virtual Desktop para Azure Stack HCI, estas son algunas tareas adicionales que puede realizar en función de las necesidades de la implementación.

### <a name="create-a-profile-container-using-a-file-share-on-azure-stack-hci"></a>Creación de un contenedor de perfiles mediante un recurso compartido de archivos en Azure Stack HCI

Para crear un contenedor de perfiles mediante un recurso compartido de archivos:

1. Implemente un recurso compartido de archivos en una implementación de máquina virtual de Windows Server única o agrupada. Las máquinas virtuales de Windows Server con el rol de servidor de archivos también se pueden colocar en el mismo clúster donde se implementan las máquinas virtuales del host de sesión.

2. Conéctese a la máquina virtual con las credenciales que proporcionó al crear la máquina virtual.

3. En la máquina virtual, inicie el **Panel de control** y seleccione **Sistema**.

4. Seleccione Nombre del equipo, luego **Cambiar configuración** y después **Cambiar…** .

5. Seleccione **Dominio** y, luego, escriba el dominio de Active Directory en la red virtual.

6. Autentíquese con una cuenta de dominio que tenga privilegios en máquinas unidas a dominio.

7. Siga las instrucciones de [Preparación de la máquina virtual para que actúe como un recurso compartido de archivos](create-host-pools-user-profile.md#prepare-the-virtual-machine-to-act-as-a-file-share-for-user-profiles) a fin de preparar la máquina virtual para la implementación.

8. Siga las instrucciones de [Configuración del contenedor de perfiles de FSLogix](create-host-pools-user-profile.md#configure-the-fslogix-profile-container) para configurar el contenedor de perfiles y usarlo.

### <a name="download-supported-os-images-from-azure-marketplace"></a>Descarga de imágenes de sistema operativo compatibles desde Azure Marketplace

Puede ejecutar cualquier imagen de sistema operativo que Azure Virtual Desktop y Azure Stack HCI admitan en la implementación. Para saber qué SO admite Azure Virtual Desktop, vea [Imágenes de sistema operativo de máquina virtual admitidas](overview.md#supported-virtual-machine-os-images).

Tiene dos opciones para descargar una imagen:

- Implemente una máquina virtual con la imagen de sistema operativo que prefiera y, después, siga las instrucciones de [Descarga de un disco duro virtual de Windows desde Azure](../virtual-machines/windows/download-vhd.md).
- Descargue un disco duro virtual (VHD) de Windows desde Azure sin implementar una máquina virtual.

La descarga de un disco duro virtual de Windows sin implementar una máquina virtual tiene varios pasos adicionales. Para descargar un disco duro virtual desde Azure sin implementar una máquina virtual, tendrá que completar las instrucciones de las secciones siguientes en orden.

### <a name="requirements-to-download-a-vhd-without-a-vm"></a>Requisitos para descargar un disco duro virtual sin una máquina virtual

Antes de empezar, asegúrese de que está conectado a Azure y de que ejecuta [Azure Cloud Shell](../cloud-shell/quickstart.md) en un símbolo del sistema o en el entorno Bash. También puede ejecutar comandos de referencia de la CLI en la interfaz de la línea de comandos (CLI) de Azure.

Si usa una instalación local, ejecute el comando [az login](/cli/azure/reference-index#az_login) para iniciar sesión en Azure.

Después, siga los mensajes que vea para terminar de iniciar sesión. Para ver otras opciones de inicio de sesión, consulte [Inicio de sesión con la CLI de Azure](/cli/azure/authenticate-azure-cli).

Si es la primera vez que usa la CLI de Azure, instale las extensiones necesarias mediante las instrucciones de [Uso de extensiones con la CLI de Azure](/cli/azure/azure-cli-extensions-overview).

Por último, ejecute el comando [az version](/cli/azure/reference-index?#az_version) para asegurarse de que el cliente está actualizado. Si no está actualizado, ejecute el comando [az upgrade](/cli/azure/reference-index?#az_upgrade) para actualizar a la versión más reciente.

### <a name="search-azure-marketplace-for-azure-virtual-desktop-images"></a>Búsqueda de imágenes de Azure Virtual Desktop en Azure Marketplace

Puede encontrar la imagen que busca mediante la función **Buscar** de Azure Marketplace en Azure Portal. A fin de buscar imágenes específicas para Azure Virtual Desktop, puede ejecutar una de las siguientes consultas de ejemplo.

Si busca una sesión múltiple de Windows 10, puede ejecutar una búsqueda con estos criterios:

```azure
az vm image list --all --publisher "microsoftwindowsdesktop" --offer "windows-10" --sku "21h1-evd-g2"
```

Este comando debe devolver el URN siguiente:

```azure
MicrosoftWindowsDesktop:Windows-10:21h1-evd-g2:latest
```

Si busca un centro de datos de Windows Server 2019, puede ejecutar los criterios siguientes en la CLI de Azure:

```azure
az vm image list --all --publisher "microsoftwindowsserver" --offer "WindowsServer" --sku "2019-Datacenter-gen2"
```

Este comando debe devolver el URN siguiente:

```azure
MicrosoftWindowsServer:windowsserver-gen2preview:2019-datacenter-gen2:latest
```

>[!IMPORTANT]
>Asegúrese de usar solo imágenes de segunda generación ("gen2"). Azure Virtual Desktop para Azure Stack HCI no admite la creación de una máquina virtual con una imagen de primera generación ("gen1"). Evite las SKU con un sufijo "-g1".

### <a name="create-a-new-azure-managed-disk-from-the-image"></a>Creación de un disco administrado de Azure a partir de la imagen

A continuación, tendrá que crear un disco administrado de Azure a partir de la imagen que ha descargado de Azure Marketplace.

Para crear un disco administrado de Azure:

1. Ejecute los comandos siguientes en un símbolo de la línea de comandos de Azure para establecer los parámetros del disco administrado. Asegúrese de reemplazar los elementos entre corchetes por los valores pertinentes para el escenario.

```azure
$urn = <URN of the Marketplace image> #Example: “MicrosoftWindowsServer:WindowsServer:2019-Datacenter:Latest”
$diskName = <disk name> #Name for new disk to be created
$diskRG = <resource group> #Resource group that contains the new disk
```

2. Ejecute estos comandos para crear el disco y generar una dirección URL de acceso SCSI conectado en serie (SAS).

```azure
az disk create -g $diskRG -n $diskName --image-reference $urn
$sas = az disk grant-access --duration-in-seconds 36000 --access-level Read --name $diskName --resource-group $diskRG
$diskAccessSAS = ($sas | ConvertFrom-Json)[0].accessSas
```

### <a name="export-a-vhd-from-the-managed-disk-to-azure-stack-hci-cluster"></a>Exportación de un disco duro virtual desde el disco administrado al clúster de Azure Stack HCI

Después, tendrá que exportar el disco duro virtual que ha creado desde el disco administrado al clúster de Azure Stack HCI, lo que le permitirá crear máquinas virtuales. Puede usar el método siguiente en un explorador web normal o en Explorador de Storage.

Para exportar el disco duro virtual:

1. Abra un explorador y vaya a la dirección URL de SAS del disco administrado que ha generado en [Creación de un disco administrado de Azure a partir de la imagen](#create-a-new-azure-managed-disk-from-the-image). En esta dirección URL puede descargar la imagen de VHD de la imagen que ha descargado desde Azure Marketplace.

2. Descargue la imagen de VHD. El proceso de descarga puede tardar varios minutos, por lo que debe tener paciencia. Asegúrese de que la imagen se ha descargado completamente antes de ir a la sección siguiente.

>[!NOTE]
>Si ejecuta azcopy, es posible que tenga que omitir md5check mediante la ejecución de este comando:
>
> ```azure
> azcopy copy “$sas" "destination_path_on_cluster" --check-md5 NoCheck
> ```

### <a name="clean-up-the-managed-disk"></a>Limpieza del disco administrado

Cuando haya terminado con el disco duro virtual, tendrá que liberar espacio mediante la eliminación del disco administrado.

Para eliminar el disco administrado que ha creado, ejecute estos comandos:

```azure
az disk revoke-access --name $diskName --resource-group $diskRG 
az disk delete --name $diskName --resource-group $diskRG --yes
```

Este comando puede tardar unos minutos en finalizar, así que tenga paciencia.

>[!NOTE]
>Opcionalmente, también puede convertir el disco duro virtual de descarga en un disco VHDx dinámico mediante la ejecución de este comando:
>
> ```powershell
> Convert-VHD -Path " destination_path_on_cluster\file_name.vhd" -DestinationPath " destination_path_on_cluster\file_name.vhdx" -VHDType Dynamic
> ```

## <a name="next-steps"></a>Pasos siguientes

Si tiene que refrescar la memoria sobre los conceptos básicos o la información de precios, vaya a [Azure Virtual Desktop para Azure Stack HCI](azure-stack-hci-overview.md).

Si tiene otras preguntas, consulte las [Preguntas más frecuentes](azure-stack-hci-faq.yml).
