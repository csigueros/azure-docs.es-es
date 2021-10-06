---
author: vicancy
ms.author: lianwei
ms.service: azure-web-pubsub
ms.topic: include
ms.date: 08/06/2021
ms.openlocfilehash: 4fc2131e57d89a99b44a8ba345b901af5bc8890a
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "126056821"
---
- **Conexión**: una conexión, también conocida como "cliente" o "conexión de cliente", representa una conexión WebSocket individual conectada al servicio Web PubSub. Cuando se conecta correctamente, el servicio Web PubSub asigna un identificador de conexión único a esta conexión.

- **Centro de conectividad**: un centro de conectividad es un concepto lógico relativo a un conjunto de conexiones de clientes. Normalmente se usa un centro de conectividad para una finalidad, por ejemplo, un centro de conectividad de *chats* o uno de *notificaciones*. Cuando se establece una conexión de cliente, esta se realiza con un centro de conectividad y, durante su vigencia, pertenece a dicho centro. Diferentes aplicaciones pueden compartir un servicio de Azure Web PubSub mediante el uso de nombres de centros de conectividad diferentes.

- **Grupo**: un grupo es un subconjunto de conexiones al centro de conectividad. Puede agregar una conexión de cliente a un grupo o quitarla de este cuando quiera. Por ejemplo, cuando un cliente se une a una sala de chat o sale de ella, dicha sala puede considerarse un grupo. Un cliente puede unirse a varios grupos, y un grupo puede contener varios clientes.

- **Usuario**: las conexiones a Web PubSub pueden pertenecer a un usuario. Un usuario puede tener varias conexiones, por ejemplo, cuando está conectado a través de varios dispositivos o distintas pestañas del explorador.

- **Mensaje**: cuando el cliente está conectado, puede enviar mensajes a la aplicación de nivel superior o recibir mensajes de esta, mediante la conexión WebSocket.
