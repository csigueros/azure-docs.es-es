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
ms.openlocfilehash: 9fd4d2171aa54da469aacfaa7090025c296dd0c1
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124804064"
---
# <a name="preview-orchestration-modes-api-comparison"></a>Comparación de API de modos de orquestación (versión preliminar) 

En este artículo se comparan las diferencias de API entre los modos de orquestación uniforme y [flexible](..\virtual-machines\flexible-virtual-machine-scale-sets.md) en conjuntos de escalado de máquinas virtuales. Para obtener más información sobre los conjuntos de escalado de máquinas virtuales uniformes y flexibles, consulte los [modos de orquestación](virtual-machine-scale-sets-orchestration-modes.md).

> [!IMPORTANT]
> En el modo de orquestación flexible, los conjuntos de escalado de máquinas virtuales se encuentran actualmente en versión preliminar pública. No es necesario ningún procedimiento de participación para usar la funcionalidad de versión preliminar pública que se describe a continuación.
> Esta versión preliminar se ofrece sin contrato de nivel de servicio y no es aconsejable usarla para cargas de trabajo de producción. Es posible que algunas características no sean compatibles o que tengan sus funcionalidades limitadas.
> Para más información, consulte [Términos de uso complementarios de las Versiones Preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


## <a name="instance-view"></a>Vista de instancia

| API uniforme | Alternativa flexible |
|-|-|
| Vista de instancia de conjuntos de escalado de máquinas virtuales | Se obtiene una vista de instancia de máquinas virtuales individuales; use Resource Graph para consultar el estado de energía. |


## <a name="scale-set-lifecycle-batch-operations"></a>Operaciones por lotes del ciclo de vida de conjuntos de escalado  

| API uniforme | Alternativa flexible |
|-|-|
| Operaciones por lotes del ciclo de vida de máquina virtual de conjuntos de escalado de máquinas virtuales:  | Invocar API de máquina virtual única en instancias específicas: |
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

### <a name="uniform-api"></a>API uniforme
Obtener o actualizar instancia de máquina virtual de conjuntos de escalado de máquinas virtuales:
- [Get](/rest/api/compute/virtualmachinescalesetvms/get) 
- [Actualizar](/rest/api/compute/virtualmachinescalesetvms/update)

### <a name="flexible-alternative"></a>Alternativa flexible 
Invocar API de máquina virtual única:
- [Bloqueo de recursos de ARM](../azure-resource-manager/management/lock-resources.md?tabs=json) para comportamiento de tipo de protección de instancias 


## <a name="list-instances"></a>Enumerar instancias. 

### <a name="uniform-api"></a>API uniforme
`VMSS List Instances`: 
- Devuelve el identificador del conjunto de escalado asociado a cada instancia durante esta versión preliminar. 

### <a name="flexible-alternative"></a>Alternativa flexible
Azure Resource Graph: 

```armasm
resources 
| where type == "microsoft.compute/virtualmachines" 
| where properties.virtualMachineScaleSet.id contains "portalbb01" 
```

## <a name="scale-set-operations"></a>Operaciones de conjunto de escalado 

### <a name="uniform-api"></a>API uniforme
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

### <a name="flexible-alternative"></a>Alternativa flexible
Invocar operaciones en máquinas virtuales individuales.


## <a name="vm-extension"></a>Extensión de máquina virtual

### <a name="uniform-api"></a>API uniforme
Extensión de máquina virtual de conjuntos de escalado de máquinas virtuales:
- [Crear o actualizar](/rest/api/compute/virtual-machine-scale-set-vm-extensions/create-or-update)
- [Eliminar](/rest/api/compute/virtual-machine-scale-set-vm-extensions/delete)
- [Get](/rest/api/compute/virtual-machine-scale-set-vm-extensions/get)
- [Lista](/rest/api/compute/virtual-machine-scale-set-vm-extensions/list)
- [Actualizar](/rest/api/compute/virtual-machine-scale-set-vm-extensions/update) 

### <a name="flexible-alternative"></a>Alternativa flexible
Invocar operaciones en máquinas virtuales individuales.


## <a name="networking"></a>Redes 

### <a name="uniform-api"></a>API uniforme
- Grupo de NAT/reenvío de puertos 
- Los conjuntos de escalado flexibles no admiten grupos de NAT.  

### <a name="flexible-alternative"></a>Alternativa flexible
- Configurar reglas NAT individuales en cada máquina virtual.


## <a name="scale-set-apis"></a>API de conjunto de escalado

### <a name="uniform-api"></a>API uniforme
API uniformes de conjuntos de escalado de máquinas virtuales:
- [Convertir en grupo de selección de ubicación único](/rest/api/compute/virtual-machine-scale-sets/convert-to-single-placement-group)
- [Forzar recorrido de dominio de actualización de plataforma de recuperación de Service Fabric](/rest/api/compute/virtual-machine-scale-sets/force-recovery-service-fabric-platform-update-domain-walk)

### <a name="flexible-alternative"></a>Alternativa flexible
No está contemplado en los conjuntos de escalado de máquinas virtuales flexibles.


## <a name="next-steps"></a>Pasos siguientes
> [!div class="nextstepaction"]
> [Información sobre los distintos modos de orquestación](virtual-machine-scale-sets-orchestration-modes.md)