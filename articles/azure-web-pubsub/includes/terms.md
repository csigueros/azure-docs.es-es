---
author: vicancy
ms.author: lianwei
ms.service: azure-web-pubsub
ms.topic: include
ms.date: 08/06/2021
ms.openlocfilehash: 2356b3af08577063326da7dbb3cf8141d52cea07
ms.sourcegitcommit: 5d605bb65ad2933e03b605e794cbf7cb3d1145f6
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/20/2021
ms.locfileid: "122597209"
---
- **Conexión**: una conexión, también conocida como "cliente" o "conexión de cliente", representa una conexión WebSocket individual conectada al servicio Web PubSub. Cuando se conecta correctamente, el servicio Web PubSub asigna un identificador de conexión único a esta conexión.

- **Centro de conectividad**: El centro de conectividad es un concepto lógico para un conjunto de conexiones de clientes. Normalmente se usa un centro de conectividad para una finalidad, por ejemplo, un centro de conectividad de *chats* o uno de *notificaciones*. Cuando se establece una conexión de cliente, esta se realiza con un centro de conectividad y, durante su vigencia, pertenece a dicho centro. Diferentes aplicaciones pueden compartir un servicio de Azure Web PubSub mediante el uso de nombres de centros de conectividad diferentes.

- **Grupo**: el grupo es un subconjunto de conexiones al centro de conectividad. El grupo es relativamente ligero. Puede agregar una conexión de cliente a un grupo o quitarla de este cuando quiera. Por ejemplo, si un cliente se une a un salón de chat y sale de este, dicho salón puede ser un grupo. Un cliente puede unirse a varios grupos, y un grupo puede contener varios clientes.

- **Usuario**: las conexiones a Web PubSub pueden pertenecer a un usuario. Un usuario puede tener varias conexiones, por ejemplo, cuando está conectado a través de varios dispositivos o distintas pestañas del explorador.

- **Mensaje**: Cuando el cliente está conectado, puede enviar mensajes a Upstream o recibir mensajes de esta característica desde la conexión WebSocket.
