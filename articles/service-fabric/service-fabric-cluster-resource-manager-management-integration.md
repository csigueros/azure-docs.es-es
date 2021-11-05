---
title: 'Cluster Resource Manager: integración de la administración'
description: Una visión general de los puntos de integración entre el Administrador de recursos de clúster y la administración de Service Fabric.
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: b3622f2a46a0e5c22b7e63c6cdad635227a30714
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131003891"
---
# <a name="cluster-resource-manager-integration-with-service-fabric-cluster-management"></a>Integración del Administrador de recursos de clúster con la administración de clústeres de Service Fabric
Cluster Resource Manager de Service Fabric no impulsa las actualizaciones en Service Fabric, pero participa. Cluster Resource Manager contribuye a la administración, en primer lugar, realizando un seguimiento del estado deseado del clúster y los servicios que contiene. Además, envía informes de mantenimiento cuando no puede colocar el clúster en la configuración deseada. Por ejemplo, si no hay capacidad suficiente, Cluster Resource Manager envía advertencias y errores de estado que indican el problema. Otro aspecto de la integración tiene que ver con el funcionamiento de las actualizaciones. Durante ellas, Cluster Resource Manager modifica ligeramente su comportamiento.  

## <a name="health-integration"></a>Integración del mantenimiento
Cluster Resource Manager realiza el seguimiento constante de las reglas que ha definido para la colocación de los servicios. Asimismo, rastrea la capacidad restante de cada métrica en los nodos y en el clúster y en el clúster como un todo. Si no es posible satisfacer esas reglas o si no hay capacidad suficiente, se emiten errores y advertencias de mantenimiento. Por ejemplo, si un nodo está por encima de su capacidad y Cluster Resource Manager trata de corregir la situación moviendo servicios. Si no puede, emite una advertencia de mantenimiento que indica qué nodo está por encima de su capacidad y en qué métricas.

Otro ejemplo de advertencias de mantenimiento de Resource Manager son las infracciones de restricciones de ubicación. Por ejemplo, si ha definido una restricción de colocación (como `“NodeColor == Blue”`) y Resource Manager detecta una infracción de esa restricción, emitirá una advertencia de mantenimiento. Esto pasa con las restricciones personalizadas y las predeterminadas (como las restricciones de dominios de actualización y de error).

A continuación se muestra un ejemplo de un informe de mantenimiento de este tipo En este caso, el informe de mantenimiento es para una de las particiones del servicio de sistema. El mensaje de mantenimiento indica que las réplicas de esa partición se empaquetan temporalmente en demasiado pocos dominios de actualización.

```posh
PS C:\Users\User > Get-ServiceFabricPartitionHealth -PartitionId '00000000-0000-0000-0000-000000000001'


PartitionId           : 00000000-0000-0000-0000-000000000001
AggregatedHealthState : Warning
UnhealthyEvaluations  :
                        Unhealthy event: SourceId='System.PLB', Property='ReplicaConstraintViolation_UpgradeDomain', HealthState='Warning', ConsiderWarningAsError=false.

ReplicaHealthStates   :
                        ReplicaId             : 130766528804733380
                        AggregatedHealthState : Ok

                        ReplicaId             : 130766528804577821
                        AggregatedHealthState : Ok

                        ReplicaId             : 130766528854889931
                        AggregatedHealthState : Ok

                        ReplicaId             : 130766528804577822
                        AggregatedHealthState : Ok

                        ReplicaId             : 130837073190680024
                        AggregatedHealthState : Ok

HealthEvents          :
                        SourceId              : System.PLB
                        Property              : ReplicaConstraintViolation_UpgradeDomain
                        HealthState           : Warning
                        SequenceNumber        : 130837100116930204
                        SentAt                : 8/10/2015 7:53:31 PM
                        ReceivedAt            : 8/10/2015 7:53:33 PM
                        TTL                   : 00:01:05
                        Description           : The Load Balancer has detected a Constraint Violation for this Replica: fabric:/System/FailoverManagerService Secondary Partition 00000000-0000-0000-0000-000000000001 is
                        violating the Constraint: UpgradeDomain Details: UpgradeDomain ID -- 4, Replica on NodeName -- Node.8 Currently Upgrading -- false Distribution Policy -- Packing
                        RemoveWhenExpired     : True
                        IsExpired             : False
                        Transitions           : Ok->Warning = 8/10/2015 7:13:02 PM, LastError = 1/1/0001 12:00:00 AM
```

Esto es lo que nos dice este mensaje de mantenimiento:

1. Todas las réplicas propiamente dichas están en buen estado: cada una tiene el elemento `AggregatedHealthState : Ok`.
2. Actualmente se está infringiendo la restricción de distribución de dominios de actualización. Esto significa que un dominio de actualización concreto tiene más réplicas de esta partición de lo que debería.
3. El nodo que contiene la réplica que produce la infracción. En este caso, es el nodo con el nombre *Node.8*.
4. Si se está produciendo una actualización en esta partición ("Currently Upgrading -- false")
5. La directiva de distribución de este servicio: "Distribution Policy -- Packing". Esta se rige por la `RequireDomainDistribution`[directiva de colocación](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md#requiring-replica-distribution-and-disallowing-packing). _Packing_ indica que, en este caso, DomainDistribution *no* era necesario, así que sabemos que no se especificó ninguna directiva de selección de ubicación para este servicio. 
6. Cuándo se produjo el informe (el 08/10/2015 a las 19:13:02).

La información de este tipo genera alertas que se activan durante la producción para advertirle de que hay un problema, y también se usa para detectar y detener las actualizaciones incorrectas. En este caso, queremos ver si podemos averiguar por qué Resource Manager tuvo que empaquetar las réplicas en el dominio de actualización. Por lo habitual, el empaquetado es transitorio porque, por ejemplo, los nodos de los demás dominios de actualización estaban inactivos.

Supongamos que Cluster Resource Manager intenta colocar algunos servicios, pero no hay soluciones que funcionen. Cuando no se pueden colocar servicios, suele ser por uno de los siguientes motivos:

1. Alguna condición transitoria ha hecho que sea imposible colocar esta instancia o réplica de servicio correctamente.
2. Los requisitos de colocación del servicio no se han satisfecho.

En estos casos, los informes de mantenimiento de Cluster Resource Manager le ayudan a determinar por qué no se puede colocar el servicio. A este proceso se le llama "secuencia de eliminación de restricciones". Durante dicho proceso, el sistema recorre las restricciones configuradas que afectan al servicio y registra lo que eliminan. De esta forma, cuando los servicios no se pueden colocar, puede ver qué nodos se eliminaron y por qué.

## <a name="constraint-types"></a>Tipos de restricción:
Vamos a hablar sobre cada una de las diferentes restricciones de estos informes de mantenimiento. Verá mensajes de mantenimiento relacionados con estas restricciones cuando no se puedan colocar réplicas.

* **ReplicaExclusionStatic** y **ReplicaExclusionDynamic**: estas restricciones indican que se rechazó una solución porque dos objetos de servicio de la misma partición se tendrían que colocar en el mismo nodo. Esto no está permitido porque entonces el error de ese nodo afectaría demasiado a esa partición. ReplicaExclusionStatic y ReplicaExclusionDynamic son casi la misma regla y las diferencias no importan realmente. Si está viendo una secuencia de eliminación de restricciones que contiene las restricciones ReplicaExclusionStatic o ReplicaExclusionDynamic, Cluster Resource Manager considera que no hay suficientes nodos. En esta situación, el resto de soluciones debe usar estas selecciones de ubicación no válidas y que no se permiten. Las otras restricciones de la secuencia nos dirán normalmente por qué los nodos se eliminan en primer lugar.
* **PlacementConstraint**: si ve este mensaje, significa que se eliminaron algunos nodos porque no coincidían con las restricciones de selección de ubicación del servicio. Realizamos el seguimiento de las restricciones de selección de ubicación configuradas actualmente como parte de este mensaje. Esto es normal si tiene definida una restricción de colocación. Sin embargo, si la restricción de colocación está provocando que demasiados nodos se eliminen, lo que es incorrecto, puede advertir esta situación de la manera siguiente.
* **NodeCapacity**: esta restricción significa que Cluster Resource Manager no pudo colocar las réplicas en los nodos indicados porque, de hacerlo, habrían sobrepasado su capacidad.
* **Affinity**: esta restricción indica que no pudimos colocar la réplica en los nodos afectados porque se produciría una infracción de la restricción de afinidad. Puede obtener más información sobre la afinidad en [este artículo](service-fabric-cluster-resource-manager-advanced-placement-rules-affinity.md).
* **FaultDomain** y **UpgradeDomain**: estas restricciones eliminan nodos si el hecho de colocar la réplica en los nodos indicados provocaría el empaquetamiento en un determinado dominio de error o de actualización. En el tema sobre [las restricciones de dominio de error y de actualización y el comportamiento resultante](service-fabric-cluster-resource-manager-cluster-description.md)
* **PreferredLocation**: lo normal es no ver esta restricción al eliminar nodos de la solución dado que, de forma predeterminada, se ejecuta como una optimización. La restricción de ubicación preferida también está presente durante las actualizaciones, Durante la actualización, se usa para volver a colocar los servicios en el lugar en el que se encontraban cuando se inició la actualización.

## <a name="blocklisting-nodes"></a>Lista de bloqueo de nodos
Otro mensaje de mantenimiento que notifica Cluster Resource Manager es cuando los nodos están en la lista de bloqueo. La lista de bloqueo se puede considerar como una restricción temporal que se le aplica automáticamente. Los nodos entran en la lista de bloqueo cuando experimentan errores repetidos al iniciar instancias de ese tipo de servicio. Los nodos se incluyen en la lista de bloqueo por tipo de servicio. Un nodo puede estar en la lista de bloqueo para un servicio pero no para otro. 

Verá que la lista de bloqueo se inicia con frecuencia durante el desarrollo: Algún error hace que el host de servicios se bloquee al iniciarse. Service Fabric intenta crear el host de servicios varias veces, pero el error sigue apareciendo. Después de varios intentos, el nodo pasa a la lista de bloqueo y Cluster Resource Manager intenta crear el servicio en otra parte. Si ese error se sigue produciendo en varios nodos, es posible que todos los nodos válidos del clúster acaben bloqueados. También se puede eliminar la lista de bloqueo para que muchos nodos que no son suficientes puedan iniciar correctamente el servicio para satisfacer la escala deseada. Normalmente verá errores o advertencias adicionales de Cluster Resource Manager que indican que el servicio está por debajo del número deseado de réplicas o instancias, junto con mensajes de mantenimiento que indican cuál es el error que está llevando a la lista de bloqueo como primera medida.

La lista de bloqueo no es una condición permanente. Al cabo de unos minutos, el nodo se elimina de la lista de bloqueo y Service Fabric puede activar de nuevo los servicios en ese nodo. Si los servicios siguen sin funcionar bien, el nodo pasa de nuevo a la lista de bloqueo de ese tipo de servicio. 

### <a name="constraint-priorities"></a>Prioridades de restricción

> [!WARNING]
> No se recomienda cambiar las prioridades, además de que podría tener efectos adversos sobre su clúster. La siguiente información se proporciona como referencia de las prioridades de restricción predeterminadas y su comportamiento. 
>

Con todas estas restricciones, puede que haya pensado: "Creo que esas restricciones de dominio de error son el aspecto más importante de mi sistema. Con el fin de garantizar que no se infringe la restricción de dominios de error, estoy dispuesto a infringir otras restricciones".

Las restricciones se pueden configurar con diferentes niveles de prioridad: Dichos componentes son:

   - "alta" (0)
   - "baja" (1)
   - "optimización" (2)
   - "desactivado" (-1). 
   
La mayoría de las restricciones se configuran de forma predeterminada como restricciones altas.

No es muy frecuente cambiar la prioridad de las restricciones. Ha habido veces en que era necesario hacerlo, normalmente para solucionar algunos errores o comportamientos que estaban afectando al entorno. Por lo general, la flexibilidad de la infraestructura de prioridades de restricciones ha funcionado muy bien, pero no suele ser necesaria. La mayoría de las veces todo funciona en las prioridades predeterminadas. 

Los niveles de prioridad no significan que _se vaya a_ infringir una restricción dada, ni que siempre se cumpla. Las prioridades de restricción definen el orden en el que se aplican las restricciones. Las prioridades definen los inconvenientes cuando es imposible satisfacer todas las restricciones. Normalmente se pueden satisfacer todas las restricciones a menos que suceda otra cosa en el entorno. Algunos ejemplos de escenarios que darán lugar a infracciones de restricción son las restricciones en conflicto o gran cantidad de errores simultáneos.

En situaciones avanzadas, puede cambiar las prioridades de restricción. Por ejemplo, supongamos que desea asegurarse de que la afinidad siempre se infringirá cuando sea necesario resolver problemas de capacidad de nodo. Para lograr esto, podría establecer la prioridad de la restricción de afinidad como "suave" (1) y dejar la restricción de capacidad establecida en "fuerte" (0).

Los valores de prioridad predeterminados de las distintas restricciones se especifican en la siguiente configuración:

ClusterManifest.xml

```xml
        <Section Name="PlacementAndLoadBalancing">
            <Parameter Name="PlacementConstraintPriority" Value="0" />
            <Parameter Name="CapacityConstraintPriority" Value="0" />
            <Parameter Name="AffinityConstraintPriority" Value="0" />
            <Parameter Name="FaultDomainConstraintPriority" Value="0" />
            <Parameter Name="UpgradeDomainConstraintPriority" Value="1" />
            <Parameter Name="PreferredLocationConstraintPriority" Value="2" />
        </Section>
```

a través de ClusterConfig.json para las implementaciones independientes o Template.json para los clústeres hospedados en Azure:

```json
"fabricSettings": [
  {
    "name": "PlacementAndLoadBalancing",
    "parameters": [
      {
          "name": "PlacementConstraintPriority",
          "value": "0"
      },
      {
          "name": "CapacityConstraintPriority",
          "value": "0"
      },
      {
          "name": "AffinityConstraintPriority",
          "value": "0"
      },
      {
          "name": "FaultDomainConstraintPriority",
          "value": "0"
      },
      {
          "name": "UpgradeDomainConstraintPriority",
          "value": "1"
      },
      {
          "name": "PreferredLocationConstraintPriority",
          "value": "2"
      }
    ]
  }
]
```

## <a name="fault-domain-and-upgrade-domain-constraints"></a>Restricciones de dominio de error y dominio de actualización
Cluster Resource Manager desea mantener los servicios repartidos entre dominios de error y de actualización. Y este requisito lo modela como una restricción dentro del motor de Cluster Resource Manager. Para más información sobre cómo se usan y su comportamiento específico, consulte el artículo sobre la [configuración del clúster](service-fabric-cluster-resource-manager-cluster-description.md#fault-and-upgrade-domain-constraints-and-resulting-behavior).

Cluster Resource Manager puede necesitar empaquetar un par de réplicas en un dominio de actualización para administrar actualizaciones, errores u otras infracciones de restricción. El empaquetado en dominios de error o de actualización solo sucede cuando hay varios errores u otras renovaciones en el sistema que impiden la correcta colocación. Si desea evitar el empaquetado incluso durante estas situaciones, puede usar la `RequireDomainDistribution` [directiva de colocación](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md#requiring-replica-distribution-and-disallowing-packing). Tenga en cuenta que, si lo hace, la disponibilidad y la confiabilidad del servicio pueden verse afectadas como efecto secundario, por lo que debe tener cuidado.

Si el entorno está configurado correctamente, todas las restricciones se respetan completamente, incluso durante las actualizaciones. Lo que realmente importa es que Cluster Resource Manager vigila las restricciones y, cuando detecta una infracción, lo notifica inmediatamente e intenta corregir el problema.

## <a name="the-preferred-location-constraint"></a>La restricción de ubicación preferida
La restricción PreferredLocation es algo diferente, dado que tiene dos usos distintos. Uno de los usos de esta restricción es durante las actualizaciones de aplicaciones. Cluster Resource Manager administra automáticamente esta restricción durante las actualizaciones. Se usa para garantizar que cuando finalicen las actualizaciones, las réplicas vuelvan a sus ubicaciones iniciales. El otro uso de la restricción PreferredLocation es para la [directiva de colocación `PreferredPrimaryDomain`](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md). Ambas son optimizaciones, de ahí que la restricción PreferredLocation sea la única establecida en "Optimización" de forma predeterminada.

## <a name="upgrades"></a>Actualizaciones
Cluster Resource Manager también es útil en las actualizaciones de clústeres y aplicaciones, durante las cuales se encarga de estas dos tareas:

* Garantizar que las reglas del clúster no se pongan en peligro
* Tratar de ayudar a que la actualización se realice sin dificultades

### <a name="keep-enforcing-the-rules"></a>Aplicación continuada de las reglas
Lo más importante a tener en cuenta en este punto es que las reglas -las restricciones estrictas como las restricciones y funcionalidades de colocación- se siguen aplicando durante las actualizaciones. Las restricciones de selección de ubicación garantizan que sus cargas de trabajo se ejecuten solamente donde tengan permiso para ello, incluso durante las actualizaciones. Cuando los servicios presentan elevadas restricciones, las actualizaciones pueden tardar más. Cuando el servicio o el nodo dejan de estar activos durante una actualización, puede haber pocas opciones respecto a dónde puede ir.

### <a name="smart-replacements"></a>Sustituciones inteligentes
Cuando se inicia una actualización, Resource Manager crea una instantánea de la disposición actual del clúster. A medida que cada dominio de actualización finaliza, intenta devolver los servicios que estaban en ese nodo de actualización a su disposición original. Por tanto, un servicio sufre al menos dos transiciones durante la actualización. Primero sale del nodo afectado y luego regresa de nuevo. El hecho de que el clúster o servicio recuperen su disposición original también garantiza que la actualización no afecte a la distribución del clúster. 

### <a name="reduced-churn"></a>Renovación reducida
Durante las actualizaciones, Cluster Resource Manager también desactiva el equilibrio. La detención del equilibrio evita respuestas innecesarias a la propia actualización, como mover servicios a nodos que se vaciaron para la actualización. Si la actualización en cuestión es una actualización de clúster, se detiene el equilibrio en todo el clúster durante la actualización. Las comprobaciones de restricciones permanecen activas, solo permanece deshabilitado el movimiento basado en el equilibrio proactivo de métricas.

### <a name="buffered-capacity-and-upgrade"></a>Capacidad de búfer y actualización
Por regla general, quiere que la actualización se complete, aunque el clúster esté restringido o casi lleno. Administrar la capacidad del clúster es incluso más importante durante las actualizaciones que de costumbre. En función del número de dominios de actualización, se debe migrar entre un 5 y un 20 por ciento de la capacidad a medida que la actualización pasa por el clúster. Ese trabajo tiene que ir a algún sitio. Aquí es donde el concepto de [capacidades almacenadas en búfer](service-fabric-cluster-resource-manager-cluster-description.md#node-buffer-and-overbooking-capacity) resulta de utilidad. La capacidad almacenada en búfer se respeta durante el funcionamiento normal. Cluster Resource Manager puede llenar los nodos hasta su capacidad total (que consume el búfer) durante las actualizaciones en caso necesario.

## <a name="next-steps"></a>Pasos siguientes
* Empiece desde el principio y [obtenga una introducción a Cluster Resource Manager de Service Fabric](service-fabric-cluster-resource-manager-introduction.md)
