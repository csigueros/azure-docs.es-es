---
title: 'Aplicaciones y tareas de replicación de mensajes: Azure Service Bus | Microsoft Docs'
description: En este artículo se proporciona información general sobre la compilación de aplicaciones y tareas de replicación de mensajes con Azure Functions.
ms.topic: article
ms.date: 09/28/2021
ms.openlocfilehash: 78598401816adc32eecb34e2bd11fa49e8a44b0f
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2021
ms.locfileid: "129211817"
---
# <a name="message-replication-tasks-and-applications"></a>Aplicaciones y tareas de replicación de mensajes

Como se explica en el artículo [Replicación de mensajes y federación entre regiones](service-bus-federation-overview.md), la replicación de las secuencias de mensajes entre pares de entidades de Service Bus y entre Service Bus y otros orígenes y destinos de mensajes generalmente se apoya en Azure Functions.

[Azure Functions](../azure-functions/functions-overview.md) es un entorno de ejecución escalable y confiable para configurar y ejecutar aplicaciones sin servidor, incluidas las tareas de [federación y replicación de mensajes](service-bus-federation-overview.md).

En esta página de información general, obtendrá información sobre las funciones integradas de Azure Functions para estas aplicaciones, sobre los bloques de código que puede adaptar y modificar para las tareas de transformación y sobre cómo configurar una aplicación de Azure Functions de modo que se integre idealmente con Service Bus y otros servicios de mensajería de Azure. Para gran parte de la información, este artículo apuntará a la documentación de Azure Functions.

[!INCLUDE [messaging-replicator-functions](../../includes/messaging-replicator-functions.md)]
