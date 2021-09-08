---
title: Tamaños disponibles para Azure Cloud Services (soporte extendido)
description: Tamaños disponibles para las implementaciones de Azure Cloud Services (soporte extendido)
ms.topic: article
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: 8075176302e81788b3b180501c951c27ee4105eb
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/03/2021
ms.locfileid: "123434167"
---
# <a name="available-sizes-for-azure-cloud-services-extended-support"></a>Tamaños disponibles para Azure Cloud Services (soporte extendido)

En este artículo se describen los tamaños de máquina virtual disponibles para las instancias de Cloud Services (soporte extendido).   

| Familia de SKU |  ACU/núcleo | 
|---|---|
|[Av2](../virtual-machines/av2-series.md) | 100 | 
|[D](../virtual-machines/sizes-previous-gen.md?bc=%2fazure%2fvirtual-machines%2flinux%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#d-series) | 160 | 
|[Dv2](../virtual-machines/dv2-dsv2-series.md) | 160 - 190* |
|[Dv3](../virtual-machines/dv3-dsv3-series.md) | 160 - 190* |
|[Dav4](../virtual-machines/dav4-dasv4-series.md) | 230 - 260 |
|[Eav4](../virtual-machines/eav4-easv4-series.md) | 230 - 260 |
|[Ev3](../virtual-machines/ev3-esv3-series.md) | 160 - 190* |
|[G](../virtual-machines/sizes-previous-gen.md?bc=%2fazure%2fvirtual-machines%2flinux%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#g-series) | 180-240* |
|[H](../virtual-machines/h-series.md) | 290 - 300* | 

>[!NOTE]
> Las ACU marcadas con un asterisco * usan la tecnología Intel® Turbo para incrementar la frecuencia de CPU y brindar una mejora del rendimiento. El volumen de la mejora puede variar según el tamaño de la máquina virtual, la carga de trabajo y las otras cargas de trabajo que se ejecutan en el mismo host.

## <a name="configure-sizes-for-cloud-services-extended-support"></a>Configuración de tamaños para Cloud Services (soporte extendido)

Puede especificar el tamaño de la máquina virtual de una instancia de rol como parte del modelo de servicio descrito por el archivo de definición de servicio. El tamaño del rol determina la cantidad de núcleos de CPU, la capacidad de memoria y el tamaño del sistema de archivos local.

Por ejemplo, establecer el tamaño de la instancia de rol web en `Standard_D2`: 

```xml
<WorkerRole name="Worker1" vmsize="Standard_D2"> 
</WorkerRole> 
```

## <a name="change-the-size-of-an-existing-role"></a>Cambio de tamaño de un rol existente

Para cambiar el tamaño de un rol existente, cambie el tamaño de la máquina virtual en el archivo de definición de servicio (csdef), vuelva a empaquetar el servicio en la nube y vuelva a implementarlo. 

## <a name="get-a-list-of-available-sizes"></a>Obtención de una lista de los tamaños disponibles 

Para recuperar una lista de los tamaños disponibles, consulte [Resource Skus - List](/rest/api/compute/resourceskus/list) y aplique los siguientes filtros:

```powershell
    # Update the location
    $location = 'WestUS2'
    # Get all Compute Resource Skus
    $allSkus = Get-AzComputeResourceSku
    # Filter virtualMachine skus for given location
    $vmSkus = $allSkus.Where{$_.resourceType -eq 'virtualMachines' -and $_.LocationInfo.Location -like $location}
    # From filtered virtualMachine skus, select PaaS Skus
    $passVMSkus = $vmSkus.Where{$_.Capabilities.Where{$_.name -eq 'VMDeploymentTypes'}.Value.Contains("PaaS")}
    # Optional step to format and sort the output by Family
    $passVMSkus | Sort-Object Family, Name | Format-Table -Property Family, Name, Size
```

## <a name="next-steps"></a>Pasos siguientes 
- Revise los [requisitos previos de implementación](deploy-prerequisite.md) de Cloud Services (soporte extendido).
- Vea las [preguntas más frecuentes](faq.yml) sobre Cloud Services (soporte extendido).
- Implemente una instancia de Cloud Services (soporte extendido) mediante [Azure Portal](deploy-portal.md), [PowerShell](deploy-powershell.md), una [plantilla](deploy-template.md) o [Visual Studio](deploy-visual-studio.md).
