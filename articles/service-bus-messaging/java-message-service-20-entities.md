---
title: 'Mensajería de Azure Service Bus: entidades de Java Message Service'
description: En este artículo se proporciona información general sobre las entidades de mensajería de Azure Service Bus accesibles a través de la API de Java Message Service.
ms.topic: article
ms.date: 09/27/2021
ms.openlocfilehash: 6900deff355f10e4274a5268bd21da189a9daf80
ms.sourcegitcommit: df2a8281cfdec8e042959339ebe314a0714cdd5e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/28/2021
ms.locfileid: "129154534"
---
# <a name="java-message-service-jms-20-entities"></a>Entidades de Java Message Service (JMS) 2.0

Las aplicaciones cliente que se conectan a Azure Service Bus Premium y usan la [biblioteca JMS de Azure Service Bus](https://search.maven.org/artifact/com.microsoft.azure/azure-servicebus-jms) pueden utilizar las siguientes entidades.

## <a name="queues"></a>Colas

Las colas de JMS se pueden comparar semánticamente con las [colas de Service Bus](service-bus-queues-topics-subscriptions.md#queues) tradicionales.

Para crear una cola, use los métodos siguientes de la clase `JMSContext`:

```java
Queue createQueue(String queueName)
```

## <a name="topics"></a>Temas

Los temas de JMS se pueden comparar semánticamente con los [temas de Service Bus](service-bus-queues-topics-subscriptions.md#topics-and-subscriptions) tradicionales.

Para crear un tema, use los métodos siguientes de la clase `JMSContext`:

```java
Topic createTopic(String topicName)
```

## <a name="temporary-queues"></a>Colas temporales

Si una aplicación cliente requiere una entidad temporal que existe durante la vigencia de la aplicación, puede usar colas temporales. Estas entidades se utilizan con el patrón de [solicitud-respuesta](https://www.enterpriseintegrationpatterns.com/patterns/messaging/RequestReply.html).

Para crear una cola temporal, use los métodos siguientes de la clase `JMSContext`:

```java
TemporaryQueue createTemporaryQueue()
```

## <a name="temporary-topics"></a>Temas temporales

Al igual que las colas temporales, los temas temporales existen para habilitar la publicación o suscripción a través de una entidad temporal que existe durante la vigencia de la aplicación.

Para crear un tema temporal, use los métodos siguientes de la clase `JMSContext`:

```java
TemporaryTopic createTemporaryTopic()
```

## <a name="java-message-service-jms-subscriptions"></a>Suscripciones de Java Message Service (JMS)

Aunque estas son semánticamente similares a las [suscripciones](service-bus-queues-topics-subscriptions.md#topics-and-subscriptions) (es decir, existen en un tema y habilitan la semántica de publicación o suscripción), la especificación de Java Message Service presenta los conceptos de los atributos **Compartido**, **No compartido**, **Duradero y **No duradero** para una suscripción determinada.

> [!NOTE]
> Las suscripciones siguientes están disponibles en el nivel Premium de Azure Service Bus para las aplicaciones cliente que se conectan a Azure Service Bus mediante la [biblioteca JMS de Azure Service Bus](https://search.maven.org/artifact/com.microsoft.azure/azure-servicebus-jms).
>
> Solo se pueden crear suscripciones duraderas mediante Azure Portal.
>

### <a name="shared-durable-subscriptions"></a>Suscripciones duraderas compartidas

Una suscripción duradera compartida se usa cuando una aplicación va a recibir y procesar todos los mensajes publicados en un tema, independientemente de si la aplicación se utiliza de forma activa en la suscripción en todo momento.

Cualquier aplicación autenticada para recibir de Service Bus puede recibir de la suscripción duradera compartida.

Para crear una suscripción duradera compartida, use los métodos siguientes de la clase `JMSContext`:

```java
JMSConsumer createSharedDurableConsumer(Topic topic, String name)

JMSConsumer createSharedDurableConsumer(Topic topic, String name, String messageSelector)
```

La suscripción duradera compartida seguirá existiendo a menos que se elimine mediante el método `unsubscribe` de la clase `JMSContext`.

```java
void unsubscribe(String name)
```

### <a name="unshared-durable-subscriptions"></a>Suscripciones duraderas no compartidas

Al igual que una suscripción duradera compartida, una suscripción duradera no compartida se usa cuando una aplicación va a recibir y procesar todos los mensajes publicados en un tema, independientemente de si la aplicación se utiliza de forma activa en la suscripción.

Sin embargo, puesto que se trata de una suscripción no compartida, solo la aplicación que creó la suscripción puede recibir de ella.

Para crear una suscripción duradera no compartida, use los métodos siguientes de la clase `JMSContext`: 

```java
JMSConsumer createDurableConsumer(Topic topic, String name)

JMSConsumer createDurableConsumer(Topic topic, String name, String messageSelector, boolean noLocal)
```

> [!NOTE]
> La característica `noLocal` no se admite actualmente y se omite.
>

La suscripción duradera no compartida seguirá existiendo a menos que se elimine mediante el método `unsubscribe` de la clase `JMSContext`.

```java
void unsubscribe(String name)
```

### <a name="shared-non-durable-subscriptions"></a>Suscripciones no duraderas compartidas

Una suscripción no duradera compartida se usa cuando es necesario que varias aplicaciones cliente reciban y procesen mensajes de una suscripción única, solo hasta que se utilicen de forma activa en ella o reciban de ella.

Dado que la suscripción no es duradera, no se conserva. Esta suscripción no recibe los mensajes cuando no hay consumidores activos en la misma.

Para crear una suscripción no duradera compartida, cree `JmsConsumer` como se muestra en los siguientes métodos de la clase `JMSContext`:

```java
JMSConsumer createSharedConsumer(Topic topic, String sharedSubscriptionName)

JMSConsumer createSharedConsumer(Topic topic, String sharedSubscriptionName, String messageSelector)
```

La suscripción no duradera compartida seguirá existiendo hasta que haya consumidores activos que reciban de la misma.

### <a name="unshared-non-durable-subscriptions"></a>Suscripciones no duraderas no compartidas

Una suscripción no duradera no compartida se usa cuando es necesario que la aplicación cliente reciba y procese un mensaje de una suscripción, solo hasta que se utilice de forma activa en ella. Solo puede existir un consumidor en esta suscripción, es decir, el cliente que creó la suscripción.

Dado que la suscripción no es duradera, no se conserva. Esta suscripción no recibe los mensajes cuando no hay ningún consumidor activo en la misma.

Para crear una suscripción no duradera no compartida, cree `JMSConsumer` como se muestra en los siguientes métodos de la clase `JMSContext`:

```java
JMSConsumer createConsumer(Destination destination)

JMSConsumer createConsumer(Destination destination, String messageSelector)

JMSConsumer createConsumer(Destination destination, String messageSelector, boolean noLocal)
```

> [!NOTE]
> La característica `noLocal` no se admite actualmente y se omite.
>

La suscripción no duradera no compartida seguirá existiendo hasta que haya un consumidor activo que reciba de la misma.

### <a name="message-selectors"></a>Selectores de mensajes

Al igual que existen **filtros y acciones** para las suscripciones normales a Service Bus, existen **selectores de mensajes** para las suscripciones de JMS.

Los selectores de mensajes se pueden configurar en cada una de las suscripciones de JMS y existen como una condición de filtro en las propiedades del encabezado del mensaje. Solo se entregarán aquellos mensajes cuyas propiedades de encabezado coincidan con la expresión de selector de mensajes. Un valor de null o una cadena vacía indica que no hay ningún selector de mensajes para el consumidor o la suscripción de JMS.

## <a name="additional-concepts-for-java-message-service-jms-20-subscriptions"></a>Conceptos adicionales para las suscripciones de Java Message Service 2.0

### <a name="client-scoping"></a>Ámbito del cliente

Las suscripciones, tal y como se especifican en la API de Java Message Service 2.0, pueden tener o no un *ámbito limitado a aplicaciones cliente específicas* (identificadas con el identificador `clientId` adecuado).

Una vez que se limita el ámbito de una suscripción, **solo se puede acceder** a ella desde las aplicaciones cliente que tengan el mismo identificador de cliente. 

Cualquier intento de acceder a una suscripción cuyo ámbito esté limitado a un identificador de cliente específico (por ejemplo, clientId1) desde una aplicación con otro identificador de cliente (por ejemplo, clientId2) dará lugar a la creación de otra suscripción cuyo ámbito sea el otro identificador de cliente (clientId2).

> [!NOTE]
> El identificador de cliente puede ser null o estar vacío, pero debe coincidir con el identificador de cliente establecido en la aplicación cliente de JMS. Desde la perspectiva de Azure Service Bus, un identificador de cliente NULL y un identificador de cliente vacío tienen el mismo comportamiento.
>
> Si el identificador de cliente es null o está vacío, solo es accesible para las aplicaciones cliente cuyo identificador de cliente también sea null o esté vacío.
>

### <a name="shareability"></a>Capacidad de uso compartido

Las suscripciones **compartidas** permiten que varios clientes o consumidores (es decir, objetos JMSConsumer) reciban mensajes de ellos.

>[!NOTE]
> Varios clientes o consumidores podrán seguir accediendo a las suscripciones compartidas con un ámbito limitado a un identificador de cliente específico (es decir, objetos JMSConsumer), pero cada una de las aplicaciones cliente debe tener el mismo identificador de cliente.
>
 

Las suscripciones **no compartidas** solo permiten que un único cliente o consumidor (es decir, un objeto JMSConsumer) reciba mensajes de ellas. Si se crea un objeto `JMSConsumer` en una suscripción no compartida al tiempo que ya tiene una escucha de `JMSConsumer` activa para los mensajes de ella, se producirá una excepción `JMSException`.


### <a name="durability"></a>Durabilidad.

Las suscripciones **duraderas** se conservan y continúan recopilando mensajes del tema, independientemente de si una aplicación (`JMSConsumer`) está consumiendo los mensajes de este.

No se conservarán las suscripciones **no duraderas** y los mensajes del tema se recopilarán siempre y cuando una aplicación (`JMSConsumer`) consuma los mensajes de este. 

## <a name="representation-of-client-scoped-subscriptions"></a>Representación de suscripciones de clientes con ámbito

Dado que las suscripciones de cliente (JMS) con ámbito deben coexistir con las [suscripciones](service-bus-queues-topics-subscriptions.md#topics-and-subscriptions) existentes, la manera en que se representan las suscripciones de clientes (JMS) con ámbito tiene el siguiente formato.

   * **\<SUBSCRIPTION-NAME\>** $ **\<CLIENT-ID\>** $**D** (para las suscripciones duraderas)
   * **\<SUBSCRIPTION-NAME\>** $ **\<CLIENT-ID\>** $**ND** (para las no duraderas)

Aquí, **$** es el delimitador.

## <a name="next-steps"></a>Pasos siguientes

Para más información y ejemplos de cómo usar la mensajería de Service Bus, consulte estos temas avanzados:

* [Introducción a la mensajería de Service Bus](service-bus-messaging-overview.md)
* [Uso de la API de Java Message Service 2.0 con Azure Service Bus Premium](how-to-use-java-message-service-20.md)



