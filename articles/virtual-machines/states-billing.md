---
title: Estados y facturación de Azure Virtual Machines
description: Información general de los distintos estados que puede especificar una máquina virtual y cuándo se factura a un usuario.
services: virtual-machines
author: mimckitt
ms.service: virtual-machines
ms.subservice: billing
ms.topic: conceptual
ms.date: 03/8/2021
ms.author: mimckitt
ms.reviewer: cynthn
ms.openlocfilehash: 3d710038c00dff56d4840f7252dcb992dcb1b8c8
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121749272"
---
# <a name="states-and-billing-of-azure-virtual-machines"></a>Estados y facturación de Azure Virtual Machines

Las máquinas virtuales de Azure pueden tener diferentes estados que se pueden clasificar en estados de *aprovisionamiento* y estados de *energía*. El propósito de este artículo es describir estos estados y resaltar específicamente cuándo se les factura a los clientes por el uso de la instancia. 

## <a name="get-states-using-instance-view"></a>Obtención de estados mediante la vista de instancia

La API de vista de instancia proporciona información sobre el estado de ejecución de la máquina virtual. Para más información, consulte la documentación de API de [Máquinas virtuales: Instance View](/rest/api/compute/virtualmachines/instanceview).

Azure Resource Explorer proporciona una interfaz de usuario sencilla para ver el estado de ejecución de una máquina virtual: [Resource Explorer](https://resources.azure.com/).

Los estados de aprovisionamiento son visibles en las propiedades y la vista de instancia de la máquina virtual. Los estados de energía están disponibles en la vista de instancia de la máquina virtual.

Para recuperar el estado de energía de todas las máquinas virtuales de la suscripción, use [Virtual Machines - List All API](/rest/api/compute/virtualmachines/listall) con el parámetro **statusOnly** establecido en *true*.

> [!NOTE]
> [Virtual Machines - List All API](/rest/api/compute/virtualmachines/listall) con el parámetro **statusOnly** establecido en true recuperará los estados de energía de todas las máquinas virtuales de una suscripción. Sin embargo, en algunas situaciones poco comunes, es posible que el estado de energía no esté disponible debido a problemas intermitentes en el proceso de recuperación. En estas situaciones, se recomienda volver a intentar usar la misma API o [Azure Resource Health](../service-health/resource-health-overview.md) o [Azure Resource Graph](..//governance/resource-graph/overview.md) para comprobar el estado de energía de las máquinas virtuales.
 
## <a name="power-states-and-billing"></a>Estados de energía y facturación

El estado de energía representa el último estado conocido de la máquina virtual.

:::image type="content" source="./media/virtual-machines-common-states-lifecycle/vm-power-states.png" alt-text="En la imagen se muestra un diagrama de los estados de energía por los que puede pasar una máquina virtual":::

La tabla siguiente proporciona una descripción del estado de cada instancia e indica si se factura por uso de instancia o no.

| Estado de energía | Description | Facturación |  
|---|---|---|
| Iniciando| La máquina virtual se está iniciando. | Facturado | 
| En ejecución | La máquina virtual está totalmente activa. Este es el estado de funcionamiento estándar. | Facturado | 
| Deteniéndose | Se trata de un estado de transición entre la ejecución y la detención. | Facturado| 
|Detenido | La máquina virtual está asignada en un host, pero no se está ejecutando. También se denomina estado Apagada o *Detenida (asignada)* . Este estado puede ser la consecuencia de invocar la operación de la API de PowerOff o de invocar un apagado desde el sistema operativo invitado. El estado Detenida también se puede observar brevemente durante la creación de una máquina virtual o al iniciarla desde el estado Desasignada.  | Facturado | 
| Desasignando | Este es el estado de transición entre la ejecución y la desasignación. | No facturado* | 
| Desasignado | La máquina virtual ha liberado la concesión en el hardware subyacente y está completamente apagada. Este estado también se conoce como *Detenido (desasignado)* . | No facturado* | 

&#42; Algunos recursos de Azure, como los [discos](https://azure.microsoft.com/pricing/details/managed-disks) y las [redes](https://azure.microsoft.com/pricing/details/bandwidth/), seguirán generando gastos.


## <a name="provisioning-states"></a>Estados de aprovisionamiento

Un estado de aprovisionamiento es el estado de una operación de plano de control iniciada por el usuario en la máquina virtual. Estos estados son independientes del estado de energía de una máquina virtual.

:::image type="content" source="./media/virtual-machines-common-states-lifecycle/vm-provisioning-states.png" alt-text="En la imagen se muestran los estados de aprovisionamiento por los que puede pasar una máquina virtual.":::

| Estado de aprovisionamiento | Description | Estado de energía | Facturación | 
|---|---|---|---|
| Crear | Creación de máquinas virtuales. | Iniciando | No facturado* | 
| Actualizar | Actualiza el modelo de una máquina virtual existente. Algunos cambios en una máquina virtual que no son del modelo, como iniciar y reiniciar, entran en el estado de actualización. | En ejecución | Facturado | 
| Eliminar | Eliminación de la máquina virtual. | Desasignando | No facturado* |
| desasignar | La máquina virtual se detiene y se quita completamente del host subyacente. Desasignar una máquina virtual se considera una actualización y mostrará estados de aprovisionamiento similares a la actualización. | Desasignando | No facturado* | 

&#42; Algunos recursos de Azure, como los [discos](https://azure.microsoft.com/pricing/details/managed-disks) y las [redes](https://azure.microsoft.com/pricing/details/bandwidth/), seguirán generando gastos.

## <a name="os-provisioning-states"></a>Estados de aprovisionamiento del sistema operativo
Los estados de aprovisionamiento del sistema operativo solo se aplican a máquinas virtuales creadas con una imagen del sistema operativo. En las imágenes especializadas no se mostrarán estos estados. 

:::image type="content" source="./media/virtual-machines-common-states-lifecycle/os-provisioning-states.png" alt-text="En la imagen se muestran los estados de aprovisionamiento del sistema operativo por los que puede pasar una máquina virtual.":::

| Estado de aprovisionamiento del sistema operativo | Description | Estado de energía | Facturación | 
|---|---|---|---|
| OSProvisioningInProgress | La máquina virtual está en ejecución y la instalación del sistema operativo invitado está en curso. | En ejecución | Facturado | 
| OSProvisioningComplete | Se trata de un estado de corta duración. La máquina virtual pasa rápidamente de este estado a **Correcto**. Si todavía se están instalando las extensiones, seguirá viendo este estado hasta que se completen. | En ejecución | Facturado | 
| Correcto | Se han completado las acciones iniciadas por el usuario. | En ejecución | Facturado | 
| Con error | Representa una operación con errores. Consulte el código de error para obtener más información y posibles soluciones. | En ejecución  | Facturado | 


## <a name="next-steps"></a>Pasos siguientes
- Revise la [documentación de Azure Cost Management y Facturación](../cost-management-billing/index.yml).
- Use la [calculadora de precios de Azure](https://azure.microsoft.com/pricing/calculator/) para planear las implementaciones.
- Para obtener más información sobre la supervisión de la máquina virtual, consulte [Supervisión de máquinas virtuales en Azure](../azure-monitor/vm/monitor-vm-azure.md).
