---
title: Orquestación flexible para conjuntos de escalado de máquinas virtuales en Azure
description: Aprenda a usar los modos de orquestación flexible y uniforme para conjuntos de escalado de máquinas virtuales en Azure.
author: fitzgeraldsteele
ms.author: fisteele
ms.topic: how-to
ms.service: virtual-machines
ms.subservice: flexible-scale-sets
ms.date: 10/13/2021
ms.reviewer: jushiman
ms.custom: mimckitt, devx-track-azurecli, vmss-flex
ms.openlocfilehash: 3403cebab79b49f3a22353bec0a03b68b5a24f0f
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2021
ms.locfileid: "131432840"
---
# <a name="flexible-orchestration-for-virtual-machine-scale-sets-in-azure"></a>Orquestación flexible para conjuntos de escalado de máquinas virtuales en Azure

**Se aplica a:** :heavy_check_mark: Conjuntos de escalado flexibles

Los conjuntos de escalado de máquinas virtuales con orquestación flexible permiten combinar la escalabilidad de los [conjuntos de escalado de máquinas virtuales del modo de orquestación uniforme](../virtual-machine-scale-sets/overview.md) con las garantías de disponibilidad regionales de los [conjuntos de disponibilidad](availability-set-overview.md).

Los conjuntos de escalado de máquinas virtuales de Azure permiten crear y administrar un grupo de máquinas virtuales con equilibrio de carga. El número de instancias de máquina virtual puede aumentar o disminuir automáticamente según la demanda, o de acuerdo a una programación definida. Los conjuntos de escalado ofrecen las siguientes ventajas principales:
- Facilitan la creación y administración de varias máquinas virtuales
- Proporciona alta disponibilidad y resistencia de aplicaciones mediante la distribución de máquinas virtuales entre zonas de disponibilidad o dominios de error.
- Permiten a la aplicación escalar automáticamente a medida que cambia la demanda de recursos
- Funciona a gran escala

Con la orquestación flexible, Azure proporciona una experiencia unificada en todo el ecosistema de máquinas virtuales de Azure. La orquestación flexible ofrece garantías de alta disponibilidad (hasta mil máquinas virtuales) mediante la propagación de máquinas virtuales entre dominios de error en una región o en una zona de disponibilidad, lo que permite escalar horizontalmente la aplicación a la vez que se mantiene el aislamiento del dominio de error, algo que es esencial para ejecutar cargas de trabajo basadas en cuórum o con estado, entre las que se incluyen:
- Cargas de trabajo basadas en cuórum
- Bases de datos de código abierto
- Aplicaciones con estado
- Servicios que requieren alta disponibilidad y gran escala
- Servicios que desean combinar tipos de máquina virtual o que aprovechan máquinas virtuales de acceso puntual y a petición conjuntamente
- Aplicaciones del conjunto de disponibilidad existentes

> [!IMPORTANT]
> En este artículo se trata sobre los conjuntos de escalado de máquinas virtuales en modo de orquestación flexible, los cuales se recomiendan para todas las nuevas implementaciones de conjuntos de escalado. Para acceder a información sobre conjuntos de escalado uniformes, vaya a la documentación sobre [conjuntos de escalado de máquinas virtuales en modo de orquestación uniforme](../virtual-machine-scale-sets/overview.md).

Obtenga más información sobre las diferencias entre conjuntos de escalado uniformes y conjuntos de escalado flexibles en [modos de orquestación](../virtual-machine-scale-sets/virtual-machine-scale-sets-orchestration-modes.md).

> [!CAUTION]
> El modo de orquestación se define al crear el conjunto de escalado y no se puede cambiar ni actualizar más adelante.


## <a name="why-use-virtual-machine-scale-sets"></a>¿Por qué utilizar conjuntos de escalado de máquinas virtuales?
Para proporcionar redundancia y mejorar el rendimiento las aplicaciones normalmente se distribuyen entre varias instancias. Los clientes pueden acceder a la aplicación mediante un equilibrador de carga que distribuye las solicitudes a una de las instancias de la aplicación. Si necesita realizar tareas de mantenimiento o actualizar una instancia de la aplicación, los clientes tienen que distribuirse a otra instancia de aplicación que esté disponible. Para hacer frente a la demanda adicional de los clientes, puede que tenga que aumentar el número de instancias de aplicación en las que se ejecuta la aplicación.

Los conjuntos de escalado de máquinas virtuales de Azure proporcionan las capacidades de administración para las aplicaciones que se ejecutan en varias máquinas virtuales, el escalado automático de recursosy el equilibrio de carga de tráfico. Los conjuntos de escalado ofrecen las siguientes ventajas principales:

- **Facilitan la creación y administración de varias máquinas virtuales**
    - Cuando tiene muchas máquinas virtuales que ejecutan la aplicación es importante mantener una configuración coherente en todo el entorno. Para obtener un rendimiento confiable de la aplicación, el tamaño de máquina virtual, la configuración de disco y las instalaciones de la aplicación deben coincidir en todas las máquinas virtuales.
    - Con los conjuntos de escalado se crean todas las instancias de máquina virtual desde la misma configuración e imagen base de sistema operativo. Este enfoque le permite administrar fácilmente cientos de máquinas virtuales sin tareas adicionales de configuración o administración de red.
    - Los conjuntos de escalado admiten el uso de [Azure Load Balancer](../load-balancer/load-balancer-overview.md) para la distribución del tráfico de nivel 4 básico, y de [Azure Application Gateway](../application-gateway/overview.md) para las características más avanzadas de distribución de nivel 7 y terminación TLS.

- **Proporcionan alta disponibilidad y resistencia de aplicación**
    - Los conjuntos de escalado se utilizan para ejecutar varias instancias de la aplicación. Si una de estas instancias de máquina virtual tiene un problema, los clientes siguen teniendo acceso a la aplicación a través de una de las otras instancias de máquina virtual con una interrupción mínima.
    - Con el fin de tener mayor disponibilidad, puede usar [zonas de disponibilidad](../availability-zones/az-overview.md) para distribuir automáticamente las instancias de máquina virtual en un conjunto de escalado dentro de un único centro de datos o en varios centros de datos.

- **Permiten a la aplicación escalar automáticamente a medida que cambia la demanda de recursos**
    - La demanda de la aplicación por parte de los clientes puede cambiar a lo largo del día o de la semana. Para cumplir con la demanda del cliente, los conjuntos de escalado pueden aumentar o disminuir automáticamente el número de instancias de máquina virtual a medida que aumenta o disminuye la demanda de la aplicación.
    - El escalado automático reduce el número de instancias innecesarias de máquina virtual que ejecutan la aplicación cuando la demanda es baja, mientras que cuando la demanda aumenta, los clientes continúan recibiendo un nivel aceptable de rendimiento gracias a la incorporación automática de instancias adicionales de máquina virtual. Esta capacidad le ayuda a reducir los costos y a crear de forma eficaz los recursos de Azure según sea necesario.

- **Funciona a gran escala**
    - Los conjuntos de escalado admiten hasta 1000 instancias de máquina virtual para imágenes estándar de Marketplace e imágenes personalizadas a través de Azure Compute Gallery (anteriormente conocida como Shared Image Gallery). Si crea un conjunto de escalado mediante una imagen administrada, el límite es de 600 instancias de máquina virtual.
    - Para el mejor rendimiento con cargas de trabajo de producción, use [Azure Managed Disks](../virtual-machines/managed-disks-overview.md).



## <a name="get-started-with-flexible-orchestration-mode"></a>Introducción al modo de orquestación flexible

Empiece a usar el modo de orquestación flexible para los conjuntos de escalado mediante [Azure Portal](flexible-virtual-machine-scale-sets-portal.md), la [CLI de Azure](flexible-virtual-machine-scale-sets-cli.md), [Azure PowerShell](flexible-virtual-machine-scale-sets-powershell.md) o [plantillas de ARM](flexible-virtual-machine-scale-sets-rest-api.md). 

> [!IMPORTANT]
> Confirme que tiene conectividad de red saliente explícita. Obtenga más información sobre esto en [redes virtuales y máquinas virtuales de Azure](../virtual-network/network-overview.md) y asegúrese de que lleva a cabo los siguientes [procedimientos recomendados](../virtual-network/concepts-and-best-practices.md) de redes de Azure.


## <a name="add-instances-with-autoscaling-or-manually"></a>Adición de instancias con escalado automático o manualmente
Los conjuntos de escalado de máquinas virtuales con orquestación flexible funcionan como una capa de orquestación fina para administrar varias máquinas virtuales. Hay varias maneras de agregar máquinas virtuales para administrarlas mediante el conjunto de escalado:

- **Número de instancias**

    Al crear el conjunto de escalado con orquestación flexible, defina un perfil de máquina virtual o una plantilla que describa la plantilla que se usará para escalar horizontalmente. A continuación, puede establecer el parámetro de capacidad para aumentar o disminuir el número de instancias de máquina virtual administradas por el conjunto de escalado. 

- **Escalado automático con métricas o programación** 

    Como alternativa, puede configurar reglas de escalado automático para aumentar o disminuir la capacidad en función de las métricas o una programación. Consulte [Use conjuntos de escalado de máquinas virtuales para el escalado automático](..\virtual-machine-scale-sets\virtual-machine-scale-sets-autoscale-overview.md). 

- **Especificación de un conjunto de escalado al crear una máquina virtual**

    Al crear una máquina virtual, puede especificar opcionalmente que se agrega a un conjunto de escalado de máquinas virtuales. Una máquina virtual solo se puede agregar a un conjunto de escalado en el momento de la creación de la máquina virtual. La máquina virtual recién creada debe estar en el mismo grupo de recursos que el conjunto de escalado flexible, independientemente de los métodos de implementación.

El modo de orquestación flexible se puede usar con SKU de VM que admiten [actualizaciones de conservación de memoria o migración en vivo](../virtual-machines/maintenance-and-updates.md#maintenance-that-doesnt-require-a-reboot), lo que incluye el 90 % de todas las VM de IaaS que se implementan en Azure. En general, esto incluye familias de tamaños de uso general, como VM de las series B-, D-, E- y F-. Actualmente, el modo Flexible no se puede organizar sobre las SKU de VM o las familias que no admiten actualizaciones que conservan la memoria, incluidas las VM de las series G-, H-, L-, M- y N-. Puede usar la [API de SKU de los recursos del proceso](/rest/api/compute/resource-skus/list) para determinar si se admite una SKU de VM específica.

```azurecli-interactive
az vm list-skus -l eastus --size standard_d2s_v3 --query "[].capabilities[].[name, value]" -o table
```

> [!IMPORTANT]
> El comportamiento de las redes variará en función de cómo elija crear las máquinas virtuales en el conjunto de escalado. Para más información, consulte el artículo sobre [conectividad de red ampliable](../virtual-machines/flexible-virtual-machine-scale-sets-migration-resources.md#create-scalable-network-connectivity).


## <a name="features"></a>Características
En las tablas siguientes se enumeran las características del modo de orquestación flexible y los vínculos a la documentación adecuada.

### <a name="basic-setup"></a>Configuración básica

| Característica | Compatible con la orquestación flexible para conjuntos de escalado |
|---|---|
| Tipo de máquina virtual  | Máquina virtual de IaaS de Azure estándar (Microsoft.compute/virtualmachines)  |
| Recuento máximo de instancias (con garantías de FD)  | 1000  |
| SKU compatibles  | Serie D, serie E, serie F, serie A, serie B, Intel, AMD. Las SKU de especialización (G, H, L, M, N) no se admiten |
| Control total sobre máquina virtual, tarjetas de interfaz de red y discos  | Sí  |
| Permisos de RBAC necesarios  | Escritura de conjuntos de escalado de máquinas virtuales de proceso, escritura de máquina virtual de proceso, red |
| Redes aceleradas  | Sí  |
| Precios e instancias de acceso puntual   | Sí, puede tener instancias de prioridad regular y de acceso puntual  |
| Combinación de sistemas operativos  | Sí, Linux y Windows pueden residir en el mismo conjunto de escalado flexible  |
| Tipos de disco  | Solo discos administrados, todos los tipos de almacenamiento  |
| Acelerador de escritura   | No  |
| Grupos de selección de ubicación de proximidad   | Sí, lea la [documentación de los grupos de selección de ubicación de proximidad](../virtual-machine-scale-sets/proximity-placement-groups.md). |
| Hosts dedicados de Azure   | No  |
| Identidad administrada  | Solo con identidad asignada por el usuario  |
| Agregar una máquina virtual existente al grupo o eliminarla de él  | No  |
| Service Fabric  | No  |
| Azure Kubernetes Service (AKS)/AKE  | No  |
| UserData  | Parcial, UserData se puede especificar para máquinas virtuales individuales |


### <a name="autoscaling-and-instance-orchestration"></a>Escalado automático y orquestación de instancias

| Característica | Compatible con la orquestación flexible para conjuntos de escalado |
|---|---|
| Enumeración de las máquinas virtuales en el conjunto | Sí |
| Escalado automático (manual, basado en las métricas, basado en la programación) | Sí |
| Eliminación automática de tarjetas de interfaz de red y discos cuando se eliminan instancias de máquina virtual | Sí |
| Directiva de actualización (conjuntos de escalado de máquinas virtuales) | No, la directiva de actualización debe ser NULL o [] durante la creación |
| Actualizaciones automáticas del sistema operativo (conjuntos de escalado de máquinas virtuales) | No |
| Actualizaciones de seguridad en el invitado | Sí |
| Notificaciones de terminación (conjuntos de escalado de máquina virtual) | Sí, lea la [documentación de los grupos de notificaciones de terminación](../virtual-machine-scale-sets/virtual-machine-scale-sets-terminate-notification.md). |
| Supervisión del estado de la aplicación | Extensión del estado de la aplicación |
| Reparación de instancias (conjuntos de escalado de máquina virtual) | Sí, lea la [documentación de la reparación de instancias](../virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-instance-repairs.md). |
| Protección de instancias | No, use el [bloqueo de recursos de Azure](../azure-resource-manager/management/lock-resources.md). |
| Directiva de reducción horizontal | No |
| Obtención de vistas de instancias de VMSS | No |
| Operaciones por lotes de máquinas virtuales (Iniciar todo, Detener todo, Eliminar subconjunto, etc.) | No (puede desencadenar operaciones en cada instancia mediante la API de máquina virtual) |

### <a name="high-availability"></a>Alta disponibilidad 

| Característica | Compatible con la orquestación flexible para conjuntos de escalado |
|---|---|
| SLA de disponibilidad | 99,95 % para instancias distribuidas entre dominios de error; 99,99 % para instancias distribuidas entre varias zonas |
| Zonas de disponibilidad | Especifique las instancias que aterrizan en las zonas de disponibilidad en 1, 2 o 3 |
| Asignación de una máquina virtual a una zona de disponibilidad específica | Sí |
| Dominio de error: propagación máxima (Azure propagará al máximo las instancias) | Sí |
| Dominio de error: propagación fija | 2-3 FD (dependiendo del recuento máximo de FD regional), 1 FD para implementaciones zonales |
| Asignación de una máquina virtual a un dominio de error específico | Sí |
| Dominios de actualización | En desuso (el mantenimiento de la plataforma se realiza en los FD, de uno en uno). |
| Realizar mantenimiento | Desencadenamiento del mantenimiento en cada instancia mediante la API de máquina virtual |

### <a name="networking"></a>Redes 

| Característica | Compatible con la orquestación flexible para conjuntos de escalado |
|---|---|
| Conectividad de salida predeterminada | No, debe tener una [conectividad de salida explícita](../virtual-network/ip-services/default-outbound-access.md). |
| SKU estándar para Azure Load Balancer | Sí |
| Application Gateway | Sí |
| Redes InfiniBand | No |
| SLB básico | No |
| Reenvío de puertos de red | Sí (reglas NAT para instancias individuales) |

### <a name="backup-and-recovery"></a>Copia de seguridad y recuperación 

| Característica | Compatible con la orquestación flexible para conjuntos de escalado |
|---|---|
| Azure Backup  | Sí |
| Azure Site Recovery | Sí (mediante PowerShell) |
| Alertas de Azure  | Sí |
| VM Insights  | Se puede instalar en máquinas virtuales individuales |

### <a name="unsupported-parameters"></a>Parámetros no admitidos

Los siguientes parámetros del conjunto de escalado de máquinas virtuales no se admiten actualmente con los conjuntos de escalado de máquinas virtuales del modo de orquestación flexible:
- Grupo de selección de ubicación único: debe elegir `singlePlacementGroup=False`.
- Implementación mediante SKU de especialización: familias de máquinas virtuales de las series G, H, L, M, N
- Configuración del disco Ultra: `diskIOPSReadWrite`, `diskMBpsReadWrite`
- Sobreaprovisionamiento de VMSS
- Actualizaciones automáticas del sistema operativo basadas en imágenes
- Estado de la aplicación a través del sondeo de estado de SLB: use la extensión de estado de la aplicación en las instancias
- Directiva de actualización del conjunto de escalado de máquinas virtuales: debe ser NULL o estar vacía
- Implementación en Azure Dedicated Host
- Discos no administrados
- Directiva de escalado horizontal del conjunto de escalado de máquinas virtuales
- Protección de instancias del conjunto de escalado de máquinas virtuales
- Versión Básico de Load Balancer
- Reenvío de puertos Standard Load Balancer grupo NAT: puede configurar reglas NAT para instancias específicas


## <a name="troubleshoot-scale-sets-with-flexible-orchestration"></a>Solución de problemas de conjuntos de escalado con orquestación flexible
Busque la solución adecuada para su escenario de solución de problemas.

<!-- error -->
### <a name="invalidparameter-the-specified-fault-domain-count-3-must-fall-in-the-range-1-to-2"></a>InvalidParameter. El número de dominios de error especificado, 3, debe estar en el intervalo entre 1 y 2.

```
InvalidParameter. The specified fault domain count 3 must fall in the range 1 to 2.
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
**Causa:** el parámetro `singlePlacementGroup` está establecido en *True*.

**Solución:** el valor de `singlePlacementGroup` se debe establecer en *False*.


<!-- error -->
### <a name="outboundconnectivitynotenabledonvm-no-outbound-connectivity-configured-for-virtual-machine"></a>OutboundConnectivityNotEnabledOnVM. No hay conectividad de salida configurada para la máquina virtual.

```
OutboundConnectivityNotEnabledOnVM. No outbound connectivity configured for virtual machine.
```
**Causa:** está intentando crear un conjunto de escalado de máquinas virtuales en modo de orquestación flexible sin conectividad de salida a Internet.

**Solución**: habilite el acceso de salida seguro para el conjunto de escalado de máquinas virtuales de la manera más adecuada para la aplicación. El acceso de salida se puede habilitar con una instancia de NAT Gateway en la subred, agregando instancias a un grupo de back-end de Load Balancer o agregando una dirección IP pública explícita por instancia. Para las aplicaciones altamente seguras, puede especificar rutas personalizadas definidas por el usuario a través del firewall o las aplicaciones de red virtual. Consulte [Acceso de salida predeterminado](../virtual-network/ip-services/default-outbound-access.md) para más información.

## <a name="next-steps"></a>Pasos siguientes
> [!div class="nextstepaction"]
> [Modo de orquestación flexible para los conjuntos de escalado con Azure Portal.](flexible-virtual-machine-scale-sets-portal.md)