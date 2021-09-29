---
title: Autorización del acceso a Azure Event Hubs
description: En este artículo se proporciona información sobre las distintas opciones para autorizar el acceso a recursos de Azure Event Hubs.
ms.topic: conceptual
ms.date: 09/20/2021
ms.author: spelluru
ms.openlocfilehash: df4498de020b2e389622fb5f495e5f42f69f186d
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128561032"
---
# <a name="authorize-access-to-azure-event-hubs"></a>Autorización del acceso a Azure Event Hubs
Cada vez que se publican o consumen eventos o datos de un centro de eventos, el cliente intenta obtener acceso a recursos de Event Hubs. Todas las solicitudes a un recurso seguro deben estar autorizadas para que el servicio pueda garantizar que el cliente tenga los permisos necesarios para publicar o consumir los datos. 

Azure Event Hubs ofrece las siguientes opciones para autorizar el acceso a los recursos seguros:

- Azure Active Directory
- Firma de acceso compartido

> [!NOTE]
> Este artículo se aplica a los escenarios de Event Hubs y [Apache Kafka](event-hubs-for-kafka-ecosystem-overview.md). 

## <a name="azure-active-directory"></a>Azure Active Directory
La integración de Azure Active Directory (Azure AD) para recursos de Event Hubs proporciona control de acceso basado en roles de Azure AD (Azure RBAC) para un control específico de acceso de los clientes a los recursos. Puede usar Azure RBAC para conceder permisos a una entidad de seguridad, que puede ser un usuario, un grupo o una entidad de servicio de aplicación. Azure AD autentica la entidad de seguridad para devolver un token de OAuth 2.0. El token se puede usar a fin de autorizar una solicitud para acceder a un recurso de Event Hubs.

Consulte los siguientes artículos para más información sobre la autenticación con Azure AD:

- [Autenticación de solicitudes para Azure Event Hubs mediante Azure Active Directory](authenticate-application.md)
- [Autorización del acceso a recursos de Event Hubs mediante Azure Active Directory](authorize-access-azure-active-directory.md)

## <a name="shared-access-signatures"></a>Firmas de acceso compartido 
Las firmas de acceso compartido (SAS) para recursos de Event Hubs proporcionan un acceso delegado limitado a recursos de Event Hubs. Agregar restricciones al período de validez de la firma o a los permisos que concede proporciona flexibilidad para administrar los recursos. Para más información, consulte [Autenticación de firmas de acceso compartido (SAS)](authenticate-shared-access-signature.md). 

La autorización de usuarios o aplicaciones mediante un token de OAuth 2.0 devuelto por Azure AD proporciona una seguridad superior y facilidad de uso sobre las firmas de acceso compartido (SAS). Con Azure AD, no hay necesidad de almacenar tokens de acceso con su código y arriesgarse a posibles puntos vulnerables de seguridad. Aunque puede seguir usando firmas de acceso compartido (SAS) para conceder acceso específico a los recursos de Event Hubs, Azure AD ofrece funcionalidades similares sin necesidad de administrar tokens de SAS ni preocuparse sobre cómo revocar una SAS en peligro. 

De forma predeterminada, todos los recursos de Event Hubs están protegidos y solo están disponibles para el propietario de la cuenta. No obstante, puede usar cualquiera de las estrategias de autorización descritas anteriormente para conceder a los clientes acceso a recursos de Event Hubs. Microsoft recomienda usar Azure AD siempre que sea posible para una mayor seguridad y facilidad de uso.

Para más información sobre la autorización mediante SAS, consulte [Autorización del acceso a recursos de Event Hubs mediante firmas de acceso compartido](authorize-access-shared-access-signature.md).

## <a name="next-steps"></a>Pasos siguientes
- Revise los [ejemplos de Azure RBAC](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac) publicados en nuestro repositorio de GitHub. 
- Vea los artículos siguientes:
    - [Autenticación de solicitudes a Azure Event Hubs desde una aplicación mediante Azure Active Directory](authenticate-application.md)
    - [Autenticación de una identidad administrada con Azure Active Directory para acceder a recursos de Event Hubs](authenticate-managed-identity.md)
    - [Autenticación de solicitudes para Azure Event Hubs mediante firmas de acceso compartido](authenticate-shared-access-signature.md)
    - [Autorización del acceso a recursos de Event Hubs mediante Azure Active Directory](authorize-access-azure-active-directory.md)
    - [Autorización del acceso a recursos de Event Hubs mediante firmas de acceso compartido](authorize-access-shared-access-signature.md)

