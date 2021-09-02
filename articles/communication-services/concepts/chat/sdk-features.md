---
title: Introducción al SDK de chat de Azure Communication Services
titleSuffix: An Azure Communication Services concept document
description: Obtenga información sobre el SDK de chat de Azure Communication Services.
author: knvsl
manager: chpalm
services: azure-communication-services
ms.author: rifox
ms.date: 06/30/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.openlocfilehash: 2279761beffa9724a56fe9a4af54787d15a3705a
ms.sourcegitcommit: 2eac9bd319fb8b3a1080518c73ee337123286fa2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/31/2021
ms.locfileid: "123258267"
---
# <a name="chat-sdk-overview"></a>Introducción al SDK de chat 

Los SDK de chat de Azure Communication Services se pueden usar para agregar chats enriquecidos en tiempo real a las aplicaciones.

## <a name="chat-sdk-capabilities"></a>Funcionalidades del SDK de chat    

En la lista siguiente se presenta el conjunto de características que están disponibles actualmente en los SDK de chat de Communication Services.  

| Grupo de características | Capacidad | JavaScript  | Java | .NET | Python | iOS | Android |
|-----------------|-------------------|---|-----|----|-----|----|----|
| Funcionalidades principales | Crear una conversación de chat entre 2 o más usuarios                                                     | ✔️   | ✔️  | ✔️    | ✔️   |  ✔️    | ✔️   |    
|                   | Actualizar el tema de una conversación de chat                                                                              | ✔️   | ✔️ | ✔️    | ✔️   |  ✔️    | ✔️   |   
|                   | Incorporación o eliminación de participantes de una conversación de chat                                                                           | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   |  
|                   | Elegir si desea compartir el historial de mensajes de chat con el participante que se va a agregar                                   | ✔️   | ✔️   | ✔️    | ✔️  |  ✔️    | ✔️   | 
|                   | Obtener una lista de participantes de una conversación de chat                                                                          | ✔️   | ✔️  | ✔️ | ✔️ |  ✔️    | ✔️   | 
|                   | Eliminar una conversación de chat                                                                                              | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   |    
|                   | Dado un usuario de comunicación, obtenga la lista de conversaciones de chat de las que forma parte el usuario.                                           | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   |   
|                   | Obtener información sobre una conversación de chat determinada                                                                              | ✔️   | ✔️  | ✔️ | ✔️ |  ✔️    | ✔️   |   
|                   | Enviar y recibir mensajes en una conversación de chat                                                                            | ✔️   | ✔️   | ✔️    | ✔️  |  ✔️    | ✔️   |   
|                   | Actualizar el contenido del mensaje enviado                                                                               | ✔️   | ✔️  | ✔️ | ✔️ |  ✔️    | ✔️   |    
|                   | Eliminar un mensaje enviado anteriormente                                                                                                      | ✔️   | ✔️  | ✔️ | ✔️ |  ✔️    | ✔️   |    
|                   | Confirmaciones de lectura de mensajes leídos por otros participantes de un chat                                        | ✔️   | ✔️  | ✔️    | ✔️   |  ✔️    | ✔️   |   
|                   | Recibir notificaciones cuando los participantes escriben activamente un mensaje en una conversación de chat                                         | ✔️   | ❌    | ❌  | ❌  | ✔️  | ✔️  |   
|                   | Obtener todos los mensajes de una conversación de chat                                                                        | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   | 
|                   | Enviar emojis de Unicode como parte del contenido del mensaje                                                                            | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   |    
|                   | Agregar metadatos a los mensajes de chat                                                                            | ✔️   | ✔️  | ✔️    | ✔️  |  ❌    | ✔️   |  
|                   | Agregar un nombre para mostrar a la notificación de indicador de escritura                                                                            | ✔️   | ✔️  | ✔️    | ✔️  |  ❌    | ✔️   |  
|Notificaciones en tiempo real (habilitadas mediante un paquete de señalización propietario**)|  Los clientes de chat se pueden suscribir para obtener actualizaciones en tiempo real de los mensajes entrantes y otras operaciones que se producen en una conversación de chat. Para ver una lista de las actualizaciones admitidas para las notificaciones en tiempo real, consulte [Conceptos de chat](concepts.md#real-time-notifications).                                     | ✔️   | ❌    | ❌  | ❌  | ✔️  | ✔️  |   
| Integración con Azure Event Grid             | Use los eventos de chat disponibles en Azure Event Grid para conectar servicios de notificación personalizados o para publicar ese evento en un webhook para ejecutar la lógica de negocios, como la actualización de los registros de CRM una vez finalizada la conversación.   | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   |    
| Generación de informes </br>(Esta información está disponible en la pestaña de supervisión del recurso de Communication Services en Azure Portal).      | Conozca el tráfico de API de la aplicación de chat mediante la supervisión de las métricas publicadas en el Explorador de métricas de Azure y establezca alertas para detectar anomalías.     | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   |  
|                   | Supervisión y depuración de la solución de Communication Services mediante la habilitación del registro de diagnóstico para el recurso    | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   |   


** El paquete de señalización propietario se implementa mediante sockets web. Si no se admiten los sockets web, se reservará a un sondeo prolongado.  

## <a name="javascript-chat-sdk-support-by-os-and-browser"></a>Compatibilidad del SDK de chat de JavaScript por sistema operativo y explorador    

En la tabla siguiente se representa el conjunto de exploradores y versiones compatibles que están disponibles actualmente.
    
|                                  | Windows          | macOS          | Ubuntu | Linux  | Android | iOS    | SO de iPad|
|--------------------------------|----------------|--------------|-------|------|------|------|-------|
| **SDK de chat** | Firefox *, Chrome*, nuevo Edge | Firefox *, Chrome*, Safari* | Chrome*  | Chrome* | Chrome* | Safari* | Safari* |

\* Tenga en cuenta que se admite la versión más reciente además de las dos versiones anteriores.<br/>   

## <a name="next-steps"></a>Pasos siguientes   

> [!div class="nextstepaction"] 
> [Introducción al chat](../../quickstarts/chat/get-started.md)    

Puede que los siguientes documentos le resulten interesantes:  
- Familiarícese con los [conceptos del chat](../chat/concepts.md)
- Conozca cómo funcionan los [precios](../pricing.md#chat) de los servicios de chat.
