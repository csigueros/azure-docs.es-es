---
title: 'Documentación conceptual para Azure Communication Services: Network Traversal'
titleSuffix: An Azure Communication Services Concept Document
description: Obtenga más información sobre los SDK y las API REST de Network Traversal de Azure Communication Services.
author: rahulva
manager: shahen
services: azure-communication-services
ms.author: rahulva
ms.date: 09/20/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.openlocfilehash: 02f52069cc9dea270cc8e6a34096af5bd80159d7
ms.sourcegitcommit: 591ffa464618b8bb3c6caec49a0aa9c91aa5e882
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/06/2021
ms.locfileid: "131894906"
---
#  <a name="network-traversal-concepts"></a>Conceptos de Network Traversal   

Las retransmisiones en tiempo real resuelven el problema de NAT (traducción de direcciones de red) traversal para las conexiones de punto a punto (P2P). En la actualidad, la mayoría de los dispositivos en Internet tienen una dirección IP que se usa para el tráfico LAN interno (en la red doméstica o corporativa) o una dirección visible externamente (enrutador o NAT Gateway). Para conectar dos dispositivos en Internet, se requiere la dirección externa, pero normalmente no está disponible para un dispositivo detrás de una instancia de NAT Gateway. Para solucionar el problema de conectividad, se usan los protocolos siguientes:

  STUN (siglas en inglés de Session Traversal Utilities for NAT) ofrece un protocolo que permite que los dispositivos intercambien direcciones IP externas en Internet. Si los clientes pueden verse entre sí, normalmente no es necesario realizar una retransmisión a través de un servicio TURN, ya que la conexión se puede realizar de punto a punto. El trabajo de un servidor STUN consiste en responder a la solicitud de la dirección IP externa de un dispositivo.
  
  TURN (siglas en inglés de Traversal Using Relays around NAT) es una extensión del protocolo STUN que también retransmite los datos entre dos puntos de conexión a través de un servidor visible mutuamente.
        
## <a name="acs-network-traversal-overview"></a>Información general de Network Traversal de ACS   

WebRTC (Web Real-Time Technologies) permite a los exploradores web transmitir audio, vídeo y datos entre dispositivos sin necesidad de tener una puerta de enlace en medio. Algunos de los casos de uso comunes son la voz, el vídeo, la difusión y el uso compartido de la pantalla. Para conectar dos puntos de conexión en Internet, se requiere su dirección IP externa. Normalmente, la dirección IP externa no está disponible para los dispositivos que se encuentran detrás de un firewall corporativo. Los protocolos como STUN y TURN se usan para ayudar a los puntos de conexión a comunicarse.

Azure Communication Service proporciona conexiones con un ancho de banda alto y latencia baja entre pares para escenarios de comunicaciones en tiempo real. El servicio Network Traversal de ACS hospeda servidores TURN para su uso con los escenarios NAT. El servicio de retransmisión en tiempo real de Azure expone la infraestructura STUN/TURN existente como una oferta de Azure de plataforma como servicio (PaaS). El servicio proporciona servicios STUN y TURN de bajo nivel  y se factura a los usuarios proporcionalmente a la cantidad de datos retransmitidos. 


## <a name="next-steps"></a>Pasos siguientes:

* Para una introducción a la autenticación, consulte [Autenticación en Azure Communication Services](./authentication.md).
* Para ver una introducción a la adquisición de candidatos de retransmisión, consulte [Creación y administración de tokens de acceso](../quickstarts/relay-token.md).
