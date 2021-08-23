---
title: Creación de un servicio de acceso de usuarios de confianza mediante Azure Functions en Azure Communication Services
titleSuffix: An Azure Communication Services tutorial
description: Aprenda a crear un servicio de acceso de usuarios de confianza para Communication Services con Azure Functions
author: ddematheu2
manager: chpalm
services: azure-communication-services
ms.author: dademath
ms.date: 06/30/2021
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: fc1c53a747ca87bcfdac1f742e6235492731e0af
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/16/2021
ms.locfileid: "114291364"
---
# <a name="build-a-trusted-user-access-service-using-azure-functions"></a>Creación de un servicio de acceso de usuarios de confianza mediante Azure Functions

En este artículo se describe cómo usar Azure Functions para crear un servicio de acceso de usuarios de confianza.

> [!IMPORTANT]
> El punto de conexión que se crea al final de este tutorial no es seguro. Asegúrese de leer los detalles de seguridad en el artículo [Seguridad de Azure Functions](../../azure-functions/security-concepts.md). Debe agregar seguridad al punto de conexión para asegurarse de que actores malintencionados no puedan aprovisionar tokens.

[!INCLUDE [Trusted Service JavaScript](./includes/trusted-service-js.md)]

## <a name="securing-azure-function"></a>Protección de Azure Functions

Como parte de la configuración de un servicio de confianza para aprovisionar tokens de acceso para los usuarios, es necesario tener en cuenta la seguridad de ese punto de conexión para asegurarse de que ningún actor malintencionado pueda crear tokens aleatoriamente para el servicio. Azure Functions proporciona características de seguridad integradas que puede usar para proteger el punto de conexión mediante diferentes tipos de directivas de autenticación. Obtenga más información en [Seguridad de Azure Functions](../../azure-functions/security-concepts.md)

## <a name="clean-up-resources"></a>Limpieza de recursos

Si quiere limpiar y quitar una suscripción a Communication Services, puede eliminar el recurso o grupo de recursos. Al eliminar el grupo de recursos, también se elimina cualquier otro recurso que esté asociado a él. Puede obtener más información sobre [la limpieza de los recursos de Azure Communication Services](../quickstarts/create-communication-resource.md#clean-up-resources) y [la limpieza de los recursos de Azure Functions](../../azure-functions/create-first-function-vs-code-csharp.md#clean-up-resources).

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Más información sobre la seguridad de Azure Functions](../../azure-functions/security-concepts.md)

> [!div class="nextstepaction"]
> [Adición de la llamada de voz a una aplicación](../quickstarts/voice-video-calling/getting-started-with-calling.md)

Puede que también le interese:

- [Incorporación de chat a una aplicación](../quickstarts/chat/get-started.md)
- [Creación de tokens de acceso de usuario](../quickstarts/access-tokens.md)
- [Información sobre la arquitectura de cliente y servidor](../concepts/client-and-server-architecture.md)
- [Información sobre la autenticación](../concepts/authentication.md)