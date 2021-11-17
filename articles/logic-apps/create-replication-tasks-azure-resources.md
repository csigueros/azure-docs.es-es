---
title: Creación de tareas de replicación para recursos de Azure
description: Replique recursos de Azure mediante plantillas de tareas de replicación basadas en flujos de trabajo de Azure Logic Apps.
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, azla
ms.topic: how-to
ms.date: 11/09/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 42364c1c1fc0f9d2d220b4b9156a4a7ab4a410fa
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/11/2021
ms.locfileid: "132337448"
---
# <a name="create-replication-tasks-for-azure-resources-using-azure-logic-apps-preview"></a>Creación de tareas de replicación para recursos de Azure mediante Azure Logic Apps (versión preliminar)

> [!IMPORTANT]
> Esta funcionalidad está en versión preliminar y está sujeta a las [Condiciones de uso complementarias para las versiones preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Aunque la disponibilidad y la confiabilidad máximas son las máximas prioridades operativas de los servicios de Azure, todavía existen muchas formas de que la comunicación se detenga debido a problemas de red o de resolución de nombres, a errores o a una falta de respuesta temporal. Estas condiciones no son tan "desastrosas" como para querer abandonar la implementación regional por completo, como podría hacer en una situación de recuperación ante desastres. Sin embargo, el escenario empresarial de algunas aplicaciones puede verse afectado por eventos de disponibilidad que no duran más que unos minutos o incluso segundos.

Para reducir el efecto que los eventos impredecibles pueden tener en sus recursos de Azure en una región de Azure, puede replicar el contenido de estos recursos de una región a otra para poder mantener la continuidad del negocio. En Azure, puede crear una [*tarea de replicación*](#replication-task) que mueva los datos, los eventos o los mensajes de un origen de una región a un destino de otra región. De esta manera, puede tener el destino fácilmente disponible si el origen se desconecta y el destino tiene que tomar el relevo.

> [!NOTE]
> También puede utilizar las tareas de replicación para mover contenidos entre entidades de la misma región, pero si toda la región deja de estar disponible o experimenta una interrupción, tanto el origen como el destino se verán afectados.

En este artículo se proporciona información general sobre las tareas de replicación impulsadas por Azure Logic Apps y se muestra cómo crear una tarea de replicación de ejemplo para colas de Azure Service Bus. Si no está familiarizado con las aplicaciones lógicas y los flujos de trabajo, revise [¿Qué es Azure Logic Apps?](logic-apps-overview.md) y [Comparación de las opciones de un solo inquilino, multiinquilino y entorno del servicio de integración para Azure Logic Apps](single-tenant-overview-compare.md).

<a name="replication-task"></a>

## <a name="what-is-a-replication-task"></a>¿Qué es una tarea de replicación?

Generalmente, una tarea de replicación recibe datos, eventos o mensajes de un origen, mueve ese contenido a un destino y luego lo elimina del origen, excepto cuando el origen es una entidad de Event Hubs. La tarea de replicación normalmente mueve el contenido sin cambios, pero las tareas de replicación impulsadas por Azure Logic Apps también agregan [propiedades de replicación](#replication-properties). Si los protocolos de origen y destino difieren, estas tareas también realizan asignaciones entre estructuras de metadatos. Las tareas de replicación no tienen estado, lo que significa que no comparten estados ni otros efectos secundarios entre ejecuciones paralelas o secuenciales de una tarea.

Cuando se usan las plantillas de tareas de replicación disponibles, cada tarea de replicación que se crea tiene un [flujo de trabajo sin estado](single-tenant-overview-compare.md#stateful-stateless) subyacente en un recurso de **Logic App (Standard)** (Aplicación lógica [estándar]), que puede incluir varios flujos de trabajo para las tareas de replicación. Este recurso se hospeda en una instancia de Azure Logic Apps de inquilino único, que es un entorno de ejecución escalable y confiable para configurar y ejecutar aplicaciones sin servidor, incluidas las tareas de replicación y federación. Este entorno de ejecución de Azure Logic Apps de inquilino único también usa el [modelo de extensibilidad de Azure Functions](../azure-functions/functions-bindings-register.md) y se hospeda como una extensión en el entorno de ejecución de esta solución. Este diseño proporciona portabilidad, flexibilidad y más rendimiento para los flujos de trabajo de las aplicaciones lógicas, además de otras funcionalidades y ventajas heredadas de la plataforma Azure Functions y del ecosistema Azure App Service.

Para más información sobre la replicación y la federación, consulte la siguiente documentación:

- [Federación en varios sitios y regiones de Event Hubs](../event-hubs/event-hubs-federation-overview.md)
- [Patrones de tareas de replicación de eventos](../event-hubs/event-hubs-federation-patterns.md)
- [Replicación de mensajes de Service Bus y federación entre regiones](../service-bus-messaging/service-bus-federation-overview.md)
- [Patrones de tareas de replicación de mensajes](../service-bus-messaging/service-bus-federation-patterns.md)

<a name="replication-task-templates"></a>

## <a name="replication-task-templates"></a>Plantillas de tareas de replicación

Actualmente, tiene a s disposición plantillas de tareas de replicación en [Azure Event Hubs](../event-hubs/event-hubs-about.md) y [Azure Service Bus](../service-bus-messaging/service-bus-messaging-overview.md). En la tabla siguiente se enumeran las plantillas de tareas de replicación disponibles actualmente en esta versión preliminar:

| Tipo de recurso | Origen y destino de replicación |
|---------------|-------------------------------|
| Espacio de nombres de Azure Event Hubs | - De instancia de Event Hubs a instancia de Event Hubs <br>- De instancia de Event Hubs a cola de Service Bus <br>- De instancia de Event Hubs a tema de Service Bus |
| Espacio de nombres de Azure Service Bus | - De cola de Service Bus a cola de Service Bus <br>- De cola de Service Bus a tema de Service Bus <br>- De tema de Service Bus a tema de Service Bus <br>- De cola de Service Bus a instancia de Event Hubs <br>- De tema de Service Bus a cola de Service Bus <br>- De tema de Service Bus a instancia de Event Hubs |
|||

### <a name="replication-topology-and-workflow"></a>Topología y flujo de trabajo de replicación

Para ayudarle a visualizar cómo funciona una tarea de replicación impulsada por Azure Logic Apps (Estándar), en los diagramas siguientes se muestra la estructura y el flujo de trabajo de la tarea de replicación para las instancias de Event Hubs y para las colas de Service Bus.

#### <a name="replication-topology-for-event-hubs"></a>Topología de replicación de Event Hubs

En el diagrama siguiente se muestra el flujo de trabajo de la topología y la tarea de replicación entre instancias de Event Hubs:

![Diagrama conceptual que muestra la topología de la tarea de replicación impulsada por un flujo de trabajo de "Logic App (Standard)" (Aplicación lógica [estándar]) entre instancias de Event Hubs.](media/create-replication-tasks-azure-resources/replication-topology-event-hubs.png)

Para información sobre la replicación y la federación en Azure Event Hubs, consulte la siguiente documentación:

- [Federación en varios sitios y regiones de Event Hubs](../event-hubs/event-hubs-federation-overview.md)
- [Patrones de tareas de replicación de eventos](../event-hubs/event-hubs-federation-patterns.md)

#### <a name="replication-topology-for-service-bus"></a>Topología de replicación de Service Bus

En el diagrama siguiente se muestra el flujo de trabajo de la topología y la tarea de replicación entre colas de Service Bus:

![Diagrama conceptual que muestra la topología de la tarea de replicación impulsada por el flujo de trabajo de "Logic App (Standard)" (Aplicación lógica [estándar]) entre colas de Service Bus.](media/create-replication-tasks-azure-resources/replication-topology-service-bus-queues.png)

Para información sobre la replicación y la federación en Azure Service Bus, consulte la siguiente documentación:

- [Replicación de mensajes de Service Bus y federación entre regiones](../service-bus-messaging/service-bus-federation-overview.md)
- [Patrones de tareas de replicación de mensajes](../service-bus-messaging/service-bus-federation-patterns.md)

<a name="replication-properties"></a>

## <a name="metadata-and-property-mappings"></a>Asignaciones de propiedades y metadatos

En el caso Event Hubs, los siguientes elementos obtenidos del espacio de nombres de Event Hubs de origen se sustituyen por nuevos valores asignados por el servicio en el espacio de nombres de Event Hubs de destino: metadatos asignados por el servicio de un evento, hora de puesta en cola original, número de secuencia y desplazamiento. Sin embargo, para las [funciones auxiliares](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/src/Azure.Messaging.Replication) y las tareas de replicación de los ejemplos proporcionados por Azure, los valores originales se conservan en las propiedades del usuario: `repl-enqueue-time` (cadena ISO8601), `repl-sequence` y `repl-offset`. Dichas propiedades son de tipo `string` y contienen el valor en cadena de las propiedades originales respectivas. Si el evento se reenvía varias veces, los metadatos asignados por el servicio del origen inmediato se anexan a las propiedades ya existentes, con los valores separados por punto y coma. Para más información, consulte [Metadatos asignados por el servicio: patrones de tareas de replicación de eventos](../event-hubs/event-hubs-federation-patterns.md#service-assigned-metadata).

En el caso de Service Bus, los siguientes elementos obtenidos de la cola o tema de Service Bus de origen se sustituyen por nuevos valores asignados por el servicio en la cola o tema de Service Bus de destino: metadatos asignados por el servicio de un mensaje, hora de puesta en cola original y número de secuencia. Sin embargo, en las tareas de replicación predeterminadas de los ejemplos proporcionados por Azure, los valores originales se conservan en las propiedades del usuario: `repl-enqueue-time` (cadena ISO8601) y `repl-sequence`. Dichas propiedades son de tipo `string` y contienen el valor en cadena de las propiedades originales respectivas. Si el mensaje se reenvía varias veces, los metadatos asignados por el servicio del origen inmediato se anexan a las propiedades existentes, y los valores se separan con punto y coma. Para más información, consulte [Metadatos asignados por el servicio: patrones de tareas de replicación de mensajes](../service-bus-messaging/service-bus-federation-patterns.md#service-assigned-metadata).

Cuando una tarea se replica de Service Bus a Event Hubs, la tarea asigna solo la propiedad `User Properties` a la propiedad `Properties`. Sin embargo, cuando la tarea se replica de Event Hubs a Service Bus, la tarea asigna las siguientes propiedades:

| De Event Hubs | A Service Bus |
|-----------------|----------------|
| ContentType | ContentType |
| CorrelationId | CorrelationId |
| MessageId | MessageId |
| PartitionKey | PartitionKey SessionId |
| Propiedades | Propiedades de usuario |
| ReplyTo | ReplyTo |
| ReplyToGroupName | ReplyToSessionId |
| Asunto | Etiqueta |
| A | A |
|||

<a name="order-preservation"></a>

## <a name="order-preservation"></a>Conservación del orden

En el caso de Event Hubs, la replicación entre el mismo número de [particiones](../event-hubs/event-hubs-features.md#partitions) crea clones 1:1 sin cambios en los eventos, pero también puede incluir duplicados. Sin embargo, en la replicación entre diferentes números de particiones, solo se conserva el orden relativo de los eventos en función de la clave de partición, pero también pueden incluirse duplicados. Para más información, consulte [Secuencias y conservación del orden](../event-hubs/event-hubs-federation-patterns.md#streams-and-order-preservation).

En el caso de Service Bus, debe habilitar las sesiones para que las secuencias de mensajes con el mismo identificador de sesión recuperado del origen se envíen a la cola o tema de destino como un lote en la secuencia original y con el mismo identificador de sesión. Para más información, consulte [Secuencias y conservación del orden](../service-bus-messaging/service-bus-federation-patterns.md#sequences-and-order-preservation).

Para más información sobre la federación de varios sitios y varias regiones de los servicios de Azure en los que puede crear tareas de replicación, consulte la siguiente documentación:

- [Federación en varios sitios y regiones de Event Hubs](../event-hubs/event-hubs-federation-overview.md)
- [Patrones de tareas de replicación de eventos](../event-hubs/event-hubs-federation-patterns.md)
- [Replicación de mensajes de Service Bus y federación entre regiones](../service-bus-messaging/service-bus-federation-overview.md)
- [Patrones de tareas de replicación de mensajes](../service-bus-messaging/service-bus-federation-patterns.md)

<a name="pricing"></a>

## <a name="pricing"></a>Precios

De forma subyacente, una tarea de replicación está impulsada por un flujo de trabajo sin estado de un recurso **Logic App (Standard)** (Aplicación lógica [estándar]) que está hospedado en una instancia de Azure Logic Apps de inquilino único. Al crear esta tarea de replicación, inmediatamente se generan costos. El uso, la medición, la facturación y el modelo de precios siguen el [plan de hospedaje estándar](logic-apps-pricing.md#standard-pricing) y los [planes de tarifa estándar](logic-apps-pricing.md#standard-pricing-tiers).

<a name="scale-up"></a>

En función del número de eventos que recibe Event Hubs o de los mensajes que administra Service Bus, el plan de hospedaje puede escalarse o reducirse verticalmente para mantener un uso mínimo de vCPU y una latencia baja durante la replicación activa. Este comportamiento requiere que cree un recurso de aplicación lógica para usar para la tarea de replicación. [Elija el plan de tarifa Estándar adecuado](#scale-out) para que Azure Logic Apps no limite ni empiece a llegar al límite de uso de la CPU y pueda garantizar una velocidad de replicación rápida.

> [!NOTE]
> Si la aplicación comienza con una instancia del plan WS1 y, a continuación, se escala horizontalmente a dos instancias, el costo es el doble del costo de WS1, suponiendo que los planes se ejecuten todo el día. Si escala verticalmente la aplicación al plan WS2 y usa una instancia, el costo es efectivamente el mismo que dos instancias del plan WS1. Del mismo modo, si escala verticalmente la aplicación al plan WS3 y usa una instancia, el costo es efectivamente el mismo que dos instancias del plan WS2 o cuatro instancias del plan WS1.

<a name="scale-out"></a>

Los ejemplos siguientes muestran el plan de tarifa del plan de hospedaje y las opciones de configuración que proporcionan el mejor rendimiento y costo para escenarios de tareas de replicación específicos, en función de si el escenario es Event Hubs o Service Bus y varios valores de configuración.

> [!NOTE]
> En los ejemplos de las secciones siguientes se usa 800 como valor predeterminado para el recuento de capturas previas, el tamaño máximo del lote de eventos para Event Hubs y el recuento máximo de mensajes para Service Bus, suponiendo que el tamaño del evento o mensaje sea de 1 KB. En función de los tamaños de evento, puede ajustar el recuento de capturas previas, el tamaño máximo del lote de eventos o el recuento máximo de mensajes. Por ejemplo, si el tamaño del evento o el tamaño del mensaje es superior a 1 KB, puede reducir los valores del recuento de capturas previas y el tamaño máximo del lote de eventos o el número de mensajes de 800.

### <a name="event-hubs-scale-out"></a>Escalado horizontal de Event Hubs

En los ejemplos siguientes se muestra el plan de tarifa de hospedaje y las opciones de configuración de una tarea de replicación entre dos espacios de nombres de Event Hubs *en la misma región*, en función del número de [particiones](../event-hubs/event-hubs-features.md#partitions), el número de eventos por segundo y otros valores de configuración.

En los ejemplos de esta sección se usa 800 como valor predeterminado para el recuento de capturas previas y el tamaño máximo del lote de eventos, suponiendo que el tamaño del evento es de 1 KB. En función de los tamaños de evento, puede ajustar el recuento de capturas previas y el tamaño máximo del lote de eventos. Por ejemplo, si el tamaño del evento es superior a 1 KB, puede reducir los valores del recuento de capturas previas y el tamaño máximo del lote de eventos de 800.

| Plan de tarifa | Recuento de particiones | Eventos por segundo | Ráfagas máximas* | Instancias siempre preparadas* | Recuento de capturas previas* | Tamaño máximo del lote de eventos* |
|--------------|-----------------|-------------------|----------------|-------------------------|-----------------|-----------------|
| **WS1** | 1 | 1000 | 1 | 1 | 800 | 800 |
| **WS1** | 2 | 2000 | 1 | 1 | 800 | 800 |
| **WS2** | 4 | 4000 | 2 | 1 | 800 | 800 |
| **WS2** | 8 | 8000 | 2 | 1 | 800 | 800 |
| **WS3** | 16 | 16000 | 2 | 1 | 800 | 800 |
| **WS3** | 32 | 32000 | 3 | 1 | 800 | 800 |
||||||||

\* Para obtener más información sobre los valores que puede cambiar para cada plan de tarifa, revise la tabla siguiente:

| Value | Descripción |
|-------|-------------|
| **Ráfagas máximas** | Número *máximo* de trabajos elásticos que se escalan horizontalmente bajo carga. Si la aplicación subyacente necesita un número de instancias mayor al de las *instancias siempre preparadas* en la fila de la tabla siguiente, la aplicación se puede seguir escalando horizontalmente hasta que el número de instancias alcance el límite máximo de ráfagas. Para cambiar este valor, consulte [Edición de la configuración de escalado horizontal del plan de hospedaje](#edit-plan-scale-out-settings) más adelante en este artículo. <p>**Nota**: Las instancias que superen el tamaño del plan *solo* se facturarán por segundo cuando estén en ejecución y las tenga asignadas. La plataforma hace todo lo posible para escalar horizontalmente la aplicación al límite máximo definido. <p>**Sugerencia**: Como recomendación, seleccione un valor máximo mayor de lo que podría necesitar para que la plataforma pueda escalar horizontalmente para controlar una carga mayor, si es necesario, ya que las instancias no usadas no se facturan. <p>Para obtener más información, revise la siguiente documentación, ya que el plan Estándar de flujo de trabajo comparte algunos aspectos con el plan Premium de Azure Functions: <p>- [Configuración del plan y la SKU: plan Premium de Azure Functions](../azure-functions/functions-premium-plan.md#plan-and-sku-settings) <br>- [¿Qué es la ampliación en la nube?](https://azure.microsoft.com/overview/what-is-cloud-bursting/)? |
| **Instancias siempre preparadas** | Número mínimo de instancias que siempre están listas y preparadas para hospedar la aplicación. El número mínimo siempre es 1. Para cambiar este valor, consulte [Edición de la configuración de escalado horizontal del plan de hospedaje](#edit-plan-scale-out-settings) más adelante en este artículo. <p>**Nota**: Las instancias que superen el tamaño del plan se facturan por segundo *tanto si* están en ejecución como si no cuando las tenga asignadas. <p>Para obtener más información, revise la siguiente documentación, ya que el plan Estándar de flujo de trabajo comparte algunos aspectos con el plan Premium de Azure Functions: [Instancias siempre preparadas: plan Premium de Azure Functions](../azure-functions/functions-premium-plan.md#always-ready-instances). |
| **Recuento de capturas previas** | Valor predeterminado para la configuración de la aplicación `AzureFunctionsJobHost__extensions__eventHubs__eventProcessorOptions__prefetchCount` en el recurso de aplicación lógica que determina el recuento de capturas previas usado por la clase `EventProcessorHost` subyacente. Para agregar o especificar un valor diferente para esta configuración de la aplicación, revise [Administración de la configuración de la aplicación: local.settings.json](edit-app-settings-host-settings.md?tabs=azure-portal#manage-app-settings), por ejemplo: <p>- **Nombre**: `AzureFunctionsJobHost__extensions__eventHubs__eventProcessorOptions__prefetchCount` <br>- **Valor**: `800` (sin límite máximo) <p>Para obtener más información sobre la propiedad `prefetchCount`, revise la siguiente documentación: <p>- [Configuración host.json: desencadenador y enlaces de Azure Event Hubs para Azure Functions](../azure-functions/functions-bindings-event-hubs.md#hostjson-settings) <br>- [Propiedad EventProcessorOptions.PrefetchCount](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessoroptions.prefetchcount) <br>- [Equilibrio de carga de particiones entre varias instancias de una aplicación](../event-hubs/event-processor-balance-partition-load.md). <br>- [Host del procesador de eventos](../event-hubs/event-hubs-event-processor-host.md) <br>- [Clase EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) |
| **Tamaño máximo del lote de eventos** | Valor predeterminado de la configuración de la aplicación `AzureFunctionsJobHost__extensions__eventHubs__eventProcessorOptions__maxBatchSize` en el recurso de aplicación lógica que determina el número máximo de eventos recibidos por cada bucle de recepción. Para agregar o especificar un valor diferente para esta configuración de la aplicación, revise [Administración de la configuración de la aplicación: local.settings.json](edit-app-settings-host-settings.md?tabs=azure-portal#manage-app-settings), por ejemplo: <p>- **Nombre**: `AzureFunctionsJobHost__extensions__eventHubs__eventProcessorOptions__maxBatchSize` <br>- **Valor**: `800` (sin límite máximo) <p>Para obtener más información sobre la propiedad `maxBatchSize`, revise la siguiente documentación: <p>- [Configuración host.json: desencadenador y enlaces de Azure Event Hubs para Azure Functions](../azure-functions/functions-bindings-event-hubs.md#hostjson-settings) <br>- [Propiedad EventProcessorOptions.MaxBatchSize](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessoroptions.maxbatchsize) <br>- [Host del procesador de eventos](../event-hubs/event-hubs-event-processor-host.md) |
|||

### <a name="service-bus-scale-out"></a>Escalado horizontal de Service Bus

En los ejemplos siguientes se muestra el plan de tarifa de hospedaje y las opciones de configuración de una tarea de replicación entre dos espacios de nombres de Service Bus *en la misma región*, en función del número de mensajes por segundo y otros valores de configuración.

En los ejemplos de esta sección se usa 800 como valor predeterminado para el recuento de capturas previas y el recuento máximo de mensajes, suponiendo que el tamaño del mensaje es de 1 KB. En función de los tamaños de mensaje, puede ajustar el recuento de capturas previas y el recuento máximo de mensajes. Por ejemplo, si el tamaño del mensaje es superior a 1 KB, puede reducir los valores del recuento de capturas previas y el recuento máximo de mensajes de 800.

| Plan de tarifa | Mensajes por segundo | Ráfagas máximas* | Instancias siempre preparadas* | Recuento de capturas previas* | Recuento máximo de mensajes* |
|--------------|---------------------|-----------------|-------------------------|-----------------|------------------------|
| **WS1** | 2000 | 1 | 1 | 800 | 800 |
| **WS2** | 2.500 | 1 | 1 | 800 | 800 |
| **WS3** | 3500 | 1 | 1 | 800 | 800 |
|||||||

\* Para obtener más información sobre los valores que puede cambiar para cada plan de tarifa, revise la tabla siguiente:

| Value | Descripción |
|-------|-------------|
| **Ráfagas máximas** | Número *máximo* de trabajos elásticos que se escalan horizontalmente bajo carga. Si la aplicación subyacente necesita un número de instancias mayor al de las *instancias siempre preparadas* en la fila de la tabla siguiente, la aplicación se puede seguir escalando horizontalmente hasta que el número de instancias alcance el límite máximo de ráfagas. Para cambiar este valor, consulte [Edición de la configuración de escalado horizontal del plan de hospedaje](#edit-plan-scale-out-settings) más adelante en este artículo. <p>**Nota**: Las instancias que superen el tamaño del plan *solo* se facturarán por segundo cuando estén en ejecución y las tenga asignadas. La plataforma hace todo lo posible para escalar horizontalmente la aplicación al límite máximo definido. <p>**Sugerencia**: Como recomendación, seleccione un valor máximo mayor de lo que podría necesitar para que la plataforma pueda escalar horizontalmente para controlar una carga mayor, si es necesario, ya que las instancias no usadas no se facturan. <p>Para obtener más información, revise la siguiente documentación, ya que el plan Estándar de flujo de trabajo comparte algunos aspectos con el plan Premium de Azure Functions: <p>- [Configuración del plan y la SKU: plan Premium de Azure Functions](../azure-functions/functions-premium-plan.md#plan-and-sku-settings) <br>- [¿Qué es la ampliación en la nube?](https://azure.microsoft.com/overview/what-is-cloud-bursting/)? |
| **Instancias siempre preparadas** | Número mínimo de instancias que siempre están listas y preparadas para hospedar la aplicación. El número mínimo siempre es 1. Para cambiar este valor, consulte [Edición de la configuración de escalado horizontal del plan de hospedaje](#edit-plan-scale-out-settings) más adelante en este artículo. <p>**Nota**: Las instancias que superen el tamaño del plan se facturan por segundo *tanto si* están en ejecución como si no cuando las tenga asignadas. <p>Para obtener más información, revise la siguiente documentación, ya que el plan Estándar de flujo de trabajo comparte algunos aspectos con el plan Premium de Azure Functions: [Instancias siempre preparadas: plan Premium de Azure Functions](../azure-functions/functions-premium-plan.md#always-ready-instances). |
| **Recuento de capturas previas** | Valor predeterminado para la configuración de la aplicación `AzureFunctionsJobHost__extensions__serviceBus__prefetchCount` en el recurso de aplicación lógica que determina el recuento de capturas previas usado por la clase `ServiceBusProcessor` subyacente. Para agregar o especificar un valor diferente para esta configuración de la aplicación, revise [Administración de la configuración de la aplicación: local.settings.json](edit-app-settings-host-settings.md?tabs=azure-portal#manage-app-settings), por ejemplo: <p>- **Nombre**: `AzureFunctionsJobHost__extensions__eventHubs__eventProcessorOptions__prefetchCount` <br>- **Valor**: `800` (sin límite máximo) <p>Para obtener más información sobre la propiedad `prefetchCount`, revise la siguiente documentación: <p>- [Configuración host.json: enlaces de Azure Service Bus en Azure Functions](../azure-functions/functions-bindings-service-bus.md#hostjson-settings) <br>- [Propiedad ServiceBusProcessor.PrefetchCount](/dotnet/api/azure.messaging.servicebus.servicebusprocessor.prefetchcount) <br>- [Clase ServiceBusProcessor](/dotnet/api/azure.messaging.servicebus.servicebusprocessor) |
| **Recuento máximo de mensajes** | Valor predeterminado de la configuración de la aplicación `AzureFunctionsJobHost__extensions__serviceBus__batchOptions__maxMessageCount` en el recurso de aplicación lógica que determina el número máximo de mensajes que se enviarán al desencadenarse. Para agregar o especificar un valor diferente para esta configuración de la aplicación, revise [Administración de la configuración de la aplicación: local.settings.json](edit-app-settings-host-settings.md?tabs=azure-portal#manage-app-settings), por ejemplo: <p>- **Nombre**: `AzureFunctionsJobHost__extensions__serviceBus__batchOptions__maxMessageCount` <br>- **Valor**: `800` (sin límite máximo) <p>Para obtener más información sobre la propiedad `maxMessageCount`, revise la siguiente documentación: [Configuración host.json: enlaces de Azure Event Hubs para Azure Functions](../azure-functions/functions-bindings-service-bus.md#hostjson-settings).|
|||

## <a name="prerequisites"></a>Requisitos previos

- Una cuenta y una suscripción de Azure. Si aún no tiene una, [regístrese para obtener una cuenta de Azure gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

- Recursos o entidades de origen y destino, que deben existir en diferentes regiones de Azure para que pueda probar el escenario de conmutación por error de recuperación ante desastres geográfica. Estas entidades pueden variar en función de la plantilla de tarea que quiera usar. En el ejemplo de este artículo se usan dos colas de Service Bus, que se encuentran en diferentes espacios de nombres y regiones de Azure.

- Un recurso **Logic App (Standard)** (Aplicación lógica [estándar]) que puede reutilizar al crear la tarea de replicación. De este modo, puede personalizar este recurso en concreto para la tarea de replicación; por ejemplo puede elegir el [plan de hospedaje y el plan de tarifa](#pricing) en función de las necesidades del escenario de replicación, como la capacidad, el rendimiento y el escalado. Aunque puede crear este recurso al crear la tarea de replicación, no puede cambiar la región, el plan de hospedaje o el plan de tarifa. En la lista siguiente se proporcionan otros motivos y procedimientos recomendados para un recurso de aplicación lógica creado anteriormente:

  - Puede crear este recurso de aplicación lógica en una región distinta a la de las entidades de origen y destino de la tarea de replicación.

    Actualmente, esta guía se proporciona debido a la integración nativa de la tarea de replicación en los recursos de Azure. Al crear una tarea de replicación entre entidades y decidir crear un recurso de aplicación lógica en lugar de usar uno existente, la *nueva aplicación lógica se crea en la misma región que la entidad de origen*. Si la región de origen deja de estar disponible, la tarea de replicación tampoco puede funcionar. En un escenario de conmutación por error, la tarea tampoco puede empezar a leer datos del nuevo origen, anteriormente la entidad de destino, que es lo que intenta lograr el [patrón de replicación activo-pasivo](../service-bus-messaging/service-bus-federation-overview.md#active-passive-replication).

  - Puede personalizar este recurso de aplicación lógica con antelación eligiendo el plan de hospedaje y el plan de tarifa, en lugar de usar los atributos predeterminados. De este modo, la tarea de replicación puede procesar más eventos o mensajes por segundo para una replicación más rápida. Si crea este recurso al crear la tarea de replicación, estos atributos predeterminados son fijos.

  - Asegúrese de que este recurso de aplicación lógica solo contenga *flujos de trabajo de tareas de replicación*, especialmente si quiere seguir el patrón de replicación activo-pasivo. Cuando se usa una aplicación lógica existente para crear la tarea de replicación, esta opción agrega la tarea (flujo de trabajo sin estado) a ese recurso de aplicación lógica.

  Para más información, consulte [Creación de un flujo de trabajo de integración con Azure Logic Apps (estándar) de inquilino único en Azure Portal](create-single-tenant-workflows-azure-portal.md).

- Opcional: cadena de conexión para el espacio de nombres de destino. Esta opción permite tener el destino en una suscripción diferente, de modo que pueda configurar la replicación entre suscripciones.

   Para buscar la cadena de conexión de la entidad de destino, siga estos pasos:

   1. En [Azure Portal](https://portal.azure.com), vaya a su espacio de nombres de destino.

   1. En el menú de navegación del espacio de nombres, en **Configuración**, seleccione **Directivas de acceso compartido**.

   1. En el panel **Directivas de acceso compartido** que se abre, en **Directiva**, seleccione **RootManageSharedAccessKey**.

   1. En el panel **Directiva SAS: RootManageSharedAccessKey** que se abre, copie el valor de **Cadena de conexión principal**.

   1. Guarde la cadena de conexión en algún lugar para poder usarla más adelante para conectarse al espacio de nombres de destino.

<a name="naming"></a>

## <a name="naming-conventions"></a>Convenciones de nomenclatura

Tenga en muy en cuenta la estrategia de nomenclatura que usa para las tareas o entidades de replicación, si aún no las ha creado. Asegúrese de que los nombres sean identificables y se puedan diferenciar fácilmente. Por ejemplo, si está trabajando con un espacio de nombres Event Hubs, la tarea de replicación se replica desde todas las instancias de Event Hubs del espacio de nombres de origen. Si está trabajando con colas de Service Bus, en la tabla siguiente se proporciona un ejemplo de cómo asignar un nombre a las entidades y a la tarea de replicación:

| Nombre de origen | Ejemplo | Aplicación de replicación | Ejemplo | Nombre de destino | Ejemplo |
|-------------|---------|-----------------|---------|-------------|---------|
| Espacio de nombres: `<name>-sb-<region>` | `fabrikam-sb-weu` | Aplicación lógica: `<name-source-region-target-region>` | `fabrikam-rep-weu-wus` | Espacio de nombres: `<name>-sb-<region>` | `fabrikam-sb-wus` |
| Queue: `<name>` | `jobs-transfer` | Flujo de trabajo: `<name>` | `jobs-transfer-workflow` | Queue: `<name>` | `jobs` |
|||||||

<a name="create-replication-task"></a>

## <a name="create-a-replication-task"></a>Creación de una tarea de replicación

En este ejemplo se muestra cómo crear una tarea de replicación para colas de Service Bus.

1. En [Azure Portal](https://portal.azure.com), busque el espacio de nombres de Service Bus que quiere usar como origen.

1. En el menú de navegación del espacio de nombres, en la sección **Automatización**, seleccione **Tareas (versión preliminar)** .

   ![Captura de pantalla que muestra Azure Portal y el menú del espacio de nombres de Service Bus de Azure con la opción "Tareas (versión preliminar)" seleccionada.](./media/create-replication-tasks-azure-resources/service-bus-automation-menu.png)

1. En el panel **Tareas**, seleccione **Agregar una tarea** para que pueda seleccionar una plantilla de tarea.

   ![Captura de pantalla que muestra el panel "Tareas (versión preliminar)" con la opción "Agregar una tarea" seleccionada.](./media/create-replication-tasks-azure-resources/add-replication-task.png)

1. En el panel **Agregar una tarea**, en **Seleccionar una plantilla**, en la plantilla de la tarea de replicación que quiere crear, seleccione **Seleccionar**. Si no aparece la página siguiente, seleccione **Siguiente: Autenticar**.

   Para continuar este ejemplo, seleccione la plantilla de tarea **Replicate from Service Bus queue to queue** (Replicar en la cola desde la cola de Service Bus), que replica el contenido entre las colas de Service Bus.

   ![Captura de pantalla que muestra el panel "Agregar una tarea" con la plantilla "Replicate from Service Bus queue to queue" (Replicar en la cola desde la cola Service Bus) seleccionada.](./media/create-replication-tasks-azure-resources/select-replicate-service-bus-template.png)

1. En **Autenticar**, en la sección **Conexiones**, seleccione **Crear** para cada conexión que aparece en la tarea a fin de poder proporcionar credenciales de autenticación para todas las conexiones. Los tipos de conexiones de cada tarea varían según la tarea.

   En este ejemplo se muestra el aviso para crear la conexión al espacio de nombres de Service Bus destino donde existe la cola de destino. La conexión ya existe para el espacio de nombres de Service Bus de origen.

   ![Captura de pantalla que muestra la opción "Crear" seleccionada para la conexión al espacio de nombres de Service Bus de destino.](./media/create-replication-tasks-azure-resources/create-authenticate-connections.png)

1. Proporcione la información necesaria sobre el destino y, luego, seleccione **Crear**.

   En este ejemplo, proporcione un nombre para mostrar para la conexión y, luego, seleccione el espacio de nombres de Service Bus donde existe la cola de destino.

   ![Captura de pantalla que muestra el panel "Conectar" con el nombre para mostrar de la conexión especificado y el espacio de nombres de Service Bus seleccionado.](./media/create-replication-tasks-azure-resources/connect-target-service-bus-namespace.png)

   > [!TIP]
   > También puede crear la conexión con una cadena de conexión. Esta opción permite tener el destino en una suscripción diferente, de modo que pueda configurar la replicación entre suscripciones. El destino, o el origen, según dónde haya empezado a crear la tarea de replicación, se configura dinámicamente para que solo tenga que conectar el destino. Para usar una cadena de conexión, siga estos pasos:
   >
   > 1. En el panel **Conectar**, seleccione **Connect via connection string** (Conectar mediante la cadena de conexión).
   >
   > 2. En el cuadro **Cadena de conexión** , escriba la cadena de conexión del espacio de nombres de destino.

   En el ejemplo siguiente se muestra la conexión creada correctamente:

   ![Captura de pantalla que muestra el panel "Agregar una tarea" con la conexión finalizada al espacio de nombres de Service Bus.](./media/create-replication-tasks-azure-resources/connected-service-bus-namespaces.png)

1. Después de finalizar todas las conexiones, seleccione **Siguiente: Configurar**.

1. En la pestaña **Configurar**, proporcione un nombre para la tarea y cualquier otra información necesaria.

   > [!NOTE]
   > No puede cambiar el nombre de la tarea después de crearla, por lo que debe considerar un nombre que se siga aplicando si [edita el flujo de trabajo subyacente](#edit-task-workflow). Los cambios que haga en el flujo de trabajo subyacente solo se aplican a la tarea que creó y no a la plantilla de tarea.
   >
   > Por ejemplo, si llama a la tarea `fabrikam-rep-weu-wus`, pero después edita el flujo de trabajo subyacente con otra finalidad, no podrá cambiar el nombre de la tarea para que coincida.

   1. Para agregar el flujo de trabajo de la tarea a un recurso **Logic App (Standard)** (Aplicación lógica [estándar]) existente, en la lista **Aplicación lógica**, seleccione la aplicación lógica existente. Si en cambio quiere crear un recurso **Logic App (Standard)** (Aplicación lógica [estándar]), en la lista **Aplicación lógica**, seleccione **Crear nuevo** y proporcione el nombre que usará para la nueva aplicación lógica.

      > [!NOTE]
      > Si crea un recurso de aplicación lógica durante la creación de la tarea de replicación, la aplicación lógica se crea en la *misma región que la entidad de origen*, lo que es problemático si la región de origen deja de estar disponible y no va a funcionar en un escenario de conmutación por error. El procedimiento recomendado es crear un recurso **Logic App (Standard)** (Aplicación lógica [estándar]) en una región diferente a la del origen. Al crear la tarea de replicación, seleccione la aplicación lógica existente y agregue a esta el flujo de trabajo sin estado subyacente. Para obtener más información, revise los [requisitos previos](#prerequisites).

   1. Seleccione **Revisar y crear** cuando haya terminado.

   ![Captura de pantalla que muestra el panel "Agregar una tarea" con información de la tarea de replicación, como el nombre de la tarea, los nombres de cola de origen y de destino, y el nombre que se usará para el recurso de aplicación lógica.](./media/create-replication-tasks-azure-resources/configure-replication-task.png)

1. En la pestaña **Revisar y crear**, confirme los recursos de Azure que necesita la tarea de replicación para funcionar.

   - Si eligió crear un recurso de aplicación lógica para la tarea de replicación, el panel muestra los recursos de Azure necesarios que la tarea de replicación creará para funcionar. Por ejemplo, estos recursos incluyen una cuenta de Azure Storage que contiene información de configuración del recurso de aplicación lógica, el flujo de trabajo y otras operaciones en tiempo de ejecución. Por ejemplo, con Event Hubs, esta cuenta de almacenamiento contiene información de punto de comprobación y la posición o *desplazamiento* en la secuencia donde se detiene la entidad de origen si la región de origen se interrumpe o deja de estar disponible.

     En el ejemplo siguiente se muestra la pestaña **Revisar y crear** si eligió crear una aplicación lógica:

     ![Captura de pantalla que muestra el panel "Revisar y crear" con información de recursos al crear una aplicación lógica.](./media/create-replication-tasks-azure-resources/validate-replication-task-new-logic-app.png)

   - Si decide reutilizar un recurso de aplicación lógica existente para la tarea de replicación, el panel muestra los recursos de Azure que la replicación reutilizará para funcionar.

     En el ejemplo siguiente se muestra la pestaña **Revisar y crear** si eligió crear reutilizar una aplicación lógica existente:

     ![Captura de pantalla que muestra el panel "Revisar y crear" con información de recursos al reutilizar una aplicación lógica existente.](./media/create-replication-tasks-azure-resources/validate-replication-task-existing-logic-app.png)

   > [!NOTE]
   > Si el origen, el destino, o ambos, están detrás de una red virtual, tiene que configurar los permisos y el acceso después de crear la tarea. En este escenario, se requieren permisos y acceso para que el flujo de trabajo de la aplicación lógica pueda realizar la tarea de replicación.

1. Cuando esté listo, seleccione **Crear**.

   La tarea que ha creado, que se activa y ejecuta de manera automática, ahora aparece en la lista **Tareas**.

   > [!TIP]
   > Si la tarea no aparece de inmediato, intente actualizar la tarea de listas o espere un poco antes de actualizar. En la barra de herramientas, seleccione **Actualizar**.

   ![Captura de pantalla que muestra el panel "Tareas" con la tarea de replicación creada.](./media/create-replication-tasks-azure-resources/created-replication-task.png)

1. Si los recursos están detrás de una red virtual, no olvide configurar permisos para que el recurso de aplicación lógica y el flujo de trabajo accedan a esos recursos.

## <a name="set-up-retry-policy"></a>Configuración de la directiva de reintentos

Para evitar la pérdida de datos durante un evento de disponibilidad en cualquier lado de una relación de replicación, debe configurar una directiva de reintentos sólida. Para configurar la directiva de reintentos de una tarea de replicación, revise la [documentación sobre las directivas de reintento en Azure Logic Apps](logic-apps-exception-handling.md#retry-policies) y los pasos para [editar el flujo de trabajo subyacente](#edit-task-workflow).

<a name="review-task-history"></a>

## <a name="review-task-history"></a>Revisión del historial de una tarea

En este ejemplo se muestra cómo ver el historial de ejecuciones de flujo de trabajo de una tarea junto con sus estados, entradas, salidas y otra información, y se sigue usando el ejemplo con una tarea de replicación de cola de Service Bus.

1. En [Azure Portal](https://portal.azure.com), busque el recurso o la entidad de Azure que tiene el historial de tareas que quiere revisar.

   En este ejemplo, este recurso es un espacio de nombres de Service Bus.

1. En el menú de navegación del recurso, en **Configuración**, en la sección **Automatización**, seleccione **Tareas (versión preliminar)** .

1. En el panel **Tareas**, busque la tarea que quiere revisar. En la columna **Ejecuciones** de la tarea, seleccione **Ver**.

   ![Captura de pantalla que muestra el panel "Tareas", una tarea de replicación y la opción "Ver" seleccionada.](./media/create-replication-tasks-azure-resources/view-runs-for-task.png)

   Este paso abre el panel **Información general** del flujo de trabajo sin estado subyacente, que se incluye en un recurso de aplicación lógica estándar.

1. Para ver el historial de ejecución de un flujo de trabajo sin estado, en la barra de herramientas del panel **Información general**, seleccione **Enable debug mode** (Habilitar el modo de depuración).

   La pestaña **Historial de ejecución** muestra las ejecuciones anteriores, en curso y en espera de la tarea, junto con sus identificadores, estados, horas de inicio y duraciones de ejecución.

   ![Captura de pantalla que muestra las ejecuciones de una tarea, sus estados y otra información.](./media/create-replication-tasks-azure-resources/run-history-list.png)

   En la tabla siguiente se describen los posibles estados de una ejecución:

   | Etiqueta de estado | Descripción |
   |--------------|-------------|
   | **Cancelado** | La tarea se canceló durante la ejecución. |
   | **Erróneo** | La tarea tiene al menos una acción con errores, pero no hubo acciones posteriores para resolver el error. |
   | **Ejecución** | La tarea está en ejecución. |
   | **Correcto** | Todas las acciones correctas. Una tarea puede finalizar correctamente de todos modos si se produjo un error en una acción, pero hubo una acción posterior para resolver dicho error. |
   | **En espera** | La ejecución todavía no se inicia y está en pausa porque una instancia anterior de la tarea todavía está en ejecución. |
   |||

1. Para ver los estados y otro tipo de información de cada paso de una ejecución, seleccione dicha ejecución.

   Se abre el panel de detalles de ejecución y se muestra el flujo de trabajo subyacente que se ejecutó.

   - Un flujo de trabajo siempre se inicia con un [*desencadenador*](../connectors/apis-list.md#triggers). En esta tarea, el flujo de trabajo comienza con un desencadenador de Service Bus que espera a que lleguen los mensajes a la cola de Service Bus origen.

   - Cada paso muestra su estado y la duración de la ejecución. Los pasos que tienen duraciones de 0 segundos tardan menos 1 segundo en ejecutarse.

   ![Captura de pantalla que muestra cada paso de la ejecución, el estado y la duración de la ejecución del flujo de trabajo.](./media/create-replication-tasks-azure-resources/run-history-details.png)

1. Para revisar las entradas y salidas de cada paso, seleccione el paso, y se abrirá un panel que muestra los detalles de las entradas, salidas y propiedades de ese paso.

   En este ejemplo se muestran las entradas del desencadenador de Service Bus.

   ![Captura de pantalla que muestra las entradas, salidas y propiedades del desencadenador.](./media/create-replication-tasks-azure-resources/view-trigger-inputs-outputs-properties.png)

Para información sobre cómo puede crear sus propios flujos de trabajo automatizados para que pueda integrar aplicaciones, datos, servicios y sistemas aparte del contexto de las tareas de replicación de los recursos de Azure, consulte [Creación de un flujo de trabajo de integración con Azure Logic Apps (estándar) de inquilino único en Azure Portal](create-single-tenant-workflows-azure-portal.md).

<a name="monitor"></a>

## <a name="monitor-replication-tasks"></a>Supervisión de las tareas de replicación

Para comprobar el rendimiento y el estado de la tarea de replicación, o el flujo de trabajo de la aplicación lógica subyacente, puede usar [Application Insights](../azure-monitor/app/app-insights-overview.md), que es una funcionalidad de Azure Monitor. El [mapa de aplicación de Application Insights](../azure-monitor/app/app-map.md) es una herramienta visual útil que se puede usar para supervisar las tareas de replicación. Este mapa se genera automáticamente a partir de la información de supervisión capturada y le permite explorar el rendimiento y la confiabilidad de las transferencias de origen y destino de la tarea de replicación. Si necesita información de diagnóstico inmediata y una visualización de latencia baja de los detalles del registro, puede trabajar con la herramienta [Live Metrics](../azure-monitor/app/live-stream.md) del portal, que es también una funcionalidad de Azure Monitor.

<a name="edit-task"></a>

## <a name="edit-the-task"></a>Edición de una tarea

Existen estas opciones si quiere modificar una tarea:

- [Editar la tarea "en línea"](#edit-task-inline) para que pueda cambiar las propiedades de la tarea, como la información de conexión o de configuración.

- [Editar el flujo de trabajo subyacente de la tarea](#edit-task-workflow) en el diseñador.

<a name="edit-task-inline"></a>

### <a name="edit-the-task-inline"></a>Edición de la tarea en línea

1. En [Azure Portal](https://portal.azure.com), busque el recurso que tiene la tarea que quiere actualizar.

1. En el menú de navegación del recurso, en la sección **Automatización**, seleccione **Tareas (versión preliminar)** .

1. En la lista de tareas, busque la tarea que quiere actualizar. Abra el menú de puntos suspensivos ( **…** ) de la tarea y seleccione **Edit in-line** (Editar en línea).

   ![Captura de pantalla que muestra el menú de puntos suspensivos abierto y la opción seleccionada, "Edit in-line" (Editar en línea).](./media/create-replication-tasks-azure-resources/edit-task-in-line.png)

   De manera predeterminada, aparece la pestaña **Autenticar** y muestra las conexiones existentes.

1. Para agregar credenciales de autenticación nuevas o seleccionar otras existentes para una conexión, abra el menú de puntos suspensivos ( **…** ) de la conexión y seleccione **Agregar nueva conexión** o bien, si es posible, otras credenciales de autenticación.

   > [!NOTE]
   > Solo puede editar la conexión de destino, no la de origen.

   ![Captura de pantalla que muestra la pestaña "Autenticar", las conexiones existentes y el menú de puntos suspensivos seleccionado.](./media/create-replication-tasks-azure-resources/edit-connections.png)

1. Para actualizar otras propiedades de la tarea, seleccione **Siguiente: Configurar**.

   En la tarea de este ejemplo, puede especificar diferentes colas de origen y destino. Sin embargo, el nombre de la tarea y la aplicación lógica y el flujo de trabajo subyacentes siguen siendo los mismos.

   ![Captura de pantalla que muestra la pestaña "Configurar" y las propiedades disponibles para editar.](./media/create-replication-tasks-azure-resources/edit-task-configuration.png)

1. Cuando finalice, seleccione **Guardar**.

<a name="edit-task-workflow"></a>

### <a name="edit-the-tasks-underlying-workflow"></a>Edición del flujo de trabajo subyacente de una tarea

Puede editar el flujo de trabajo subyacente a una tarea de replicación, que cambia la configuración original de la tarea que creó, pero no la propia plantilla de tarea. Después de hacer y guardar los cambios, la tarea editada ya no realiza la misma función que la tarea original. Si quiere una tarea que realice la funcionalidad original, es posible que tenga que crear una tarea con la misma plantilla. Si no quiere volver a crear la tarea original, evite cambiar el flujo de trabajo que subyace a la tarea mediante el diseñador. En su lugar, cree un flujo de trabajo sin estado de **Logic App (Standard)** (Aplicación lógica [estándar]) para satisfacer sus necesidades de integración. Para más información, consulte [Creación de un flujo de trabajo de integración con Azure Logic Apps (estándar) de inquilino único en Azure Portal](create-single-tenant-workflows-azure-portal.md).

1. En [Azure Portal](https://portal.azure.com), busque el recurso que tiene la tarea que quiere actualizar.

1. En el menú de navegación del recurso, en la sección **Automatización**, seleccione **Tareas**.

1. En la lista de tareas, busque la tarea que quiere actualizar. Abra el menú de puntos suspensivos ( **…** ) de la tarea y seleccione **Open in Logic Apps** (Abrir en Logic Apps).

   ![Captura de pantalla que muestra el menú de puntos suspensivos abierto y la opción seleccionada, "Abrir en Logic Apps".](./media/create-replication-tasks-azure-resources/open-task-in-designer.png)

   Azure Portal cambia el contexto al diseñador, donde ahora puede editar el flujo de trabajo.

   ![Captura de pantalla que muestra el diseñador y el flujo de trabajo subyacente.](./media/create-replication-tasks-azure-resources/view-task-workflow-designer.png)

   Ahora puede editar el desencadenador y las acciones del flujo de trabajo, así como las propiedades del desencadenador y las acciones.

1. Para ver las propiedades del desencadenador o de una acción, seleccione el desencadenador o la acción.

   ![Captura de pantalla que muestra el panel de propiedades del desencadenador de Service Bus.](./media/create-replication-tasks-azure-resources/edit-service-bus-trigger.png)

   En este ejemplo, la propiedad **IsSessionsEnabled** del desencadenador se cambia a **Sí**.

1. Para guardar los cambios, en la barra de herramientas del diseñador, seleccione **Guardar**.

   ![Captura de pantalla que muestra la barra de herramientas del diseñador y el comando "Guardar" seleccionado.](./media/create-replication-tasks-azure-resources/save-updated-workflow.png)

1. Para probar y ejecutar el flujo de trabajo actualizado, abra el recurso de aplicación lógica que contiene el flujo de trabajo actualizado. En el menú de navegación del flujo de trabajo, seleccione **Información general** > **Ejecutar desencadenador** > **Ejecutar**.

   Al finalizar la ejecución, el diseñador muestra los detalles de ejecución del flujo de trabajo. Para revisar las entradas y salidas de cada paso, seleccione el paso, y se abrirá un panel que muestra los detalles de las entradas, salidas y propiedades de ese paso.

   En este ejemplo se muestran las entradas, las salidas y las propiedades del desencadenador de Service Bus, junto con el valor de propiedad del desencadenador actualizado.

   ![Captura de pantalla que muestra los detalles de ejecución del flujo de trabajo con las entradas, las salidas y las propiedades del desencadenador.](./media/create-replication-tasks-azure-resources/view-updated-run-details-trigger-inputs.png)

1. Para deshabilitar el flujo de trabajo para que la tarea no se siga ejecutando, en la barra de herramientas **Información general**, seleccione **Deshabilitar**. Para más información, consulte [Deshabilitación o habilitación de flujos de trabajo de inquilino único](create-single-tenant-workflows-azure-portal.md#disable-or-enable-workflows).

<a name="failover"></a>

## <a name="set-up-failover-for-azure-event-hubs"></a>Configuración de la conmutación por error en Azure Event Hubs

En el caso de la replicación de Azure Event Hubs entre los mismos tipos de entidad, en la recuperación ante desastres geográfica es necesario realizar una conmutación por error de la entidad de origen a la entidad de destino y, luego, avisar a los consumidores y productores de eventos afectados de que usen el punto de conexión de la entidad de destino, que se convierte en el nuevo origen. Por lo tanto, si se produce un desastre y la entidad de origen conmuta por error, los consumidores y productores, incluida la tarea de replicación, se redirigen al nuevo origen. La cuenta de almacenamiento que creó la tarea de replicación contiene información de punto de comprobación y la posición o el desplazamiento en la secuencia donde se detiene la entidad de origen si la región de origen se interrumpe o deja de estar disponible.

Para asegurarse de que la cuenta de almacenamiento no contiene ninguna información heredada del origen inicial y de que la tarea de replicación comienza a leer y replicar eventos desde el inicio de la nueva secuencia de origen, debe limpiar toda la información heredada del origen original y volver a configurar la tarea de replicación manualmente.

1. En [Azure Portal](https://portal.azure.com), abra el recurso de aplicación lógica o el flujo de trabajo subyacente a la tarea de replicación.

   > [!NOTE]
   > El recurso de aplicación lógica solo debe contener flujos de trabajo de tareas de replicación.

1. En el menú de navegación del recurso o del flujo de trabajo, seleccione **Información general**. En la barra de herramientas **Información general**, seleccione **Deshabilitar** el flujo de trabajo o elija **Detener** el recurso de aplicación lógica.

1. Para buscar la cuenta de almacenamiento que usa el recurso de aplicación lógica subyacente de la tarea de replicación para almacenar el punto de control y la información de desplazamiento de flujo de la entidad de origen, siga estos pasos:

   1. En el menú del recurso de aplicación lógica, en **Configuración**, seleccione **Configuración**.

   1. En el panel **Configuración**, en la pestaña **Configuración de la aplicación**, seleccione la configuración de la aplicación **AzureWebJobsStorage**.

      Esta configuración especifica la cadena de conexión y la cuenta de almacenamiento que usa el recurso de aplicación lógica.

      > [!NOTE]
      > Si la configuración de la aplicación no aparece en la lista, seleccione **Mostrar valores**.

   1. Seleccione la configuración de la aplicación **AzureWebJobsStorage** para que pueda ver el nombre de la cuenta de almacenamiento.

   En este ejemplo se muestra cómo buscar el nombre de esta cuenta de almacenamiento, que es `storagefabrikamreplb0c` aquí:

   ![Captura de pantalla que muestra el panel "Configuración" del recurso de aplicación lógica subyacente con la configuración de la aplicación "AzureWebJobsStorage" y la cadena de conexión con el nombre de la cuenta de almacenamiento.](./media/create-replication-tasks-azure-resources/find-storage-account-name.png)

   1. Para confirmar que el recurso de la cuenta de almacenamiento existe, en el cuadro de búsqueda de Azure Portal, escriba el nombre y, a continuación, seleccione la cuenta de almacenamiento, por ejemplo:

   ![Captura de pantalla que muestra el cuadro de búsqueda de Azure Portal con el nombre de la cuenta de almacenamiento especificado.](./media/create-replication-tasks-azure-resources/find-storage-account.png)

1. Ahora, elimine la carpeta que contiene el punto de control y la información de desplazamiento de la entidad de origen mediante los pasos siguientes:

   1. Descargue, instale y abra la versión más reciente del [cliente de escritorio de Explorador de Azure Storage](https://azure.microsoft.com/features/storage-explorer/), si aún no tiene la versión más reciente.

      > [!NOTE]
      > Para la tarea de limpieza de eliminación, actualmente tiene que usar el cliente de Explorador de Azure Storage, *no* el explorador de almacenamiento, el explorador, el editor o la experiencia de administración en Azure Portal.
      >
      > Aunque puede eliminar carpetas de contenedor con el [comando `Remove-AzStorageDirectory`](/powershell/module/az.storage/remove-azstoragedirectory) de PowerShell, este comando solo funciona en carpetas *vacías*.

   1. Si aún no lo ha hecho, inicie sesión con su cuenta de Azure y asegúrese de que la suscripción de Azure para el recurso de la cuenta de almacenamiento está seleccionada. Para más información, consulte [Introducción al Explorador de Storage](../vs-azure-tools-storage-manage-with-storage-explorer.md).

   1. En la ventana del Explorador, en el nombre de la suscripción de Azure, vaya a **Cuentas de almacenamiento** >  **{*your-storage-account-name*}**  > **Contenedores de blobs** > **azure-webjobs-eventhub**.

      > [!NOTE]
      > Si la carpeta **azure-webjobs-eventhub** no existe, la tarea de replicación aún no se ha ejecutado. La carpeta solo aparece después de que la tarea de replicación se ejecute al menos una vez.

      ![Captura de pantalla que muestra el Explorador de Azure Storage con la cuenta de almacenamiento y el contenedor de blobs abierto para mostrar la carpeta "azure-webjobs-eventhub" seleccionada.](./media/create-replication-tasks-azure-resources/azure-webjobs-eventhub-storage-explorer.png)

   1. En el panel **azure-webjobs-eventhub** que se abre, seleccione la carpeta del espacio de nombres de Event Hubs, que tiene un nombre con el formato siguiente: `<source-Event-Hubs-namespace-name>.servicebus.windows.net`.

   1. Una vez que se abra la carpeta del espacio de nombres, en el panel **azure-webjobs-eventhub**, seleccione la carpeta <*former-source-entity-name*>. En el menú contextual de la barra de herramientas o de la carpeta, seleccione **Eliminar**, por ejemplo:

      ![Captura de pantalla que muestra la carpeta de entidad de Event Hubs de origen anterior seleccionada con el botón "Eliminar" también seleccionado.](./media/create-replication-tasks-azure-resources/delete-former-source-entity-folder-storage-explorer.png)

   1. Confirme que desea eliminar la carpeta.

1. Vuelva al recurso de aplicación lógica o al flujo de trabajo que subyace a la tarea de replicación. Reinicie la aplicación lógica o vuelva a habilitar el flujo de trabajo.

Para que los productores y consumidores usen el nuevo punto de conexión de origen, es necesario que la información sobre la nueva entidad de origen esté disponible para su uso y encontrarla en una ubicación fácil de alcanzar y actualizar. Si los productores o consumidores encuentran errores frecuentes o persistentes, deben comprobar esa ubicación y ajustar su configuración. Hay muchas maneras de compartir esa configuración; DNS y los recursos compartidos de archivos son ejemplos.

Para más información sobre la recuperación ante desastres geográfica, revise la siguiente documentación:

- [Azure Event Hubs: recuperación ante desastres geográfica](../event-hubs/event-hubs-geo-dr.md)
- [Recuperación ante desastres con localización geográfica de Azure Service Bus](../service-bus-messaging/service-bus-geo-dr.md)

<a name="edit-plan-scale-out-settings"></a>

## <a name="edit-hosting-plan-scale-out-settings"></a>Edición de la configuración de escalado horizontal del plan de hospedaje

### <a name="portal"></a>[Portal](#tab/portal)

1. En [Azure Portal](https://portal.azure.com), abra el recurso de aplicación lógica subyacente para la tarea de replicación.

1. En el menú del recurso de aplicación lógica, en **Configuración**, seleccione **Escalado horizontal (plan de App Service)** .

   ![Captura de pantalla que muestra la configuración del plan de hospedaje para ráfagas máximas, instancias mínimas, instancias siempre preparadas y cumplimiento del límite de escalado horizontal.](./media/create-replication-tasks-azure-resources/edit-app-service-plan-settings.png)

1. En función de las necesidades del escenario, en **Plan de escalabilidad horizontal** y **Escalado horizontal de aplicaciones**, cambie los valores de la ráfaga máxima y las instancias siempre preparadas, respectivamente.

1. Cuando haya terminado, en la barra de herramientas del panel **Escalabilidad horizontalmente (plan de App Service),** seleccione **Guardar**.

### <a name="azure-cli"></a>[CLI de Azure](#tab/azurecli)

Las instancias siempre preparadas para una aplicación también se pueden configurar con la CLI de Azure.

```azurecli-interactive
az resource update -g <resource_group> -n <logic-app-app-name>/config/web --set properties.minimumElasticInstanceCount=<desired_always_ready_count> --resource-type Microsoft.Web/sites
```

---

Para obtener más información, revise la siguiente documentación, ya que el plan Estándar de flujo de trabajo comparte algunos aspectos con el plan Premium de Azure Functions:

- [Configuración del plan y la SKU: plan Premium de Azure Functions](../azure-functions/functions-premium-plan.md#plan-and-sku-settings)
- [¿Qué es la ampliación en la nube?](https://azure.microsoft.com/overview/what-is-cloud-bursting/)
- [Instancias siempre preparadas: plan Premium de Azure Functions](../azure-functions/functions-premium-plan.md#always-ready-instances)

<a name="problems-failures"></a>

## <a name="replication-problems-and-failures"></a>Problemas y errores de replicación

En esta sección se describen las posibles formas en que la replicación puede producir un error o dejar de funcionar:

- Límites de tamaño de los mensajes

  Asegúrese de enviar mensajes de menos de 1 MB, ya que la tarea de replicación agrega [propiedades de replicación](#replication-properties). De lo contrario, si el tamaño del mensaje es mayor que el tamaño de los eventos que se pueden enviar a una entidad de Event Hubs después de que la tarea agrega [propiedades de replicación](#replication-properties), se produce un error en el proceso de replicación.

  Por ejemplo, suponga que el tamaño del mensaje es de 1 MB. Después de que la tarea agrega propiedades de replicación, el tamaño del mensaje es mayor que 1 MB. La llamada saliente que intenta enviar el mensaje generará un error.

- Claves de partición

  Si existen claves de partición en los eventos, la replicación entre las instancias de Event Hubs no se realizará si esas instancias tienen el mismo número de particiones.

## <a name="next-steps"></a>Pasos siguientes

- [Acerca del diseñador de flujos de trabajo en el entorno de Azure Logic Apps de inquilino único](designer-overview.md)
- [Edición de la configuración de host y aplicación para aplicaciones lógicas en Azure Logic Apps de inquilino único](edit-app-settings-host-settings.md)
- [Creación de parámetros para usar en los flujos de trabajo entre entornos de Azure Logic Apps de un solo inquilino](parameterize-workflow-app.md)
