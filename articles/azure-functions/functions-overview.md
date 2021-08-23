---
title: Información general sobre Azure Functions
description: Conozca cómo Azure Functions puede ayudar a crear aplicaciones sin servidor sólidas.
author: craigshoemaker
ms.assetid: 01d6ca9f-ca3f-44fa-b0b9-7ffee115acd4
ms.topic: overview
ms.date: 11/20/2020
ms.author: cshoe
ms.custom: contperf-fy21q2
ms.openlocfilehash: 8562acbf0b7dc6cac0ef03e6d0ca41691f752cdf
ms.sourcegitcommit: f2eb1bc583962ea0b616577f47b325d548fd0efa
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/28/2021
ms.locfileid: "114729803"
---
# <a name="introduction-to-azure-functions"></a>Introducción a Azure Functions

Azure Functions es una solución sin servidor que le permite escribir menos código, mantener menos infraestructura y ahorrar costos. En lugar de preocuparse por implementar y mantener servidores, la infraestructura en la nube proporciona todos los recursos actualizados necesarios para mantener las aplicaciones en ejecución.

Usted se centra en los fragmentos de código que más le importan y Azure Functions se ocupa del resto.<br /><br />

> [!VIDEO https://www.youtube.com/embed/8-jz5f_JyEQ]

A menudo, se crean sistemas para que reaccionen a una serie de eventos críticos. Independientemente de si compila una API web, responde a cambios en una base de datos, procesa flujos de datos de IoT o incluso si administra colas de mensajes, cada aplicación necesita una forma de ejecutar código a medida que se producen estos eventos.

Para ello, Azure Functions ofrece "proceso a petición" de dos maneras significativas.

En primer lugar, Azure Functions permite implementar la lógica del sistema en bloques de código fácilmente disponibles. Estos bloques de código se denominan "funciones". Se pueden ejecutar distintas funciones cada vez que necesite responder a eventos críticos.

En segundo lugar, a medida que aumentan las solicitudes, Azure Functions satisface la demanda con tantos recursos e instancias de función como se necesiten, pero solo cuando sea necesario. A medida que disminuyan las solicitudes, todos los recursos e instancias de la aplicación adicionales se descartarán automáticamente.

¿De dónde proceden todos los recursos de proceso? Azure Functions [proporciona tantos recursos de proceso como sea necesario](./functions-scale.md) para satisfacer la demanda de la aplicación.

Proporcionar recursos de proceso a petición es la esencia de la [informática sin servidor](https://azure.microsoft.com/solutions/serverless/) en Azure Functions.

## <a name="scenarios"></a>Escenarios

En muchos casos, una función [se integra con una matriz de servicios en la nube](./functions-triggers-bindings.md) para proporcionar implementaciones con numerosas características.

A continuación se incluye un conjunto de escenarios habituales de Azure Functions, _si bien no están reflejadas todas las posibilidades_.

| Si desea... | entonces... |
| --- | --- |
| **Crear una API web** | Implemente un punto de conexión para las aplicaciones web mediante el [desencadenador HTTP](./functions-bindings-http-webhook.md) |
| **Procesar cargas de archivos** | Ejecute código cuando se cargue o se cambie un archivo en el [almacenamiento de blobs](./functions-bindings-storage-blob.md) |
| **Compilar un flujo de trabajo sin servidor** | Encadene una serie de funciones mediante [Durable Functions](./durable/durable-functions-overview.md) |
| **Responder a cambios en una base de datos** | Ejecute una lógica personalizada cuando se cree o actualice un documento en [Cosmos DB](./functions-bindings-cosmosdb-v2.md) |
| **Ejecutar tareas programadas** | Ejecución de código en [intervalos de tiempo predefinidos](./functions-bindings-timer.md) |
| **Crear sistemas de cola de mensajes confiables** | Procese colas de mensajes mediante [Queue Storage](./functions-bindings-storage-queue.md), [Service Bus](./functions-bindings-service-bus.md) o [Event Hubs](./functions-bindings-event-hubs.md) |
| **Analizar flujos de datos de IoT** | Recopile y procese [datos de dispositivos IoT](./functions-bindings-event-iot.md) |
| **Procesar datos en tiempo real** | Use [Functions y SignalR](./functions-bindings-signalr-service.md) para responder a los datos en el momento. |

Al compilar las funciones, dispone de las siguientes opciones y recursos:

- **Usar el lenguaje que prefiera**: escriba funciones en [C#, Java, JavaScript, PowerShell o Python](./supported-languages.md), o use un [controlador personalizado](./functions-custom-handlers.md) para usar prácticamente cualquier otro lenguaje.

- **Automatizar la implementación**: desde un enfoque basado en herramientas hasta el uso de canalizaciones externas, dispone de una [enorme cantidad de opciones de implementación](./functions-deployment-technologies.md).

- **Solucionar problemas de una función**: use [herramientas de supervisión](./functions-monitoring.md) y [estrategias de pruebas](./functions-test-a-function.md) para obtener información sobre las aplicaciones.

- **Opciones de precios flexibles**: con el plan de [Consumo](./pricing.md), solo pagará mientras se ejecuten las funciones, mientras que los planes [Premium](./pricing.md) y [App Service](./pricing.md) ofrecen características para necesidades especializadas.

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Introducción con lecciones, ejemplos y tutoriales interactivos](./functions-get-started.md)
