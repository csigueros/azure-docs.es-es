---
title: Estados y estado de facturación de Azure Virtual Machines
description: Información general de los distintos estados que puede especificar una máquina virtual y cuándo se factura a un usuario.
services: virtual-machines
author: mimckitt
ms.service: virtual-machines
ms.subservice: billing
ms.topic: conceptual
ms.date: 03/8/2021
ms.author: mimckitt
ms.reviewer: cynthn
ms.openlocfilehash: 7458a35b6227ca620f08a02aaadc5b95e8528141
ms.sourcegitcommit: 838413a8fc8cd53581973472b7832d87c58e3d5f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/10/2021
ms.locfileid: "132134344"
---
# <a name="states-and-billing-status-of-azure-virtual-machines"></a>Estados y estado de facturación de Azure Virtual Machines

**Se aplica a:** :heavy_check_mark: Máquinas virtuales Linux :heavy_check_mark: Máquinas virtuales Windows :heavy_check_mark: Conjuntos de escalado flexibles :heavy_check_mark: Conjuntos de escalado uniformes

Las máquinas virtuales de Azure pueden tener diferentes estados que se pueden clasificar en estados de *aprovisionamiento* y estados de *energía*. El propósito de este artículo es describir estos estados y resaltar específicamente cuándo se les factura a los clientes por el uso de la instancia. 

## <a name="get-states-using-instance-view"></a>Obtención de estados mediante la vista de instancia

La API de vista de instancia proporciona información sobre el estado de ejecución de la máquina virtual. Para más información, consulte la documentación de API de [Máquinas virtuales: Instance View](/rest/api/compute/virtualmachines/instanceview).

Azure Resource Explorer proporciona una interfaz de usuario sencilla para ver el estado de ejecución de una máquina virtual: [Resource Explorer](https://resources.azure.com/).

El estado de aprovisionamiento de la máquina virtual está disponible (en formas ligeramente diferentes) en las propiedades de la máquina virtual `provisioningState` e InstanceView. En la propiedad InstanceView de la máquina virtual, habrá un elemento dentro de la matriz `status` en forma de `ProvisioningState/<state>[/<errorCode>]`.

Para recuperar el estado de energía de todas las máquinas virtuales de la suscripción, use [Virtual Machines - List All API](/rest/api/compute/virtualmachines/listall) con el parámetro **statusOnly** establecido en *true*.

> [!NOTE]
> [Virtual Machines - List All API](/rest/api/compute/virtualmachines/listall) con el parámetro **statusOnly** establecido en true recuperará los estados de energía de todas las máquinas virtuales de una suscripción. Sin embargo, en algunas situaciones poco comunes, es posible que el estado de energía no esté disponible debido a problemas intermitentes en el proceso de recuperación. En estas situaciones, se recomienda volver a intentar usar la misma API o bien [Azure Resource Health](../service-health/resource-health-overview.md) para comprobar el estado de energía de las máquinas virtuales.
 
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


**Ejemplo de PowerState en JSON**

```json
        {
          "code": "PowerState/running",
          "level": "Info",
          "displayStatus": "VM running"
        }
```

&#42; Algunos recursos de Azure, como los [discos](https://azure.microsoft.com/pricing/details/managed-disks) y las [redes](https://azure.microsoft.com/pricing/details/bandwidth/), seguirán generando gastos.


## <a name="provisioning-states"></a>Estados de aprovisionamiento

El estado de aprovisionamiento es el estado de una operación de plano de control iniciada por el usuario en la máquina virtual. Estos estados son independientes del estado de energía de una máquina virtual.

| Estado de aprovisionamiento | Descripción |
|---|---|
| Creating | Se está creando la máquina virtual. |
| Actualizando | La máquina virtual se está actualizando al modelo más reciente. Algunos cambios que no tienen que ver con el modelo en una máquina virtual, como el inicio y el reinicio, entran dentro del estado de actualización. |
| Con error | La última operación en el recurso de máquina virtual no se realizó correctamente. | 
| Correcto | La última operación en el recurso de máquina virtual se realizó correctamente. | 
| Eliminando | Se está eliminando la máquina virtual. | 
| Migración | Aparece cuando se migra de Azure Service Manager a Azure Resource Manager. | 

## <a name="os-provisioning-states"></a>Estados de aprovisionamiento del sistema operativo
Los estados de aprovisionamiento del sistema operativo solo se aplican a las máquinas virtuales creadas con una imagen del sistema operativo [generalizada](./linux/imaging.md#generalized-images). Las imágenes [especializadas](./linux/imaging.md#specialized-images) y los discos conectados como discos del sistema operativo no mostrarán estos estados. El estado de aprovisionamiento del sistema operativo no se muestra aparte. Es un subestado del estado de aprovisionamiento en la propiedad InstanceView de la máquina virtual. Por ejemplo, `ProvisioningState/creating/osProvisioningComplete`.

:::image type="content" source="./media/virtual-machines-common-states-lifecycle/os-provisioning-states.png" alt-text="En la imagen se muestran los estados de aprovisionamiento del sistema operativo por los que puede pasar una máquina virtual.":::

| Estado de aprovisionamiento del sistema operativo | Description | 
|---|---|
| OSProvisioningInProgress | La máquina virtual se está ejecutando y la inicialización (instalación) del sistema operativo invitado está en curso. |
| OSProvisioningComplete | Se trata de un estado de corta duración. La máquina virtual pasa rápidamente de este estado a **Correcto**. Si todavía se están instalando las extensiones, seguirá viendo este estado hasta que se completen. |
| Correcto | Se han completado las acciones iniciadas por el usuario. | 
| Con error | Representa una operación con errores. Consulte el código de error para obtener más información y posibles soluciones. | 

## <a name="troubleshooting-vm-states"></a>Solución de problemas de estados de la máquina virtual

Para solucionar problemas específicos de los estados de la máquina virtual, consulte [Solución de problemas de implementación de máquinas virtuales Windows](https://docs.microsoft.com/troubleshoot/azure/virtual-machines/troubleshoot-deployment-new-vm-windows) y [Solución de problemas de implementación de máquinas virtuales Linux](https://docs.microsoft.com/troubleshoot/azure/virtual-machines/troubleshoot-deployment-new-vm-linux).

Si necesita más ayuda de solución de problemas, visite [Documentación de solución de problemas de máquinas virtuales de Azure](https://docs.microsoft.com/troubleshoot/azure/virtual-machines/welcome-virtual-machines).


## <a name="next-steps"></a>Pasos siguientes
- Revise la [documentación de Azure Cost Management y Facturación](../cost-management-billing/index.yml).
- Use la [calculadora de precios de Azure](https://azure.microsoft.com/pricing/calculator/) para planear las implementaciones.
- Para obtener más información sobre la supervisión de la máquina virtual, consulte [Supervisión de máquinas virtuales en Azure](../azure-monitor/vm/monitor-vm-azure.md).
