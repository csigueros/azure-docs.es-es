---
title: Arquitectura de cliente y servidor
titleSuffix: An Azure Communication Services concept document
description: Obtenga información sobre la arquitectura de Communication Services.
author: probableprime
manager: mikben
services: azure-communication-services
ms.author: rifox
ms.date: 06/30/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.openlocfilehash: 1346c9f6505c03ccebb2d2e2dc33e899050bfe20
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128672027"
---
# <a name="client-and-server-architecture"></a>Arquitectura de cliente y servidor

En esta página se muestran los componentes arquitectónicos y flujos de datos típicos en varios escenarios de Azure Communication Services. Los componentes pertinentes incluyen:

1. **Aplicación cliente.** Los usuarios finales utilizan este sitio web o aplicación nativa para comunicarse. Azure Communication Services proporciona [bibliotecas cliente del SDK](sdk-options.md) para varios exploradores y plataformas de aplicaciones. Además de los SDK principales, hay [una biblioteca de interfaces de usuario](https://aka.ms/acsstorybook) disponible para acelerar el desarrollo de aplicaciones de explorador.
1. **Servicio de administración de identidades.**  Esta funcionalidad de servicio no solo se crea para asignar usuarios y otros conceptos de la lógica de negocios a Azure Communication Services, sino también para crear tokens para esos usuarios cuando sea necesario.
1. **Servicio de administración de llamadas.**  Esta funcionalidad de servicio se crea para administrar y supervisar llamadas de voz y vídeo.  Este servicio puede crear llamadas, invitar a usuarios, llamar a números de teléfono, reproducir audio, escuchar tonos DMTF y aprovechar muchas otras características de las llamadas mediante el SDK de Automatización de llamadas y las API REST.


## <a name="user-access-management"></a>Administración de acceso de usuario

Los clientes de Azure Communication Services deben presentar `user access tokens` para acceder a los recursos de Communication Services de forma segura. Dada la naturaleza confidencial del token y la cadena de conexión o identidad administrada necesaria para generarlo, `User access tokens` debe generarse y administrarse mediante un servicio de confianza. Si no se administran correctamente los tokens de acceso, pueden producirse cargos adicionales debido a un uso incorrecto de los recursos.

:::image type="content" source="../media/scenarios/architecture_v2_identity.svg" alt-text="Diagrama que muestra la arquitectura de token de acceso de usuario.":::

### <a name="dataflows"></a>Flujos de datos
1. El usuario inicia la aplicación cliente. Usted controla el diseño de esta aplicación y del esquema de autenticación del usuario.
2. La aplicación cliente se pone en contacto con el servicio de administración de identidades. Este servicio mantiene una asignación entre los usuarios y otros objetos direccionables (por ejemplo, servicios o bots) a identidades de Azure Communication Services.
3. El servicio de administración de identidades crea un token de acceso de usuario para la identidad aplicable. Si nunca se ha asignado una identidad de Azure Communication Services, se crea una identidad.  

### <a name="resources"></a>Recursos
- **Concepto:** [Identidad del usuario](identity-model.md)
- **Inicio rápido:** [Creación y administración de tokens de acceso](../quickstarts/access-tokens.md)
- **Tutorial:** [Creación de un servicio de acceso de usuarios de confianza mediante Azure Functions](../tutorials/trusted-service-tutorial.md)

> [!IMPORTANT]
> Por motivos de simplicidad, no se muestra la administración del acceso de los usuarios ni la distribución de tokens en los flujos de arquitectura posteriores.


## <a name="calling-a-user-without-push-notifications"></a>Llamada a un usuario sin notificaciones push
Los escenarios de llamadas de voz y vídeo más sencillos implican que un usuario llame a otro en primer plano sin notificaciones push.

:::image type="content" source="../media/scenarios/architecture_v2_calling_without_notifications.svg" alt-text="Diagrama que muestra que la arquitectura de Communication Services llama sin notificaciones push.":::

### <a name="dataflows"></a>Flujos de datos

1. El usuario que acepta la llamada inicializa el cliente de llamada, lo que le permite recibir llamadas telefónicas entrantes.
2. El usuario que inicia la llamada necesita la identidad de Azure Communication Services de la persona a la que quiere llamar. Una experiencia típica puede ser que se tiene una *lista de amigos* mantenida por el servicio de administración de identidades que intercala los amigos del usuario y las identidades de Azure Communication Services asociadas.
3. El usuario que inicia la llamada inicializa su cliente de llamada y llama al usuario remoto.
4. El usuario que acepta la llamada recibe la notificación de la llamada entrante a través del SDK de llamada.
5. En las llamadas, los usuarios se comunican entre sí mediante voz y vídeo.

### <a name="resources"></a>Recursos
- **Concepto:** [Introducción a las llamadas](voice-video-calling/calling-sdk-features.md)
- **Inicio rápido:** [Adición de llamadas de voz a una aplicación](../quickstarts/voice-video-calling/getting-started-with-calling.md)
- **Inicio rápido:** [Adición de llamadas de vídeo a una aplicación](../quickstarts/voice-video-calling/get-started-with-video-calling.md)
- **Ejemplo principal:** [Llamadas grupales en Web, iOS y Android](../samples/calling-hero-sample.md)


## <a name="joining-a-user-created-group-call"></a>Unión a una llamada grupal creada por un usuario
Los usuarios se pueden unir a las llamadas sin una invitación explícita. Por ejemplo, puede tener un *espacio social* con una llamada asociada y los usuarios se pueden unir a esa llamada cuando deseen. En este primer flujo de datos, se muestra una llamada creada inicialmente por un cliente.

:::image type="content" source="../media/scenarios/architecture_v2_calling_join_client_driven.svg" alt-text="Diagrama que muestra que la arquitectura de Communication Services llama a las señales de fuera de banda.":::

### <a name="dataflows"></a>Flujos de datos
1. El usuario que inicia la llamada inicializa su cliente de llamada y realiza una llamada grupal.
2. El usuario que inicia la llamada comparte el identificador de la llamada grupal con un servicio de administración de llamadas.
3. El servicio de administración de llamadas comparte el identificador de llamada con otros usuarios. Por ejemplo, si la aplicación se orienta en torno a los eventos programados, el identificador de llamada grupal podría ser un atributo del modelo de datos del evento programado.
4. Otros usuarios se unen a la llamada mediante el identificador de la llamada grupal.
5. En las llamadas, los usuarios se comunican entre sí mediante voz y vídeo.


## <a name="joining-a-scheduled-teams-call"></a>Unión a una llamada de Teams programada
Las aplicaciones de Azure Communication Service pueden unirse a las llamadas con Teams. Esto es ideal para muchos escenarios de empresa a consumidor, en los que el consumidor aprovecha una aplicación personalizada y una identidad personalizada, mientras que la parte de la empresa usa Teams.

:::image type="content" source="../media/scenarios/architecture_v2_calling_join_teams_driven.svg" alt-text="Diagrama que muestra la arquitectura de Communication Services para unirse a una reunión en Teams.":::


### <a name="dataflows"></a>Flujos de datos
1. El servicio de administración de llamadas crea una llamada grupal con [Graph API](/graph/api/resources/onlinemeeting?view=graph-rest-1.0). Otro patrón implica que los usuarios finales creen la llamada grupal mediante [Bookings](https://www.microsoft.com/microsoft-365/business/scheduling-and-booking-app), Outlook, Teams, o cualquier otra aplicación de programación del ecosistema Microsoft 365.
2. El servicio de administración de llamadas comparte los detalles de las llamadas de Teams con los clientes de Azure Communication Services.
3. Normalmente, los usuarios de Teams deben unirse a la llamada y permitir que los usuarios externos se unan a través de la sala. Sin embargo, esta experiencia es sensible a la configuración del inquilino de Teams y a la configuración específica de la reunión.
4. Los usuarios de Azure Communication Services inicializan su cliente de llamada y se unen a la reunión de Teams mediante los detalles recibidos en el paso 2.
5. En las llamadas, los usuarios se comunican entre sí mediante voz y vídeo.

### <a name="resources"></a>Recursos
- **Concepto:** [Interoperabilidad de Teams](teams-interop.md)
- **Inicio rápido**: [Unión a una reunión de Teams](../quickstarts/voice-video-calling/get-started-teams-interop.md)
