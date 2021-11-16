---
title: Información general sobre la mensajería de Azure Service Bus | Microsoft Docs
description: En este artículo se proporciona información general de alto nivel sobre Azure Service Bus, un agente de mensajes de integración empresarial totalmente administrado.
ms.topic: overview
ms.date: 11/11/2021
ms.openlocfilehash: 8aa59315dcb196cf7f5c5b107e8cf575a5eb4f86
ms.sourcegitcommit: 901ea2c2e12c5ed009f642ae8021e27d64d6741e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/12/2021
ms.locfileid: "132373126"
---
# <a name="what-is-azure-service-bus"></a>Qué es Azure Service Bus
Azure Service Bus es un agente de mensajes empresarial totalmente administrado que incluye colas de mensajes y temas que se pueden publicar y a los que es posible suscribirse. Service Bus se usa para desacoplar aplicaciones de servicios, lo que proporciona las siguientes ventajas:

- Equilibrio de carga del trabajo entre trabajadores paralelos
- Enrutamiento y transferencia de datos de forma segura y control entre los límites de aplicaciones y servicios
- Coordinación del trabajo transaccional que requiere un alto grado de confiabilidad 

> [!NOTE]
> Para obtener una comparación de los servicios de mensajería de Azure, consulte [Elección entre servicios de mensajería de Azure: Event Grid, Event Hubs y Service Bus](../event-grid/compare-messaging-services.md?toc=%2fazure%2fservice-bus-messaging%2ftoc.json&bc=%2fazure%2fservice-bus-messaging%2fbreadcrumb%2ftoc.json). 

## <a name="overview"></a>Información general
Los datos se transfieren entre distintas aplicaciones y servicios mediante **mensajes**. Un mensaje es un contenedor decorado con metadatos que contiene datos. Los datos pueden ser cualquier tipo de información, como datos estructurados codificados con los formatos comunes, como los siguientes: JSON, XML, Apache Avro, texto sin formato.

Algunos escenarios de mensajería comunes son:

* **Mensajería**. Transferir datos empresariales, como pedidos de ventas o compras, diarios o movimientos de inventario.
* **Desacoplar aplicaciones**. Mejore la confiabilidad y la escalabilidad de las aplicaciones y los servicios. No es necesario que el productor y el consumidor estén en línea o disponibles al mismo tiempo. La [carga se redistribuye](/azure/architecture/patterns/queue-based-load-leveling), de tal forma que los picos de tráfico no sobrecarguen un servicio. 
* **Equilibrio de carga**. Permite que varios [consumidores paralelos](/azure/architecture/patterns/competing-consumers) lean una cola al mismo tiempo y que cada uno obtenga de forma segura la propiedad exclusiva de mensajes específicos. 
* **Temas y suscripciones**. Permite relaciones 1:*n* entre [publicadores y suscriptores](/azure/architecture/patterns/publisher-subscriber), lo que hace que los suscriptores puedan seleccionar mensajes concretos de una secuencia de mensajes publicados.
* **Transacciones**. Permite realizar varias operaciones, todas ellas en el ámbito de una transacción atómica. Por ejemplo, se pueden realizar las siguientes operaciones en el ámbito de una transacción.  

    1. Obtener un mensaje de una cola.
    2. Publicar resultados de procesamiento en una o varias colas diferentes.
    3. Mover el mensaje de entrada desde la cola original. 
    
    Los resultados se hacen visibles para los consumidores de nivel inferior solo si son correctos, incluida la liquidación correcta del mensaje de entrada, lo que permite una semántica de procesamiento de una sola vez. Este modelo de transacción es una base sólida para el patrón de [transacciones de compensación](/azure/architecture/patterns/compensating-transaction) en el contexto de una solución mayor. 
* **Sesiones de mensajes**. Implemente la coordinación a gran escala de flujos de trabajo y transferencias multiplexadas que requieran un ordenamiento o un aplazamiento de mensajes estricto.

Si está familiarizado con otros agentes de mensajes, como Apache ActiveMQ, los conceptos de Service Bus son parecidos a los que ya conoce. Como Service Bus es una oferta de plataforma como servicio (PaaS), una diferencia clave es que no es necesario preocuparse por las siguientes acciones. Azure se encarga de esas tareas. 

- Preocuparse por los errores de hardware. 
- Mantener revisados los sistemas operativos o productos.
- Colocar los registros y administrar el espacio en disco.
- Administrar las copias de seguridad.
- Conmutar por error a una máquina de reserva.

## <a name="concepts"></a>Conceptos
En esta sección se de abordan los conceptos básicos de Service Bus.

### <a name="queues"></a>Colas
Los mensajes se envían y se reciben desde **colas**. Las colas almacenan mensajes hasta que la aplicación receptora está disponible para recibirlos y procesarlos.

![Cola](./media/service-bus-messaging-overview/about-service-bus-queue.png)

Los mensajes de las colas se ordenan y se les asigna una marca de tiempo a su llegada. Una vez aceptados por el agente, el mensaje siempre se mantiene de forma duradera en un almacenamiento con redundancia triple, distribuido entre las zonas de disponibilidad si el espacio de nombres está habilitado para zonas. Service Bus nunca deja mensajes en memoria o almacenamiento volátil una vez que se han notificado al cliente como aceptados.

Los mensajes se entregan en modo de **extracción** y solo entregan mensajes cuando se solicitan. A diferencia del modelo de sondeo de estado ocupado de otras colas de nube, la operación de extracción puede ser de larga duración y completarse solamente una vez que un mensaje esté disponible. 

> [!NOTE]
> Para obtener una comparación de las colas de Service Bus con las de Storage, consulte [Colas de Storage y de Service Bus: comparación y diferencias](service-bus-azure-and-service-bus-queues-compared-contrasted.md).

### <a name="topics"></a>Temas

También puede usar **temas** para enviar y recibir mensajes. Mientras que una cola se utiliza a menudo para la comunicación punto a punto, los temas son útiles en escenarios de publicación y suscripción.

![Tema](./media/service-bus-messaging-overview/about-service-bus-topic.png)

Los temas pueden tener varias suscripciones independientes asociadas; por lo demás, funcionan exactamente igual que las colas del lado receptor. Un suscriptor a un tema puede recibir una copia de cada mensaje enviado a ese tema. Las suscripciones son entidades con nombre. De forma predeterminada, las suscripciones son duraderas, pero se pueden configurar para que expiren y se eliminen luego automáticamente. Mediante la API Java Message Service (JMS), Service Bus Premium también permite crear suscripciones volátiles que existen mientras dura la conexión.

Puede definir reglas en una suscripción. Una regla de suscripción tiene un **filtro** para definir una condición del mensaje que se va a copiar en la suscripción, y una acción **opcional** que puede modificar los metadatos del mensaje. Para más información, consulte [Filtros y acciones de temas](topic-filters.md). Esta característica es útil en los escenarios siguientes:

- No quiere que una suscripción reciba todos los mensajes enviados a un tema.
- Quiere marcar los mensajes con metadatos adicionales cuando pasan a través de una suscripción.

> [!NOTE]
> Para obtener más información sobre las colas y los temas, consulte [colas de Service Bus, temas y suscripciones](service-bus-queues-topics-subscriptions.md).


### <a name="namespaces"></a>Espacios de nombres
Un espacio de nombres es un contenedor de todos los componentes de mensajería (colas y temas). Varias colas y temas pueden estar en un solo espacio de nombres, y los espacios de nombres suelen servir como contenedores de aplicación. 

Un espacio de nombres puede compararse con un servidor en la terminología de otros agentes, pero los conceptos no son directamente equivalentes. Un espacio de nombres de Service Bus es su propio segmento de capacidad de un clúster grande compuesto por docenas de máquinas virtuales activas. Opcionalmente, puede abarcar tres [zonas de disponibilidad de Azure](../availability-zones/az-overview.md). Por lo tanto, obtiene todas las ventajas de disponibilidad y solidez de la ejecución del agente de mensajes a gran escala. Además, no es necesario preocuparse por las complejidades subyacentes. Service Bus es mensajería sin servidor.

## <a name="advanced-features"></a>Características avanzadas

Service Bus también tiene características avanzadas que permiten solucionar problemas de mensajería más complejos. Las siguientes secciones describen estas características principales:

### <a name="message-sessions"></a>Sesiones de mensajes

Para realizar una garantía primero en entrar/primero en salir (FIFO) en Service Bus, use sesiones. Las [sesiones de mensajes](message-sessions.md) permiten la administración ordenada y conjunta de secuencias sin enlace de mensajes relacionados. 

### <a name="auto-forwarding"></a>Reenvío automático

La característica de [reenvío automático](service-bus-auto-forwarding.md) permite encadenar una cola o suscripción a otra cola o tema que forme parte del mismo espacio de nombres. Cuando el reenvío automático está habilitado, Service Bus elimina automáticamente los mensajes que se colocan en la primera cola o suscripción (origen) y los coloca en la segunda cola o en el segundo tema (destino).

### <a name="dead-lettering"></a>Colas de mensajes fallidos

Service Bus admite una [cola de mensajes fallidos](service-bus-dead-letter-queues.md) (DLQ) para mantener los mensajes que no se pudieron entregar a ningún destinatario o los mensajes que no se pudieron procesar. A continuación, puede eliminar mensajes de la cola DLQ y examinarlos.

### <a name="scheduled-delivery"></a>Entrega programada

Puede enviar mensajes a una cola o tema para el [procesamiento retrasado](message-sequencing.md#scheduled-messages). Por ejemplo, puede programar un trabajo para que esté disponible para que lo procese un sistema en un momento determinado.

### <a name="message-deferral"></a>Aplazamiento de mensajes

Cuando un cliente de una cola o suscripción recibe un mensaje que se quiere procesar, pero cuyo procesamiento no es posible en ese momento debido a circunstancias especiales dentro de la aplicación, la entidad tiene la opción de [aplazar la recuperación del mensaje](message-deferral.md) para un momento posterior. El mensaje permanece en la cola o la suscripción, pero se reserva.

### <a name="batching"></a>Procesamiento por lotes

El [procesamiento por lotes en el lado del cliente](service-bus-performance-improvements.md#client-side-batching) permite que un cliente de una cola o un tema retrase el envío de un mensaje durante un período determinado. Si el cliente envía más mensajes durante este período, los transmite en un único lote. 

### <a name="transactions"></a>Transacciones

Una [transacción](service-bus-transactions.md) agrupa dos o más operaciones en un ámbito de ejecución. Service Bus admite operaciones de agrupación en una sola entidad de mensajería (cola, tema, suscripción) dentro del ámbito de una transacción.

### <a name="filtering-and-actions"></a>Filtrado y acciones

Los suscriptores pueden definir los mensajes que quieren recibir de un tema. Estos mensajes se especifican en forma de una o varias [reglas de suscripción con nombre](topic-filters.md). Con cada condición de regla de coincidencia, la suscripción crea una copia del mensaje, que se puede anotar de manera diferente para cada regla coincidente.

### <a name="auto-delete-on-idle"></a>Eliminación automática en estado inactivo

La [eliminación automática en estado inactivo](/dotnet/api/microsoft.servicebus.messaging.queuedescription.autodeleteonidle) le permite especificar un intervalo de inactividad después del cual se eliminará automáticamente la cola. La duración mínima es de 5 minutos.

### <a name="duplicate-detection"></a>Detección de duplicados

Si se produce un error que hace que el cliente tenga dudas sobre el resultado de una operación de envío, la [detección de duplicados](duplicate-detection.md) saca de dudas en estas situaciones al permitir que el remitente reenvíe el mismo mensaje y la cola o el tema descartan cualquier copia duplicada.

### <a name="shared-access-signature-sas-role-based-access-control-and-managed-identities"></a>Firma de acceso compartido (SAS), control de acceso basado en roles e identidades administradas

Service Bus admite protocolos de seguridad como las [firmas de acceso compartido](service-bus-sas.md) (SAS), el [Control de acceso basado en rol](service-bus-role-based-access-control.md) (RBAC) y [Entidades administradas para recursos de Azure](service-bus-managed-service-identity.md).

### <a name="geo-disaster-recovery"></a>Recuperación ante desastres geográfica

Cuando las regiones o los centros de datos de Azure experimentan un tiempo de inactividad, la [recuperación ante desastres geográfica](service-bus-geo-dr.md) permite que el procesamiento de datos siga funcionando en una región o un centro de datos diferentes.

### <a name="security"></a>Seguridad

Service Bus admite los protocolos [Advanced Message Queuing Protocol (AMQP) 1.0](service-bus-amqp-overview.md) y [HTTP/REST](/rest/api/servicebus/).

> [!NOTE]
> Para obtener más información sobre estas características, consulte [Características avanzadas de Azure Service Bus](advanced-features-overview.md).

## <a name="compliance-with-standards-and-protocols"></a>Cumplimiento de estándares y protocolos

El protocolo de conexión principal de Service Bus es [Advanced Message Queuing Protocol (AMQP) 1.0](service-bus-amqp-overview.md), un estándar abierto ISO/IEC. Este protocolo permite a los clientes escribir aplicaciones que funcionan en agentes de Service Bus y locales, como ActiveMQ o RabbitMQ. En la [guía del protocolo AMQP](service-bus-amqp-protocol-guide.md) se proporciona información detallada en caso de que quiera crear una abstracción de este tipo.

[Service Bus Premium](service-bus-premium-messaging.md) es totalmente compatible con la API [Java Message Service (JMS) 2.0](how-to-use-java-message-service-20.md) de Java/Jakarta EE. Además, Service Bus Standard admite el subconjunto JMS 1.1 centrado en las colas. JMS es una abstracción común de los agentes de mensajes y se integra con muchas aplicaciones y marcos, incluido el conocido marco Spring. Para pasar de otros agentes a Azure Service Bus, solo tiene que volver a crear la topología de colas y temas y cambiar la configuración y las dependencias del proveedor del cliente. Puede encontrar un ejemplo en la [guía de migración de ActiveMQ](migrate-jms-activemq-to-servicebus.md).

## <a name="client-libraries"></a>Bibliotecas de clientes

Todas bibliotecas cliente de Service Bus están disponibles a través de Azure SDK.

- [Azure Service Bus para .NET](/dotnet/api/overview/azure/service-bus?preserve-view=true)
- [Bibliotecas de Azure Service Bus para Java](/java/api/overview/azure/servicebus?preserve-view=true)
- [Proveedor de Azure Service Bus para Java JMS 2.0](how-to-use-java-message-service-20.md)
- [Módulos de Azure Service Bus para JavaScript y TypeScript](/javascript/api/overview/azure/service-bus?preserve-view=true)
- [Bibliotecas de Azure Service Bus para Python](/python/api/overview/azure/servicebus?preserve-view=true)

El [protocolo principal de Azure Service Bus es AMQP 1.0](service-bus-amqp-overview.md) y se puede usar desde cualquier cliente compatible con este protocolo. Varios clientes de AMQP de código abierto tienen ejemplos que demuestran explícitamente la interoperabilidad de Service Bus. Revise la [guía del protocolo AMQP 1.0](service-bus-amqp-protocol-guide.md) para aprender a usar las características de Service Bus directamente con los clientes de AMQP 1.0.

[!INCLUDE [messaging-oss-amqp-stacks.md](../../includes/messaging-oss-amqp-stacks.md)]

## <a name="integration"></a>Integración

Service Bus se integra totalmente con muchos servicios de Microsoft y Azure, por ejemplo:

* [Event Grid](service-bus-to-event-grid-integration-example.md)
* [Logic Apps](../connectors/connectors-create-api-servicebus.md)
* [Funciones de Azure](../azure-functions/functions-bindings-service-bus.md)
* [Power Platform](../connectors/connectors-create-api-servicebus.md)
* [Dynamics 365](/dynamics365/fin-ops-core/dev-itpro/business-events/how-to/how-to-servicebus)
* [Azure Stream Analytics](../stream-analytics/stream-analytics-define-outputs.md)

## <a name="next-steps"></a>Pasos siguientes

Para comenzar a trabajar con la mensajería de Service Bus, consulte los siguientes artículos:

- [Elija entre los servicios de mensajería de Azure: Event Grid, Event Hubs y Service Bus](../event-grid/compare-messaging-services.md?toc=%2fazure%2fservice-bus-messaging%2ftoc.json&bc=%2fazure%2fservice-bus-messaging%2fbreadcrumb%2ftoc.json).
- [Colas, temas y suscripciones de Service Bus](service-bus-queues-topics-subscriptions.md)
- Guías de inicio rápido: [.NET](service-bus-dotnet-get-started-with-queues.md), [Java](service-bus-java-how-to-use-queues.md) o [JMS](service-bus-java-how-to-use-jms-api-amqp.md).
- [Precios de Service Bus](https://azure.microsoft.com/pricing/details/service-bus/). 
- [Mensajería premium](service-bus-premium-messaging.md).