---
title: Conceptos básicos de Azure Web PubSub sobre centros de conectividad, grupos y conexiones
description: Comprenda mejor los términos que se usan en Azure Web PubSub.
author: lianwei
ms.author: lianwei
ms.service: azure-web-pubsub
ms.topic: quickstart
ms.date: 11/08/2021
ms.openlocfilehash: dc05e81228149fbeae647af81f97bc1e3a320c1f
ms.sourcegitcommit: 27ddccfa351f574431fb4775e5cd486eb21080e0
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/08/2021
ms.locfileid: "131997711"
---
# <a name="azure-web-pubsub-basic-concepts"></a>Conceptos básicos de Azure Web PubSub

El servicio Azure Web PubSub le ayuda a crear aplicaciones web de mensajería en tiempo real. Los clientes se conectan al servicio mediante el [protocolo WebSocket estándar](https://datatracker.ietf.org/doc/html/rfc6455) y el servicio expone las [API REST](/rest/api/webpubsub) y los SDK para que pueda administrar estos clientes.

## <a name="terms"></a>Términos

Estos son algunos términos importantes que usa el servicio:

[!INCLUDE [Terms](includes/terms.md)]

## <a name="workflow"></a>Flujo de trabajo

A continuación, se muestra un flujo de trabajo típico que usa el servicio:

![Diagrama que muestra el flujo de trabajo del servicio Web PubSub.](./media/concept-service-internals/workflow.png)

Como se muestra en el gráfico de flujo de trabajo anterior:

1. Un *cliente* se conecta al punto de conexión `/client` del servicio mediante el transporte de WebSocket. El servicio reenvía cada marco de WebSocket al servidor ascendente configurado. La conexión de WebSocket puede conectarse con cualquier subprotocolo personalizado para que el servidor lo controle, o bien puede conectarse con el subprotocolo `json.webpubsub.azure.v1` compatible con el servicio, que permite a los clientes publicar o suscribir directamente. En [Protocolo del cliente](concept-service-internals.md#client_protocol) se describen los detalles.

2. El servicio invoca el servidor mediante el **protocolo HTTP CloudEvents** en eventos de cliente diferentes. [**CloudEvents**](https://github.com/cloudevents/spec/blob/v1.0.1/http-protocol-binding.md) es una definición del protocolo, estandarizada e independiente de la estructura, y una descripción de metadatos de los eventos hospedados por Cloud Native Computing Foundation (CNCF). Los detalles se describen en [Protocolo del servidor](concept-service-internals.md#server_protocol).

3. El servidor puede invocar el servicio mediante la API REST para enviar mensajes a los clientes o para administrar los clientes conectados. Los detalles se describen en [Protocolo del servidor](concept-service-internals.md#server_protocol).