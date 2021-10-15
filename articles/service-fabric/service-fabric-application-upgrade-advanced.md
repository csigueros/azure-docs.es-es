---
title: Temas avanzados de actualización de aplicación
description: En este artículo se tratan algunos temas avanzados relacionados con la actualización de una aplicación de Service Fabric.
ms.topic: conceptual
ms.date: 03/11/2020
ms.openlocfilehash: dca3ec669fde61be86f2424d9b314b4b26546c55
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/01/2021
ms.locfileid: "129363318"
---
# <a name="service-fabric-application-upgrade-advanced-topics"></a>Actualización de la aplicación de Service Fabric: temas avanzados

## <a name="add-or-remove-service-types-during-an-application-upgrade"></a>Adición o eliminación de tipos de servicio durante la actualización de una aplicación

Si se agrega un nuevo tipo de servicio a una aplicación publicada como parte de una actualización, el nuevo tipo de servicio se agrega a la aplicación implementada. Esta actualización no afecta a ninguna de las instancias de servicio que ya formaban parte de la aplicación, pero debe crearse una instancia del tipo de servicio que se ha agregado para que el nuevo tipo de servicio esté activo (consulte [New-ServiceFabricService](/powershell/module/servicefabric/new-servicefabricservice)).

De manera similar, los tipos de servicio pueden quitarse de una aplicación como parte de una actualización. Sin embargo, todas las instancias de servicio del tipo de servicio que va a quitarse se deben quitar antes de continuar con la actualización (consulte [Remove-ServiceFabricService](/powershell/module/servicefabric/remove-servicefabricservice)).

## <a name="avoid-connection-drops-during-stateless-service-planned-downtime"></a>Evitar interrupciones de la conexión durante el tiempo de inactividad planeado del servicio sin estado

En el caso de tiempos de inactividad planeados de instancias sin estado, como la actualización de aplicaciones o clústeres o la desactivación de nodos, las conexiones se pueden interrumpir si el punto de conexión expuesto se quita después de que la instancia se desactive. Como resultado, se realizan cierres de conexión efectivos.

Para evitarlo, configure la característica *RequestDrain* agregando una *duración de retraso de cierre de instancia* en la configuración del servicio para permitir que las solicitudes existentes dentro del clúster se purguen en los puntos de conexión expuestos. Esto se consigue cuando el punto de conexión anunciado por la instancia sin estado se quita *antes* de que se inicie el retraso y con anterioridad al cierre de la instancia. Este retraso permite que las solicitudes existentes se vacíen correctamente antes de que la instancia se desactive realmente. A los clientes se les notifica el cambio del punto de conexión mediante una función de devolución de llamada en el momento en que inicia el retraso, por lo que pueden volver a resolver el punto de conexión y evitar el envío de nuevas solicitudes a la instancia que se desactivará. Estas solicitudes podrían originarse en clientes que usan un [proxy inverso](./service-fabric-reverseproxy.md) o mediante API de resolución de puntos de conexión de servicio con el modelo de notificación ([ServiceNotificationFilterDescription](/dotnet/api/system.fabric.description.servicenotificationfilterdescription)) para actualizar los puntos de conexión.

### <a name="service-configuration"></a>Configuración del servicio

Hay varias maneras de configurar el retraso en el lado del servicio.

 * **Al crear un servicio**, especifique un valor de `-InstanceCloseDelayDuration`:

    ```powershell
    New-ServiceFabricService -Stateless [-ServiceName] <Uri> -InstanceCloseDelayDuration <TimeSpan>
    ```

 * **Al definir el servicio en la sección de valores predeterminados del manifiesto de la aplicación**, asigne la propiedad `InstanceCloseDelayDurationSeconds`:

    ```xml
          <StatelessService ServiceTypeName="Web1Type" InstanceCount="[Web1_InstanceCount]" InstanceCloseDelayDurationSeconds="15">
              <SingletonPartition />
          </StatelessService>
    ```

 * **Al actualizar un servicio existente**, especifique un valor de `-InstanceCloseDelayDuration`:

    ```powershell
    Update-ServiceFabricService [-Stateless] [-ServiceName] <Uri> [-InstanceCloseDelayDuration <TimeSpan>]`
    ```

 * **Al crear o actualizar un servicio existente mediante la plantilla de ARM**, especifique el valor de `InstanceCloseDelayDuration` (versión de API mínima admitida: 2020-03-01):

    ```ARM template to define InstanceCloseDelayDuration of 30seconds
    {
      "apiVersion": "2020-03-01",
      "type": "Microsoft.ServiceFabric/clusters/applications/services",
      "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'), '/', parameters('serviceName'))]",
      "location": "[variables('clusterLocation')]",
      "dependsOn": [
        "[concat('Microsoft.ServiceFabric/clusters/', parameters('clusterName'), '/applications/', parameters('applicationName'))]"
      ],
      "properties": {
        "provisioningState": "Default",
        "serviceKind": "Stateless",
        "serviceTypeName": "[parameters('serviceTypeName')]",
        "instanceCount": "-1",
        "partitionDescription": {
          "partitionScheme": "Singleton"
        },
        "serviceLoadMetrics": [],
        "servicePlacementPolicies": [],
        "defaultMoveCost": "",
        "instanceCloseDelayDuration": "00:00:30.0"
      }
    }
    ```

### <a name="client-configuration"></a>Configuración de cliente

Para recibir una notificación cuando un punto de conexión ha cambiado, los clientes deben registrar una devolución de llamada; consulte[ServiceNotificationFilterDescription](/dotnet/api/system.fabric.description.servicenotificationfilterdescription).
La notificación de cambios indica que los puntos de conexión han cambiado, que el cliente debe volver a resolver los puntos de conexión y que no se deben usar los puntos de conexión que ya no se anuncian, ya que se desactivarán pronto.

### <a name="optional-upgrade-overrides"></a>Invalidaciones de actualización opcionales

Además de establecer la duración predeterminada del retraso por servicio, también puede invalidar el retraso durante la actualización de la aplicación o del clúster con la misma opción (`InstanceCloseDelayDurationSec`):

```powershell
Start-ServiceFabricApplicationUpgrade [-ApplicationName] <Uri> [-ApplicationTypeVersion] <String> [-InstanceCloseDelayDurationSec <UInt32>]

Start-ServiceFabricClusterUpgrade [-CodePackageVersion] <String> [-ClusterManifestVersion] <String> [-InstanceCloseDelayDurationSec <UInt32>]
```

La duración del retraso invalidado solo se aplica a la instancia de actualización invocada y no cambia las configuraciones del retraso de los servicios individuales. Por ejemplo, puede usarla para especificar un retraso de `0` con el fin de omitir los retrasos de actualización preconfigurados.

> [!NOTE]
> * La configuración para purgar solicitudes no podrá impedir que Azure Load Balancer envíe nuevas solicitudes a los puntos de conexión que se están purgando.
> * Un mecanismo de resolución basado en quejas no dará como resultado una purga estable de las solicitudes, ya que desencadena una resolución de servicio después de un error. Tal y como se ha descrito antes, esto debería mejorarse para suscribirse a las notificaciones de cambio de punto de conexión mediante [ServiceNotificationFilterDescription](/dotnet/api/system.fabric.description.servicenotificationfilterdescription).
> * La configuración no se respeta cuando la actualización no tiene ningún impacto; es decir, cuando las réplicas no se desactivan durante la actualización.
>
>

> [!NOTE]
> Esta característica se puede configurar en los servicios actuales mediante el cmdlet Update-ServiceFabricService o la plantilla de ARM, como se mencionó anteriormente, cuando la versión del código del clúster es 7.1.XXX o superior.
>
>

## <a name="manual-upgrade-mode"></a>Modo de actualización manual

> [!NOTE]
> El modo de actualización *Monitored* se recomienda para todas las actualizaciones de Service Fabric.
> El modo de actualización *UnmonitoredManual* debe considerarse solo si la actualización presenta errores o si se suspende. 
>
>

En el modo *Monitored*, Service Fabric aplica directivas de mantenimiento para asegurarse de que la aplicación esté en un estado correcto a medida que avance la actualización. Si se infringen las directivas de mantenimiento, la actualización se suspende o se revierte automáticamente según la *FailureAction* especificada.

En modo *UnmonitoredManual*, el administrador de la aplicación tiene un control total sobre el avance de la actualización. Este modo resulta útil al aplicar las directivas de evaluación de mantenimiento personalizadas o realizar actualizaciones no convencional al omitir completamente la supervisión de estado (por ejemplo, la aplicación ya está en pérdida de datos). Una actualización que se ejecuta en este modo se suspenderá después de completar cada UD y se debe reanudar de forma explícita mediante [Resume-ServiceFabricApplicationUpgrade](/powershell/module/servicefabric/resume-servicefabricapplicationupgrade). Cuando se suspende una actualización y está lista para que el usuario la reanude, su estado de actualización mostrará *RollforwardPending* (consulte [UpgradeState](/dotnet/api/system.fabric.applicationupgradestate)).

Por último, el modo *UnmonitoredAuto* es útil para realizar iteraciones rápidas de actualización durante el desarrollo o las pruebas del servicio, ya que no se necesitan entradas del usuario y no se evalúa ninguna directiva de mantenimiento de la aplicación.

## <a name="upgrade-with-a-diff-package"></a>Actualización con un paquete de diferencias

En lugar de aprovisionar un paquete de aplicación completo, las actualizaciones también pueden realizarse mediante el aprovisionamiento de paquetes de diferencias que contienen solo los paquetes actualizados de código, de configuración y de datos, junto con el manifiesto de aplicación completo y los manifiestos de servicio completos. Los paquetes de aplicación completos se requieren únicamente para la instalación inicial de una aplicación en el clúster. Las actualizaciones subsiguientes pueden provenir de paquetes de aplicación completos o de diferencias.  

Toda referencia en el manifiesto de aplicación o los manifiestos de servicio de un paquete de diferencias, que no se encuentre en el paquete de aplicación se reemplazará automáticamente por la versión aprovisionada actualmente.

Los escenarios de uso de un paquete de diferencias son:

* Cuando tiene un paquete de aplicación grande que hace referencia a varios archivos del manifiesto de servicio o varios paquetes de código, de configuración o de datos.
* Cuando dispone de un sistema de implementación que genera el diseño de compilación directamente desde el proceso de compilación de la aplicación. En este caso, aunque el código no ha cambiado, los ensamblados recién creados obtienen una suma de comprobación diferente. Para usar un paquete de aplicación completo, sería necesario que actualizara la versión de todos los paquetes de código. Con un paquete de diferencias, solo proporciona los archivos que cambiaron y los archivos de manifiesto cuya versión ha cambiado.

Cuando se actualiza una aplicación mediante Visual Studio, un paquete de diferencias se publica automáticamente. Para crear manualmente un paquete de diferencias, se deben actualizar el manifiesto de aplicación y los manifiestos de servicio, pero solo los paquetes modificados deben incluirse en el paquete de aplicación final.

Por ejemplo, comencemos con la siguiente aplicación (se proporcionan los números de versión para facilitar la comprensión):

```text
app1           1.0.0
  service1     1.0.0
    code       1.0.0
    config     1.0.0
  service2     1.0.0
    code       1.0.0
    config     1.0.0
```

Supongamos que desea actualizar solo el paquete de código de service1 mediante un paquete de diferencias. La aplicación actualizada tiene los cambios de versión siguientes:

```text
app1           2.0.0      <-- new version
  service1     2.0.0      <-- new version
    code       2.0.0      <-- new version
    config     1.0.0
  service2     1.0.0
    code       1.0.0
    config     1.0.0
```

En este caso, actualice el manifiesto de aplicación a 2.0.0 y el manifiesto de servicio de service1 para que refleje la actualización del paquete de código. La estructura de carpetas para el paquete de aplicación sería similar a la siguiente:

```text
app1/
  service1/
    code/
```

En otras palabras, cree un paquete de aplicación completo normalmente, a continuación, quite las carpetas de paquetes de datos, de configuración y de código para las que no haya cambiado la versión.

## <a name="upgrade-application-parameters-independently-of-version"></a>Actualización de parámetros de la aplicación independientemente de la versión

A veces, es conveniente cambiar los parámetros de una aplicación de Service Fabric sin cambiar la versión del manifiesto. Puede hacerlo fácilmente mediante la marca **-ApplicationParameter** con el cmdlet **Start-ServiceFabricApplicationUpgrade** de PowerShell de Azure Service Fabric. Suponga que hay una aplicación de Service Fabric con las siguientes propiedades:

```PowerShell
PS C:\> Get-ServiceFabricApplication -ApplicationName fabric:/Application1

ApplicationName        : fabric:/Application1
ApplicationTypeName    : Application1Type
ApplicationTypeVersion : 1.0.0
ApplicationStatus      : Ready
HealthState            : Ok
ApplicationParameters  : { "ImportantParameter" = "1"; "NewParameter" = "testBefore" }
```

Ahora, actualice la aplicación con el cmdlet **Start-ServiceFabricApplicationUpgrade**. En este ejemplo se muestra una actualización supervisada, pero también se puede usar una actualización no supervisada. Para ver una descripción completa de las marcas que acepta este cmdlet, consulte la [referencia del módulo de PowerShell de Azure Service Fabric](/powershell/module/servicefabric/start-servicefabricapplicationupgrade#parameters)

```PowerShell
PS C:\> $appParams = @{ "ImportantParameter" = "2"; "NewParameter" = "testAfter"}

PS C:\> Start-ServiceFabricApplicationUpgrade -ApplicationName fabric:/Application1 -ApplicationTypeVers
ion 1.0.0 -ApplicationParameter $appParams -Monitored

```

Después de la actualización, confirme que la aplicación tiene los parámetros actualizados y la misma versión:

```PowerShell
PS C:\> Get-ServiceFabricApplication -ApplicationName fabric:/Application1

ApplicationName        : fabric:/Application1
ApplicationTypeName    : Application1Type
ApplicationTypeVersion : 1.0.0
ApplicationStatus      : Ready
HealthState            : Ok
ApplicationParameters  : { "ImportantParameter" = "2"; "NewParameter" = "testAfter" }
```

## <a name="roll-back-application-upgrades"></a>Reversión de actualizaciones de aplicaciones

Si bien las actualizaciones se pueden poner al día en uno de tres modos (*Monitored*, *UnmonitoredAuto* o *UnmonitoredManual*), solo se pueden revertir en el modo *UnmonitoredAuto* o *UnmonitoredManual*. La reversión en modo *UnmonitoredAuto* funciona del mismo modo que la puesta al día, con la excepción de que el valor predeterminado de *UpgradeReplicaSetCheckTimeout* es diferente; consulte [Parámetros de actualización de la aplicación](service-fabric-application-upgrade-parameters.md). La reversión en el modo *UnmonitoredManual* funciona del mismo modo que la puesta al día: la reversión se suspenderá después de completar cada UD y se debe reanudar de forma explícita mediante [ Resume-ServiceFabricApplicationUpgrade](/powershell/module/servicefabric/resume-servicefabricapplicationupgrade) para continuar con la reversión.

Las reversiones pueden activarse automáticamente cuando se infringen las directivas de mantenimiento de una actualización en modo *Monitored* con una *FailureAction* de *Rollback* (consulte [Parámetros de actualización de la aplicación](service-fabric-application-upgrade-parameters.md)), o explícitamente con [Start-ServiceFabricApplicationRollback](/powershell/module/servicefabric/start-servicefabricapplicationrollback).

Durante la reversión, el valor de *UpgradeReplicaSetCheckTimeout* y el modo se pueden cambiar en cualquier momento con [Update-ServiceFabricApplicationUpgrade](/powershell/module/servicefabric/update-servicefabricapplicationupgrade).

## <a name="next-steps"></a>Pasos siguientes
[actualización de aplicaciones usando Visual Studio](service-fabric-application-upgrade-tutorial.md) ofrece información para actualizar una aplicación mediante Visual Studio.

[Actualización de aplicaciones de Service Fabric con PowerShell](service-fabric-application-upgrade-tutorial-powershell.md) se explica en detalle lo que tiene que hacer para actualizar una aplicación mediante PowerShell.

Puede controlar cómo se actualiza una aplicación usando [parámetros de actualización](service-fabric-application-upgrade-parameters.md).

Consiga que sus actualizaciones de aplicaciones sean compatibles aprendiendo a usar la [serialización de datos](service-fabric-application-upgrade-data-serialization.md).

Solucione problemas habituales en las actualizaciones de aplicaciones consultando los pasos que figuran en [Solución de problemas de las actualizaciones de aplicaciones](service-fabric-application-upgrade-troubleshooting.md).
