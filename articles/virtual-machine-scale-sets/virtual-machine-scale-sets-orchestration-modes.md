---
title: Modos de orquestación para los conjuntos de escalado de máquinas virtuales de Azure
description: Aprenda a usar los modos de orquestación flexible y uniforme para conjuntos de escalado de máquinas virtuales en Azure.
author: fitzgeraldsteele
ms.author: fisteele
ms.topic: conceptual
ms.service: virtual-machine-scale-sets
ms.date: 08/05/2021
ms.reviewer: jushiman
ms.custom: mimckitt, devx-track-azurecli, vmss-flex, devx-track-azurepowershell
ms.openlocfilehash: 7983ae912d29f2a27d35b261d1654205fe503651
ms.sourcegitcommit: 851b75d0936bc7c2f8ada72834cb2d15779aeb69
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/31/2021
ms.locfileid: "123305077"
---
# <a name="preview-orchestration-modes-for-virtual-machine-scale-sets-in-azure"></a>Versión preliminar: modos de orquestación para los conjuntos de escalado de máquinas virtuales de Azure


**Se aplica a:** :heavy_check_mark: Máquinas virtuales Linux :heavy_check_mark: Máquinas virtuales Windows :heavy_check_mark: Conjuntos de escalado flexibles :heavy_check_mark: Conjuntos de escalado uniformes

Los conjuntos de escalado de máquinas virtuales proporcionan una agrupación lógica de máquinas virtuales administradas por una plataforma. Con los conjuntos de escalado, se crea un modelo de configuración de máquina virtual, se agregan o quitan automáticamente instancias adicionales en función de la carga de CPU o de memoria y se actualiza automáticamente a la versión más reciente del sistema operativo. Tradicionalmente, los conjuntos de escalado han permitido crear máquinas virtuales con un modelo de configuración de máquina virtual proporcionado en el momento de la creación del conjunto de escalado. Además, el conjunto de escalado solo puede administrar aquellas máquinas virtuales que se creen de forma implícita según el modelo de configuración. 

Los modos de orquestación del conjunto de escalado permiten tener un mayor control sobre la forma en que el conjunto de escalado administra las instancias de máquina virtual.

> [!IMPORTANT]
> El modo de orquestación se define al crear el conjunto de escalado y no se puede cambiar ni actualizar más adelante.


## <a name="scale-sets-with-uniform-orchestration"></a>Conjuntos de escalado con orquestación uniforme
Están optimizados para cargas de trabajo sin estado a gran escala con instancias idénticas.

Los conjuntos de escalado de máquinas virtuales con orquestación uniforme usan un perfil o una plantilla de máquina virtual para realizar un escalado vertical hasta la capacidad deseada. Aunque existe cierta capacidad para administrar o personalizar instancias de máquina virtual individuales, el modo de orquestación uniforme usa instancias de máquina virtual idénticas. Las instancias de máquina virtual individuales del modo uniforme se exponen mediante los comandos de la API de máquina virtual del conjunto de escalado de máquinas virtuales. Las instancias individuales no son compatibles con los comandos estándar de la API de máquina virtual de VM de IaaS de Azure, las características de administración de Azure, como los permisos de RBAC de etiquetado de recursos de Azure Resource Manager, Azure Backup o Azure Site Recovery. La orquestación uniforme proporciona garantías de alta disponibilidad del dominio de error cuando se configura con menos de 100 instancias. La orquestación uniforme está disponible con carácter general y es compatible con una gama completa de administración y orquestación del conjunto de escalado, lo que incluye el escalado automático basado en métricas, la protección de instancias y las actualizaciones automáticas del sistema operativo.


## <a name="scale-sets-with-flexible-orchestration"></a>Conjuntos de escalado con orquestación flexible
Logre una alta disponibilidad a escala con tipos de máquina virtual idénticos o múltiples.

Con la orquestación flexible, Azure proporciona una experiencia unificada en todo el ecosistema de máquinas virtuales de Azure. La orquestación flexible ofrece garantías de alta disponibilidad (hasta mil máquinas virtuales) mediante la propagación de máquinas virtuales entre dominios de error en una región o en una zona de disponibilidad, lo que permite escalar horizontalmente la aplicación a la vez que se mantiene el aislamiento del dominio de error, algo que es esencial para ejecutar cargas de trabajo basadas en cuórum o con estado, entre las que se incluyen:
- Cargas de trabajo basadas en cuórum
- Bases de datos de código abierto
- Aplicaciones con estado
- Servicios que requieren alta disponibilidad y gran escala
- Servicios que desean combinar tipos de máquina virtual o que aprovechan máquinas virtuales de acceso puntual y a petición conjuntamente
- Aplicaciones del conjunto de disponibilidad existentes

> [!IMPORTANT]
> En el modo de orquestación flexible, los conjuntos de escalado de máquinas virtuales se encuentran actualmente en versión preliminar pública. No es necesario ningún procedimiento de participación para usar la funcionalidad de versión preliminar pública que se describe a continuación.
> Esta versión preliminar se ofrece sin contrato de nivel de servicio y no es aconsejable usarla para cargas de trabajo de producción. Es posible que algunas características no sean compatibles o que tengan sus funcionalidades limitadas.
> Para más información, consulte [Términos de uso complementarios de las Versiones Preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


## <a name="what-has-changed-with-flexible-orchestration-mode"></a>¿Qué ha cambiado con el modo de orquestación flexible?
Una de las principales ventajas de la orquestación flexible es que proporciona características de orquestación a través de máquinas virtuales de IaaS de Azure estándar, en lugar de máquinas virtuales secundarias del conjunto de escalado, lo que significa que puede usar todas las API de máquina virtual estándar al administrar instancias de orquestación flexible, en lugar de las API de VM del conjunto de escalado de máquinas virtuales que se usan con la orquestación uniforme. Durante el período de versión preliminar, hay varias diferencias entre administrar instancias en la orquestación flexible y hacerlo en la orquestación uniforme. En general, se recomienda usar las API de VM de IaaS de Azure estándar siempre que sea posible. En esta sección, resaltamos ejemplos de prácticas recomendadas para la administración de instancias de máquina virtual con una orquestación flexible.

### <a name="assign-fault-domain-during-vm-creation"></a>Asignación de un dominio de error durante la creación de máquinas virtuales
Puede elegir el número de dominios de error para el conjunto de escalado de orquestación flexible. De forma predeterminada, cuando se agrega una máquina virtual a un conjunto de escalado flexible, Azure distribuye uniformemente las instancias entre los dominios de error. Aunque se recomienda dejar de Azure asigne el dominio de error, en los escenarios avanzados o de solución de problemas se puede invalidar este comportamiento predeterminado y especificar el dominio de error en el que se ubicará la instancia.

```azurecli-interactive
az vm create –vmss "myVMSS"  –-platform_fault_domain 1
```

### <a name="instance-naming"></a>Asignación de nombres a instancias
Al crear una máquina virtual y agregarla a un conjunto de escalado flexible, tiene control total sobre los nombres de instancia en las reglas de convención de nomenclatura de Azure. Cuando se agregan automáticamente máquinas virtuales al conjunto de escalado mediante el escalado automático, se proporciona un prefijo y Azure anexa un número único al final del nombre.

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

### <a name="list-scale-sets-vm-api-changes"></a>Enumeración de cambios en la API de VM de conjuntos de escalado
Virtual Machine Scale Sets permite enumerar las instancias que pertenecen al conjunto de escalado. Con una orquestación flexible, el comando de máquina virtual list de Virtual Machine Scale Sets proporciona una lista de los identificadores de máquina virtual de los conjuntos de escalado. Después, se puede llamar a los comandos de máquina virtual GET de Virtual Machine Scale Sets para obtener más detalles sobre cómo funciona el conjunto de escalado con la instancia de máquina virtual. Para obtener todos los detalles de la máquina virtual, use los comandos de máquina virtual GET estándar o [Azure Resource Graph](../governance/resource-graph/overview.md).

### <a name="retrieve-boot-diagnostics-data"></a>Recuperación de datos de diagnósticos de arranque
Use las API y los comandos de máquina virtual estándar para recuperar las capturas de pantalla y los datos de los diagnósticos de arranque de la instancia. Los comandos y las API de diagnóstico de arranque de máquina virtual de Virtual Machine Scale Sets no se usan con las instancias del modo de orquestación flexible.

### <a name="vm-extensions"></a>Extensiones de máquina virtual
Use las extensiones destinadas a las máquinas virtuales estándar, en lugar de las destinadas a instancias del modo de orquestación uniforme.


## <a name="a-comparison-of-flexible-uniform-and-availability-sets"></a>Una comparación de conjuntos de disponibilidad, y los modos de orquestación flexible y uniforme
En la tabla siguiente se compara el modo de orquestación flexible, el modo de orquestación uniforme y los conjuntos de disponibilidad por sus características.

| Característica  | Compatible con la orquestación flexible (versión preliminar)  | Compatible con la orquestación uniforme (disponibilidad general)  | Compatible con conjuntos de disponibilidad (disponibilidad general)  |
|-|-|-|-|
| Tipo de máquina virtual  | Máquina virtual de IaaS de Azure estándar (Microsoft.compute/virtualmachines)  | Máquinas virtuales específicas de conjunto de escalado (Microsoft.compute /virtualmachinescalesets/virtualmachines)  | Máquina virtual de IaaS de Azure estándar (Microsoft.compute/virtualmachines)  |
| SKU compatibles  | Serie D, serie E, serie F, serie A, serie B, Intel y AMD  | Todos los SKU  | Todos los SKU  |
| Zonas de disponibilidad  | Opcionalmente, especifique todas las instancias que aterrizan en una sola zona de disponibilidad  | Especifique las instancias que aterrizan en las zonas de disponibilidad en 1, 2 o 3  | No compatible  |
| Dominio de error: propagación máxima (Azure propagará al máximo las instancias)  | Sí  | Sí  | No  |
| Dominio de error: propagación fija  | 2 o 3 FD (en función del máximo regional); 1 para implementaciones zonales  | 2, 3 o 5 FD; 1 o 5 para implementaciones zonales  | 2 o 3 FD (en función del máximo regional)  |
| Dominios de actualización  | En desuso, mantenimiento de la plataforma realizado por FD | 5 dominios de actualización  | Hasta 20 dominios de actualización  |
| SLA de disponibilidad  | No en este momento  | 99,95 % para FD>1 en un único grupo de selección de ubicación; 99,99 % para instancias distribuidas entre varias zonas  | 99,95%  |
| Control total sobre máquina virtual, tarjetas de interfaz de red y discos  | Sí  | Control limitado con la API de VM de conjuntos de escalado de máquinas virtuales  | Sí  |
| Escalado automático (manual, basado en las métricas, basado en la programación)  | Sí  | Sí  | No  |
| Asignación de una máquina virtual a un dominio de error específico  | Sí  | No  | No  |
| Eliminación automática de tarjetas de interfaz de red y discos cuando se eliminan instancias de máquina virtual  | Sí  | Sí  | No  |
| Directiva de actualización (conjuntos de escalado de máquinas virtuales)  | No, la directiva de actualización debe ser NULL o [] durante la creación  | Automático, gradual, manual  | N/D  |
| Actualizaciones automáticas del sistema operativo basadas en imágenes  | No  | Sí  | N/D  |
| Actualizaciones de seguridad en el invitado  | Sí  | No  | Sí  |
| Notificaciones de terminación (conjuntos de escalado de máquina virtual)  | Sí  | Sí  | N/D  |
| Reparación de instancias (conjuntos de escalado de máquina virtual)  | Sí  | Sí  | N/D  |
| Redes aceleradas  | No  | Sí  | Sí  |
| Precios e instancias de acceso puntual   | Sí, puede tener instancias de prioridad regular y de acceso puntual  | Sí, las instancias deben ser todas regulares o de acceso puntual  | No, solo instancias de prioridad regular  |
| Combinación de sistemas operativos  | Sí, Linux y Windows pueden residir en el mismo conjunto de escalado flexible  | No, las instancias son el mismo sistema operativo  | Sí, Linux y Windows pueden residir en el mismo conjunto de escalado flexible  |
| Supervisión del estado de la aplicación  | Extensión del estado de la aplicación  | Sondeo de la extensión del estado de la aplicación o de Azure Load Balancer  | Extensión del estado de la aplicación  |
| Discos UltraSSD   | Sí  | Sí, solo para implementaciones zonales  | No  |
| Infiniband   | No  | Sí, solo grupo de selección de ubicación único  | Sí  |
| Acelerador de escritura   | No  | Sí  | Sí  |
| Grupos de selección de ubicación de proximidad   | Sí  | Sí  | Sí  |
| Hosts dedicados de Azure   | No  | Sí  | Sí  |
| SLB básico   | No  | Sí  | Sí  |
| SKU estándar para Azure Load Balancer  | Sí  | Sí  | Sí  |
| Application Gateway  | Sí  | Sí  | Sí  |
| Control de mantenimiento   | No  | Sí  | Sí  |
| Enumeración de las máquinas virtuales en el conjunto  | Sí  | Sí  | Sí, enumerar las máquinas virtuales en el conjunto de disponibilidad  |
| Alertas de Azure  | No  | Sí  | Sí  |
| VM Insights  | No  | Sí  | Sí  |
| Azure Backup  | Sí  | No  | Sí  |
| Azure Site Recovery  | Sí (mediante PowerShell)  | No  | Sí  |
| Service Fabric  | No  | Sí  | No  |
| Azure Kubernetes Service (AKS)/AKE  | No  | Sí  | No  |


## <a name="get-started-with-flexible-orchestration-mode"></a>Introducción al modo de orquestación flexible

Registre y comience a usar el [modo de orquestación flexible](..\virtual-machines\flexible-virtual-machine-scale-sets.md) para el conjunto de escalado de su máquina virtual. 


## <a name="frequently-asked-questions"></a>Preguntas más frecuentes

- **¿Cuánta escala admite la orquestación flexible?**

    En el modo de orquestación flexible se pueden agregar un máximo de 1000 máquinas virtuales a un conjunto de escalado.

- **Diferencias y similitudes de la orquestación flexible con los conjuntos de disponibilidad o la orquestación uniforme**

    | Atributo de disponibilidad  | Orquestación flexible  | Orquestación uniforme  | Conjuntos de disponibilidad  |
    |-|-|-|-|
    | Implementación en zonas de disponibilidad  | No  | Sí  | No  |
    | Garantía de disponibilidad de dominios de error en una región  | Se pueden distribuir hasta 1000 instancias en un máximo de 3 dominios de error en la región. El número máximo de dominios de error varía en función de la región  | Sí, hasta 100 instancias.  | Sí, hasta 200 instancias.  |
    | Grupos de selección de ubicación  | El modo flexible siempre usa varios grupos de selección de ubicación (singlePlacementGroup = false)  | Puede elegir entre un grupo de selección de ubicación único o varios grupos de selección de ubicación | N/D  |
    | Dominios de actualización  | Ninguno, el mantenimiento o las actualizaciones del host se realizan por cada dominio de error individual  | Hasta 5 dominios de actualización  | Hasta 20 dominios de actualización  |

- **¿Cuál es el recuento máximo absoluto de instancias con disponibilidad de dominio de error garantizada?**

    | Característica  | Compatible con la orquestación flexible (versión preliminar)  | Compatible con la orquestación uniforme (disponibilidad general)  | Compatible con conjuntos de disponibilidad (disponibilidad general)  |
    |-|-|-|-|
    | Recuento máximo de instancias (con garantía de disponibilidad de FD)  | 1000  | 3000  | 200  |


## <a name="troubleshoot-scale-sets-with-flexible-orchestration"></a>Solución de problemas de conjuntos de escalado con orquestación flexible
Busque la solución adecuada para su escenario de solución de problemas.

```
InvalidParameter. The value 'False' of parameter 'singlePlacementGroup' is not allowed. Allowed values are: True
```

**Causa:** la suscripción no está registrada para la versión preliminar pública del modo de orquestación flexible.

**Solución:** siga las instrucciones que encontrará más arriba para registrar la versión preliminar pública del modo de orquestación flexible.

```
InvalidParameter. The specified fault domain count 2 must fall in the range 1 to 1.
```

**Causa:** el parámetro `platformFaultDomainCount` no es válido para la región o zona seleccionadas.

**Solución:** Debe seleccionar un valor de `platformFaultDomainCount` válido. En el caso de las implementaciones zonales, el valor máximo de `platformFaultDomainCount` es 1. En el caso de las implementaciones regionales en las que no se especifica zona, el valor máximo de `platformFaultDomainCount` varía en función de la región. Consulte [Administración de la disponibilidad de las máquinas virtuales para los scripts](../virtual-machines/availability.md) para determinar el número máximo de dominios de error por región.

```
OperationNotAllowed. Deletion of Virtual Machine Scale Set is not allowed as it contains one or more VMs. Please delete or detach the VM(s) before deleting the Virtual Machine Scale Set.
```

**Causa:** se intentando eliminar un conjunto de escalado en modo de orquestación flexible que está asociado a una o varias máquinas virtuales.

**Solución:** elimine todas las máquinas virtuales asociadas al conjunto de escalado en el modo de orquestación flexible y, después, podrá eliminar el conjunto de escalado.

```
InvalidParameter. The value 'True' of parameter 'singlePlacementGroup' is not allowed. Allowed values are: False.
```
**Causa:** la suscripción está registrada para la versión preliminar del modo de orquestación flexible; sin embargo, el parámetro `singlePlacementGroup` se establece en *True*.

**Solución:** el valor de `singlePlacementGroup` se debe establecer en *False*.


## <a name="next-steps"></a>Pasos siguientes
> [!div class="nextstepaction"]
> [Aprenda a implementar una aplicación en un conjunto de escalado](virtual-machine-scale-sets-deploy-app.md).
