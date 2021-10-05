---
title: Introducción al procesamiento de transacciones en Azure Service Bus
description: En este artículo se ofrece información general sobre el procesamiento de transacciones y la característica de envío a través de Azure Service Bus.
ms.topic: article
ms.date: 09/21/2021
ms.custom: devx-track-csharp
ms.openlocfilehash: 5eb3bf6eef551fd13788f7659eb8becede8e250d
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128666219"
---
# <a name="overview-of-service-bus-transaction-processing"></a>Información general sobre el procesamiento de transacciones de Service Bus

En este artículo se describen las funcionalidades de transacciones de Microsoft Azure Service Bus. Gran parte de la discusión se ilustra mediante el [ejemplo de transacciones AMQP con Service Bus](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.Azure.ServiceBus/TransactionsAndSendVia/TransactionsAndSendVia/AMQPTransactionsSendVia). Este artículo se limita a proporcionar información general sobre el procesamiento de transacciones y la característica *Enviar por* de Service Bus, mientras que el ejemplo de transacciones atómicas es más amplio y con un ámbito más complejo.

> [!NOTE]
> El nivel básico de Service Bus no admite transacciones. Los niveles estándar y prémium sí las admiten. Para conocer las diferencias entre estos niveles, consulte [Precios de Service Bus](https://azure.microsoft.com/pricing/details/service-bus/).

## <a name="transactions-in-service-bus"></a>Transacciones en Service Bus

Una *transacción* agrupa dos o más operaciones en un *ámbito de ejecución*. Por naturaleza, una transacción de este tipo debe garantizar que todas las operaciones que pertenecen a un grupo determinado de operaciones tendrán éxito o darán error de forma conjunta. En este sentido, las transacciones actúan como una unidad, lo que a menudo se conoce como *atomicidad*.

Service Bus es un agente de mensajes transaccional, y garantiza la integridad transaccional de todas las operaciones internas en sus almacenes de mensajes. Todas las transferencias de mensajes de Service Bus, como mover mensajes a una [cola de mensajes fallidos](service-bus-dead-letter-queues.md) o [reenviar automáticamente](service-bus-auto-forwarding.md) mensajes entre entidades, son transaccionales. Por lo tanto, si Service Bus acepta un mensaje, es que ya se ha almacenado y etiquetado con un número de secuencia. Desde ese momento, todas las transferencias de mensajes dentro de Service Bus son operaciones coordinadas entre entidades, y ninguna de ellas dará lugar a la pérdida (el origen tiene éxito y el destino da error) o a la desduplicación (el origen da error y el destino tiene éxito) del mensaje.

Service Bus admite operaciones de agrupación en una sola entidad de mensajería (cola, tema, suscripción) dentro del ámbito de una transacción. Por ejemplo, puede enviar varios mensajes a una cola desde dentro de un ámbito de transacción y los mensajes solo se confirmarán en el registro de la cola cuando la transacción se complete correctamente.

## <a name="operations-within-a-transaction-scope"></a>Operaciones dentro de un ámbito de transacción

Las operaciones que pueden realizarse dentro de un ámbito de transacción son las siguientes:

- Envío
- Completo
- Abandon
- Deadletter
- Defer
- Renew lock

Las operaciones de recepción no se incluyen, porque se supone que la aplicación captura mensajes mediante el modo de inspección y bloqueo, dentro de algún bucle de recepción o con una devolución de llamada, y solo entonces se abre un ámbito de transacción para procesar el mensaje.

La disposición del mensaje (completar, abandonar, correo devuelto, aplazar), se produce entonces dentro del ámbito del resultado general de la transacción y depende de este.

## <a name="transfers-and-send-via"></a>Transferencias y "enviar por"

Para permitir la entrega transaccional de datos de una cola o un tema a un procesador y luego a otra cola u otro tema, Service Bus admite *transferencias*. En las operaciones de transferencia, en primer lugar un remitente envía un mensaje a una *cola o un tema de transferencia*, que lo mueve inmediatamente a la cola o el tema de destino deseado usando la misma implementación de transferencias sólida que la funcionalidad de reenvío automático en la que se basa. El mensaje nunca se confirma en el registro del tema o la cola de transferencia de forma que se vuelve visible para los consumidores de dicho tema o cola.

La eficacia de esta funcionalidad transaccional se hace evidente cuando el propio tema o la cola de transferencia es el origen de los mensajes de entrada del remitente. En otras palabras, Service Bus puede transferir el mensaje a la cola o el tema de destino "mediante" la cola o el tema de transferencia y al mismo tiempo realizar una operación completa (o de aplazamiento o correo devuelto) en el mensaje de entrada, todo en una operación atómica. 

Si necesita recibir de una suscripción a un tema y, a continuación, enviar a una cola o un tema en la misma transacción, la entidad de transferencia debe ser un tema. En este escenario, inicie el ámbito de la transacción en el tema, reciba desde la suscripción en el ámbito de la transacción y envíe a través del tema de transferencia a una cola o a un destino de tema. 

### <a name="see-it-in-code"></a>Verlo en el código

Para configurar tales transferencias, se crea el remitente de un mensaje que se dirige a la cola de destino mediante la cola de transferencia. También hay un receptor que extrae los mensajes de esa misma cola. Por ejemplo:

En una transacción sencilla, se usan estos elementos, como en el ejemplo siguiente. Para ver el ejemplo completo, consulte el [código fuente en GitHub](https://github.com/Azure/azure-sdk-for-net/blob/main/sdk/servicebus/Azure.Messaging.ServiceBus/samples/Sample06_Transactions.md#transactions-across-entities):

```csharp
var options = new ServiceBusClientOptions { EnableCrossEntityTransactions = true };
await using var client = new ServiceBusClient(connectionString, options);

ServiceBusReceiver receiverA = client.CreateReceiver("queueA");
ServiceBusSender senderB = client.CreateSender("queueB");

ServiceBusReceivedMessage receivedMessage = await receiverA.ReceiveMessageAsync();

using (var ts = new TransactionScope(TransactionScopeAsyncFlowOption.Enabled))
{
    await receiverA.CompleteMessageAsync(receivedMessage);
    await senderB.SendMessageAsync(new ServiceBusMessage());
    ts.Complete();
}
```


## <a name="timeout"></a>Tiempo de espera
Una transacción agota el tiempo de espera después de 2 minutos. El temporizador de la transacción se inicia cuando comienza la primera operación de la transacción. 

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre colas de Service Bus, consulte los siguientes artículos:

* [Utilización de las colas de Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Encadenamiento de entidades de Service Bus con reenvío automático](service-bus-auto-forwarding.md)
* [Ejemplo de reenvío automático](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/AutoForward)
* [Ejemplo de transacciones atómicas con Service Bus](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/AtomicTransactions)
* [Comparación de colas de Azure y colas de Service Bus](service-bus-azure-and-service-bus-queues-compared-contrasted.md)


