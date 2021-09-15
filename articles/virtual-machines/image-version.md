---
title: Creación de una definición de imagen y una versión de imagen
description: Aprenda a crear una imagen en Shared Image Gallery en Azure.
author: cynthn
ms.service: virtual-machines
ms.subservice: shared-image-gallery
ms.topic: how-to
ms.workload: infrastructure
ms.date: 08/31/2021
ms.author: cynthn
ms.custom: ''
ms.openlocfilehash: f0ff3a0cc6bc228951fa47eb5723c520684d1dc5
ms.sourcegitcommit: 43dbb8a39d0febdd4aea3e8bfb41fa4700df3409
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/03/2021
ms.locfileid: "123452410"
---
# <a name="create-an-image-definition-and-an-image-version"></a>Creación de una definición de imagen y una versión de imagen 

Una [galería de imágenes compartidas](shared-image-galleries.md) simplifica el uso compartido de imágenes personalizadas en toda una organización. Las imágenes personalizadas son como las imágenes de Marketplace, pero las puede crear usted mismo. Las imágenes personalizadas se pueden usar para realizar tareas de implementación de arranque, como la carga previa de aplicaciones, configuraciones de aplicaciones y otras configuraciones del sistema operativo. 

La Galería de imágenes compartidas le permite compartir sus imágenes de máquina virtual personalizadas con otros usuarios de su organización, ya sea dentro o entre regiones, dentro de un inquilino de AAD. Elija las imágenes que desea compartir, qué regiones desea que estén disponibles en ellas y con quién desea compartirlas. Puede crear varias galerías que le permitirán agrupar lógicamente las imágenes compartidas. 

La característica de galería de imágenes compartidas tiene varios tipos de recursos:

[!INCLUDE [virtual-machines-shared-image-gallery-resources](./includes/virtual-machines-shared-image-gallery-resources.md)]


## <a name="before-you-begin"></a>Antes de empezar

Para completar este artículo, debe tener una instancia de Shared Image Gallery existente y un origen para su imagen disponible en Azure. Los orígenes de imagen pueden ser los siguientes:
- Una máquina virtual de la suscripción. Puede capturar una imagen a partir de máquinas virtuales [especializadas y generalizadas](shared-image-galleries.md#generalized-and-specialized-images). 
- Una imagen administrada.
- Discos de datos y sistema operativo administrados.
- Discos de datos y sistema operativo como discos duros virtuales en una cuenta de almacenamiento.
- Otras versiones de imagen en la misma galería u otra galería de la misma suscripción.

Si la imagen va a contener discos de datos, el tamaño del disco de datos no puede ser mayor de 1 TB.

Los nombres de las definiciones de imagen pueden estar formados por letras mayúsculas o minúsculas, números, puntos y guiones. Para más información sobre los valores que se pueden especificar para una definición de imagen, consulte [Definiciones de imagen](shared-image-galleries.md#image-definitions).

Los caracteres permitidos para la versión de la imagen son números y puntos. Los números deben estar dentro del rango de un entero de 32 bits. Formato: *VersiónPrincipal*.*VersiónSecundaria*.*Revisión*.

Al trabajar en este artículo, reemplace los nombres de los recursos y de la máquina virtual cuando proceda.

## <a name="create-an-image"></a>Crear una imagen 

Elija una opción a continuación para crear la definición y la versión de la imagen:

### <a name="portal"></a>[Portal](#tab/portal)

Para crear una imagen a partir de una máquina virtual en el portal, consulte [Captura de una imagen de una máquina virtual](capture-image-portal.md). 

Para crear una imagen con un origen distinto de una máquina virtual, siga estos pasos.

1. En [Azure Portal](https://portal.azure.com), busque y seleccione **Shared Image Gallery**.
1. Seleccione la galería que desea usar en la lista.
1. En la página de la galería de imágenes, seleccione **Add a new image definition** (Agregar una nueva definición de imagen) en la parte superior de la página. 
1. En la página **Agregar nueva definición de imagen a la galería de imágenes compartidas**, en la pestaña **Conceptos básicos**, seleccione una **Región**. 
1. En **Nombre de definición de la imagen**, escriba un nombre como *myImageDefinition*.
1. En **Sistema operativo**, seleccione la opción correcta en función de su origen.  
1. En **Generación de VM**, seleccione la opción en función de su origen. En la mayoría de los casos, será *Gen 1*. Para obtener más información, consulte [Compatibilidad para máquinas virtuales de generación 2 en Azure](generation-2.md).
1. En **Estado del sistema operativo**, seleccione la opción en función de su origen. Para más información, consulte [Generalizada o Especializada](shared-image-galleries.md#generalized-and-specialized-images).
1. En **Editor**, escriba un nombre único, como *miEditor*. 
1. En **Oferta**, escriba un nombre único como *miOferta*.
1. En **SKU**, escriba un nombre único como *miSKU*.
1. En la parte inferior de la página, seleccione **Revisar y crear**.
1. Después de que la definición de imagen pasa la validación, seleccione **Crear**.
1. Cuando la implementación finalice, seleccione **Ir al recurso**.
1. En la página de la definición de la imagen, en la pestaña **Introducción**, seleccione **Crear una versión**.
1. En **Región**, seleccione la región donde quiera que se cree la imagen. En algunos casos, el origen debe estar en la misma región en que se crea la imagen. Si no ve el origen en las lista desplegables posteriores, intente cambiar la región de la imagen. Siempre puede replicar la imagen en otras regiones más adelante.
1. En **Número de versión**, escriba un número como *1.0.0*. El nombre de la versión de la imagen debe seguir el formato *principal*. *secundaria*. *revisión* con números enteros. 
1. En **Origen**, seleccione el tipo de archivo que está usando como origen en la lista desplegable. Consulte la tabla siguiente para obtener detalles específicos de cada tipo de origen.

    | Origen | Otros campos |
    |---|---|
    | Discos o instantáneas | - En **Disco del SO**, seleccione el disco o la instantánea correspondiente en la lista desplegable. <br> - Para agregar un disco de datos, escriba el número de unidad lógica (LUN) y, a continuación, seleccione el disco de datos correspondiente en la lista desplegable. |
    | Versión de la imagen | - Seleccione la **galería de origen** en la lista desplegable. <br> - Seleccione la definición de imagen correcta en la lista desplegable. <br>- Seleccione la versión de la imagen existente que quiere usar en la lista desplegable. |
    | Imagen administrada | Seleccione la **imagen de origen** en la lista desplegable. <br>La imagen administrada debe estar en la misma región que eligió en **Detalles de la instancia**.
    | VHD en una cuenta de almacenamiento | Seleccione **Examinar** para elegir la cuenta de almacenamiento para el VHD. |

1. En **Excluir de la versión más reciente**, deje el valor predeterminado *No* a menos que no desee que esta versión se use al crear una máquina virtual mediante `latest` en lugar de un número de versión.
1. En **Fecha de finalización del ciclo de vida**, seleccione la fecha del calendario en que considere que esta versión debe dejar de usarse.
1. En la pestaña **Replicación**, seleccione el tipo de almacenamiento en la lista desplegable.
1. Establezca el valor de **Número de réplicas predeterminado**; puede reemplazarlo para cada una de las regiones que agregue. 
1. Debe replicar en la región de origen, por lo que la primera réplica de la lista será en la región donde creó la imagen. Para agregar más réplicas, seleccione la región en la lista desplegable y ajuste el número de réplicas según sea necesario.
1. Cuando haya terminado, seleccione **Revisar y crear**. Azure validará la configuración.
1. Una vez que la versión de la imagen supere la validación, seleccione **Crear**.
1. Cuando la implementación finalice, seleccione **Ir al recurso**.

La imagen puede tardar un rato en replicarse en todas las regiones de destino.

También puede capturar una máquina virtual existente como imagen desde el portal. Para obtener más información, consulte [Creación de una imagen de una máquina virtual en el portal](capture-image-portal.md).

### <a name="cli"></a>[CLI](#tab/cli)

Las definiciones de imagen crean una agrupación lógica de imágenes. Estas se usan para administrar la información sobre las versiones de la imagen que se crean dentro de ellas.

Cree una definición de imagen en una galería mediante [az sig image-definition create](/cli/azure/sig/image-definition#az_sig_image_definition_create). Asegúrese de que la definición de la imagen sea del tipo correcto. Si ha generalizado la máquina virtual (con Sysprep para Windows o waagent-deprovision para Linux), debe crear una definición de imagen generalizada mediante `--os-state generalized`. Si quiere usar la máquina virtual sin quitar las cuentas de usuario existentes, cree una definición de imagen especializada mediante `--os-state specialized`.

Para más información sobre los parámetros que se pueden especificar para una definición de imagen, consulte [Definiciones de imagen](shared-image-galleries.md#image-definitions).

En este ejemplo, la definición de la imagen se denomina *myImageDefinition* y es para una imagen del sistema operativo Linux [especializada](shared-image-galleries.md#generalized-and-specialized-images). Para crear una definición para las imágenes que usan un sistema operativo Windows, utilice `--os-type Windows`. 

```azurecli-interactive 
az sig image-definition create \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --publisher myPublisher \
   --offer myOffer \
   --sku mySKU \
   --os-type Linux \
   --os-state specialized
```

> [!NOTE]
> Para las definiciones de imagen que contendrán imágenes descendientes de imágenes de Marketplace de terceros, la información del plan debe coincidir exactamente con la información del plan de la imagen de terceros. Para incluir la información del plan en la definición de imagen, agregue `--plan-name`, `--plan-product`y `--plan-publisher` al crearla.
>

**Creación de la versión de la imagen**

Cree una versión de imagen con [az sig image version create](/cli/azure/sig/image-version#az_sig_image_version_create).  

La sintaxis para crear la imagen cambiará, en función de lo que use como origen. Puede mezclar los tipos de origen, siempre y cuando solo tenga un origen del sistema operativo. También puede tener orígenes diferentes para cada disco de datos.

| Origen  | Conjunto de parámetros |
|---|---|
| **Disco de SO**| |
| Máquina virtual con el identificador de máquina virtual| `--managed-image <Resource ID of the VM>` |
| Imagen administrada u otra versión de imagen | `--managed-image <Resource ID of the managed image or image version` |
| Instantánea o disco administrado | `--os-snapshot <Resource ID of the snapshot or managed disk>` |
| VHD en una cuenta de almacenamiento | `--os-vhd-uri <URI> --os-vhd-storage-account <storage account name>`.  | 
| **Disco de datos** |
| Instantánea o disco administrado | `--data-snapshots <Resource ID of the snapshot or managed disk> --data-snapshot-luns <LUN number>` |
| VHD en una cuenta de almacenamiento | `--data-vhds-sa <storageaccountname> --data-vhds-uris <URI> --data-vhds-luns <LUN number>` |

Para obtener ejemplos detallados de cómo especificar diferentes orígenes para la imagen, consulte los ejemplos de [az sig image-version create](/cli/azure/sig/image-version#az_sig_image_version_create-examples).

En el siguiente ejemplo, vamos a crear una imagen a partir de una **máquina virtual**. La versión de la imagen es la *1.0.0* y vamos a crear dos réplicas en la región *Centro-oeste de EE. UU.* , una réplica en la región *Centro-sur de EE. UU.* y una réplica en la región *Este de EE. UU. 2* mediante almacenamiento con redundancia de zona. Las regiones de replicación deben incluir la región donde se encuentra la máquina virtual de origen.

Se recomienda detener o desasignar la máquina virtual antes de crear una imagen.

Reemplace el valor de `--managed-image` en este ejemplo por el identificador de la máquina virtual.

```azurecli-interactive 
az sig image-version create \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 \
   --target-regions "westcentralus" "southcentralus=1" "eastus=1=standard_zrs" \
   --replica-count 2 \
   --managed-image "/subscriptions/<Subscription ID>/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM"
```

> [!NOTE]
> Deberá esperar a que la versión de la imagen termine de compilarse y replicarse por completo antes de poder usar la misma imagen administrada para crear otra versión de la imagen.
>
> También puede almacenar la imagen en almacenamiento Premium agregando `--storage-account-type  premium_lrs`, o en el [almacenamiento con redundancia de zona](../storage/common/storage-redundancy.md) agregando `--storage-account-type  standard_zrs` al crear la versión de la imagen.

### <a name="powershell"></a>[PowerShell](#tab/powershell)

Las definiciones de imagen crean una agrupación lógica de imágenes. Al crear la definición de la imagen, asegúrese de tener toda la información correcta. Si ha generalizado el origen de la imagen (con Sysprep para Windows o waagent-deprovision para Linux), debe crear una definición de imagen mediante `-OsState generalized`. Si no ha generalizado el origen, cree una definición de imagen mediante `-OsState specialized`.

Para más información sobre los valores que se pueden especificar para una definición de imagen, consulte [Definiciones de imagen](./shared-image-galleries.md#image-definitions).

Cree la definición de imagen mediante [New-AzGalleryImageDefinition](/powershell/module/az.compute/new-azgalleryimageversion). 

En este ejemplo, la definición de la imagen se denomina *myImageDefinition* y es para una máquina virtual especializada que ejecuta Windows. Para crear una definición para las imágenes que usan Linux, use `-OsType Linux`. 

```azurepowershell-interactive
$imageDefinition = New-AzGalleryImageDefinition `
   -GalleryName $gallery.Name `
   -ResourceGroupName $gallery.ResourceGroupName `
   -Location $gallery.Location `
   -Name 'myImageDefinition' `
   -OsState specialized `
   -OsType Windows `
   -Publisher 'myPublisher' `
   -Offer 'myOffer' `
   -Sku 'mySKU'
```

> [!NOTE]
> Para las definiciones de imagen que contendrán imágenes descendientes de imágenes de terceros, la información del plan debe coincidir exactamente con la del plan de la imagen de terceros. Para incluir la información del plan en la definición de imagen, agregue `-PurchasePlanName`, `-PurchasePlanProduct`y `-PurchasePlanPublisher` al crearla.
>

**Creación de la versión de una imagen**

Cree una versión de la imagen mediante [New-AzGalleryImageVersion](/powershell/module/az.compute/new-azgalleryimageversion). 

La sintaxis para crear la imagen cambiará, en función de lo que use como origen. 

| Origen  | Conjunto de parámetros |
|---|---|
| **Disco de SO**| |
| Máquina virtual con el identificador de máquina virtual| `-SourceImageId <Resource ID of the VM>` |
| Imagen administrada u otra versión de imagen | `-SourceImageId <Resource ID of the managed image or image version` |
| Instantánea o disco administrado | `-OSDiskImage <Resource ID of the snapshot or managed disk>` |
| **Disco de datos** |
| Instantánea o disco administrado | `-DataDiskImage @{Source = @{Id=<source_id>}; Lun=<LUN>; SizeInGB = <Size in GB>; HostCaching = <Caching> }` |


En el siguiente ejemplo, vamos a crear una versión de imagen a partir de una máquina virtual. Se recomienda detener o desasignar la máquina virtual antes de crear una imagen mediante [Stop-AzVM](/powershell/module/az.compute/stop-azvm).

En este ejemplo, la versión de imagen es *1.0.0* y se replica en los centros de datos *Centro-oeste de EE. UU.* y *Centro-sur de EE. UU..* Al elegir las regiones de destino de la replicación, recuerde que también debe incluir la región de *origen* como destino de la replicación.


```azurepowershell-interactive
   $region1 = @{Name='South Central US';ReplicaCount=1}
   $region2 = @{Name='East US';ReplicaCount=2}
   $targetRegions = @($region1,$region2)

$job = $imageVersion = New-AzGalleryImageVersion `
   -GalleryImageDefinitionName $imageDefinition.Name`
   -GalleryImageVersionName '1.0.0' `
   -GalleryName $gallery.Name `
   -ResourceGroupName $gallery.ResourceGroupName `
   -Location $gallery.Location `
   -TargetRegion $targetRegions  `
   -SourceImageId $sourceVm.Id.ToString() `
   -PublishingProfileEndOfLifeDate '2020-12-01' `  
   -asJob 
```

Puede tardar un rato en replicar la imagen en todas las regiones de destino, por lo que creamos un trabajo para que pueda hacer un seguimiento del progreso. Para ver el progreso del trabajo, escriba `$job.State`.

```azurepowershell-interactive
$job.State
```

> [!NOTE]
> Deberá esperar a que la versión de la imagen termine de compilarse y replicarse por completo antes de poder usar la misma imagen administrada para crear otra versión de la imagen.
>
> También puede almacenar la imagen en almacenamiento Premium agregando `-StorageAccountType Premium_LRS`, o en el [almacenamiento con redundancia de zona](../storage/common/storage-redundancy.md) agregando `-StorageAccountType Standard_ZRS` al crear la versión de la imagen.
>

### <a name="rest"></a>[REST](#tab/rest)

Cree una definición de imagen mediante la [API de REST](/rest/api/compute/gallery-images/create-or-update)

```rest
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/galleries/{galleryName}/images/{galleryDefinitionName}?api-version=2019-12-01

{
    "location": "eastus",
    "properties": {
        "hyperVGeneration": "V1",
        "identifier": {
            "offer": "myOffer",
            "publisher": "myPublisher",
            "sku": "mySKU"
        },
        "osState": "Specialized",
        "osType": "Linux",
    },
}
```

Cree una versión de imagen mediante la [API de REST](/rest/api/compute/gallery-image-versions/create-or-update). En este ejemplo, vamos a crear una versión de imagen a partir de una máquina virtual. Para usar otro origen, pase el identificador de recurso para el origen (por ejemplo, pase el identificador de la instantánea de disco del sistema operativo).

```rest
# @name imageVersion
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/galleries/{galleryName}/images/{galleryDefinitionName}/versions/{galleryImageVersionName}?api-version=2019-12-01

{
    "location": "{region}",
    "properties": {
        "publishingProfile": {
            "endOfLifeDate": "2024-12-02T00:00:00+00:00",
            "replicaCount": 1,
            "storageAccountType": "Standard_ZRS",
            "targetRegions": [
                {
                    "name": "eastus",
                    "regionalReplicaCount": 2,
                    "storageAccountType": "Standard_LRS",
                },
                {
                    "name": "westus2",
                }
            ]
        },
        "storageProfile": {
            "source": {
                "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{vmName}"
            }
        }
    }
}
```

---


## <a name="next-steps"></a>Pasos siguientes

Para saber cómo proporcionar información del plan de compra, consulte [Indicación de la información del plan de compra de Azure Marketplace al crear imágenes](marketplace-images.md).
