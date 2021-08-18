---
title: Introducción a la supervisión y el diagnóstico de Azure Service Fabric
description: Obtenga información sobre la supervisión y el diagnóstico para los clústeres, las aplicaciones y los servicios de Azure Service Fabric.
ms.topic: conceptual
ms.date: 1/17/2019
ms.openlocfilehash: d3a7060b7a12cb9a57a78c1bbb3fc0b58656ef6d
ms.sourcegitcommit: 30e3eaaa8852a2fe9c454c0dd1967d824e5d6f81
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/22/2021
ms.locfileid: "112455165"
---
# <a name="monitoring-and-diagnostics-for-azure-service-fabric"></a>Supervisión y diagnóstico para Azure Service Fabric

En este artículo se proporciona información general acerca de la supervisión y del diagnóstico de Azure Service Fabric. La supervisión y el diagnóstico son fundamentales para el desarrollo, las pruebas y la implementación de flujos de trabajo en cualquier entorno de la nube. Por ejemplo, puede realizar un seguimiento de cómo se usan las aplicaciones, de las acciones que realiza la plataforma Service Fabric, del uso de recursos con los contadores de rendimiento y del estado general del clúster. Puede utilizar esta información para diagnosticar y corregir cualquier problema, así como para prevenir que se repita en el futuro. En las secciones siguientes se explican brevemente todas las áreas de la supervisión de Service Fabric que se deben tener en cuenta para las cargas de trabajo de producción. 

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="application-monitoring"></a>Supervisión de aplicaciones
La supervisión de aplicaciones realiza un seguimiento del uso de las características y componentes de una aplicación. Le recomendamos supervisar las aplicaciones para asegurarse de que se detectan los problemas que afectan a los usuarios. La responsabilidad de la supervisión de las aplicaciones es de los usuarios que desarrollan una aplicación y sus servicios, ya que es única para la lógica de negocios de la aplicación. La supervisión de las aplicaciones puede ser útil en los siguientes escenarios:
* ¿Cuánto tráfico experimenta mi aplicación? - ¿Necesita escalar los servicios para satisfacer las demandas del usuario o abordar un potencial cuello de botella en la aplicación?
* ¿Mis llamadas de servicio a servicio se han realizado correctamente y se ha llevado a cabo su seguimiento?
* ¿Qué acciones realizan los usuarios de mi aplicación? - La recopilación de datos de telemetría puede guiar el desarrollo de futuras características y un mejor diagnóstico de los errores de la aplicación
* ¿Mi aplicación está iniciando excepciones no controladas? 
* ¿Qué ocurre en los servicios que se ejecutan en mis contenedores?

Lo mejor de la supervisión de la aplicación es que los desarrolladores pueden usar las herramientas y el marco que quieran, puesto que reside en el contexto de la aplicación. Puede obtener más información acerca de la solución de Azure para la supervisión de aplicaciones con Azure Monitor: Application Insights en [Análisis de eventos con Application Insights](service-fabric-diagnostics-event-analysis-appinsights.md).
También tenemos un tutorial con instrucciones para [configurarlo para las aplicaciones de .NET](service-fabric-tutorial-monitoring-aspnet.md). En este tutorial se incluyen instrucciones para instalar las herramientas adecuadas, un ejemplo para escribir telemetría personalizada en la aplicación y la visualización de telemetría y diagnósticos de la aplicación en Azure Portal. 


## <a name="platform-cluster-monitoring"></a>Supervisión de plataforma (clúster)
Un usuario controla qué telemetría procede de su aplicación, puesto que escribe el propio código, pero ¿qué sucede con los diagnósticos de la plataforma Service Fabric? Uno de los objetivos de Service Fabric es que las aplicaciones sean resistentes a los errores de hardware. Esto se logra gracias a la capacidad que tienen los servicios del sistema de la plataforma de detectar problemas de infraestructura y conmutar por error las cargas de trabajo rápidamente a otros nodos del clúster. Pero en este caso particular, ¿qué ocurre si los propios servicios del sistema tienen problemas? ¿O, si al intentar implementar o mover una carga de trabajo, se infringen las reglas de ubicación de los servicios? Service Fabric proporciona diagnósticos para estos y otros casos, a fin de garantizar que esté informado de las actividades que tienen lugar en su clúster. Algunos escenarios de ejemplo de la supervisión del clúster son:

Service Fabric proporciona un conjunto completo de eventos listos para usar. Estos [eventos de Service Fabric](service-fabric-diagnostics-events.md) están disponibles para el acceso a través de EventStore o el canal operativo (canal de eventos expuesto por la plataforma). 

* Canales de eventos de Service Fabric: en Windows, los eventos de Service Fabric están disponibles en un solo proveedor ETW con un conjunto de `logLevelKeywordFilters` relevantes que se usan para elegir entre el canal operativo y el canal de datos y mensajería (es la manera en que se separan los eventos de Service Fabric salientes que se van a filtrar, si es necesario). En Linux, los eventos de Service Fabric proceden de LTTng y se colocan en una tabla de almacenamiento, desde la cual pueden filtrarse según sea necesario. Estos canales contienen eventos estructurados protegidos que pueden utilizarse para comprender mejor el estado del clúster. Los diagnósticos se habilitan de forma predeterminada en el momento de la creación del clúster, donde se crea una tabla de Azure Storage donde se envían los eventos de estos canales para que pueda consultarlos en el futuro. 

* EventStore: EventStore es una característica que ofrece la plataforma que proporciona eventos de la plataforma Service Fabric y que está disponible en Service Fabric Explorer y en la API de REST. Puede ver una vista de instantánea de lo que está ocurriendo en el clúster para cada entidad (por ejemplo, nodo, servicio, aplicación y consulta) según la hora del evento. Obtenga más información sobre EventStore en [Información general de EventStore](service-fabric-diagnostics-eventstore.md).    

![Captura de pantalla que muestra la pestaña EVENTOS con los diversos eventos del panel Nodos, incluido un evento NodeDown.](media/service-fabric-diagnostics-overview/eventstore.png)

Los diagnósticos se proporcionan en forma de un conjunto completo de eventos predefinidos. Estos [eventos de Service Fabric](service-fabric-diagnostics-events.md) muestran las acciones que realiza la plataforma en entidades diferentes, como nodos, aplicaciones, servicios, particiones, etc. En el último escenario de los anteriores, si un nodo dejara de funcionar, la plataforma emitiría un evento `NodeDown` y podría recibir una notificación inmediatamente de la herramienta de supervisión de su elección. Otros ejemplos comunes son `ApplicationUpgradeRollbackStarted` o `PartitionReconfigured` durante una conmutación por error. **Los mismos eventos están disponibles en los clústeres de Windows y Linux.**

Los eventos se envían a través de canales estándar en Windows y Linux, y se pueden leer con cualquier herramienta de supervisión que los admita. La solución de Azure Monitor son los registros de Azure Monitor. Puede obtener más información sobre nuestra [integración de registros de Azure Monitor](service-fabric-diagnostics-event-analysis-oms.md), que incluye un panel operativo personalizado para el clúster y algunas consultas de ejemplo a partir de las que puede crear alertas. Existen más conceptos de supervisión del clúster disponibles en [Generación de eventos y registros de nivel de plataforma](service-fabric-diagnostics-event-generation-infra.md).

### <a name="health-monitoring"></a>Supervisión del estado
La plataforma Service Fabric incluye un modelo de estado, el cual proporciona informes de estado extensibles para el estado de las entidades de un clúster. Cada nodo, aplicación, servicio, participación, réplica o instancia tiene un estado de mantenimiento que se actualiza continuamente. El estado de mantenimiento puede ser "Correcto", "Advertencia" o "Error". Piense en los eventos de Service Fabric como verbos de acciones que realiza el clúster en varias entidades y en el estado como un adjetivo para cada entidad. Cada vez que cambie el estado de una entidad determinada, también se emitirá un evento. De esta forma, puede configurar consultas y alertas para eventos de estado en la herramienta de supervisión de su elección, al igual que cualquier otro evento. 

Asimismo, también permitimos que los usuarios invaliden el estado de entidades. Si la aplicación se somete a una actualización y se producen errores en las pruebas de validación, puede escribir a Service Fabric Health mediante la API de Health para indicar que el estado de la aplicación ya no es correcto, y Service Fabric revertirá automáticamente la actualización. Para obtener más información sobre el modelo de estado, consulte [Introducción a la supervisión del mantenimiento de Service Fabric](service-fabric-health-introduction.md).

![Panel de estado de SFX](media/service-fabric-diagnostics-overview/sfx-healthstatus.png)


### <a name="watchdogs"></a>Guardianes
Por lo general, un guardián es un servicio independiente que vigila el mantenimiento y la carga de los servicios, hace ping a los puntos de conexión e informa acerca de los eventos de mantenimiento inesperados del clúster. Esto puede ayudar a evitar errores que se podrían pasar pasan por alto si solo se tuviera en cuenta el rendimiento de un servicio. Los guardianes son también un buen lugar para hospedar código que realice acciones de subsanación que no requieran interacción del usuario, como limpiar los archivos de registro de almacenamiento a determinados intervalos de tiempo. Si quiere un servicio de vigilancia de SF de código abierto totalmente implementado que incluya un modelo de extensibilidad de vigilancia fácil de usar y que se ejecute en clústeres de Windows y Linux, consulte el proyecto [FabricObserver](https://github.com/Azure-Samples/service-fabric-watchdog-service). FabricObserver es un software listo para producción. Le recomendamos que implemente FabricObserver en los clústeres de producción y de prueba, y que lo amplíe para satisfacer sus necesidades, ya sea a través de su modelo de complementos o mediante su bifurcación y la escritura de sus propios observadores integrados. El primero (complementos) es el enfoque recomendado.

## <a name="infrastructure-performance-monitoring"></a>Supervisión de la infraestructura (rendimiento)
Ahora que hemos analizado los diagnósticos en la aplicación y la plataforma, ¿cómo sabemos si el hardware funciona según lo previsto? La supervisión de la infraestructura subyacente es una parte fundamental del conocimiento del estado del clúster y la utilización de los recursos. La medición del rendimiento del sistema depende de muchos factores, que pueden ser subjetivos según las cargas de trabajo. Estos factores se miden normalmente mediante contadores de rendimiento. Estos contadores de rendimiento pueden proceder de diversos orígenes, como el sistema operativo, .NET Framework o la propia plataforma de Service Fabric. Algunos escenarios en los que podrían ser útiles son los siguientes:

* ¿Estoy usando mi hardware de manera eficiente? Es posible que quiera utilizar el hardware con un rendimiento de la CPU del 90 % o del 10 %. Esto resulta útil al escalar el clúster u optimizar los procesos de la aplicación.
* ¿Puedo predecir problemas de infraestructura de manera proactiva? Muchos problemas vienen precedidos de cambios bruscos (caídas) del rendimiento, por lo que puede utilizar los contadores de rendimiento, como las E/S de la red y la utilización de la CPU, para predecir y diagnosticar los problemas de forma proactiva.

Encontrará una lista de contadores de rendimiento que deberían recopilarse en el nivel de infraestructura en el artículo sobre [Métricas de rendimiento](service-fabric-diagnostics-event-generation-perf.md). 

Service Fabric también proporciona un conjunto de contadores de rendimiento para los modelos de programación de Reliable Services y Reliable Actors. Si usa alguno de estos modelos, estos contadores de rendimiento pueden proporcionar información para garantizar que los actores se aceleran y desaceleran correctamente, o que las solicitudes de servicio de confianza se están gestionando lo suficientemente rápido. Para más información al respecto, consulte [Supervisión de comunicación remota de Reliable Service](service-fabric-reliable-serviceremoting-diagnostics.md#performance-counters) y [Supervisión de rendimiento para Reliable Actors](service-fabric-reliable-actors-diagnostics.md#performance-counters). 

La solución de Azure Monitor para recopilarlos son los registros de Azure Monitor, del mismo modo que la supervisión de nivel de plataforma. Use el [agente de Log Analytics](service-fabric-diagnostics-oms-agent.md) para recopilar los contadores de rendimiento correspondientes y verlos en los registros de Azure Monitor.

## <a name="recommended-setup"></a>Configuración recomendada
Después de repasar cada área de los escenarios de supervisión y ejemplo, aquí tiene un resumen de las herramientas de supervisión de Azure y la configuración necesarias para supervisar todas las áreas anteriores. 

* Supervisión de aplicaciones con [Application Insights](service-fabric-tutorial-monitoring-aspnet.md)
* Supervisión de clústeres con el [agente de Azure Diagnostics](service-fabric-diagnostics-event-aggregation-wad.md) y [registros de Azure Monitor](service-fabric-diagnostics-oms-setup.md)
* Supervisión de la infraestructura con [registros de Azure Monitor](service-fabric-diagnostics-oms-agent.md)

También puede utilizar y modificar la plantilla de ARM de ejemplo disponible [aquí](service-fabric-diagnostics-oms-setup.md#deploy-azure-monitor-logs-with-azure-resource-manager) para automatizar la implementación de todos los recursos y agentes necesarios. 

## <a name="other-logging-solutions"></a>Otras soluciones de registro

Aunque las dos soluciones recomendadas ([registros de Azure Monitor](service-fabric-diagnostics-event-analysis-oms.md) y [Application Insights](service-fabric-diagnostics-event-analysis-appinsights.md)) tienen incorporada la integración con Service Fabric, muchos eventos se escriben mediante proveedores de ETW y pueden ampliarse a otras soluciones de registro. También necesita consultar [Elastic Stack](https://www.elastic.co/products) (especialmente, si planea ejecutar un clúster en un entorno sin conexión), [Dynatrace](https://www.dynatrace.com/) o cualquier otra plataforma que prefiera. [Aquí](service-fabric-diagnostics-partners.md) encontrará una lista de los partners integrados.

Los puntos clave para cualquier plataforma que elija deben incluir su grado de comodidad con la interfaz de usuario, las funcionalidades de consulta, las visualizaciones y los paneles personalizados disponibles, y las herramientas adicionales que proporcionen para mejorar la experiencia de supervisión. 

## <a name="next-steps"></a>Pasos siguientes

* Para empezar a trabajar con la instrumentación de las aplicaciones, consulte [Generación de eventos y registros de nivel de aplicación](service-fabric-diagnostics-event-generation-app.md).
* Consulte los pasos necesarios para configurar Application Insights para una aplicación en [Supervisión y diagnóstico de una aplicación de ASP.NET Core de Service Fabric](service-fabric-tutorial-monitoring-aspnet.md).
* Obtenga más información acerca de la supervisión de la plataforma y los eventos que proporciona Service Fabric en [Generación de eventos y registros de nivel de plataforma](service-fabric-diagnostics-event-generation-infra.md)
* Configure la integración de los registros de Azure Monitor con Service Fabric en [Configurar registros de Azure Monitor para un clúster](service-fabric-diagnostics-oms-setup.md)
* Aprenda a configurar registros de Azure Monitor para supervisar contenedores. Consulte el tema sobre la [supervisión y el diagnóstico de contenedores Windows en Azure Service Fabric](service-fabric-tutorial-monitoring-wincontainers.md).
* Vea problemas y soluciones de diagnóstico de ejemplo con Service Fabric en [Escenarios comunes de diagnóstico](service-fabric-diagnostics-common-scenarios.md).
* Vea otros productos de diagnóstico que se integran con Service Fabric en [Partners de diagnóstico de Service Fabric](service-fabric-diagnostics-partners.md).
* Más información acerca de las recomendaciones generales de supervisión para los recursos de Azure: [Procedimientos recomendados: supervisión y diagnóstico](/azure/architecture/best-practices/monitoring). 
