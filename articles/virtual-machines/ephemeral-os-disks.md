---
title: Discos de sistema operativo efímero
description: Obtenga más información acerca de los discos de SO efímeros para máquinas virtuales de Azure.
author: Aarthi-Vijayaraghavan
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 07/23/2020
ms.author: aarthiv
ms.subservice: disks
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 7318baacb8765a6009c0cd502a5eafe1ad7ba20e
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131082687"
---
# <a name="ephemeral-os-disks-for-azure-vms"></a>Discos de SO efímeros para máquinas virtuales de Azure

**Se aplica a:** :heavy_check_mark: Máquinas virtuales Linux :heavy_check_mark: Máquinas virtuales Windows :heavy_check_mark: Conjuntos de escalado flexibles :heavy_check_mark: Conjuntos de escalado uniformes

Los discos del sistema operativo efímeros se crean en el almacenamiento local de la máquina virtual y no se guardan en la instancia remota de Azure Storage. Estos discos están indicados para cargas de trabajo sin estado, donde las aplicaciones toleran errores de máquinas virtuales individuales, pero tienen más en cuenta el tiempo de implementación de las máquinas virtuales o el restablecimiento de las instancias individuales de dichas máquinas. Con los discos del sistema operativo efímeros, observará una latencia de lectura y escritura inferior en el disco del sistema operativo y un restablecimiento más rápido de la imagen inicial de la máquina virtual. 
 
Las principales características de los discos efímeros son: 
- Perfecto para las aplicaciones sin estado.
- Compatible con Marketplace, las imágenes personalizadas y con [Shared Image Gallery](./shared-image-galleries.md).
- Posibilidad de restablecimiento rápido o de restablecimiento de la imagen inicial de las máquinas virtuales y de las instancias de conjunto de escalado al estado de arranque original.  
- Latencia inferior, similar a la de un disco temporal. 
- Los discos del sistema operativo efímeros son gratuitos, es decir, no generan costos de almacenamiento.
- Disponibilidad en todas las regiones de Azure.  

 
Principales diferencias entre discos del sistema operativo efímeros y persistentes:

|                             | Disco del sistema operativo persistente                          | Disco de sistema operativo efímero                              |
|-----------------------------|---------------------------------------------|------------------------------------------------|
| **Límite de tamaño del disco del sistema operativo**      | 2 TiB                                                                                        | El tamaño de caché para el tamaño de máquina virtual o 2 TiB, el que sea menor. Para el **tamaño de caché en GiB**, consulte [DS](sizes-general.md), [ES](sizes-memory.md), [M](sizes-memory.md), [FS](sizes-compute.md) y [GS](sizes-previous-gen.md#gs-series).              |
| **Tamaños de máquina virtual admitidos**          | All                                                                                          | Tamaños de máquina virtual que admiten Premium Storage, como DSv1, DSv2, DSv3, Esv3, Fs, FsV2, GS, M Mdsv2, Bs, Dav4 y Eav4                                               |
| **Compatibilidad con los tipos de discos**           | Disco del sistema operativo administrado y no administrado                                                                | Solo disco del sistema operativo administrado                                                               |
| **Regiones admitidas**              | Todas las regiones                                                                                  | Todas las regiones                              |
| **Persistencia de los datos**            | Los datos del disco del sistema operativo escritos en un disco del sistema operativo se almacenan en Azure Storage.                                  | Los datos escritos en un disco del sistema operativo se almacenan en el almacenamiento de máquina virtual local y no se conservan en Azure Storage. |
| **Estado detenido (desasignado)**      | Las máquinas virtuales y las instancias del conjunto de escalado pueden estar detenidas (desasignadas) y reiniciarse a partir de este estado. | Las máquinas virtuales y las instancias del conjunto de escalado no pueden estar detenidas (desasignadas).                                  |
| **Compatibilidad con discos del sistema operativo especializados** | Sí                                                                                          | No                                                                                 |
| **Cambio de tamaño del disco del sistema operativo**              | Se admite durante la creación de la máquina virtual y después de que esta se detiene (desasigna).                                | Se admite solo durante la creación de la máquina virtual                                                  |
| **Cambio a un nuevo tamaño de máquina virtual**   | Se conservan los datos del disco del sistema operativo                                                                    | Se eliminan los datos del disco del sistema operativo, se vuelve a aprovisionar el sistema operativo       
| **Ubicación del archivo de paginación**   | En Windows, el archivo de paginación se almacena en el disco de recursos                                              | En Windows, el archivo de página se almacena en el disco del sistema operativo (tanto para la ubicación de caché del sistema operativo como para la ubicación del disco temporal).   |

## <a name="size-requirements"></a>Requisitos de tamaño

Puede optar por implementar un disco de sistema operativo efímero en la caché de la máquina virtual o en el disco temporal de la máquina virtual.
El tamaño del disco del sistema operativo de la imagen debe ser menor o igual que el tamaño de la caché o temporal del tamaño de máquina virtual elegido.

Por ejemplo, si quiere elegir la **ubicación de la caché del sistema operativo**: las imágenes estándar de Windows Server de Marketplace son de aproximadamente 127 GiB, lo que significa que necesita un tamaño de máquina virtual que tenga una caché superior o igual a 127 GiB. Las máquinas virtuales Standard_DS3_v2 tienen un tamaño de caché de 127 GiB, que es lo suficientemente grande. En este caso, Standard_DS3_v2 tienen el tamaño menor de la serie DSv2 que se puede usar con esta imagen. 

Si desea optar por elegir la **ubicación de disco temporal**: la imagen de servidor estándar de Ubuntu de Marketplace es de aproximadamente 30 GiB. Para habilitar el disco del sistema operativo efímero con tamaño temporal, el tamaño del disco temporal debe ser igual o mayor que 30 GiB. Standard_B4ms tiene un tamaño temporal de 32 GiB, que puede ajustarse al disco del sistema operativo de 30 GiB. Tras la creación de la máquina virtual, el espacio en disco temporal sería de 2 GiB. 
> [!Important] 
> Si opta por elegir la ubicación del disco temporal, el tamaño del disco temporal final = (tamaño del disco temporal inicial - tamaño de la imagen del sistema operativo).

Las imágenes básicas de Linux y Windows Server en Marketplace indicadas por `[smallsize]` tienden a tener alrededor de 30 GiB y pueden usar la mayoría de los tamaños de máquinas virtuales disponibles.
Los discos efímeros también requieren que el tamaño de la máquina virtual admita **Premium Storage**. Los tamaños normalmente (pero no siempre) tienen un `s` en el nombre, como DSv2 y EsV3. Para más información, consulte [Tamaños de máquinas virtuales de Azure](sizes.md) para más información sobre qué tamaños se admiten Premium Storage.

## <a name="ephemeral-os-disks-can-now-be-stored-on-tempresource-disks"></a>Los discos efímeros del sistema operativo ahora se pueden almacenar en discos temporales o de recursos
El disco del sistema operativo efímero ahora puede almacenarse en el disco de caché de la máquina virtual o en el disco temporal o de recursos de la máquina virtual. Esta característica permite crear discos del sistema operativo efímeros para todas las máquinas virtuales, que no tienen caché o no tienen memoria caché suficiente (como Dav3, Dav4, Eav4 y Eav3), pero tienen suficiente disco temporal para hospedar el disco del sistema operativo efímero.
[DiffDiskPlacement](/rest/api/compute/virtualmachines/list#diffdiskplacement) es la nueva propiedad que se puede usar para especificar dónde desea colocar el disco del sistema operativo efímero. Con esta característica, cuando se aprovisiona una máquina virtual Windows, se configura el archivo de paginación para que se encuentre en el disco del sistema operativo.

## <a name="portal"></a>Portal

En Azure Portal, puede elegir usar discos efímeros al implementar una máquina virtual o conjuntos de escalado de máquinas virtuales abriendo la sección **Avanzado** de la pestaña **Discos**. Para elegir la ubicación del disco del sistema operativo efímero, seleccione **Ubicación de caché del sistema operativo** o **Ubicación de disco temporal**.

![Captura de pantalla que muestra el botón de radio para elegir un disco de sistema operativo efímero](./media/virtual-machines-common-ephemeral/ephemeral-portal-temp.png)
 

Si la opción de utilizar un disco efímero, una ubicación de caché del sistema operativo o una ubicación de disco temporal está atenuada, es posible que haya seleccionado un tamaño de máquina virtual que no tenga un tamaño temporal o de caché mayor que la imagen del sistema operativo o que no admita Premium Storage. Vuelva a la página **Basics** (Aspectos básicos) y pruebe con otro tamaño de máquina virtual.

## <a name="scale-set-template-deployment"></a>Implementación de plantillas de conjunto de escalado  
El proceso para crear un conjunto de escalado que use un disco del sistema operativo efímero consiste en agregar la propiedad `diffDiskSettings` al tipo de recurso `Microsoft.Compute/virtualMachineScaleSets/virtualMachineProfile` en la plantilla. Además, la directiva de almacenamiento en caché debe establecerse en `ReadOnly` para el disco del sistema operativo efímero. la ubicación se puede cambiar a `CacheDisk` para la ubicación del disco de caché del sistema operativo.

```json
{ 
  "type": "Microsoft.Compute/virtualMachineScaleSets", 
  "name": "myScaleSet", 
  "location": "East US 2", 
  "apiVersion": "2019-12-01", 
  "sku": { 
    "name": "Standard_DS2_v2", 
    "capacity": "2" 
  }, 
  "properties": { 
    "upgradePolicy": { 
      "mode": "Automatic" 
    }, 
    "virtualMachineProfile": { 
       "storageProfile": { 
        "osDisk": { 
          "diffDiskSettings": { 
            "option": "Local" ,
            "placement": "ResourceDisk"
          }, 
          "caching": "ReadOnly", 
          "createOption": "FromImage" 
        }, 
        "imageReference":  { 
          "publisher": "Canonical", 
          "offer": "UbuntuServer", 
          "sku": "16.04-LTS", 
          "version": "latest" 
        } 
      }, 
      "osProfile": { 
        "computerNamePrefix": "myvmss", 
        "adminUsername": "azureuser", 
        "adminPassword": "P@ssw0rd!" 
      } 
    } 
  } 
}  
```

## <a name="vm-template-deployment"></a>Implementación de plantillas de máquinas virtuales 
Puede implementar una máquina virtual con un disco del sistema operativo efímero mediante una plantilla. El proceso para crear una máquina virtual que use discos del sistema operativo efímeros consiste en agregar la propiedad `diffDiskSettings` al tipo de recurso Microsoft.COMPUTE/virtualmachines en la plantilla. Además, la directiva de almacenamiento en caché debe establecerse en `ReadOnly` para el disco del sistema operativo efímero. la opción de ubicación se puede cambiar a `CacheDisk` para la ubicación del disco de caché del sistema operativo.

```json
{ 
  "type": "Microsoft.Compute/virtualMachines", 
  "name": "myVirtualMachine", 
  "location": "East US 2", 
  "apiVersion": "2019-12-01", 
  "properties": { 
       "storageProfile": { 
            "osDisk": { 
              "diffDiskSettings": { 
                "option": "Local" ,
                "placement": "ResourceDisk"
              }, 
              "caching": "ReadOnly", 
              "createOption": "FromImage" 
            }, 
            "imageReference": { 
                "publisher": "MicrosoftWindowsServer", 
                "offer": "WindowsServer", 
                "sku": "2016-Datacenter-smalldisk", 
                "version": "latest" 
            }, 
            "hardwareProfile": { 
                 "vmSize": "Standard_DS2_v2" 
             } 
      }, 
      "osProfile": { 
        "computerNamePrefix": "myvirtualmachine", 
        "adminUsername": "azureuser", 
        "adminPassword": "P@ssw0rd!" 
      } 
    } 
 } 
```

> [!NOTE] 
> La opción de ubicación del disco del sistema operativo efímero (disco de caché de máquina virtual o disco temporal o de recursos de máquina virtual) estará disponible próximamente en PowerShell y la CLI.

## <a name="powershell"></a>PowerShell
Para usar un disco efímero para una implementación de máquina virtual de PowerShell, utilice [Set-AzVMOSDisk](/powershell/module/az.compute/set-azvmosdisk) en la configuración de la máquina virtual. Establezca `-DiffDiskSetting` en `Local` y `-Caching` en `ReadOnly`.     
```powershell
Set-AzVMOSDisk -DiffDiskSetting Local -Caching ReadOnly
```
Para las implementaciones de conjunto de escalado, use el cmdlet [Set-AzVmssStorageProfile](/powershell/module/az.compute/set-azvmssstorageprofile) en la configuración. Establezca `-DiffDiskSetting` en `Local` y `-Caching` en `ReadOnly`.

```powershell
Set-AzVmssStorageProfile -DiffDiskSetting Local -OsDiskCaching ReadOnly
```

## <a name="cli"></a>CLI

Para usar un disco efímero para una implementación de la máquina virtual de la CLI, establezca el parámetro `--ephemeral-os-disk` de [az vm create](/cli/azure/vm#az_vm_create) en `true` y el parámetro `--os-disk-caching` en `ReadOnly`.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --image UbuntuLTS \
  --ephemeral-os-disk true \
  --os-disk-caching ReadOnly \
  --admin-username azureuser \
  --generate-ssh-keys
```

Para los conjuntos de escalado, use el mismo parámetro `--ephemeral-os-disk true` para [az vmss create](/cli/azure/vmss#az_vmss_create) y establezca el parámetro `--os-disk-caching` en `ReadOnly`.

## <a name="reimage-a-vm-using-rest"></a>Restablecimiento de la imagen inicial de una máquina virtual mediante REST
Puede restablecer la imagen inicial de una instancia de máquina virtual con un disco de sistema operativo efímero mediante la API REST, como se describe a continuación y mediante Azure Portal en el panel Información general de la máquina virtual. Para los conjuntos de escalado, el restablecimiento de la imagen inicial ya está disponible mediante PowerShell, la CLI y el portal.

```
POST https://management.azure.com/subscriptions/{sub-
id}/resourceGroups/{rgName}/providers/Microsoft.Compute/VirtualMachines/{vmName}/reimage?a pi-version=2019-12-01" 
```
 
## <a name="frequently-asked-questions"></a>Preguntas más frecuentes

**P: ¿Cuál es el tamaño de los discos del sistema operativo locales?**

R: Se admiten imágenes de plataforma, Shared Image Gallery y personalizadas, hasta el tamaño de caché de la máquina virtual con la ubicación de la caché del sistema operativo y hasta el tamaño del disco temporal con la ubicación de disco temporal, donde todas las operaciones de lectura y escritura en el disco del sistema operativo serán locales en el mismo nodo que la máquina virtual. 

**P: ¿Se puede cambiar el tamaño del disco del sistema operativo efímero?**

A. No, una vez que se aprovisiona el disco del sistema operativo efímero, no puede cambiarse su tamaño. 

**P: ¿Se puede modificar la ubicación del disco del sistema operativo efímero después de crear la máquina virtual?**

R: No, una vez que se aprovisiona el disco del sistema operativo efímero, no puede cambiarse su ubicación. Sin embargo, la máquina virtual se puede volver a crear a través de la implementación de plantillas de ARM, PowerShell o la CLI actualizando la ubicación del disco del sistema operativo que elija. Con esto, la máquina virtual se volvería a crear con datos en el disco del sistema operativo eliminados y se volvería a aprovisionar el sistema operativo.

**P: ¿Se crea algún disco temporal si el tamaño de la imagen es igual al tamaño del disco temporal del tamaño de máquina virtual seleccionado?**

R: No. En ese caso, no se creará ninguna unidad de disco temporal.

**P: ¿Se admiten discos de sistema operativo efímeros en máquinas virtuales de prioridad baja y máquinas virtuales de acceso puntual?**

A. Sí. No hay ninguna opción de Detener-Desasignar para las máquinas virtuales efímeras, sino que los usuarios tienen que eliminarlas en lugar de desasignarlas.

**P: ¿Puedo asociar un disco administrado a una máquina virtual efímera?**

A. Sí, puede asociar un disco de datos administrado a una máquina virtual que use un disco del sistema operativo efímero. 

**P: ¿Se admitirán todos los tamaños de máquina virtual con los discos del sistema operativo efímeros?**

A. No, se admiten la mayoría de los tamaños de máquina virtual de Premium Storage (DS, ES, FS, GS, M, etc). Para saber si un tamaño de máquina virtual determinado admite discos efímeros del sistema operativo, puede:

Llamar al cmdlet de PowerShell `Get-AzComputeResourceSku`
```azurepowershell-interactive
 
$vmSizes=Get-AzComputeResourceSku | where{$_.ResourceType -eq 'virtualMachines' -and $_.Locations.Contains('CentralUSEUAP')} 

foreach($vmSize in $vmSizes)
{
   foreach($capability in $vmSize.capabilities)
   {
       if($capability.Name -eq 'EphemeralOSDiskSupported' -and $capability.Value -eq 'true')
       {
           $vmSize
       }
   }
}
```
 
**P: ¿Se puede aplicar el disco del sistema operativo efímero a máquinas virtuales y conjuntos de escalado existentes?**

A. No, el disco del sistema operativo efímero solo se puede usar durante la creación de la máquina virtual y el conjunto de escalado. 

**P: ¿Se pueden combinar discos del sistema operativo efímeros y normales en un conjunto de escalado?**

A. No, no se puede tener una combinación de instancias de disco del sistema operativo efímeras y persistentes dentro del mismo conjunto de escalado. 

**P: ¿Se puede crear el disco del sistema operativo efímero con PowerShell o la CLI?**

R: Sí, puede crear máquinas virtuales con discos del sistema operativo efímeros mediante REST, plantillas, PowerShell y la CLI.

**P: ¿Qué características no se admiten con los discos del sistema operativo efímeros?**

A. Los discos efímeros no admiten:
- Captura de imágenes de máquina virtual
- Instantáneas de disco. 
- Azure Disk Encryption 
- Azure Backup
- Azure Site Recovery  
- Intercambio de discos del sistema operativo 

> [!NOTE]
> 
> No se podrá acceder al disco efímero mediante el portal. Recibirá el error "Recurso no encontrado" o "404" al acceder al disco efímero que se espera.
> 
 
## <a name="next-steps"></a>Pasos siguientes
Puede crear una máquina virtual con un disco de SO efímero mediante la [CLI de Azure](/cli/azure/vm#az_vm_create).