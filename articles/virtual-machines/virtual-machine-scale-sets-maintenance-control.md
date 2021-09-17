---
title: Información general sobre el control de mantenimiento para las actualizaciones de imágenes del sistema operativo en conjuntos de escalado de máquinas virtuales de Azure
description: Aprenda a controlar cuándo se implementan las actualizaciones automáticas de las imágenes del sistema operativo en los conjuntos de escalado de máquinas virtuales de Azure mediante el control de mantenimiento.
author: ju-shim
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 09/11/2020
ms.author: jushiman
ms.openlocfilehash: 60922ca7b2e910c883d39a89bebcd6de3c4b4ec8
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/23/2021
ms.locfileid: "122695754"
---
# <a name="maintenance-control-for-azure-virtual-machine-scale-sets"></a>Vista previa: Controles de seguridad para conjuntos de escalado de máquinas virtuales de Azure 

**Se aplica a:** :heavy_check_mark: Conjuntos de escalado uniformes

Administre [actualizaciones automáticas de imágenes del sistema operativo](../virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade.md) para los conjuntos de escalado de máquinas virtuales mediante el control de mantenimiento.

El control de mantenimiento le permite decidir cuándo se aplican las actualizaciones a los discos del sistema operativo en los conjuntos de escalado de máquinas virtuales gracias a una experiencia más sencilla y predecible. 

Mantener el trabajo de las configuraciones entre suscripciones y grupos de recursos.

Todo el flujo de trabajo se reduce a estos pasos: 
- Crear una configuración de mantenimiento.
- Asociar un conjunto de escalado de máquinas virtuales a una configuración de mantenimiento.
- Habilitar las actualizaciones automáticas del sistema operativo.


## <a name="limitations"></a>Limitaciones

- Las máquinas virtuales deben estar en un conjunto de escalado.
- El usuario debe tener acceso de **colaborador del recurso**.
- La duración del mantenimiento debe ser de cinco horas o más en la configuración de mantenimiento.
- La periodicidad de mantenimiento debe establecerse en "días" en la configuración de mantenimiento


## <a name="management-options"></a>Opciones de administración

Puede crear y administrar configuraciones de mantenimiento con cualquiera de estas opciones:

- [Azure PowerShell](virtual-machine-scale-sets-maintenance-control-powershell.md)
- [CLI de Azure](virtual-machine-scale-sets-maintenance-control-cli.md)
- [Azure Portal](virtual-machine-scale-sets-maintenance-control-portal.md)


## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Control de mantenimiento del conjunto de escalado de máquinas virtuales mediante PowerShell](virtual-machine-scale-sets-maintenance-control-powershell.md)
