---
title: Archivo de inclusión
description: archivo de inclusión
services: service-bus-messaging
author: spelluru
ms.service: service-bus-messaging
ms.topic: include
ms.date: 09/29/2021
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 0b60d1c84ca5b88f0a97d23397a25c3b9f9a5053
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2021
ms.locfileid: "129273175"
---
## <a name="prerequisites"></a>Requisitos previos
Si no tiene una [suscripción a Azure](../../guides/developer/azure-developer-guide.md#understanding-accounts-subscriptions-and-billing), cree una [cuenta gratuita](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) antes de empezar.

## <a name="create-a-service-bus-namespace"></a>Creación de un espacio de nombres de Service Bus
Siga las instrucciones de este tutorial: [Inicio rápido: Use Azure Portal para crear un tema de Service Bus y suscripciones al tema ](../service-bus-quickstart-topics-subscriptions-portal.md) para realizar las siguientes tareas:

- Cree un espacio de nombres de Service Bus **Premium**. 
- Obtenga la cadena de conexión. 
- Cree un tema de Service Bus.
- Cree una suscripción al tema. En este tutorial solo necesita una suscripción, por lo que no es necesario crear suscripciones S2 o S3. 

## <a name="send-messages-to-the-service-bus-topic"></a>Envío de mensaje al tema de Service Bus
En este paso utilizará una aplicación de ejemplo para enviar mensajes al tema de Service Bus que creó en el paso anterior. 

1. Clone el [repositorio azure-service-bus de GitHub](https://github.com/Azure/azure-service-bus/) o descargue el archivo zip y extraiga los archivos. 
2. En Visual Studio, vaya a la carpeta *\samples\DotNet\Azure.Messaging.ServiceBus\ServiceBusEventGridIntegrationV2* y abra el archivo *SBEventGridIntegration.sln*.
3. En la ventana del Explorador de soluciones, expanda el proyecto **MessageSender** y seleccione **Program.cs**.
4. Reemplace `<SERVICE BUS NAMESPACE - CONNECTION STRING>` por la cadena de conexión del espacio de nombres de Service Bus y `<TOPIC NAME>` por el nombre del tema. 

    ```csharp
    const string ServiceBusConnectionString = "<SERVICE BUS NAMESPACE - CONNECTION STRING>";
    const string TopicName = "<TOPIC NAME>";
    ```
5. Compile y ejecute el programa para enviar cinco mensajes de prueba (`const int numberOfMessages = 5;`) al tema de Service Bus. 

    :::image type="content" source="./media/service-bus-event-grid-prerequisites/console-app-output.png" alt-text="Salida de la aplicación de consola":::
