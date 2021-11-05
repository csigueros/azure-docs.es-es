---
title: Migración de implementaciones y recursos a conjuntos de escalado de máquinas virtuales en orquestación flexible
description: Aprenda a migrar implementaciones y recursos a conjuntos de escalado de máquinas virtuales en orquestación flexible.
author: fitzgeraldsteele
ms.author: fisteele
ms.topic: conceptual
ms.service: virtual-machines
ms.subservice: flexible-scale-sets
ms.date: 10/14/2021
ms.reviewer: jushiman
ms.custom: mimckitt, devx-track-azurecli, vmss-flex
ms.openlocfilehash: b4662c2fd6c5a0950bc3b2b6f336fabab12fc1aa
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131008490"
---
# <a name="migrate-deployments-and-resources-to-virtual-machine-scale-sets-in-flexible-orchestration"></a>Migración de implementaciones y recursos a conjuntos de escalado de máquinas virtuales en orquestación flexible 

**Se aplica a:** :heavy_check_mark: Conjuntos de escalado flexibles

Al igual que los conjuntos de disponibilidad, los conjuntos de escalado de máquinas virtuales permiten distribuir máquinas virtuales entre varios dominios de error. Los conjuntos de escalado de máquinas virtuales con orquestación flexible permiten combinar la escalabilidad de los [conjuntos de escalado de máquinas virtuales del modo de orquestación uniforme](../virtual-machine-scale-sets/overview.md) con las garantías de disponibilidad regionales de los [conjuntos de disponibilidad](availability-set-overview.md). En este artículo se tratan aspectos de la migración al cambiar al modo de orquestación flexible para conjuntos de escalado de máquinas virtuales. 

## <a name="update-availability-set-deployments-templates-and-scripts"></a>Actualización de plantillas y scripts de implementaciones de conjuntos de disponibilidad

En primer lugar, debe crear un conjunto de escalado de máquinas virtuales sin ningún perfil de escalado automático mediante la [CLI de Azure](flexible-virtual-machine-scale-sets-cli.md), [Azure PowerShell](flexible-virtual-machine-scale-sets-powershell.md) o una [plantilla de ARM](flexible-virtual-machine-scale-sets-rest-api.md). Azure Portal solo permite crear un conjunto de escalado de máquinas virtuales con un perfil de escalado automático. Si no desea un perfil de escalado automático, o no lo necesita, y desea crear un conjunto de escalado mediante [Azure Portal](flexible-virtual-machine-scale-sets-portal.md), puede establecer la capacidad inicial en 0. 
 
Debe especificar el número de dominios de error para el conjunto de escalado de máquinas virtuales. En el caso de las implementaciones regionales (no zonales), los conjuntos de escalado de máquinas virtuales ofrecen las mismas garantías de dominio de error que los conjuntos de disponibilidad. Sin embargo, puede escalar hasta 1000 instancias. En el caso de las implementaciones zonales en las que se distribuyen instancias entre varias zonas de disponibilidad, el número de dominios de error debe establecerse en 1.

Los dominios de actualización han quedado en desuso en el modo de orquestación flexible. La mayoría de las actualizaciones de plataforma con SKU de uso general se realizan con Migración en vivo y no requieren el reinicio de la instancia. En el caso de que un mantenimiento de la plataforma requiera que se reinicien las instancias, las actualizaciones se aplicarán a los dominios de error de uno en uno.  
    
La orquestación flexible para conjuntos de escalado de máquinas virtuales también admite la implementación de instancias en varias zonas de disponibilidad. Es posible que quiera considerar la posibilidad de actualizar las implementaciones de máquinas virtuales para que se distribuyan entre varias zonas de disponibilidad. 

El último paso de este proceso es la creación de una máquina virtual. En lugar de especificar un conjunto de disponibilidad, especifique el conjunto de escalado de máquinas virtuales. Opcionalmente, puede especificar la zona de disponibilidad o el dominio de error en el que desea colocar la máquina virtual. 


## <a name="migrate-existing-availability-set-vms"></a>Migración de máquinas virtuales de conjuntos de disponibilidad existentes 

Actualmente no hay ninguna herramienta automatizada para mover directamente las instancias existentes de un conjunto de disponibilidad a un conjunto de escalado de máquinas virtuales. Sin embargo, hay varias estrategias que puede usar para migrar las instancias existentes a un conjunto de escalado flexible: 

### <a name="bluegreen-or-side-by-side-migration"></a>Migración azul/verde o en paralelo 

1. Muestre nuevas instancias de conjuntos de escalado de máquinas virtuales con una configuración similar en el mismo grupo de recursos, red virtual, equilibrador de carga, etc., que las máquinas virtuales del conjunto de disponibilidad. 
1. Migre datos, tráfico de red, etc. para usar las nuevas instancias del conjunto de escalado. 
1. Desasigne o elimine las máquinas virtuales originales del conjunto de disponibilidad, dejando las máquinas virtuales del conjunto de escalado en ejecución para la aplicación.

### <a name="replace-vm-instances"></a>Sustitución de instancias de máquina virtual 

1. Tenga en cuenta los parámetros que desea conservar de la máquina virtual (nombre, identificador de NIC, identificadores de disco de sistema operativo y de datos, valores de configuración de la máquina virtual, ubicación de dominio de error, etc.). 
1. Elimine la máquina virtual del conjunto de disponibilidad. Las NIC y los discos de la máquina virtual no se eliminarán.  
1. Cree un nuevo objeto de máquina virtual con los parámetros de la máquina virtual original. 
    - Identificador de NIC 
    - Discos del sistema operativo y de datos 
    - Selección de dominio de error 
    - Otras configuraciones de máquina virtual 


## <a name="update-uniform-scale-sets-deployment-templates-and-scripts"></a>Actualización de plantillas y scripts de implementación de conjuntos de escalado uniformes

Actualice los scripts y plantillas de implementación de los conjuntos de escalado de máquinas virtuales uniformes para usar la orquestación flexible. Cambie los siguientes elementos en las plantillas para completar correctamente el proceso. 

- Elimine `LoadBalancerNATPool` (no válido para flex). 
- Elimine el parámetro de sobreaprovisionamiento (no válido para flex). 
- Elimine `upgradePolicy` (no válido para flex, aún). 
- Actualice la versión de la API de proceso a la **2021-03-01**. 
- Agregue el modo de orquestación `flexible`. 
- Se requiere `platformFaultDomainCount` 
- Se requiere `singlePlacementGroup`=false 
- Actualice la versión de la API de red a la **2021-11-01** u otra superior. 
- Establezca el valor `configuration.properties.primary` de IP en *true* (obligatorio para las reglas de salida).


## <a name="migrate-existing-uniform-scale-sets"></a>Migración de conjuntos de escalado existentes uniformes 

Actualmente no hay ninguna herramienta automatizada para mover directamente las instancias existentes o actualizar un conjunto de escalado uniforme a un conjunto de escalado de máquinas virtuales flexible. Sin embargo, esta es una estrategia que puede usar para migrar las instancias existentes a un conjunto de escalado flexible:

### <a name="bluegreen-or-side-by-side-migration"></a>Migración azul/verde o en paralelo 

1. Muestre un nuevo conjunto de escalado del modo de orquestación flexible con una configuración similar en el mismo grupo de recursos, red virtual, equilibrador de carga, etc., que el conjunto de escalado original del modo de orquestación uniforme
1. Migre datos, tráfico de red, etc. para usar las nuevas instancias del conjunto de escalado. 
1. Reduzca verticalmente o elimine las máquinas virtuales originales del conjunto de escalado uniforme, dejando las máquinas virtuales del conjunto de escalado en ejecución para la aplicación.


## <a name="flexible-scale-sets-considerations"></a>Consideraciones sobre los conjuntos de escalado flexibles 

Los conjuntos de escalado de máquinas virtuales con orquestación flexible le permiten combinar la escalabilidad de los [conjuntos de escalado de máquinas virtuales del modo de orquestación uniforme](../virtual-machine-scale-sets/overview.md) con las garantías de disponibilidad regionales de los conjuntos de disponibilidad. Los siguientes son aspectos clave a la hora de decidirse a trabajar con el modo de orquestación flexible. 

### <a name="create-scalable-network-connectivity"></a>Creación de una conectividad de red escalable 
<!-- the following is an important link to use in FLEX documentation to reference this section:
/virtual-machines/flexible-virtual-machine-scale-sets-migration-resources.md#create-scalable-network-connectivity
-->

El comportamiento del acceso de salida de red variará en función de cómo elija crear las máquinas virtuales en el conjunto de escalado. Las **instancias de VM agregadas manualmente** tienen acceso de conectividad de salida predeterminado. Las **instancias de VM creadas implícitamente** no tienen acceso predeterminado. 

Para mejorar la seguridad de red predeterminada, **las instancias de máquina virtual creadas implícitamente a través del perfil de escalado automático no tienen acceso de salida predeterminado**. Para usar conjuntos de escalado de máquinas virtuales con instancias de VM creadas implícitamente, el acceso de salida debe definirse explícitamente mediante uno de los métodos siguientes: 

- En la mayoría de los escenarios, se recomienda [NAT Gateway adjunto a la subred](../virtual-network/nat-gateway/tutorial-create-nat-gateway-portal.md).
- En escenarios con requisitos de seguridad elevados, o si se usa Azure Firewall o Network Virtual Appliance (NVA), puede especificar una ruta definida por el usuario personalizada como próximo salto a través del firewall. 
- Las instancias están en el grupo de back-end de una instancia de Azure Load Balancer de SKU estándar. 
- Adjunte una dirección IP pública a la interfaz de red de la instancia. 

Entre los escenarios comunes que requieren conectividad de salida explícita se incluyen: 

- La activación de una máquina virtual Windows requiere que se haya definido la conectividad de salida desde la instancia de máquina virtual al Servicio de administración de claves (KMS) de activación de Windows. Vea [Solución de problemas de activación de máquinas virtuales Windows](/troubleshoot/azure/virtual-machines/troubleshoot-activation-problems) para obtener más información.  
- Acceda a cuentas de almacenamiento o Key Vault. La conectividad con los servicios de Azure también se puede establecer por medio de [Private Link](../private-link/private-link-overview.md). 
- Actualizaciones de Windows.
- Acceso a los administradores de paquetes de Linux. 

Vea [Acceso de salida predeterminado en Azure](../virtual-network/ip-services/default-outbound-access.md) para obtener más detalles sobre cómo definir la conectividad de salida.

Con las VM de instancia única en las que se crea explícitamente la NIC, se proporciona acceso de salida predeterminado. Los conjuntos de escalado de máquinas virtuales en modo de orquestación uniforme también tienen conectividad de salida predeterminada. 


> [!IMPORTANT]
> Confirme que tiene conectividad de red saliente explícita. Obtenga más información sobre esto en [redes virtuales y máquinas virtuales de Azure](../virtual-network/network-overview.md) y asegúrese de que lleva a cabo los siguientes [procedimientos recomendados](../virtual-network/concepts-and-best-practices.md) de redes de Azure. 


### <a name="assign-fault-domain-during-vm-creation"></a>Asignación de un dominio de error durante la creación de máquinas virtuales
Puede elegir el número de dominios de error para el conjunto de escalado de orquestación flexible. De forma predeterminada, cuando se agrega una máquina virtual a un conjunto de escalado flexible, Azure distribuye uniformemente las instancias entre los dominios de error. Aunque se recomienda dejar de Azure asigne el dominio de error, en los escenarios avanzados o de solución de problemas se puede invalidar este comportamiento predeterminado y especificar el dominio de error en el que se ubicará la instancia.

```azurecli-interactive
az vm create –vmss "myVMSS"  –-platform_fault_domain 1
```

### <a name="instance-naming"></a>Asignación de nombres a instancias
Al crear una máquina virtual y agregarla a un conjunto de escalado flexible, tiene control total sobre los nombres de instancia en las reglas de convención de nomenclatura de Azure. Cuando se agregan automáticamente máquinas virtuales al conjunto de escalado mediante el escalado automático, se proporciona un prefijo y Azure anexa un número único al final del nombre. 

### <a name="list-scale-sets-vm-api-changes"></a>Enumeración de cambios en la API de VM de conjuntos de escalado
Virtual Machine Scale Sets permite enumerar las instancias que pertenecen al conjunto de escalado. Con una orquestación flexible, el comando de máquina virtual list de Virtual Machine Scale Sets proporciona una lista de los identificadores de máquina virtual de los conjuntos de escalado. Después, se puede llamar a los comandos de máquina virtual GET de Virtual Machine Scale Sets para obtener más detalles sobre cómo funciona el conjunto de escalado con la instancia de máquina virtual. Para obtener todos los detalles de la máquina virtual, use los comandos de máquina virtual GET estándar o [Azure Resource Graph](../governance/resource-graph/overview.md).


### <a name="query-instances-for-power-state"></a>Instancias de consulta para el estado de energía
El método preferido es usar Azure Resource Graph para realizar consultas en todas las máquinas virtuales de un conjunto de escalado de máquinas virtuales. Azure Resource Graph proporciona funcionalidades de consulta eficaces para los recursos de Azure a escala en todas las suscripciones.

```
| where type =~ 'Microsoft.Compute/virtualMachines'
| where properties.virtualMachineScaleSet contains "demo"
| extend powerState = properties.extended.instanceView.powerState.code
| project name, resourceGroup, location, powerState
| order by resourceGroup desc, name desc
```

La consulta de recursos con [Azure Resource Graph](../governance/resource-graph/overview.md) es una forma cómoda y eficaz de consultar los recursos de Azure, y minimiza las llamadas API al proveedor de recursos. Azure Resource Graph es una caché eventualmente consistente en la que los recursos nuevos o actualizados puede que no se reflejen hasta 60 segundos. Puede:
- Enumerar máquinas virtuales en un grupo de recursos o suscripción.
- Usar la opción de expansión para recuperar la vista de la instancia (estados de asignación de dominios, de energía y de aprovisionamiento) de todas las máquinas virtuales de su suscripción.
- Use los comandos y la API de VM de Get para obtener una vista de modelo y de instancia para una sola instancia.


### <a name="scale-sets-vm-batch-operations"></a>Operaciones por lotes de máquinas virtuales de conjuntos de escalado
Use los comandos de máquina virtual estándar para iniciar, detener, reiniciar y eliminar instancias, en lugar de las API de VM del conjunto de escalado de máquinas virtuales. Las operaciones por lotes de máquinas virtuales del conjunto de escalado de máquinas virtuales (iniciar todo, detener todo, restablecer la imagen inicial de todo, etc.) no se usan con el modo Orquestación flexible.


### <a name="monitor-application-health"></a>Supervisión del estado de la aplicación
El seguimiento del estado de la aplicación permite que la aplicación proporcione a Azure un latido para determinar si la aplicación está en estado correcto o incorrecto. Azure puede reemplazar automáticamente las instancias de máquina virtual que no sean correctas. En el caso de las instancias del conjunto de escalado flexible, debe instalar y configurar la extensión de estado de la aplicación en la máquina virtual. En el caso de instancias de conjunto de escalado uniforme, puede usar la extensión de estado de la aplicación, o bien medir el estado con un sondeo de estado personalizado de Azure Load Balancer.


### <a name="retrieve-boot-diagnostics-data"></a>Recuperación de datos de diagnósticos de arranque
Use las API y los comandos de máquina virtual estándar para recuperar las capturas de pantalla y los datos de los diagnósticos de arranque de la instancia. Los comandos y las API de diagnóstico de arranque de máquina virtual de Virtual Machine Scale Sets no se usan con las instancias del modo de orquestación flexible.


### <a name="vm-extensions"></a>Extensiones de máquina virtual
Use las extensiones destinadas a las máquinas virtuales estándar, en lugar de las destinadas a instancias del modo de orquestación uniforme.


### <a name="protect-instances-from-delete"></a>Protección de instancias frente a la eliminación

Los conjuntos de escalado de máquinas virtuales en modo de orquestación flexible no tienen actualmente opciones de protección de instancias. Si tiene habilitado el escalado automático en un conjunto de escalado de máquinas virtuales, algunas VM podrían estar en riesgo de eliminación durante el escalado. Si desea proteger determinadas instancias de VM de la eliminación, use el [bloqueo de Azure Resource Manager](../azure-resource-manager/management/lock-resources.md).



## <a name="next-steps"></a>Pasos siguientes
> [!div class="nextstepaction"]
> [Comparación sobre las diferencias de API entre los modos de orquestación uniforme y flexible.](../virtual-machine-scale-sets/orchestration-modes-api-comparison.md)