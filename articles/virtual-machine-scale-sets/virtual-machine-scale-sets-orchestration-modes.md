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
ms.openlocfilehash: eac0244393bb3fe8ef2291d27e9dab33563523b2
ms.sourcegitcommit: 01dcf169b71589228d615e3cb49ae284e3e058cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/19/2021
ms.locfileid: "130166727"
---
# <a name="orchestration-modes-for-virtual-machine-scale-sets-in-azure"></a>Modos de orquestación para los conjuntos de escalado de máquinas virtuales de Azure


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


## <a name="what-has-changed-with-flexible-orchestration-mode"></a>¿Qué ha cambiado con el modo de orquestación flexible?
Una de las principales ventajas de la orquestación flexible es que proporciona características de orquestación a través de máquinas virtuales de IaaS de Azure estándar, en lugar de máquinas virtuales secundarias del conjunto de escalado, lo que significa que puede usar todas las API de máquina virtual estándar al administrar instancias de orquestación flexible, en lugar de las API de VM del conjunto de escalado de máquinas virtuales que se usan con la orquestación uniforme. Hay varias diferencias entre administrar instancias en la orquestación flexible y hacerlo en la orquestación uniforme. En general, se recomienda usar las API de VM de IaaS de Azure estándar siempre que sea posible. En esta sección, resaltamos ejemplos de prácticas recomendadas para la administración de instancias de máquina virtual con una orquestación flexible.

### <a name="scale-out-with-standard-azure-virtual-machines"></a>Escalado horizontal con máquinas virtuales de Azure estándar
Los conjuntos de escalado de máquinas virtuales en modo de orquestación flexible administran máquinas virtuales de Azure estándar. Tiene control total sobre el ciclo de vida de la máquina virtual, así como sobre las interfaces de red y los discos mediante las API y comandos de Azure estándar. Las máquinas virtuales creadas con el modo de orquestación uniforme se exponen y administran a través de los comandos de la API de máquina virtual del conjunto de escalado de máquinas virtuales. Las instancias individuales no son compatibles con los comandos estándar de la API de máquina virtual de VM de IaaS de Azure, las características de administración de Azure, como los permisos de RBAC de etiquetado de recursos de Azure Resource Manager, Azure Backup o Azure Site Recovery.

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

### <a name="basic-setup"></a>Configuración básica
| Característica | Compatible con la orquestación flexible para conjuntos de escalado | Compatible con la orquestación uniforme para conjuntos de escalado | Compatible con los conjuntos de disponibilidad |
|---|---|---|---|
| Tipo de máquina virtual  | Máquina virtual de IaaS de Azure estándar (Microsoft.compute/virtualmachines)  | Máquinas virtuales específicas de conjunto de escalado (Microsoft.compute /virtualmachinescalesets/virtualmachines)  | Máquina virtual de IaaS de Azure estándar (Microsoft.compute/virtualmachines) |
| Recuento máximo de instancias (con garantías de FD)  | 1000  | 100  | 200 |
| SKU compatibles  | Serie D, serie E, serie F, serie A, serie B, Intel, AMD. Las SKU de especialización (G, H, L, M, N) no se admiten | Todos los SKU  | Todos los SKU |
| Control total sobre máquina virtual, tarjetas de interfaz de red y discos  | Sí  | Control limitado con la API de VM de conjuntos de escalado de máquinas virtuales  | Sí  |
| Permisos de RBAC necesarios  | Escritura de conjuntos de escalado de máquinas virtuales de proceso, escritura de máquina virtual de proceso, red | Escritura de conjuntos de escalado de máquinas virtuales de proceso  | N/D |
| Redes aceleradas  | Sí  | Sí  | Sí |
| Precios e instancias de acceso puntual   | Sí, puede tener instancias de prioridad regular y de acceso puntual  | Sí, las instancias deben ser todas regulares o de acceso puntual  | No, solo instancias de prioridad regular |
| Combinación de sistemas operativos  | Sí, Linux y Windows pueden residir en el mismo conjunto de escalado flexible  | No, las instancias son el mismo sistema operativo  | Sí, Linux y Windows pueden residir en el mismo conjunto de escalado flexible |
| Tipos de disco  | Solo discos administrados, todos los tipos de almacenamiento  | Discos administrados y no administrados, todos los tipos de almacenamiento  | Discos administrados y no administrados, no se admiten discos Ultra |
| Acelerador de escritura   | No  | Sí  | Sí |
| Grupos de selección de ubicación de proximidad   | Sí, lea la [documentación de los grupos de selección de ubicación de proximidad](../virtual-machine-scale-sets/proximity-placement-groups.md). | Sí, lea la [documentación de los grupos de selección de ubicación de proximidad](../virtual-machine-scale-sets/proximity-placement-groups.md). | Sí |
| Hosts dedicados de Azure   | No  | Sí  | Sí |
| Identidad administrada  | Solo con identidad asignada por el usuario  | Asignada por el sistema o por el usuario  | N/D (puede especificar una identidad administrada en instancias individuales) |
| Agregar una máquina virtual existente al grupo o eliminarla de él  | No  | No  | No |
| Service Fabric  | No  | Sí  | No |
| Azure Kubernetes Service (AKS) / AKE / grupo de nodos k8s  | No  | Sí  | No |
| UserData  | Parcial, UserData se puede especificar para máquinas virtuales individuales | Sí  | UserData se puede especificar para máquinas virtuales individuales |


### <a name="autoscaling-and-instance-orchestration"></a>Escalado automático y orquestación de instancias
| Característica | Compatible con la orquestación flexible para conjuntos de escalado | Compatible con la orquestación uniforme para conjuntos de escalado | Compatible con los conjuntos de disponibilidad |
|---|---|---|---|
| Enumeración de las máquinas virtuales en el conjunto | Sí | Sí | Sí, enumerar las máquinas virtuales en el conjunto de disponibilidad |
| Escalado automático (manual, basado en las métricas, basado en la programación) | Sí | Sí | No |
| Eliminación automática de tarjetas de interfaz de red y discos cuando se eliminan instancias de máquina virtual | Sí | Sí | No |
| Directiva de actualización (conjuntos de escalado de máquinas virtuales) | No, la directiva de actualización debe ser NULL o [] durante la creación | Automático, gradual, manual | N/D |
| Actualizaciones automáticas del sistema operativo (conjuntos de escalado de máquinas virtuales) | No | Sí | N/D |
| Actualizaciones de seguridad en el invitado | Sí | No | Sí |
| Notificaciones de terminación (conjuntos de escalado de máquina virtual) | Sí, lea la [documentación de los grupos de notificaciones de terminación](../virtual-machine-scale-sets/virtual-machine-scale-sets-terminate-notification.md). | Sí, lea la [documentación de los grupos de notificaciones de terminación](../virtual-machine-scale-sets/virtual-machine-scale-sets-terminate-notification.md). | N/D |
| Supervisión del estado de la aplicación | Extensión del estado de la aplicación | Sondeo de la extensión del estado de la aplicación o de Azure Load Balancer | Extensión del estado de la aplicación |
| Reparación de instancias (conjuntos de escalado de máquina virtual) | Sí, lea la [documentación de la reparación de instancias](../virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-instance-repairs.md). | Sí, lea la [documentación de la reparación de instancias](../virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-instance-repairs.md). | N/D |
| Protección de instancias | No, use el [bloqueo de recursos de Azure](../azure-resource-manager/management/lock-resources.md). | Sí | No |
| Directiva de reducción horizontal | No | Sí | No |
| Obtención de vistas de instancias de VMSS | No | Sí | N/D |
| Operaciones por lotes de máquinas virtuales (Iniciar todo, Detener todo, Eliminar subconjunto, etc.) | No (puede desencadenar operaciones en cada instancia mediante la API de máquina virtual) | Sí | No |

### <a name="high-availability"></a>Alta disponibilidad 

| Característica | Compatible con la orquestación flexible para conjuntos de escalado | Compatible con la orquestación uniforme para conjuntos de escalado | Compatible con los conjuntos de disponibilidad |
|---|---|---|---|
| SLA de disponibilidad | 99,95 % para instancias distribuidas entre dominios de error; 99,99 % para instancias distribuidas entre varias zonas | 99,95 % para FD>1 en un único grupo de selección de ubicación; 99,99 % para instancias distribuidas entre varias zonas | 99,95% |
| Zonas de disponibilidad | Especifique las instancias que aterrizan en las zonas de disponibilidad en 1, 2 o 3 | Especifique las instancias que aterrizan en las zonas de disponibilidad en 1, 2 o 3 | No compatible |
| Asignación de una máquina virtual a una zona de disponibilidad específica | Sí | No | No |
| Dominio de error: propagación máxima (Azure propagará al máximo las instancias) | Sí | Sí | No |
| Dominio de error: propagación fija | 2-3 FD (dependiendo del recuento máximo de FD regional), 1 FD para implementaciones zonales | 2, 3 o 5 FD; 1 o 5 para implementaciones zonales | 2 o 3 FD (en función del máximo regional) |
| Asignación de una máquina virtual a un dominio de error específico | Sí | No | No |
| Dominios de actualización | En desuso (el mantenimiento de la plataforma se realiza en los FD, de uno en uno). | 5 dominios de actualización | Hasta 20 dominios de actualización |
| Realizar mantenimiento | Desencadenamiento del mantenimiento en cada instancia mediante la API de máquina virtual | Sí | N/D |

### <a name="networking"></a>Redes 

| Característica | Compatible con la orquestación flexible para conjuntos de escalado | Compatible con la orquestación uniforme para conjuntos de escalado | Compatible con los conjuntos de disponibilidad |
|---|---|---|---|
| Conectividad de salida predeterminada | No, debe tener una [conectividad de salida explícita](../virtual-network/ip-services/default-outbound-access.md). | Sí | Sí |
| SKU estándar para Azure Load Balancer | Sí | Sí | Sí |
| Application Gateway | Sí | Sí | Sí |
| Redes InfiniBand | No | Sí, solo grupo de selección de ubicación único | Sí |
| SLB básico | No | Sí | Sí |
| Reenvío de puertos de red | Sí (reglas NAT para instancias individuales) | Sí (grupo NAT) | Sí (reglas NAT para instancias individuales) |

### <a name="backup-and-recovery"></a>Copia de seguridad y recuperación 

| Característica | Compatible con la orquestación flexible para conjuntos de escalado | Compatible con la orquestación uniforme para conjuntos de escalado | Compatible con los conjuntos de disponibilidad |
|---|---|---|---|
| Azure Backup  | Sí | No | Sí |
| Azure Site Recovery | Sí (mediante PowerShell) | No | Sí |
| Alertas de Azure  | Sí | Sí | Sí |
| VM Insights  | Se puede instalar en máquinas virtuales individuales | Sí | Sí |


## <a name="get-started-with-flexible-orchestration-mode"></a>Introducción al modo de orquestación flexible

Registre y comience a usar el [modo de orquestación flexible](..\virtual-machines\flexible-virtual-machine-scale-sets.md) para el conjunto de escalado de su máquina virtual. 


## <a name="frequently-asked-questions"></a>Preguntas más frecuentes

- **¿Cuánta escala admite la orquestación flexible?**

    En el modo de orquestación flexible se pueden agregar un máximo de 1000 máquinas virtuales a un conjunto de escalado.

- **Diferencias y similitudes de la orquestación flexible con los conjuntos de disponibilidad o la orquestación uniforme**

    | Atributo de disponibilidad  | Orquestación flexible  | Orquestación uniforme  | Conjuntos de disponibilidad  |
    |-|-|-|-|
    | Implementación en zonas de disponibilidad  | Sí  | Sí  | No  |
    | Garantía de disponibilidad de dominios de error en una región  | Se pueden distribuir hasta 1000 instancias en un máximo de 3 dominios de error en la región. El número máximo de dominios de error varía en función de la región  | Sí, hasta 100 instancias.  | Sí, hasta 200 instancias.  |
    | Grupos de selección de ubicación  | El modo flexible siempre usa varios grupos de selección de ubicación (singlePlacementGroup = false)  | Puede elegir entre un grupo de selección de ubicación único o varios grupos de selección de ubicación | N/D  |
    | Dominios de actualización  | Ninguno, el mantenimiento o las actualizaciones del host se realizan por cada dominio de error individual  | Hasta 5 dominios de actualización  | Hasta 20 dominios de actualización  |

- **¿Cuál es el recuento máximo absoluto de instancias con disponibilidad de dominio de error garantizada?**

    | Característica  | Compatible con la orquestación flexible  | Compatible con la orquestación uniforme (disponibilidad general)  | Compatible con conjuntos de disponibilidad (disponibilidad general)  |
    |-|-|-|-|
    | Recuento máximo de instancias (con garantía de disponibilidad de FD)  | 1000  | 3000  | 200  |


## <a name="next-steps"></a>Pasos siguientes
> [!div class="nextstepaction"]
> [Aprenda a implementar una aplicación en un conjunto de escalado](virtual-machine-scale-sets-deploy-app.md).
