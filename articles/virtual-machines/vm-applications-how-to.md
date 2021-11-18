---
title: Creación e implementación de paquetes de aplicación de máquina virtual (versión preliminar)
description: Aprenda a crear e implementar aplicaciones de máquina virtual mediante Azure Compute Gallery.
ms.service: virtual-machines
ms.subservice: shared-image-gallery
ms.topic: how-to
ms.workload: infrastructure
ms.date: 11/02/2021
ms.reviewer: amjads
ms.custom: ''
ms.openlocfilehash: b98d8da852ed280638af33c6e9c52e1ea6963fbb
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/17/2021
ms.locfileid: "132709033"
---
# <a name="create-and-deploy-vm-applications-preview"></a>Creación e implementación de aplicaciones de máquina virtual (versión preliminar)

Las aplicaciones de máquina virtual son un tipo de recurso de Azure Compute Gallery (anteriormente denominada Shared Image Gallery) que simplifica la administración, el uso compartido y la distribución global de aplicaciones para las máquinas virtuales.


> [!IMPORTANT]
> Las **aplicaciones de máquina virtual de Azure Compute Gallery** se encuentran actualmente en versión preliminar pública.
> Esta versión preliminar se ofrece sin un Acuerdo de Nivel de Servicio y no se recomienda para cargas de trabajo de producción. Es posible que algunas características no sean compatibles o que tengan sus funcionalidades limitadas. Para más información, consulte [Términos de uso complementarios de las Versiones Preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


## <a name="prerequisites"></a>Prerrequisitos

Antes de empezar, asegúrese de que dispone de lo siguiente:


En este artículo se da por supuesto que ya tiene una instancia de Azure Compute Gallery. Si todavía no tiene una galería, primero cree una. Para más información, vea [Creación de una galería para almacenar y compartir recursos](create-gallery.md).

Debe haber cargado la aplicación en un contenedor de una cuenta de almacenamiento de Azure. La aplicación se puede almacenar en un blob en bloques o un blob en páginas. Si decide usar un blob en páginas, debe alinear los archivos por bytes antes de cargarlos. Este es un ejemplo en el que se alineará por bytes el archivo:

```azurepowershell-interactive
$inputFile = <the file you want to pad>

$fileInfo = Get-Item -Path $inputFile

$remainder = $fileInfo.Length % 512

if ($remainder -ne 0){

    $difference = 512 - $remainder

    $bytesToPad = [System.Byte[]]::CreateInstance([System.Byte], $difference)

    Add-Content -Path $inputFile -Value $bytesToPad -Encoding Byte
    }
```

Debe asegurarse de que los archivos están disponibles públicamente o necesitará el URI de SAS para los archivos de la cuenta de almacenamiento. Puede usar [Explorador de Storage](../vs-azure-tools-storage-explorer-blobs.md) para crear rápidamente un URI de SAS si todavía no tiene uno.

## <a name="create-the-vm-application"></a>Creación de la aplicación de máquina virtual

Elija una de las opciones siguientes para crear la definición y la versión de la aplicación de máquina virtual:

### <a name="portal"></a>[Portal](#tab/portal)


1. Vaya a [Azure Portal](https://portal.azure.com) y busque y seleccione **Azure Compute Gallery**.
1. Seleccione la galería que desea usar en la lista.
1. En la página de la galería, seleccione **Agregar** en la parte superior de la página y, después, seleccione **VM image definition** (Definición de imagen de máquina virtual) en la lista desplegable. Se abrirá la página **Create a VM application definition** (Crear una definición de aplicación de máquina virtual).
1. En la pestaña **Datos básicos**, escriba un nombre para la aplicación y elija si es para máquinas virtuales que ejecutan Linux o Windows.
1. Seleccione la pestaña **Opciones de publicación** si quiere especificar cualquiera de las siguientes opciones para la definición de la aplicación de máquina virtual:
    - Descripción de la definición de la aplicación de máquina virtual.
    - Fecha final del ciclo de vida
    - Vínculo a los Términos de licencia
    - URI de una declaración de privacidad
    - URI de las notas de la versión
1. Cuando haya terminado, seleccione **Revisar y crear**.
1. Al finalizar la validación, seleccione **Crear** para implementar la definición.
1. Una vez finalizada la implementación, seleccione **Ir al recurso**.


Ahora puede crear una máquina virtual e implementar la aplicación de máquina virtual en ella mediante el portal. Simplemente cree la máquina virtual como de costumbre y, en la pestaña **Opciones avanzadas**, elija **Select a VM application to install** (Seleccionar una aplicación de máquina virtual para instalar).

:::image type="content" source="media/vmapps/advanced-tab.png" alt-text="Captura de pantalla de la pestaña Opciones avanzadas donde puede elegir instalar una aplicación de máquina virtual.":::

Seleccione la aplicación de máquina virtual en la lista y, después, seleccione **Guardar** en la parte inferior de la página.

:::image type="content" source="media/vmapps/select-app.png" alt-text="Captura de pantalla en la que se muestra la selección de una aplicación de máquina virtual para instalarla en la máquina virtual.":::

Si tiene más de una aplicación de máquina virtual para instalar, puede volver a establecer el orden de instalación de cada aplicación de máquina virtual en la **pestaña Opciones avanzadas**.

### <a name="cli"></a>[CLI](#tab/cli)

En las aplicaciones de máquina virtual se necesita la versión 2.30.0 o posterior de la [CLI de Azure](/cli/azure/install-azure-cli).

Cree la definición de la aplicación de máquina virtual mediante [az sig gallery-application create](/cli/azure/sig/gallery-application#az_sig_gallery_application_create). En este ejemplo se crea una definición de aplicación de máquina virtual denominada *myApp* para máquinas virtuales basadas en Linux.

```azurecli-interactive
az sig gallery-application create \
    --application-name myApp \
    --gallery-name myGallery \
    --resource-group myResourceGroup \
    --os-type Linux \
    --location "East US"
```

Cree una versión de la aplicación de máquina virtual mediante [az sig gallery-application version create](/cli/azure/sig/gallery-application/version#az_sig_gallery_application_version_create). Los caracteres permitidos para la versión son números y puntos. Los números deben estar dentro del rango de un entero de 32 bits. Formato: *VersiónPrincipal*.*VersiónSecundaria*.*Revisión*.

Reemplace los valores de los parámetros por los suyos propios.

```azurecli-interactive
az sig gallery-application version create \
   --version-name 1.0.0 \
   --application-name myApp \
   --gallery-name myGallery \
   --location "East US" \
   --resource-group myResourceGroup \
   --package-file-link "https://<storage account name>.blob.core.windows.net/<container name>/<filename>" \
   --install-command "mv myApp .\myApp\myApp" \
   --remove-command "rm .\myApp\myApp" \
   --update-command  "mv myApp .\myApp\myApp \
   --default-configuration-file-link "https://<storage account name>.blob.core.windows.net/<container name>/<filename>"\
```


### <a name="powershell"></a>[PowerShell](#tab/powershell)

Cree la definición de aplicación de máquina virtual mediante `New-AzGalleryApplication`. En este ejemplo, se crea una aplicación Linux denominada *myApp* en la instancia de Azure Compute Gallery *myGallery*, en el grupo de recursos *myGallery*, y se le asigna una breve descripción de la aplicación de máquina virtual para uso personal. Reemplace los valores según sea necesario.

```azurepowershell-interactive
$galleryName = myGallery
$rgName = myResourceGroup
$applicationName = myApp
New-AzGalleryApplication `
  -ResourceGroupName $rgName `
  -GalleryName $galleryName `
  -Name $applicationName `
  -SupportedOSType Linux `
  -Description "Backend Linux application for finance."
```

Cree una versión de la aplicación mediante `New-AzGalleryApplicationVersion`. Los caracteres permitidos para la versión son números y puntos. Los números deben estar dentro del rango de un entero de 32 bits. Formato: *VersiónPrincipal*.*VersiónSecundaria*.*Revisión*.

En este ejemplo, se crea el número de versión *1.0.0*. Reemplace los valores de las variables según sea necesario.

```azurepowershell-interactive
$version = 1.0.0
New-AzGalleryApplicationVersion `
   -ResourceGroupName $rgName `
   -GalleryName $galleryName `
   -GalleryApplicationName $applicationName `
   -Name $version `
   -PackageFileLink "https://<storage account name>.blob.core.windows.net/<containder name>/<filename>" `
   -Location "East US" `
   -Install myApp.exe /silent `
   -Remove myApp.exe /uninstall `
```


Para agregar la aplicación a una máquina virtual existente, obtenga la versión de la aplicación y úsela para obtener el identificador de versión de la aplicación de máquina virtual. Use el identificador para agregar la aplicación a la configuración de la máquina virtual.

```azurepowershell-interactive
$vm = Get-AzVM -ResourceGroupName $rgname -Name myVM
$vmapp = Get-AzGalleryApplicationVersion `
   -ResourceGroupName $rgname `
   -GalleryName $galleryname `
   -ApplicationName $applicationname `
   -Version $version

$vm = Add-AzVmGalleryApplication `
   -VM $vm `
   -Id $vmapp.Id

Update-AzVm -ResourceGroupName $rgname -VM $vm
```


### <a name="rest"></a>[REST](#tab/rest2)

Cree la definición de la aplicación.


```rest
PUT
/subscriptions/\<**subscriptionId**\>/resourceGroups/\<**resourceGroupName**\>/providers/Microsoft.Compute/galleries/\<**galleryName**\>/applications/\<**applicationName**\>?api-version=2019-03-01

{
    "location": "West US",
    "name": "myApp",
    "properties": {
        "supportedOSType": "Windows | Linux",
        "endOfLifeDate": "2020-01-01"
    }
}

```

| Nombre del campo | Descripción | Limitaciones |
|--|--|--|
| name | Nombre único para la aplicación de máquina virtual dentro de la galería | La longitud máxima es de 117 caracteres. Los caracteres permitidos son letras mayúsculas o minúsculas, números, guiones (-), puntos (.) y guiones bajos (_). No se permite que los nombres finalicen con un punto (.). |
| supportedOSType | Si se trata de una aplicación Windows o Linux | "Windows" o "Linux" |
| endOfLifeDate | Fecha de finalización futura de la aplicación. Tenga en cuenta que esto es solo para referencia y no se aplica. | Fecha futura válida |

Cree una versión de la aplicación de máquina virtual.

```rest
PUT
/subscriptions/\<**subscriptionId**\>/resourceGroups/\<**resourceGroupName**\>/providers/Microsoft.Compute/galleries/\<**galleryName**\>/applications/\<**applicationName**\>/versions/\<**versionName**\>?api-version=2019-03-01

{
  "location": "$location",
  "properties": {
    "publishingProfile": {
      "source": {
        "mediaLink": "$mediaLink",
        "defaultConfigurationLink": "$configLink"
      },
      "manageActions": {
        "install": "echo installed",
        "remove": "echo removed",
        "update": "echo update"
      },
      "targetRegions": [
        {
          "name": "$location1",
          "regionalReplicaCount": 1 
        },
        { "name": "$location1" }
      ]
    },
    "endofLifeDate": "datetime",
    "excludeFromLatest": "true | false"
  }
}

```

| Nombre del campo | Descripción | Limitaciones |
|--|--|--|
| ubicación | Ubicación de origen de la versión de la aplicación de máquina virtual | Región de Azure válida |
| mediaLink | Dirección URL que contiene el paquete de versión de la aplicación | Dirección URL de almacenamiento válida y existente |
| defaultConfigurationLink | Opcional. Dirección URL que contiene la configuración predeterminada, que se puede invalidar en el momento de la implementación. | Dirección URL de almacenamiento válida y existente |
| Instalar | Comando para instalar la aplicación | Comando válido para el sistema operativo indicado |
| Quitar | Comando para quitar la aplicación | Comando válido para el sistema operativo indicado |
| Actualizar | Opcional. Comando para actualizar la aplicación. Si no se especifica y se necesita una actualización, se quitará la versión anterior y se instalará la nueva. | Comando válido para el sistema operativo indicado |
| targetRegions/name | Nombre de una región en la que se va a replicar | Validación de la región de Azure |
| targetRegions/regionalReplicaCount | Opcional. Número de réplicas de la región que se van a crear. De manera predeterminada, su valor es 1. | Un entero comprendido entre 1 y 3, ambos incluidos |
| endOfLifeDate | Fecha de finalización futura de la versión de la aplicación. Tenga en cuenta que esto es solo para referencia del cliente y no se aplica. | Fecha futura válida |
| excludeFromLatest | Si se especifica, esta versión no se tendrá en cuenta para la versión más reciente. | Verdadero o falso |




Para agregar una versión de la aplicación de máquina virtual a una máquina virtual, realice una operación PUT en la máquina virtual.

```rest
PUT
/subscriptions/\<**subscriptionId**\>/resourceGroups/\<**resourceGroupName**\>/providers/Microsoft.Compute/virtualMachines/\<**VMName**\>?api-version=2019-03-01

{
  "properties": {
    "applicationProfile": {
      "galleryApplications": [
        {
          "order": 1,
          "packageReferenceId": "/subscriptions/{subscriptionId}/resourceGroups/<resource group>/providers/Microsoft.Compute/galleries/{gallery name}/applications/{application name}/versions/{version}",
          "configurationReference": "{path to configuration storage blob}"
        }
      ]
    }
  },
  "name": "{vm name}",
  "id": "/subscriptions/{subscriptionId}/resourceGroups/{resource group}/providers/Microsoft.Compute/virtualMachines/{vm name}",
  "location": "{vm location}"
}
```


Para aplicar la aplicación de máquina virtual a un conjunto de escalado uniforme:

```rest
PUT
/subscriptions/\<**subscriptionId**\>/resourceGroups/\<**resourceGroupName**\>/providers/Microsoft.Compute/
virtualMachineScaleSets/\<**VMSSName**\>?api-version=2019-03-01

{
  "properties": {
    "virtualMachineProfile": {
      "applicationProfile": {
        "galleryApplications": [
          {
            "order": 1,
            "packageReferenceId": "/subscriptions/{subscriptionId}/resourceGroups/<resource group>/providers/Microsoft.Compute/galleries/{gallery name}/applications/{application name}/versions/{version}",
            "configurationReference": "{path to configuration storage blob}"
          }
        ]
      }
    }
  },
  "name": "{vm name}",
  "id": "/subscriptions/{subscriptionId}/resourceGroups/{resource group}/providers/Microsoft.Compute/virtualMachines/{vm name}",
  "location": "{vm location}"
}
```


| Nombre del campo | Descripción | Limitaciones |
|--|--|--|
| orden | Opcional. Orden en el que se deben implementar las aplicaciones. Véase a continuación. | Validación de entero |
| packageReferenceId | Referencia a la versión de la aplicación de la galería | Referencia válida de la versión de la aplicación |
| configurationReference | Opcional. Dirección URL completa de un blob de almacenamiento que contiene la configuración de esta implementación. Esto invalidará cualquier valor proporcionado anteriormente para defaultConfiguration. | Referencia válida de blob de almacenamiento |

Se puede usar el campo order para especificar dependencias entre aplicaciones. Las reglas de orden son las siguientes:

| Caso | Significado de instalación | Significado del error |
|--|--|--|
| No se especifica ningún orden | Las aplicaciones sin ordenar se instalan después de las aplicaciones ordenadas. No hay ninguna garantía de orden de instalación entre las aplicaciones sin ordenar. | Los errores de instalación de otras aplicaciones, ya sean ordenadas o sin ordenar, no afectan a la instalación de las aplicaciones sin ordenar. |
| Valores de orden duplicados | La aplicación se instalará en cualquier orden en comparación con otras aplicaciones con el mismo orden. Todas las aplicaciones del mismo orden se instalarán después de las que tienen órdenes inferiores y antes de las que tienen órdenes superiores. | Si no se ha podido instalar una aplicación anterior con un orden inferior, no se instalará ninguna aplicación con este orden. Si no se ha podido instalar una aplicación con este orden, no se instalará ninguna aplicación con un orden superior. |
| Órdenes crecientes | Las aplicaciones se instalarán después de las que tienen órdenes inferiores y antes de las que de órdenes superiores. | Si no se puede instalar una aplicación anterior con un orden inferior, esta aplicación no se instalará. Si no se puede instalar esta aplicación, no se instalará ninguna aplicación con un orden superior. |

La respuesta incluirá el modelo de máquina virtual completo. A continuación se den los elementos pertinentes.

```rest
{
  "name": "{vm name}",
  "id": "{vm id}",
  "type": "Microsoft.Compute/virtualMachines",
  "location": "{vm location}",
  "properties": {
    "applicationProfile": {
      "galleryApplications": ""
    },
    "provisioningState": "Updating"
  },
  "resources": [
    {
      "name": "VMAppExtension",
      "id": "{extension id}",
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "location": "centraluseuap",
      "properties": "@{autoUpgradeMinorVersion=True; forceUpdateTag=7c4223fc-f4ea-4179-ada8-c8a85a1399f5; provisioningState=Creating; publisher=Microsoft.CPlat.Core; type=VMApplicationManagerLinux; typeHandlerVersion=1.0; settings=}"
    }
  ]
}

```

Si las aplicaciones de máquina virtual todavía no se han instalado en la máquina virtual, el valor estará vacío. 

---



## <a name="next-steps"></a>Pasos siguientes
Obtenga más información sobre las [aplicaciones de máquina virtual](vm-applications.md).