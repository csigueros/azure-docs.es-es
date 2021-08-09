---
title: Gobernanza de recursos para contenedores y servicios
description: Azure Service Fabric le permite especificar los límites y las solicitudes de recursos de los servicios que se ejecutan como procesos o contenedores.
ms.topic: conceptual
ms.date: 8/9/2017
ms.openlocfilehash: 2265640346525c6521d7f421c2e589979cceb4ca
ms.sourcegitcommit: 7f59e3b79a12395d37d569c250285a15df7a1077
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/02/2021
ms.locfileid: "110783413"
---
# <a name="resource-governance"></a>Regulación de recursos

Cuando ejecuta varios servicios en el mismo clúster o nodo, es posible que un servicio pueda consumir más recursos y privar así a otros servicios en el proceso. A este problema se le conoce como el problema del entorno ruidoso. Azure Service Fabric permite al desarrollador controlar este comportamiento al especificar las solicitudes y los límites por servicio a fin de limitar el uso de recursos.

> Antes de continuar con este artículo, le recomendamos que se familiarice con el [modelo de aplicación de Service Fabric][application-model-link] y con el [modelo de hospedaje de Service Fabric][hosting-model-link].
>

## <a name="resource-governance-metrics"></a>Métricas de gobernanza de recursos

Service Fabric admite la gobernanza de recursos de acuerdo con el [paquete de servicio][application-model-link]. Los recursos asignados al paquete de servicio pueden dividirse además entre paquetes de código. Service Fabric admite la gobernanza de la memoria y la CPU por cada paquete de servicio, con dos [métricas](service-fabric-cluster-resource-manager-metrics.md) integradas:

* *CPU* (nombre de la métrica `servicefabric:/_CpuCores`): un núcleo lógico que está disponible en la máquina host. Todos los núcleos de los nodos se ponderan igual.

* *Memoria* (nombre de métrica `servicefabric:/_MemoryInMB`): la memoria se expresa en megabytes y se asigna a la memoria física que está disponible en la máquina.

Para estas dos métricas, [Cluster Resource Manager (CRM)][cluster-resource-manager-description-link] realiza un seguimiento de la capacidad total del clúster, la carga de cada nodo del clúster y los recursos restantes del clúster. Estas dos métricas son equivalentes a cualquier otra métrica personalizada o de usuario. Todas las características existentes pueden utilizarse con ellas:

* El clúster puede [equilibrarse](service-fabric-cluster-resource-manager-balancing.md) en función de estas dos métricas (comportamiento predeterminado).
* El clúster puede [desfragmentarse](service-fabric-cluster-resource-manager-defragmentation-metrics.md) en función de estas dos métricas.
* Para [describir un clúster][cluster-resource-manager-description-link], se puede establecer la capacidad de almacenamiento en búfer para estas dos métricas.

> [!NOTE]
> El [informe de carga dinámica](service-fabric-cluster-resource-manager-metrics.md) no es compatible con estas métricas, y las cargas para estas métricas se definen en el momento de la creación.

## <a name="resource-governance-mechanism"></a>Mecanismo de gobernanza de recursos

A partir de la versión 7.2, el entorno de ejecución de Service Fabric admite la especificación de solicitudes y límites de recursos de CPU y memoria.

> [!NOTE]
> Las versiones del entorno de ejecución de Service Fabric anteriores a 7.2 solo admiten un modelo en el que un único valor actúa como la **solicitud** y el **límite**  de un recurso determinado (CPU o memoria). Esto se describe como la especificación **RequestsOnly** en este documento.

* *Solicitudes:* los valores de solicitud de la CPU y la memoria representan las cargas que utiliza [Cluster Resource Manager (CRM)][cluster-resource-manager-description-link] para las métricas `servicefabric:/_CpuCores` y `servicefabric:/_MemoryInMB`. En otras palabras, CRM considera que el consumo de recursos de un servicio es igual a sus valores de solicitud y usa estos valores al tomar decisiones de selección de ubicación.

* *Límites:* los valores de límite de memoria y CPU representan los límites de recursos reales que se aplican cuando se activa un proceso o un contenedor en un nodo.

Service Fabric permite tanto las especificaciones **RequestsOnly y LimitsOnly** como ambas (**RequestsAndLimits**) para la CPU y la memoria.
* Cuando se usa la especificación RequestsOnly, Service Fabric también usa los valores de solicitud como límites.
* Cuando se usa la especificación LimitsOnly, Service Fabric considera que los valores de solicitud son 0.
* Cuando se usa la especificación RequestsAndLimits, los valores de límite deben ser mayores o iguales que los valores de solicitud.

Para comprender mejor el mecanismo de gobernanza de recursos, echemos un vistazo a un escenario de selección de ubicación de ejemplo con la especificación **RequestsOnly** para el recurso de CPU (el mecanismo para la gobernanza de memoria es equivalente). Considere un nodo con dos núcleos de CPU y dos paquetes de servicio que se colocarán en él. El primer paquete de servicio que se va a colocar se compone de un solo paquete de código de contenedor y solo especifica una solicitud de un núcleo de CPU. El segundo paquete de servicio que se va a colocar se compone de un solo paquete de código basado en proceso y también especifica una solicitud de un núcleo de CPU. Puesto que ambos paquetes de servicio tienen una especificación RequestsOnly, sus valores límite se establecen en sus valores de solicitud.

1. En primer lugar, el paquete de servicio basado en contenedor que solicita un núcleo de CPU se coloca en el nodo. El entorno de ejecución activa el contenedor y establece el límite de CPU en un núcleo. El contenedor no podrá usar más de un núcleo.

2. A continuación, el paquete de servicio basado en proceso que solicita un núcleo de CPU se coloca en el nodo. El entorno de ejecución activa el proceso de servicio y establece su límite de CPU en un núcleo.

En este momento, la suma de las solicitudes es igual a la capacidad del nodo. CRM no colocará más contenedores ni procesos de servicio con solicitudes de CPU en este nodo. En el nodo, un proceso y un contenedor se ejecutan con un núcleo cada uno y no compiten entre sí para la CPU.

Vamos a consultar de nuevo nuestro ejemplo con la especificación **RequestsAndLimits**. Esta vez, el paquete de servicio basado en contenedor especifica una solicitud de un núcleo de CPU y un límite de dos núcleos de CPU. El paquete de servicio basado en proceso especifica una solicitud y un límite de un núcleo de CPU.
  1. En primer lugar, el paquete de servicio basado en contenedor se coloca en el nodo. El entorno de ejecución activa el contenedor y establece el límite de CPU en dos núcleos. El contenedor no podrá usar más de dos núcleos.
  2. A continuación, el paquete de servicio basado en proceso se coloca en el nodo. El entorno de ejecución activa el proceso de servicio y establece su límite de CPU en un núcleo.

  En este momento, la suma de las solicitudes de CPU de los paquetes de servicio que se colocan en el nodo es igual a la capacidad de CPU del nodo. CRM no colocará más contenedores ni procesos de servicio con solicitudes de CPU en este nodo. Sin embargo, en el nodo, la suma de los límites (dos núcleos para el contenedor y un núcleo para el proceso) supera la capacidad de dos núcleos. Si el contenedor y el proceso se expanden al mismo tiempo, existe la posibilidad de realizar una contención del recurso de la CPU. El sistema operativo subyacente de la plataforma administra ese tipo de contención. En este ejemplo, el contenedor podría expandirse en un total de dos núcleos de CPU, por lo que la solicitud del proceso de un núcleo de CPU no se garantiza.

> [!NOTE]
> Como se muestra en el ejemplo anterior, los valores de solicitud de CPU y memoria **no dan lugar a la reserva de recursos en un nodo**. Estos valores representan el consumo de recursos que Cluster Resource Manager tiene en cuenta al tomar decisiones de selección de ubicación. Los valores de límite representan los límites de los recursos reales que se aplican cuando se activa un proceso o un contenedor en un nodo.


Existen algunas situaciones en las que puede haber contención de la CPU. En estas situaciones, el proceso y el contenedor del ejemplo pueden experimentar el problema del entorno ruidoso:

* *Combinación de servicios con gobierno y sin gobierno y contenedores*: si el usuario crea un servicio sin especificar una gobernanza de recursos, el entorno de tiempo de ejecución considera que no estaba consumiendo ningún recurso y puede colocarlo en el nodo de nuestro ejemplo. En este caso, este nuevo proceso consume eficazmente algún recurso de CPU a costa de los servicios que se ejecutan en el nodo. Hay dos soluciones para este problema. Una solución consiste en no combinar servicios con gobierno y sin gobierno en el mismo clúster, y la otra en usar [restricciones de posición](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md) para que estos dos tipos de servicio no finalicen en el mismo conjunto de nodos.

* *Cuando se inicia otro proceso en el nodo, fuera de Service Fabric (por ejemplo, un servicio de sistema operativo)* : En esta situación, el proceso fuera de Service Fabric también competirá por la CPU con los servicios existentes. La solución a este problema consiste en configurar correctamente las capacidades del nodo en la cuenta para la sobrecarga del sistema operativo, tal como se muestra en la sección siguiente.

* *Cuando las solicitudes no son iguales a los límites*: Tal y como se describe en el ejemplo de RequestsAndLimits anterior, las solicitudes no dan lugar a la reserva de recursos en un nodo. Cuando se coloca un servicio con límites superiores a las solicitudes en un nodo, es posible que este consuma recursos (si están disponibles) hasta sus límites. En tales casos, es posible que otros servicios del nodo no puedan consumir recursos hasta sus valores de solicitud.

## <a name="cluster-setup-for-enabling-resource-governance"></a>Configuración del clúster para habilitar la gobernanza de recursos

Cuando el nodo se inicia y se une al clúster, Service Fabric detecta la cantidad de memoria disponible y el número de núcleos disponibles, y establece las capacidades del nodo para esos dos recursos.

Para dejar algún espacio en búfer al sistema operativo y para otros procesos que puedan ejecutarse en el nodo, Service Fabric solo utiliza el 80 % de los recursos disponibles en el nodo. Este porcentaje es configurable y puede cambiarse en el manifiesto de clúster.

Este es un ejemplo que indica a Service Fabric cómo usar el 50 % de la CPU disponible y el 70 % de memoria disponible:

```xml
<Section Name="PlacementAndLoadBalancing">
    <!-- 0.0 means 0%, and 1.0 means 100%-->
    <Parameter Name="CpuPercentageNodeCapacity" Value="0.5" />
    <Parameter Name="MemoryPercentageNodeCapacity" Value="0.7" />
</Section>
```

Para la mayoría de los clientes y escenarios, la detección automática de las capacidades del nodo para la CPU y la memoria es la configuración recomendada (la detección automática está activada de forma predeterminada). No obstante, si necesita realizar una configuración manual completa de las capacidades del nodo, puede hacerlo por tipo de nodo mediante el mecanismo para describir los nodos del clúster. Este es un ejemplo de cómo configurar el tipo de nodo con cuatro núcleos y 2 GB de memoria:

```xml
    <NodeType Name="MyNodeType">
      <Capacities>
        <Capacity Name="servicefabric:/_CpuCores" Value="4"/>
        <Capacity Name="servicefabric:/_MemoryInMB" Value="2048"/>
      </Capacities>
    </NodeType>
```

Cuando está habilitada la detección automática de los recursos disponibles y las capacidades de nodo se han definido manualmente en el manifiesto de clúster, Service Fabric comprueba si el nodo tiene suficientes recursos para admitir la capacidad que ha definido el usuario:

* Si las capacidades de nodo que se definen en el manifiesto son menores o iguales que los recursos disponibles en el nodo, Service Fabric usa las capacidades que se especifican en el manifiesto.

* Si las capacidades de nodo que se definen en el manifiesto son mayores que los recursos disponibles, Service Fabric usa los recursos disponibles como capacidades de nodo.

Puede desactivar la detección automática de los recursos disponibles si no es necesario. Para desactivar esta función, cambie la configuración siguiente:

```xml
<Section Name="PlacementAndLoadBalancing">
    <Parameter Name="AutoDetectAvailableResources" Value="false" />
</Section>
```

Para obtener un rendimiento óptimo, también es necesario activar la siguiente configuración en el manifiesto de clúster:

```xml
<Section Name="PlacementAndLoadBalancing">
    <Parameter Name="PreventTransientOvercommit" Value="true" />
    <Parameter Name="AllowConstraintCheckFixesDuringApplicationUpgrade" Value="true" />
</Section>
```

> [!IMPORTANT]
> A partir de la versión 7.0 de Service Fabric, actualizamos la regla de cómo se calculan las capacidades de los recursos de nodo en casos donde el usuario proporciona manualmente los valores de las capacidades de los recursos de nodo. Consideremos el escenario siguiente:
>
> * Hay un total de 10 núcleos de CPU en el nodo.
> * SF está configurado para usar el 80 % del total de los recursos para los servicios de usuario (configuración predeterminada), que deja un búfer del 20 % para los demás servicios que se ejecutan en el nodo (incluidos los servicios del sistema de Service Fabric).
> * El usuario decide invalidar manualmente la capacidad del recurso del nodo correspondiente a la métrica de núcleos de la CPU y la establece en 5 núcleos.
>
> Modificamos la regla sobre cómo se calcula la capacidad disponible para los servicios de usuario de Service Fabric de la manera siguiente:
>
> * Antes de Service Fabric 7.0, la capacidad disponible de los servicios de usuario se calcularía en **5 núcleos** (se omite el búfer de capacidad del 20 %).
> * A partir de Service Fabric 7.0, la capacidad disponible de los servicios de usuario se calcularía en **4 núcleos** (no se omite el búfer de capacidad del 20 %).

## <a name="specify-resource-governance"></a>Especificación de la gobernanza de recursos

Los límites y las solicitudes de la gobernanza de recursos se especifican en el manifiesto de aplicación (sección ServiceManifestImport). Veamos algunos ejemplos:

**Ejemplo 1: Especificación de RequestsOnly**
```xml
<?xml version='1.0' encoding='UTF-8'?>
<ApplicationManifest ApplicationTypeName='TestAppTC1' ApplicationTypeVersion='vTC1' xsi:schemaLocation='http://schemas.microsoft.com/2011/01/fabric ServiceFabricServiceModel.xsd' xmlns='http://schemas.microsoft.com/2011/01/fabric' xmlns:xsi='https://www.w3.org/2001/XMLSchema-instance'>
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName='ServicePackageA' ServiceManifestVersion='v1'/>
    <Policies>
      <ServicePackageResourceGovernancePolicy CpuCores="1"/>
      <ResourceGovernancePolicy CodePackageRef="CodeA1" CpuShares="512" MemoryInMB="1024" />
      <ResourceGovernancePolicy CodePackageRef="CodeA2" CpuShares="256" MemoryInMB="1024" />
    </Policies>
  </ServiceManifestImport>
```

En este ejemplo, el atributo `CpuCores` se usa para especificar una solicitud de un núcleo de CPU para **ServicePackageA**. Dado que no se especifica el límite de CPU (atributo `CpuCoresLimit`), Service Fabric también utiliza el valor de solicitud especificado de un núcleo como límite de CPU del paquete de servicio.

**ServicePackageA** solo se colocará en un nodo en el que la capacidad restante de la CPU sea mayor o igual que un núcleo después de restar la **suma de las solicitudes de CPU de todos los paquetes de servicio colocados en ese nodo**. En el nodo, el paquete de servicio se limitará a un núcleo. El paquete de servicio contiene dos paquetes de código (**CodeA1** y **CodeA2**), y ambos especifican el atributo `CpuShares`. La proporción de CpuShares 512:256 se usa para calcular los límites de CPU para los paquetes de código individuales. Por lo tanto, CodeA1 se limitará a dos tercios de un núcleo y CodeA2 se limitará a un tercio de un núcleo. Si no se especifican las proporciones de CpuShares de todos los paquetes de código, Service Fabric dividirá el límite de la CPU equitativamente entre ellos.

Aunque las proporciones de CpuShares especificadas para los paquetes de código representan su proporción relativa del límite total de CPU del paquete de servicio, los valores de memoria de los paquetes de código se especifican en términos absolutos. En este ejemplo, el atributo `MemoryInMB` se usa para especificar solicitudes de memoria de 1024 MB para CodeA1 y CodeA2. Dado que no se especifica el límite de memoria (atributo `MemoryInMBLimit`), Service Fabric también utiliza los valores de solicitud especificados como límites para los paquetes de código. La solicitud de memoria (y el límite) del paquete de servicio se calcula como la suma de los valores de solicitud de memoria (y límite) de sus paquetes de código constituyentes. Por lo tanto, para **ServicePackageA**, la solicitud de memoria y el límite se calculan como 2048 MB.

**ServicePackageA** solo se colocará en un nodo en el que la capacidad de memoria restante sea mayor o igual que 2048 MB después de restar la **suma de las solicitudes de memoria de todos los paquetes de servicio colocados en ese nodo**. En el nodo, ambos paquetes de código se limitarán a 1024 MB de memoria cada uno. Los paquetes de código (contenedores o procesos) no podrán asignar más memoria de la que establece este límite y, si se intenta, el resultado serán excepciones de memoria insuficiente.

**Ejemplo 2: Especificación RequestsOnly**
```xml
<?xml version='1.0' encoding='UTF-8'?>
<ApplicationManifest ApplicationTypeName='TestAppTC1' ApplicationTypeVersion='vTC1' xsi:schemaLocation='http://schemas.microsoft.com/2011/01/fabric ServiceFabricServiceModel.xsd' xmlns='http://schemas.microsoft.com/2011/01/fabric' xmlns:xsi='https://www.w3.org/2001/XMLSchema-instance'>
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName='ServicePackageA' ServiceManifestVersion='v1'/>
    <Policies>
      <ServicePackageResourceGovernancePolicy CpuCoresLimit="1"/>
      <ResourceGovernancePolicy CodePackageRef="CodeA1" CpuShares="512" MemoryInMBLimit="1024" />
      <ResourceGovernancePolicy CodePackageRef="CodeA2" CpuShares="256" MemoryInMBLimit="1024" />
    </Policies>
  </ServiceManifestImport>
```
En este ejemplo se utilizan los atributos `CpuCoresLimit` y `MemoryInMBLimit` , que solo están disponibles en las versiones de SF 7.2 y posteriores. El atributo CpuCoresLimit se usa para especificar un límite de CPU de 1 núcleo para **ServicePackageA**. Puesto que no se especifica la solicitud de CPU (atributo `CpuCores`), se considera que su valor es 0. El atributo `MemoryInMBLimit` se usa para especificar los límites de memoria de 1024 MB para CodeA1 y CodeA2 y, dado que no se especifican las solicitudes (atributo `MemoryInMB`), se considera que su valor es 0. El límite y la solicitud de memoria de **ServicePackageA** se calculan como 0 y 2048, respectivamente. Dado que las solicitudes de CPU y memoria de **ServicePackageA** son 0, no presenta ninguna carga que CRM deba tener en cuenta para la selección de ubicación, en el caso de las métricas `servicefabric:/_CpuCores` y `servicefabric:/_MemoryInMB` . Por lo tanto, desde la perspectiva de la gobernanza de recursos, **ServicePackageA** se puede colocar en cualquier nodo **independientemente de la capacidad restante**. Al igual que en el ejemplo 1, en el nodo, CodeA1 se limitará a dos tercios de un núcleo y 1024 MB de memoria, mientras que CodeA2 se limitará a un tercio de un núcleo y 1024 MB de memoria.

**Ejemplo 3: Especificación RequestsAndLimits**
```xml
<?xml version='1.0' encoding='UTF-8'?>
<ApplicationManifest ApplicationTypeName='TestAppTC1' ApplicationTypeVersion='vTC1' xsi:schemaLocation='http://schemas.microsoft.com/2011/01/fabric ServiceFabricServiceModel.xsd' xmlns='http://schemas.microsoft.com/2011/01/fabric' xmlns:xsi='https://www.w3.org/2001/XMLSchema-instance'>
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName='ServicePackageA' ServiceManifestVersion='v1'/>
    <Policies>
      <ServicePackageResourceGovernancePolicy CpuCores="1" CpuCoresLimit="2"/>
      <ResourceGovernancePolicy CodePackageRef="CodeA1" CpuShares="512" MemoryInMB="1024" MemoryInMBLimit="3072" />
      <ResourceGovernancePolicy CodePackageRef="CodeA2" CpuShares="256" MemoryInMB="2048" MemoryInMBLimit="4096" />
    </Policies>
  </ServiceManifestImport>
```
A partir de los dos primeros ejemplos, en este ejemplo se muestra la especificación de las solicitudes y los límites de la CPU y la memoria. **ServicePackageA** tiene solicitudes de CPU y memoria de un núcleo y 3072 (1024 + 2048) MB, respectivamente. Solo se puede colocar en un nodo que tenga al menos un núcleo (y 3072 MB) de capacidad restante después de restar la suma de todas las solicitudes de CPU (y memoria) de todos los paquetes de servicio que se colocan en el nodo de la capacidad total de CPU (y memoria) del nodo. En el nodo, CodeA1 se limitará a dos tercios de 2 núcleos y 3072 MB de memoria, mientras que CodeA2 se limitará a un tercio de 2 núcleos y 4096 MB de memoria.

### <a name="using-application-parameters"></a>Uso de los parámetros de la aplicación

Al especificar la configuración de gobernanza de recursos, es posible utilizar [parámetros de la aplicación](service-fabric-manage-multiple-environment-app-configuration.md) para administrar varias configuraciones de la aplicación. En el ejemplo siguiente se muestra el uso de los parámetros de la aplicación:

```xml
<?xml version='1.0' encoding='UTF-8'?>
<ApplicationManifest ApplicationTypeName='TestAppTC1' ApplicationTypeVersion='vTC1' xsi:schemaLocation='http://schemas.microsoft.com/2011/01/fabric ServiceFabricServiceModel.xsd' xmlns='http://schemas.microsoft.com/2011/01/fabric' xmlns:xsi='https://www.w3.org/2001/XMLSchema-instance'>

  <Parameters>
    <Parameter Name="CpuCores" DefaultValue="4" />
    <Parameter Name="CpuSharesA" DefaultValue="512" />
    <Parameter Name="CpuSharesB" DefaultValue="512" />
    <Parameter Name="MemoryA" DefaultValue="2048" />
    <Parameter Name="MemoryB" DefaultValue="2048" />
  </Parameters>

  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName='ServicePackageA' ServiceManifestVersion='v1'/>
    <Policies>
      <ServicePackageResourceGovernancePolicy CpuCores="[CpuCores]"/>
      <ResourceGovernancePolicy CodePackageRef="CodeA1" CpuShares="[CpuSharesA]" MemoryInMB="[MemoryA]" />
      <ResourceGovernancePolicy CodePackageRef="CodeA2" CpuShares="[CpuSharesB]" MemoryInMB="[MemoryB]" />
    </Policies>
  </ServiceManifestImport>
```

En este ejemplo, se establecen los valores de los parámetros predeterminados para el entorno de producción, donde cada paquete de servicio obtendría 4 núcleos y 2 GB de memoria. Es posible cambiar los valores predeterminados con los archivos de parámetros de la aplicación. En este ejemplo se puede utilizar un archivo de parámetros para probar la aplicación localmente, donde obtendría menos recursos que en producción:

```xml
<!-- ApplicationParameters\Local.xml -->

<Application Name="fabric:/TestApplication1" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Parameters>
    <Parameter Name="CpuCores" DefaultValue="2" />
    <Parameter Name="CpuSharesA" DefaultValue="512" />
    <Parameter Name="CpuSharesB" DefaultValue="512" />
    <Parameter Name="MemoryA" DefaultValue="1024" />
    <Parameter Name="MemoryB" DefaultValue="1024" />
  </Parameters>
</Application>
```

> [!IMPORTANT]
> La especificación de la gobernanza de recursos con parámetros de la aplicación está disponible a partir de Service Fabric versión 6.1.<br>
>
> Cuando se usan parámetros de la aplicación para especificar la gobernanza de recursos, Service Fabric no se puede degradar a una versión anterior a la versión 6.1.

## <a name="enforcing-the-resource-limits-for-user-services"></a>Aplicación de los límites de recursos para los servicios de usuario

Si bien la aplicación de la gobernanza de recursos a los servicios de Service Fabric garantiza que esos servicios gobernados por los recursos no pueden exceder la cuota de recursos, muchos usuarios todavía tienen que ejecutar algunos de sus servicios de Service Fabric en modo no controlado. Cuando se usan los servicios de Service Fabric sin control, es posible que surjan situaciones donde los servicios "descontrolados" consuman todos los recursos disponibles en los nodos de Service Fabric, lo que puede generar problemas graves como:

* El colapso de los recursos de otros servicios que se ejecutan en los nodos (incluidos los servicios de sistema de Service Fabric)
* Nodos que terminan en un estado incorrecto
* API de administración de clústeres de Service Fabric sin capacidad de respuesta

Para evitar que se produzcan estas situaciones, Service Fabric le permite *aplicar los límites de recursos para todos los servicios de usuario de Service Fabric que se ejecutan en el nodo* (tanto controlados como no controlados) para garantizar que los servicios de usuario nunca usen más que la cantidad de recursos especificada. Para ello, el valor de la configuración EnforceUserServiceMetricCapacities de la sección PlacementAndLoadBalancing de ClusterManifest se establece en true. De manera predeterminada, esta configuración está desactivada.

```xml
<SectionName="PlacementAndLoadBalancing">
    <ParameterName="EnforceUserServiceMetricCapacities" Value="false"/>
</Section>
```

Comentarios adicionales:

* La aplicación de límites para los recursos solo se aplica a las métricas de recursos `servicefabric:/_CpuCores` y `servicefabric:/_MemoryInMB`.
* La aplicación de límites para los recursos solo funciona si las capacidades de nodo para las métricas de recursos están disponibles para Service Fabric, ya sea a través de un mecanismo de detección automática como mediante usuarios que especifican de manera manual las capacidades de nodo (tal como se explicó en la sección [Configuración del clúster para habilitar la gobernanza de recursos](service-fabric-resource-governance.md#cluster-setup-for-enabling-resource-governance)). Si las capacidades de nodo no están configuradas, no se puede usar la funcionalidad de aplicación de límites para los recursos ya que Service Fabric no puede saber cuántos recursos reservar para los servicios de usuario. Service Fabric emitirá una advertencia de estado si el valor de "EnforceUserServiceMetricCapacities" es true, pero las capacidades de nodo no están configuradas.

## <a name="other-resources-for-containers"></a>Otros recursos para los contenedores

Además de la CPU y de la memoria, es posible especificar otros [límites de recursos para los contenedores](service-fabric-service-model-schema-complex-types.md#resourcegovernancepolicytype-complextype). Estos límites se especifican en el nivel del paquete de código y se aplican cuando se inicia el contenedor. A diferencia de con la CPU y la memoria, Cluster Resource Manager no tiene en cuenta estos recursos y no realiza ninguna comprobación de capacidad ni de equilibrio de carga para ellos.

* *MemorySwapInMB*: la cantidad total de memoria de intercambio que puede utilizarse, en MB. Debe ser un entero positivo.
* *MemoryReservationInMB*: límite flexible (en MB) para la gobernanza de memoria que se aplica únicamente cuando se detecta contención de la memoria en el nodo. Debe ser un entero positivo.
* *CpuPercent*: porcentaje que se puede utilizar de las CPU disponibles (solo para Windows). Debe ser un entero positivo. No se puede usar con CpuShares, CpuCores o CpuCoresLimit.
* *CpuShares*: peso relativo de CPU. Debe ser un entero positivo. No se puede usar con CpuPercent, CpuCores o CpuCoresLimit.
* *MaximumIOps*: máxima tasa de E/S (lectura y escritura) en términos de IOPS que se puede usar. Debe ser un entero positivo.
* *MaximumIOBandwidth*: máximo de E/S (bytes por segundo) que se puede usar (lectura y escritura). Debe ser un entero positivo.
* *BlockIOWeight*: peso de E/S en bloque, en relación con otros paquetes de código. Debe ser un entero positivo comprendido entre 10 y 1000.
* *DiskQuotaInMB*: cuota de disco para contenedores. Debe ser un entero positivo.
* *KernelMemoryInMB:* límites de memoria del kernel en bytes.  Debe ser un entero positivo.  Tenga en cuenta que esto es específico de Linux, y Docker en Windows generará un error si se establece.
* *ShmSizeInMB*: tamaño de */dev/shm* en bytes. Si se omite, el sistema usa 64 MB.  Debe ser un entero positivo. Tenga en cuenta que esto es específico de Linux, pero Docker solo ignorará (y no generará un error) si se especifica.

Estos recursos se pueden combinar con la CPU y la memoria. Este es un ejemplo de cómo se especifican recursos adicionales para los contenedores:

```xml
    <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="FrontendServicePackage" ServiceManifestVersion="1.0"/>
        <Policies>
            <ResourceGovernancePolicy CodePackageRef="FrontendService.Code" CpuPercent="5"
            MemorySwapInMB="4084" MemoryReservationInMB="1024" MaximumIOPS="20" />
        </Policies>
    </ServiceManifestImport>
```

## <a name="next-steps"></a>Pasos siguientes

* Para más información sobre Cluster Resource Manager, consulte [Presentación de Cluster Resource Manager de Service Fabric](service-fabric-cluster-resource-manager-introduction.md).
* Para más información sobre el modelo de aplicación, paquetes de servicio, paquetes de código y cómo las réplicas los asignan, lea [Modelar una aplicación en Service Fabric][application-model-link].

<!-- Links -->
[application-model-link]: service-fabric-application-model.md
[hosting-model-link]: service-fabric-hosting-model.md
[cluster-resource-manager-description-link]: service-fabric-cluster-resource-manager-cluster-description.md
