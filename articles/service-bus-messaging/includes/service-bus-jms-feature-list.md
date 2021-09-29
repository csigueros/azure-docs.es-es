---
title: Archivo de inclusión
description: archivo de inclusión
services: service-bus-messaging
author: axisc
ms.service: service-bus-messaging
ms.topic: include
ms.date: 09/28/2021
ms.author: aschhab
ms.custom: include file
ms.openlocfilehash: 6d2207b01f6ea9eff3dd2d41e9738f756f3f9fff
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2021
ms.locfileid: "129220601"
---
En la tabla siguiente se enumeran las características de Java Message Service (JMS) que Azure Service Bus admite actualmente. También se muestran las características que no son compatibles.


| Característica | API |Estado |
|---|---|---|
| Colas   | <ul> <li> JMSContext.createQueue( String queueName) </li> </ul>| **Compatible** |
| Temas   | <ul> <li> JMSContext.createTopic( String topicName) </li> </ul>| **Compatible** |
| Colas temporales |<ul> <li> JMSContext.createTemporaryQueue() </li> </ul>| **Compatible** |
| Temas temporales |<ul> <li> JMSContext.createTemporaryTopic() </li> </ul>| **Compatible** |
| Productor de mensajes /<br/> JMSProducer |<ul> <li> JMSContext.createProducer() </li> </ul>| **Compatible** |
| Exploradores de colas |<ul> <li> JMSContext.createBrowser(Queue queue) </li> <li> JMSContext.createBrowser(Queue queue, String messageSelector) </li> </ul> | **Compatible** |
| Consumidor de mensajes/ <br/> JMSConsumer | <ul> <li> JMSContext.createConsumer( Destination destination) </li> <li> JMSContext.createConsumer( Destination destination, String messageSelector) </li> <li> JMSContext.createConsumer( Destination destination, String messageSelector, boolean noLocal)</li> </ul>  <br/> Actualmente, noLocal no se admite. | **Compatible** |
| Suscripciones duraderas compartidas | <ul> <li> JMSContext.createSharedDurableConsumer(Topic topic, String name) </li> <li> JMSContext.createSharedDurableConsumer(Topic topic, String name, String messageSelector) </li> </ul>| **Compatible**|
| Suscripciones duraderas no compartidas | <ul> <li> JMSContext.createDurableConsumer(Topic topic, String name) </li> <li> createDurableConsumer(Topic topic, String name, String messageSelector, boolean noLocal) </li> </ul> <br/> Actualmente no se admite noLocal y debe establecerse en false. | **Compatible** |
| Suscripciones no duraderas compartidas |<ul> <li> JMSContext.createSharedConsumer(Topic topic, String sharedSubscriptionName) </li> <li> JMSContext.createSharedConsumer(Topic topic, String sharedSubscriptionName, String messageSelector) </li> </ul> | **Compatible** |
| Suscripciones no duraderas no compartidas |<ul> <li> JMSContext.createConsumer(Destination destination) </li> <li> JMSContext.createConsumer( Destination destination, String messageSelector) </li> <li> JMSContext.createConsumer( Destination destination, String messageSelector, boolean noLocal) </li> </ul> <br/> Actualmente no se admite noLocal y debe establecerse en false. | **Compatible** |
| Selectores de mensajes | dependen del consumidor creado | **Compatible** |
| Retraso de la entrega (mensajes programados) | <ul> <li> JMSProducer.setDeliveryDelay( long deliveryDelay) </li> </ul>|**Compatible**|
| Mensaje creado |<ul> <li> JMSContext.createMessage() </li> <li> JMSContext.createBytesMessage() </li> <li> JMSContext.createMapMessage() </li> <li> JMSContext.createObjectMessage( Serializable object) </li> <li> JMSContext.createStreamMessage() </li> <li> JMSContext.createTextMessage() </li> <li> JMSContext.createTextMessage( String text) </li> </ul>| **Compatible** |
| Transacciones entre entidades |<ul> <li> Connection.createSession(true, Session.SESSION_TRANSACTED) </li> </ul> | **Compatible** |
| Distributed transactions || No compatibles |
