---
title: Autenticación de los clientes que publican eventos en temas, dominios y espacios de nombres de los asociados personalizados de Event Grid.
description: En este artículo se describen diferentes maneras de autenticar a los clientes que publican eventos en temas, dominios y espacios de nombres de los asociados personalizados de Event Grid.
ms.topic: conceptual
ms.date: 08/10/2021
ms.openlocfilehash: 9e9718d6b39bd41cf91e610a01ce5f95bd0d5a18
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121780613"
---
# <a name="client-authentication-when-publishing-events-to-event-grid"></a>Autenticación de cliente al publicar eventos en Event Grid
La autenticación de los clientes que publican eventos Event Grid se admite a través de los métodos siguientes:

- Azure Active Directory (Azure AD)
- Clave de acceso o firma de acceso compartido (SAS)

## <a name="authenticate-using-azure-active-directory-preview"></a>Autenticación mediante Azure Active Directory (versión preliminar)
La integración de Azure AD para recursos de Event Grid proporciona control de acceso basado en rol de Azure (RBAC) para el control específico del acceso de los clientes a los recursos. Puede usar Azure RBAC para conceder permisos a una entidad de seguridad, que puede ser un usuario, un grupo o una entidad de servicio de aplicación. Azure AD autentica la entidad de seguridad para devolver un token de OAuth 2.0. Se puede usar el token para autorizar una solicitud de acceso a los recursos de Event Grid (temas, dominios o espacios de nombres de los asociados). Para información detallada, consulte [Autenticación y autorización con la plataforma de identidad de Microsoft](authenticate-with-active-directory.md).


> [!IMPORTANT]
> Autenticar y autorizar a usuarios o aplicaciones con identidades de Azure AD proporciona una seguridad y facilidad de uso superiores en lo que respecta a la autenticación basada en claves y en firmas de acceso compartido (SAS). Con Azure AD, no es preciso almacenar los secretos que se utilizan para la autenticación en el código y arriesgarse a posibles vulnerabilidades en la seguridad. Se recomienda encarecidamente usar Azure AD con las aplicaciones de publicación de eventos de Azure Event Grid.

> [!NOTE]
> La compatibilidad de la autenticación de Azure AD con Azure Event Grid se lanzó como versión preliminar. Azure Event Grid en Kubernetes aún no admite la autenticación de Azure AD. 

## <a name="authenticate-using-access-keys-and-shared-access-signatures"></a>Autenticación mediante claves de acceso o firmas de acceso compartido
Puede autenticar a los clientes que publican eventos en temas, dominios, espacios de nombres de los asociados de Azure Event Grid mediante **clave de acceso** o token de **Firma de acceso compartido (SAS)** . Para más información, consulte el artículo sobre el [uso de claves de acceso o el uso de firmas de acceso compartido (SAS)](authenticate-with-access-keys-shared-access-signatures.md). 
   

## <a name="next-steps"></a>Pasos siguientes
En este artículo se habla de la autenticación al **publicar** eventos en Event Grid (entrada de eventos). Para información sobre la autenticación al **entregar** eventos (salida de eventos), consulte [Autenticación de la entrega de eventos en los controladores de eventos](security-authentication.md). 

