---
title: ¿Qué es Relay de Azure? | Microsoft Docs
description: En este artículo se proporciona información general sobre el servicio Azure Relay que le permite desarrollar aplicaciones en la nube que consumen servicios locales que se ejecutan en la red corporativa sin necesidad de abrir una conexión de firewall ni realizar cambios molestos en la infraestructura de red.
ms.topic: conceptual
ms.date: 09/02/2021
ms.custom: contperf-fy22q1
ms.openlocfilehash: af12067cd73b8767beb243e9e098d2cbbf9c456a
ms.sourcegitcommit: 10029520c69258ad4be29146ffc139ae62ccddc7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/27/2021
ms.locfileid: "129081878"
---
# <a name="what-is-azure-relay"></a>¿Qué es Relay de Azure?
El servicio Azure Relay le permite exponer de forma segura servicios que se ejecutan en la red corporativa en la nube pública. Eso se puede hacer sin tener que abrir un puerto en el firewall y sin realizar cambios molestos en la infraestructura de la red corporativa. 

El servicio Azure Relay admite los siguientes escenarios entre servicios locales y aplicaciones que se ejecutan en la nube o en otro entorno local. 

- Comunicación unidireccional tradicional, de solicitud/respuesta y de punto a punto. 
- Distribución de eventos en el ámbito de Internet para habilitar escenarios de publicación y suscripción 
- Comunicación de socket bidireccional y sin almacenamiento en búfer en los límites de red.

Azure Relay es diferente a las tecnologías de integración en el nivel de red, como VPN. Una instancia de Azure Relay se puede limitar a un único punto de conexión de la aplicación de una única máquina. La tecnología VPN es mucho más intrusiva, ya que se basa en modificar el entorno de red. 

## <a name="basic-flow"></a>Flujo básico
En el patrón de transferencia de datos, los pasos básicos son:

1. Un servicio local se conecta al servicio de retransmisión a través de un puerto de salida. 
2. Este servicio crea un socket bidireccional para la comunicación asociado a una dirección determinada. 
3. Después, el cliente puede comunicarse con el servicio local enviando tráfico al servicio de retransmisión destinado a esa dirección. 
4. El servicio de retransmisión *retransmite* entonces los datos al servicio local a través de un socket bidireccional dedicado al cliente. El cliente no necesita una conexión directa al servicio local. No necesita conocer la ubicación del servicio. Y el servicio local no necesita ningún puerto de entrada abierto en el firewall.


## <a name="features"></a>Características 
Relay de Azure tiene dos características:

- [Conexiones híbridas](#hybrid-connections): usa los sockets web de estándar abierto, con lo que se admiten escenarios multiplataforma.
- [Retransmisiones de WCF](#wcf-relay): usa Windows Communication Foundation (WCF) para habilitar las llamadas a procedimientos remotos. WCF Relay es la oferta de Relay heredada que muchos clientes ya pueden utilizar con sus modelos de programación de WCF.

## <a name="hybrid-connections"></a>conexiones híbridas

La característica Conexiones híbridas de Azure Relay es una evolución segura y de protocolo abierto de las características de Relay que ya existían anteriormente. Puede usarla en cualquier plataforma y en cualquier lenguaje. La característica Conexiones híbridas de Azure Relay se basa en protocolos de HTTP y WebSockets. Le permite enviar solicitudes y recibir respuestas a través de sockets web o HTTP (S). Esta característica es compatible con la API de WebSocket en los exploradores web más habituales. 

Para más información sobre el protocolo de Conexiones híbridas, consulte [Guía del protocolo de conexiones híbridas](relay-hybrid-connections-protocol.md). Puede usar Conexiones híbridas con cualquier biblioteca de sockets web de cualquier entorno de ejecución o lenguaje.

> [!NOTE]
> Conexiones híbridas de Azure Relay reemplaza a la anterior característica Conexiones híbridas de BizTalk Services. La característica Conexiones híbridas de BizTalk Services se basaba en Azure Service Bus WCF Relay. La funcionalidad Conexiones híbridas de Azure Relay complementa la característica WCF Relay que ya existía anteriormente. Estas dos funcionalidades del servicio (WCF Relay y Conexiones híbridas) coexisten en el servicio Azure Relay. Aunque comparten una puerta de enlace común, se trata de implementaciones diferentes.

Para empezar a usar conexiones híbridas en Azure Relay, consulte los siguientes inicios rápidos: 

- [Introducción a WebSockets de Conexiones híbridas de Relay en .NET](relay-hybrid-connections-dotnet-get-started.md)
- [Introducción a WebSockets de Conexiones híbridas de Relay en Node.js](relay-hybrid-connections-node-get-started.md)
- [Introducción a las solicitudes HTTP de Conexiones híbridas de Relay en .NET](relay-hybrid-connections-http-requests-dotnet-get-started.md)
- [Introducción a las solicitudes HTTP de Conexiones híbridas de Relay en Node](relay-hybrid-connections-http-requests-node-get-started.md)

Para más ejemplos, consulte [Azure Relay: ejemplos de conexiones híbridas en GitHub](https://github.com/Azure/azure-relay/tree/master/samples/hybrid-connections).

## <a name="wcf-relay"></a>Retransmisión de WCF
WCF Relay es totalmente compatible con .NET Framework y WCF. Puede crear una conexión entre el servicio local y el servicio de retransmisión mediante un conjunto de enlaces de “retransmisión” WCF. Los enlaces de retransmisión se asignan a nuevos elementos de enlace de transporte diseñados para crear componentes de canal WCF que se integran con Service Bus en la nube.

Para empezar a usar WCF Relay, consulte los siguientes inicios rápidos: 

- [Exposición de un servicio WCF local a una aplicación web en la nube con Azure Relay](service-bus-dotnet-hybrid-app-using-service-bus-relay.md)
- [Exposición de un servicio WCF local en un cliente WCF fuera de la red](service-bus-relay-tutorial.md)
- [Exposición de un servicio WCF REST local en un cliente fuera de la red](service-bus-relay-rest-tutorial.md)

Para más ejemplos,consulte [Azure Relay: ejemplos de WCF Relay en GitHub](https://github.com/Azure/azure-relay/tree/master/samples/wcf-relay).

## <a name="hybrid-connections-vs-wcf-relay"></a>Conexiones híbridas en comparación con Retransmisión de WCF
Conexiones híbridas y WCF Relay habilitan una conexión segura a los recursos de dentro de una red corporativa. El uso de una u otra depende de sus necesidades particulares que se detallan en la siguiente tabla:

|  | Retransmisión de WCF | conexiones híbridas |
| --- |:---:|:---:|
| **WCF** |x | |
| **.NET Core** | |x |
| **.NET Framework** |x |x |
| **JavaScript/Node.js** | |x |
| **Protocolo abierto basado en estándares** | |x |
| **Modelos de programación de RPC** | |x |

## <a name="architecture-processing-of-incoming-relay-requests"></a>Arquitectura: Procesamiento de solicitudes entrantes de retransmisión
El siguiente diagrama muestra el control de las solicitudes de transmisión entrantes por parte del servicio Azure Relay:

![Procesamiento de solicitudes entrantes de retransmisión WCF](./media/relay-what-is-it/ic690645.png)

1. El cliente de escucha envía una solicitud de escucha al servicio Azure Relay. Azure Load Balancer enruta la solicitud a uno de los nodos de la puerta de enlace. 
2. El servicio Azure Relay crea una retransmisión en el almacén de puerta de enlace. 
3. El cliente de envío envía una solicitud para conectarse al servicio de escucha. 
4. La puerta de enlace que recibe la solicitud busca la transmisión en el almacén de puerta de enlace. 
5. La puerta de enlace reenvía la solicitud de conexión a la puerta de enlace correcta mencionada en el almacén de puerta de enlace. 
6. La puerta de enlace envía una solicitud al cliente de escucha para que cree un canal temporal al nodo de la puerta de enlace que esté más próximo al cliente de envío. 
7. El cliente de escucha crea un canal temporal a la puerta de enlace más cercana al cliente de envío. Ahora que la conexión está establecida entre los clientes a través de una puerta de enlace, estos pueden intercambiarse mensajes. 
8. La puerta de enlace reenvía cualquier mensaje del cliente de escucha al cliente de envío. 
9. La puerta de enlace reenvía cualquier mensaje del cliente de envío al cliente de escucha.  

## <a name="next-steps"></a>Pasos siguientes
Siga uno o varios de los siguientes inicios rápidos o consulte [Ejemplos de Azure Relay en GitHub](https://github.com/Azure/azure-relay/tree/master/samples).

- conexiones híbridas
    - [Introducción a WebSockets de Conexiones híbridas de Relay en .NET](relay-hybrid-connections-dotnet-get-started.md)
    - [Introducción a WebSockets de Conexiones híbridas de Relay en Node.js](relay-hybrid-connections-node-get-started.md)
    - [Introducción a las solicitudes HTTP de Conexiones híbridas de Relay en .NET](relay-hybrid-connections-http-requests-dotnet-get-started.md)
    - [Introducción a las solicitudes HTTP de Conexiones híbridas de Relay en Node](relay-hybrid-connections-http-requests-node-get-started.md)
- Retransmisión de WCF
    - [Exposición de un servicio WCF local a una aplicación web en la nube con Azure Relay](service-bus-dotnet-hybrid-app-using-service-bus-relay.md)
    - [Exposición de un servicio WCF local en un cliente WCF fuera de la red](service-bus-relay-tutorial.md)
    - [Exposición de un servicio WCF REST local en un cliente fuera de la red](service-bus-relay-rest-tutorial.md)

Para obtener una lista de las preguntas más frecuentes y sus respuestas, consulte [Preguntas frecuentes sobre Azure Relay](relay-faq.yml).

