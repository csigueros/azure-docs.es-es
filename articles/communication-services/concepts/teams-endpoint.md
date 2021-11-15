---
title: Creación de un punto de conexión de Teams personalizado
titleSuffix: An Azure Communication Services concept document
description: En este artículo se describe cómo crear un punto de conexión de Teams personalizado.
author: tomaschladek
manager: nmurav
services: azure-communication-services
ms.author: tchladek
ms.date: 06/30/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.subservice: teams-interop
ms.openlocfilehash: 2038efe285f36466b0b562d4d87fbd172a44dd42
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2021
ms.locfileid: "131469601"
---
# <a name="build-a-custom-teams-endpoint"></a>Creación de un punto de conexión de Teams personalizado

> [!IMPORTANT]
> Para habilitar o deshabilitar la experiencia del punto de conexión de Teams personalizado, [rellene y envíe este formulario](https://forms.office.com/r/B8p5KqCH19).

Se puede usar Azure Communication Services para crear puntos de conexión de Teams personalizados para la comunicación con el cliente de Microsoft Teams u otros puntos de conexión de Teams personalizados. Con el punto de conexión de Teams personalizado, puede personalizar la experiencia de voz, vídeo, chat y uso compartido de pantalla para los usuarios de Teams.

Puede usar el SDK identidad de Azure Communication Services para intercambiar tokens de acceso de Azure Active Directory de usuarios de Teams con tokens de acceso de identidades de Communication Services. En los diagramas de las secciones siguientes se muestran casos de uso multiinquilino, donde la empresa ficticia Fabrikam es el cliente de la empresa ficticia Contoso.

## <a name="calling"></a>Llamar 

Las funcionalidades de voz, vídeo y uso compartido de pantalla se proporcionan mediante Calling SDK de Azure Communication Services. En el diagrama siguiente, se muestra información general del proceso que se va a seguir al integrar las experiencias de llamadas con los puntos de conexión de Teams personalizados.

![Diagrama del proceso de habilitación de la característica de llamada para una experiencia de punto de conexión de Teams personalizado.](./media/teams-identities/teams-identity-calling-overview.svg)

## <a name="chat"></a>Chat

También tiene la opción de usar puntos de conexión de Teams personalizados para integrar funcionalidades de chat mediante Graph API. Para más información sobre Graph API, consulte la documentación sobre el [chat como tipo de recurso](/graph/api/channel-post-messages). 

![Diagrama del proceso de habilitación de la característica de chat para una experiencia de punto de conexión de Teams personalizado.](./media/teams-identities/teams-identity-chat-overview.png)

## <a name="azure-communication-services-permissions"></a>Permisos de Azure Communication Services

### <a name="delegated-permissions"></a>Permisos delegados

|   Permiso    |  Cadena de visualización   |  Descripción | Se necesita el consentimiento del administrador | Se admite la cuenta de Microsoft |
|:--- |:--- |:--- |:--- |:--- |
| _`https://auth.msft.communication.azure.com/VoIP`_ | Administrar llamadas en Teams | Inicie una llamada de Teams, únase a ella, transfiérala o abandónela y actualice las propiedades de la llamada. | No | No |

### <a name="application-permissions"></a>Permisos de aplicación

Ninguna.

### <a name="roles-for-granting-consent-on-behalf-of-a-company"></a>Roles para conceder consentimiento en nombre de una empresa

- Administrador global
- Administrador de aplicaciones (solo en versión preliminar privada)
- Administrador de aplicaciones en la nube (solo en versión preliminar privada)

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Generación de un token de acceso de Teams](../quickstarts/manage-teams-identity.md)

Más información sobre la [interoperabilidad de Teams](./teams-interop.md).
