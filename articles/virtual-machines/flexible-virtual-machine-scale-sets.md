---
title: Orquestación flexible para conjuntos de escalado de máquinas virtuales en Azure
description: Aprenda a usar los modos de orquestación flexible y uniforme para conjuntos de escalado de máquinas virtuales en Azure.
author: fitzgeraldsteele
ms.author: fisteele
ms.topic: how-to
ms.service: virtual-machines
ms.subservice: flexible-scale-sets
ms.date: 08/11/2021
ms.reviewer: jushiman
ms.custom: mimckitt, devx-track-azurecli, vmss-flex
ms.openlocfilehash: bf52db4950fd14e15cbd52d94b2e4ffbb9d225bb
ms.sourcegitcommit: 851b75d0936bc7c2f8ada72834cb2d15779aeb69
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/31/2021
ms.locfileid: "123314555"
---
# <a name="preview-flexible-orchestration-for-virtual-machine-scale-sets-in-azure"></a>Versión preliminar: Orquestación flexible para conjuntos de escalado de máquinas virtuales

**Se aplica a:** :heavy_check_mark: Conjuntos de escalado flexibles

Los conjuntos de escalado de máquinas virtuales con orquestación flexible permiten combinar la escalabilidad de los [conjuntos de escalado de máquinas virtuales](../virtual-machine-scale-sets/overview.md) con las garantías de disponibilidad regionales de los [conjuntos de disponibilidad](availability-set-overview.md).

Los conjuntos de escalado de máquinas virtuales de Azure permiten crear y administrar un grupo de máquinas virtuales con equilibrio de carga. El número de instancias de máquina virtual puede aumentar o disminuir automáticamente según la demanda, o de acuerdo a una programación definida. Los conjuntos de escalado ofrecen las siguientes ventajas principales:
- Facilitan la creación y administración de varias máquinas virtuales
- Proporciona alta disponibilidad y resistencia de aplicaciones mediante la distribución de máquinas virtuales entre dominios de error.
- Permiten a la aplicación escalar automáticamente a medida que cambia la demanda de recursos
- Funciona a gran escala

Con la orquestación flexible, Azure proporciona una experiencia unificada en todo el ecosistema de máquinas virtuales de Azure. La orquestación flexible ofrece garantías de alta disponibilidad (hasta mil máquinas virtuales) mediante la propagación de máquinas virtuales entre dominios de error en una región o en una zona de disponibilidad, lo que permite escalar horizontalmente la aplicación a la vez que se mantiene el aislamiento del dominio de error, algo que es esencial para ejecutar cargas de trabajo basadas en cuórum o con estado, entre las que se incluyen:
- Cargas de trabajo basadas en cuórum
- Bases de datos de código abierto
- Aplicaciones con estado
- Servicios que requieren alta disponibilidad y gran escala
- Servicios que desean combinar tipos de máquina virtual o que aprovechan máquinas virtuales de acceso puntual y a petición conjuntamente
- Aplicaciones del conjunto de disponibilidad existentes

Obtenga más información sobre las diferencias entre conjuntos de escalado uniformes y conjuntos de escalado flexibles en [modos de orquestación](../virtual-machine-scale-sets/virtual-machine-scale-sets-orchestration-modes.md).


> [!IMPORTANT]
> En el modo de orquestación flexible, los conjuntos de escalado de máquinas virtuales se encuentran actualmente en versión preliminar pública. No es necesario ningún procedimiento de participación para usar la funcionalidad de versión preliminar pública que se describe a continuación.
> Esta versión preliminar se ofrece sin contrato de nivel de servicio y no es aconsejable usarla para cargas de trabajo de producción. Es posible que algunas características no sean compatibles o que tengan sus funcionalidades limitadas.
> Para más información, consulte [Términos de uso complementarios de las Versiones Preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


> [!CAUTION]
> El modo de orquestación se define al crear el conjunto de escalado y no se puede cambiar ni actualizar más adelante.


## <a name="register-for-flexible-orchestration-mode"></a>Registro para el modo de orquestación flexible
Para poder implementar conjuntos de escalado de máquinas virtuales en el modo de orquestación flexible, antes es preciso registrar la suscripción en la característica en vista previa (GB). El registro puede tardar varios minutos en terminar. Puede registrarse mediante Azure Portal, ¡Azure PowerShell o la CLI de Azure.

### <a name="azure-portal"></a>Azure portal

Durante el modo de orquestación flexible de versión preliminar de conjuntos de escalado, use la *característica en vista previa* de Azure Portal cuyo vínculo encontrará en los siguientes pasos. 

1. Inicie sesión en Azure Portal en https://preview.portal.azure.com.
1. Vaya a **Suscripciones**.
1. Vaya a la página de detalles de la suscripción en la que quiere crear un conjunto de escalado en modo de orquestación flexible y seleccione el nombre de dicha suscripción.
1. En el menú bajo **Configuración**, seleccione **Características en vista previa**.
1. Seleccione las cuatro características de orquestador que pueden habilitarse: *VMOrchestratorSingleFD*, *VMOrchestratorMultiFD*, *VMScaleSetFlexPreview* y *SkipPublicIpWriteRBACCheckForVMNetworkInterfaceConfigurationsPublicPreview*.
1. Seleccione **Registrar**.

Una vez que se hayan registrado las características para su suscripción, complete el proceso de participación mediante la propagación del cambio en el proveedor de recursos de Compute. 

1. En el menú bajo **Configuración**, seleccione **Proveedores de recursos**.
1. Seleccione `Microsoft.compute`.
1. Seleccione **Volver a registrar**.


### <a name="azure-powershell"></a>Azure PowerShell
Use el cmdlet [Register-AzProviderFeature](/powershell/module/az.resources/register-azproviderfeature) para habilitar la versión preliminar de su suscripción.

```azurepowershell-interactive
Register-AzProviderFeature -FeatureName VMOrchestratorMultiFD -ProviderNamespace Microsoft.Compute `
Register-AzProviderFeature -FeatureName VMOrchestratorSingleFD -ProviderNamespace Microsoft.Compute `
Register-AzProviderFeature -FeatureName VMScaleSetFlexPreview -ProviderNamespace Microsoft.Compute `
Register-AzProviderFeature -FeatureName SkipPublicIpWriteRBACCheckForVMNetworkInterfaceConfigurationsPublicPreview -ProviderNamespace Microsoft.Compute
```

El registro de la característica puede tardar hasta 15 minutos. Para comprobar el estado del registro, siga estos pasos:

```azurepowershell-interactive
Get-AzProviderFeature -FeatureName VMOrchestratorMultiFD -ProviderNamespace Microsoft.Compute
```

### <a name="azure-cli-20"></a>CLI de Azure 2.0
Utilice [az feature register](/cli/azure/feature#az_feature_register) para habilitar la versión preliminar de su suscripción.

```azurecli-interactive
az feature register --namespace Microsoft.Compute --name VMOrchestratorMultiFD
az feature register --namespace microsoft.compute --name VMOrchestratorSingleFD
az feature register --namespace Microsoft.Compute --name VMScaleSetFlexPreview 
az feature register --namespace Microsoft.Compute --name SkipPublicIpWriteRBACCheckForVMNetworkInterfaceConfigurationsPublicPreview
```

El registro de la característica puede tardar hasta 15 minutos. Para comprobar el estado del registro, siga estos pasos:

```azurecli-interactive
az feature show --namespace Microsoft.Compute --name VMOrchestratorMultiFD
```


## <a name="get-started-with-flexible-orchestration-mode"></a>Introducción al modo de orquestación flexible

Empiece a usar el modo de orquestación flexible para los conjuntos de escalado mediante [Azure Portal](flexible-virtual-machine-scale-sets-portal.md), la [CLI de Azure](flexible-virtual-machine-scale-sets-cli.md), [Azure PowerShell](flexible-virtual-machine-scale-sets-powershell.md) o [plantillas de ARM](flexible-virtual-machine-scale-sets-rest-api.md). 


## <a name="add-instances-with-autoscaling-or-manually"></a>Adición de instancias con escalado automático o manualmente
Los conjuntos de escalado de máquinas virtuales con orquestación flexible funcionan como una capa de orquestación fina para administrar varias máquinas virtuales. Hay varias maneras de agregar máquinas virtuales para administrarlas mediante el conjunto de escalado:

- **Número de instancias**

    Al crear el conjunto de escalado con orquestación flexible, defina un perfil de máquina virtual o una plantilla que describa la plantilla que se usará para escalar horizontalmente. A continuación, puede establecer el parámetro de capacidad para aumentar o disminuir el número de instancias de máquina virtual administradas por el conjunto de escalado. 

- **Escalado automático con métricas o programación** 

    Como alternativa, puede configurar reglas de escalado automático para aumentar o disminuir la capacidad en función de las métricas o una programación. Consulte [Use conjuntos de escalado de máquinas virtuales para el escalado automático](..\virtual-machine-scale-sets\virtual-machine-scale-sets-autoscale-overview.md). 

- **Especificación de un conjunto de escalado al crear una máquina virtual**

    Al crear una máquina virtual, puede especificar opcionalmente que se agrega a un conjunto de escalado de máquinas virtuales. Una máquina virtual solo se puede agregar a un conjunto de escalado en el momento de la creación de la máquina virtual.


## <a name="explicit-network-outbound-connectivity-required"></a>Se requiere conectividad de salida de red explícita 

Para mejorar la seguridad de red predeterminada, los conjuntos de escalado de máquinas virtuales con orquestación flexible exigen que las instancias creadas de manera implícita por medio del perfil de escalado automático tengan conectividad de salida definida de forma explícita gracias a alguno de los métodos siguientes: 

- En la mayoría de los escenarios, se recomienda [NAT Gateway adjunto a la subred](../virtual-network/nat-gateway/tutorial-create-nat-gateway-portal.md).
- En escenarios con requisitos de seguridad elevados, o si se usa Azure Firewall o Network Virtual Appliance (NVA), puede especificar una ruta definida por el usuario personalizada como próximo salto a través del firewall. 
- Las instancias están en el grupo de back-end de una instancia de Azure Load Balancer de SKU estándar. 
- Adjunte una dirección IP pública a la interfaz de red de la instancia. 

Con máquinas virtuales de instancia única y conjuntos de escalado de máquinas virtuales con orquestación uniforme, la conectividad de salida se proporciona automáticamente. 

Entre los escenarios comunes que requieren conectividad de salida explícita se incluyen: 

- La activación de una máquina virtual Windows requiere que se haya definido la conectividad de salida desde la instancia de máquina virtual al Servicio de administración de claves (KMS) de activación de Windows. Vea [Solución de problemas de activación de máquinas virtuales Windows](https://docs.microsoft.com/troubleshoot/azure/virtual-machines/troubleshoot-activation-problems) para obtener más información.  
- Acceda a cuentas de almacenamiento o Key Vault. La conectividad con los servicios de Azure también se puede establecer por medio de [Private Link](../private-link/private-link-overview.md). 

Vea [Acceso de salida predeterminado en Azure](https://aka.ms/defaultoutboundaccess) para obtener más detalles sobre cómo definir conexiones de salida seguras.


## <a name="assign-fault-domain-during-vm-creation"></a>Asignación de un dominio de error durante la creación de máquinas virtuales
Puede elegir el número de dominios de error para el conjunto de escalado de orquestación flexible. De forma predeterminada, cuando se agrega una máquina virtual a un conjunto de escalado flexible, Azure distribuye uniformemente las instancias entre los dominios de error. Aunque se recomienda dejar de Azure asigne el dominio de error, en los escenarios avanzados o de solución de problemas se puede invalidar este comportamiento predeterminado y especificar el dominio de error en el que se ubicará la instancia.

```azurecli-interactive
az vm create –vmss "myVMSS"  –-platform_fault_domain 1
```

## <a name="instance-naming"></a>Asignación de nombres a instancias
Al crear una máquina virtual y agregarla a un conjunto de escalado flexible, tiene control total sobre los nombres de instancia en las reglas de convención de nomenclatura de Azure. Cuando se agregan automáticamente máquinas virtuales al conjunto de escalado mediante el escalado automático, se proporciona un prefijo y Azure anexa un número único al final del nombre. 

## <a name="list-scale-sets-vm-api-changes"></a>Enumeración de cambios en la API de VM de conjuntos de escalado
Virtual Machine Scale Sets permite enumerar las instancias que pertenecen al conjunto de escalado. Con una orquestación flexible, el comando de máquina virtual list de Virtual Machine Scale Sets proporciona una lista de los identificadores de máquina virtual de los conjuntos de escalado. Después, se puede llamar a los comandos de máquina virtual GET de Virtual Machine Scale Sets para obtener más detalles sobre cómo funciona el conjunto de escalado con la instancia de máquina virtual. Para obtener todos los detalles de la máquina virtual, use los comandos de máquina virtual GET estándar o [Azure Resource Graph](../governance/resource-graph/overview.md).


## <a name="query-instances-for-power-state"></a>Instancias de consulta para el estado de energía
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


## <a name="scale-sets-vm-batch-operations"></a>Operaciones por lotes de máquinas virtuales de conjuntos de escalado
Use los comandos de máquina virtual estándar para iniciar, detener, reiniciar y eliminar instancias, en lugar de las API de VM del conjunto de escalado de máquinas virtuales. Las operaciones por lotes de máquinas virtuales del conjunto de escalado de máquinas virtuales (iniciar todo, detener todo, restablecer la imagen inicial de todo, etc.) no se usan con el modo Orquestación flexible.


## <a name="monitor-application-health"></a>Supervisión del estado de la aplicación
El seguimiento del estado de la aplicación permite que la aplicación proporcione a Azure un latido para determinar si la aplicación está en estado correcto o incorrecto. Azure puede reemplazar automáticamente las instancias de máquina virtual que no sean correctas. En el caso de las instancias del conjunto de escalado flexible, debe instalar y configurar la extensión de estado de la aplicación en la máquina virtual. En el caso de instancias de conjunto de escalado uniforme, puede usar la extensión de estado de la aplicación, o bien medir el estado con un sondeo de estado personalizado de Azure Load Balancer.


## <a name="retrieve-boot-diagnostics-data"></a>Recuperación de datos de diagnósticos de arranque
Use las API y los comandos de máquina virtual estándar para recuperar las capturas de pantalla y los datos de los diagnósticos de arranque de la instancia. Los comandos y las API de diagnóstico de arranque de máquina virtual de Virtual Machine Scale Sets no se usan con las instancias del modo de orquestación flexible.


## <a name="vm-extensions"></a>Extensiones de máquina virtual
Use las extensiones destinadas a las máquinas virtuales estándar, en lugar de las destinadas a instancias del modo de orquestación uniforme.


## <a name="features"></a>Características
En la tabla siguiente se enumeran las características del modo de orquestación flexible y los vínculos a la documentación adecuada.

| Característica | Compatible con la orquestación flexible (versión preliminar) |
|-|-|
| Tipo de máquina virtual | Máquina virtual de IaaS de Azure estándar (Microsoft.compute/virtualmachines) |
| Recuento máximo de instancias (con garantía de disponibilidad de FD) | 1000 |
| SKU compatibles | Serie D, serie E, serie F, serie A, serie B, Intel y AMD |
| Zonas de disponibilidad | Opcionalmente, especifique todas las instancias que aterrizan en una sola zona de disponibilidad |
| Dominio de error: propagación máxima (Azure propagará al máximo las instancias) | Sí |
| Dominio de error: propagación fija | 2-3 FD (dependiendo del recuento máximo de FD regional), 1 FD para implementaciones zonales |
| Dominios de actualización | En desuso, mantenimiento de la plataforma realizado por FD |
| SLA de disponibilidad | Ninguno (durante la versión preliminar) |
| Control total sobre máquina virtual, tarjetas de interfaz de red y discos | Sí |
| Asignación de una máquina virtual a un dominio de error específico | Sí |
| Redes aceleradas | No (durante la versión preliminar) |
| Actualizaciones de seguridad en el invitado | Sí |
| Precios e instancias de acceso puntual  | Sí, puede tener instancias de prioridad regular y de acceso puntual |
| Combinación de sistemas operativos | Sí, Linux y Windows pueden residir en el mismo conjunto de escalado flexible |
| Supervisión del estado de la aplicación | Extensión del estado de la aplicación |
| Discos UltraSSD  | Sí |
| Grupos de selección de ubicación de proximidad  | Sí, lea la [documentación de los grupos de selección de ubicación de proximidad](../virtual-machine-scale-sets/proximity-placement-groups.md). |
| SKU estándar para Azure Load Balancer | Sí |
| Enumeración de las máquinas virtuales en el conjunto | Sí |
| Azure Backup | Sí |
| Notificaciones de terminación (conjuntos de escalado de máquina virtual) | Sí, lea la [documentación de los grupos de notificaciones de terminación](../virtual-machine-scale-sets/virtual-machine-scale-sets-terminate-notification.md). |
| Reparación de instancias (conjuntos de escalado de máquina virtual) | Sí, lea la [documentación de la reparación de instancias](../virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-instance-repairs.md). |
| Escalado automático | Sí |
| Eliminación de tarjetas de interfaz de red y discos cuando se eliminan instancias de máquina virtual | Sí |
| Directiva de actualización (conjuntos de escalado de máquinas virtuales) | No |
| Actualizaciones automáticas del sistema operativo basadas en imágenes | No |
| Infiniband  | No |
| Acelerador de escritura  | No |
| Hosts dedicados de Azure  | No |
| SLB básico  | No |
| Application Gateway | Sí |
| Control de mantenimiento  | No |
| Alertas de Azure | No |
| VM Insights | No |
| Azure Site Recovery |  Si (mediante PowerShell) |
| Service Fabric | No |
| Azure Kubernetes Service (AKS) | No |
| Trusted Virtual Machines (versión preliminar) | Sí |
| Actualizaciones automáticas de extensión | Sí |


### <a name="unsupported-parameters"></a>Parámetros no admitidos

Los siguientes parámetros del conjunto de escalado de máquinas virtuales no se admiten actualmente durante la versión preliminar pública de orquestación flexible del conjunto de escalado de máquinas virtuales:
- Grupo de selección de ubicación único: debe elegir `singlePlacementGroup=False`.
- Implementación de varias zonas: las implementaciones pueden ser regionales o todas las máquinas virtuales de una sola zona.
- Implementación mediante SKU de especialización: familias de máquinas virtuales de las series G, H, L, M, N
- Sobreaprovisionamiento de VMSS
- Actualizaciones automáticas del sistema operativo basadas en imágenes
- Estado de la aplicación a través del sondeo de estado de SLB: use la extensión de estado de la aplicación en las instancias
- Directiva de actualización del conjunto de escalado de máquinas virtuales: debe ser NULL o estar vacía
- Implementación en Azure Dedicated Host
- Discos no administrados
- Directiva de escalado horizontal del conjunto de escalado de máquinas virtuales
- Protección de instancias del conjunto de escalado de máquinas virtuales
- Redes aceleradas
- Versión Básico de Load Balancer
- Reenvío de puertos Standard Load Balancer grupo NAT: puede configurar reglas NAT para instancias específicas


## <a name="troubleshoot-scale-sets-with-flexible-orchestration"></a>Solución de problemas de conjuntos de escalado con orquestación flexible
Busque la solución adecuada para su escenario de solución de problemas.

<!-- error -->
### <a name="invalidparameter-parameter-virtualmachineprofile-is-not-allowed"></a>InvalidParameter. No se permite el parámetro "virtualMachineProfile".

```
InvalidParameter. Parameter 'virtualMachineProfile' is not allowed.
```

**Causa:** la suscripción no está registrada para la versión preliminar pública del modo de orquestación flexible.

**Solución**: debe registrar la característica VMScaleSetPublicPreview para su suscripción. Siga las instrucciones que encontrará más arriba para registrar la versión preliminar pública del modo de orquestación flexible.


<!-- error -->
### <a name="badrequest-creating-a-virtual-machine-with-a-public-ip-address-via-networkinterfaceconfigurations"></a>BadRequest. Creación de una máquina virtual con una dirección IP pública mediante NetworkInterfaceConfigurations

```
BadRequest. Creating a Virtual Machine with a Public IP Address via NetworkInterfaceConfigurations during the Public Preview of VM NetworkInterfaceConfigurations initially requires the feature 'Microsoft.Compute/SkipPublicIpWriteRBACCheckForVMNetworkInterfaceConfigurationsPublicPreview'.
```

**Causa:** la suscripción no está registrada para la versión preliminar pública del modo de orquestación flexible.

**Solución**: Registre la característica `SkipPublicIpWriteRBACCheckForVMNetworkInterfaceConfigurationsPublicPreview` de la suscripción. Siga las instrucciones que encontrará más arriba para registrar la versión preliminar pública del modo de orquestación flexible.


<!-- error -->
### <a name="invalidparameter-the-value-false-of-parameter-singleplacementgroup-is-not-allowed-allowed-values-are-true"></a>InvalidParameter. No se permite el valor "False" del parámetro "singlePlacementGroup". Los valores permitidos son: True

```
InvalidParameter. The value 'False' of parameter 'singlePlacementGroup' is not allowed. Allowed values are: True
```

**Causa:** la suscripción no está registrada para la versión preliminar pública del modo de orquestación flexible.

**Solución:** siga las instrucciones que encontrará más arriba para registrar la versión preliminar pública del modo de orquestación flexible.


<!-- error -->
### <a name="invalidparameter-the-specified-fault-domain-count-2-must-fall-in-the-range-1-to-1"></a>InvalidParameter. El número de dominios de error especificado, 2, debe estar en el intervalo entre 1 y1.

```
InvalidParameter. The specified fault domain count 2 must fall in the range 1 to 1.
```

**Causa:** el parámetro `platformFaultDomainCount` no es válido para la región o zona seleccionadas.

**Solución:** Debe seleccionar un valor de `platformFaultDomainCount` válido. En el caso de las implementaciones zonales, el valor máximo de `platformFaultDomainCount` es 1. En el caso de las implementaciones regionales en las que no se especifica zona, el valor máximo de `platformFaultDomainCount` varía en función de la región. Consulte [Administración de la disponibilidad de las máquinas virtuales para los scripts](../virtual-machines/availability.md) para determinar el número máximo de dominios de error por región.


<!-- error -->
### <a name="operationnotallowed-deletion-of-virtual-machine-scale-set-is-not-allowed-as-it-contains-one-or-more-vms-please-delete-or-detach-the-vms-before-deleting-the-virtual-machine-scale-set"></a>OperationNotAllowed. No se permite la eliminación del conjunto de escalado de máquinas virtuales, ya que contiene una o varias máquinas virtuales. Elimine o desasocie las máquinas virtuales antes de eliminar el conjunto de escalado de máquinas virtuales.

```
OperationNotAllowed. Deletion of Virtual Machine Scale Set is not allowed as it contains one or more VMs. Please delete or detach the VM(s) before deleting the Virtual Machine Scale Set.
```

**Causa:** se intentando eliminar un conjunto de escalado en modo de orquestación flexible que está asociado a una o varias máquinas virtuales.

**Solución:** elimine todas las máquinas virtuales asociadas al conjunto de escalado en el modo de orquestación flexible y, después, podrá eliminar el conjunto de escalado.


<!-- error -->
### <a name="invalidparameter-the-value-true-of-parameter-singleplacementgroup-is-not-allowed-allowed-values-are-false"></a>InvalidParameter. No se permite el valor "True" del parámetro "singlePlacementGroup". Los valores permitidos son: False.

```
InvalidParameter. The value 'True' of parameter 'singlePlacementGroup' is not allowed. Allowed values are: False.
```
**Causa:** la suscripción está registrada para la versión preliminar del modo de orquestación flexible; sin embargo, el parámetro `singlePlacementGroup` se establece en *True*.

**Solución:** el valor de `singlePlacementGroup` se debe establecer en *False*.


## <a name="next-steps"></a>Pasos siguientes
> [!div class="nextstepaction"]
> [Modo de orquestación flexible para los conjuntos de escalado con Azure Portal.](flexible-virtual-machine-scale-sets-portal.md)
