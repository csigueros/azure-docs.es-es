---
title: Comparación de API de modos de orquestación
description: Información sobre las diferencias de API entre los modos de orquestación uniforme y flexible
author: fitzgeraldsteele
ms.author: fisteele
ms.topic: conceptual
ms.service: virtual-machine-scale-sets
ms.date: 08/05/2021
ms.reviewer: jushiman
ms.custom: mimckitt, devx-track-azurecli, vmss-flex
ms.openlocfilehash: 65f3ea7217930b680cfb197092533989a3206894
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131054578"
---
# <a name="orchestration-modes-api-comparison"></a>Comparación de API de modos de orquestación 

En este artículo se comparan las diferencias de API entre los modos de orquestación uniforme y [flexible](..\virtual-machines\flexible-virtual-machine-scale-sets.md) en conjuntos de escalado de máquinas virtuales. Para obtener más información sobre los conjuntos de escalado de máquinas virtuales uniformes y flexibles, consulte los [modos de orquestación](virtual-machine-scale-sets-orchestration-modes.md).


## <a name="instance-view"></a>Vista de instancia

| API uniforme | Alternativa flexible |
|-|-|
| Vista de instancia de conjuntos de escalado de máquinas virtuales | Se obtiene una vista de instancia de máquinas virtuales individuales; use Resource Graph para consultar el estado de energía. |


## <a name="scale-set-lifecycle-batch-operations"></a>Operaciones por lotes del ciclo de vida de conjuntos de escalado  

| API uniforme | Alternativa flexible |
|-|-|
| [Desasignar](/rest/api/compute/virtualmachinescalesetvms/deallocate)  | [Invocar API de máquina virtual única - Desasignar](/rest/api/compute/virtualmachines/deallocate)   |
| [Eliminar](/rest/api/compute/virtualmachinescalesetvms/delete)  | [Invocar API de máquina virtual única - Eliminar](/rest/api/compute/virtualmachines/delete)  |
| [Obtener vista de instancia](/rest/api/compute/virtualmachinescalesetvms/getinstanceview)  | [Invocar API de máquina virtual única - Vista de instancia](/rest/api/compute/virtualmachines/instanceview)  |
| [Realizar mantenimiento](/rest/api/compute/virtualmachinescalesetvms/performmaintenance)  | [Invocar API de máquina virtual única: Realizar mantenimiento](/rest/api/compute/virtualmachines/performmaintenance)  |
| [Apagar](/rest/api/compute/virtualmachinescalesetvms/poweroff)  | [Invocar API de máquina virtual única: Apagar](/rest/api/compute/virtualmachines/poweroff)  |
| [Volver a implementar](/rest/api/compute/virtualmachinescalesetvms/redeploy)   | [Invocar API de máquina virtual única: Volver a implementar](/rest/api/compute/virtualmachines/redeploy)  |
| [Restablecer imagen inicial](/rest/api/compute/virtualmachinescalesetvms/reimage)  | [Invocar API de máquina virtual única: Restablecer imagen inicial](/rest/api/compute/virtualmachines/reimage)  |
| [Restablecer imagen inicial de todo](/rest/api/compute/virtualmachinescalesetvms/reimageall)  | No aplicable |
| [Reiniciar](/rest/api/compute/virtualmachinescalesetvms/restart)  | [Invocar API de máquina virtual única: Reiniciar](/rest/api/compute/virtualmachines/restart)  |
| [Simular expulsión](/rest/api/compute/virtualmachinescalesetvms/simulateeviction) | [Invocar API de máquina virtual única: Simular expulsión](/rest/api/compute/virtualmachines/simulateeviction)  |
| [Iniciar](/rest/api/compute/virtualmachinescalesetvms/start) | [Invocar API de máquina virtual única: Iniciar](/rest/api/compute/virtualmachines/start) |


## <a name="get-or-update"></a>Obtener o actualizar 

**API uniforme:**

Obtener o actualizar instancia de máquina virtual de conjuntos de escalado de máquinas virtuales:
- [Get](/rest/api/compute/virtualmachinescalesetvms/get) 
- [Actualizar](/rest/api/compute/virtualmachinescalesetvms/update)

**Alternativa flexible:** 

Invocar API de máquina virtual única:
- [Bloqueo de recursos de ARM](../azure-resource-manager/management/lock-resources.md?tabs=json) para comportamiento de tipo de protección de instancias 
    

## <a name="get-or-update-scale-set-vm-instances"></a>Obtener o actualizar instancias de máquina virtual del conjunto de escalado

| API uniforme | Alternativa flexible |
|-|-|
| [Obtener detalles de la máquina virtual del conjunto de escalado](/rest/api/compute/virtualmachinescalesetvms/get) | [Obtención de una máquina virtual](/rest/api/compute/virtualmachines/get) |
| [Actualizar una instancia de máquina virtual del conjunto de escalado](/rest/api/compute/virtualmachinescalesetvms/update) | [Actualizar una máquina virtual](/rest/api/compute/virtualmachines/update) |


## <a name="instance-protection"></a>Protección de instancias 

| API uniforme | Alternativa flexible |
|-|-|
| [Protección de instancias](virtual-machine-scale-sets-instance-protection.md) | [Bloqueo de recursos de ARM](../azure-resource-manager/management/lock-resources.md?tabs=json) para comportamiento de tipo de protección de instancias | 


## <a name="list-instances"></a>Enumerar instancias. 

**API uniforme:**

`VMSS List Instances`: 
- Devuelve el identificador del conjunto de escalado asociado a cada instancia.

**Alternativa flexible:**

Azure Resource Graph: 

```armasm
resources 
| where type == "microsoft.compute/virtualmachines" 
| where properties.virtualMachineScaleSet.id contains "portalbb01" 
```

## <a name="scale-set-instance-operations"></a>Operaciones de instancia del conjunto de escalado 

**API uniforme:**

Operaciones de conjuntos de escalado de máquinas virtuales:
- [Actualizar instancias](/rest/api/compute/virtual-machine-scale-sets/update-instances)
- [Desasignar](/rest/api/compute/virtual-machine-scale-sets/deallocate)
- [Realizar mantenimiento](/rest/api/compute/virtual-machine-scale-sets/perform-maintenance)
- [Apagar](/rest/api/compute/virtual-machine-scale-sets/power-off)
- [Volver a implementar](/rest/api/compute/virtual-machine-scale-sets/redeploy) 
- [Restablecer imagen inicial](/rest/api/compute/virtual-machine-scale-sets/reimage)
- [Restablecer imagen inicial de todo](/rest/api/compute/virtual-machine-scale-sets/reimage-all)
- [Reiniciar](/rest/api/compute/virtual-machine-scale-sets/restart)
- [Establecer el estado del servicio de orquestación](/rest/api/compute/virtual-machine-scale-sets/set-orchestration-service-state)
- [Iniciar](/rest/api/compute/virtual-machine-scale-sets/start)

**Alternativa flexible:**

Invocar operaciones en máquinas virtuales individuales.

Operaciones de máquinas virtuales:
- [Restablecer imagen inicial](/rest/api/compute/virtual-machines/reimage): invocación de una sola API de máquina virtual: restablecimiento de una imagen inicial solo en máquinas virtuales del sistema operativo efímero

## <a name="vm-extension"></a>Extensión de máquina virtual

**API uniforme:**

Extensión de máquina virtual de conjuntos de escalado de máquinas virtuales:
- [Crear o actualizar](/rest/api/compute/virtual-machine-scale-set-vm-extensions/create-or-update)
- [Eliminar](/rest/api/compute/virtual-machine-scale-set-vm-extensions/delete)
- [Get](/rest/api/compute/virtual-machine-scale-set-vm-extensions/get)
- [Lista](/rest/api/compute/virtual-machine-scale-set-vm-extensions/list)
- [Actualizar](/rest/api/compute/virtual-machine-scale-set-vm-extensions/update) 

**Alternativa flexible:**

Invocar operaciones en máquinas virtuales individuales.


## <a name="networking"></a>Redes 

| API uniforme | Alternativa flexible |
|-|-|
| Grupo NAT del equilibrador de carga | Especificar una regla NAT en instancias específicas | 

> [!IMPORTANT]
> El comportamiento de las redes variará en función de cómo elija crear las máquinas virtuales en el conjunto de escalado. Las **instancias de VM agregadas manualmente** tienen acceso de conectividad de salida predeterminado. Las **instancias de VM creadas implícitamente** no tienen acceso predeterminado.
>
> Para obtener más información sobre las redes para conjuntos de escalado flexibles, consulte [Conectividad de red escalable](../virtual-machines/flexible-virtual-machine-scale-sets-migration-resources.md#create-scalable-network-connectivity).


## <a name="scale-set-apis"></a>API de conjunto de escalado

**API uniforme:**

API uniformes de conjuntos de escalado de máquinas virtuales:
- [Convertir en grupo de selección de ubicación único](/rest/api/compute/virtual-machine-scale-sets/convert-to-single-placement-group)
- [Forzar recorrido de dominio de actualización de plataforma de recuperación de Service Fabric](/rest/api/compute/virtual-machine-scale-sets/force-recovery-service-fabric-platform-update-domain-walk)

**Alternativa flexible:**

No está contemplado en los conjuntos de escalado de máquinas virtuales flexibles.


## <a name="next-steps"></a>Pasos siguientes
> [!div class="nextstepaction"]
> [Información sobre los distintos modos de orquestación](virtual-machine-scale-sets-orchestration-modes.md)