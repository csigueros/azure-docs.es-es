---
title: Autorización del acceso a Azure SignalR Service mediante Azure Active Directory
description: En este artículo se proporciona información sobre cómo autorizar el acceso a recursos de Azure SignalR Service mediante Azure Active Directory.
author: terencefan
ms.author: tefa
ms.date: 09/06/2021
ms.service: signalr
ms.topic: conceptual
ms.openlocfilehash: 0e644f792e47243f3c6924cd8546efb5661a9193
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2021
ms.locfileid: "131478144"
---
# <a name="authorize-access-with-azure-active-directory-for-azure-signalr-service"></a>Autorización del acceso a Azure SignalR Service mediante Azure Active Directory

Azure SignalR Service admite el uso de Azure Active Directory (Azure AD) para autorizar solicitudes a recursos de SignalR Service. Con Azure AD, puede usar el control de acceso basado en rol (RBAC) para conceder permisos a una entidad de seguridad<sup>[<a href="#security-principal">1</a>]</sup>. Azure AD autentica la entidad de seguridad y devuelve un token de OAuth 2.0. El token se puede usar entonces para autorizar una solicitud en el recurso de SignalR.

La autorización de solicitudes en SignalR con Azure AD proporciona mayor seguridad y facilidad de uso con respecto a la autorización con clave de acceso. Microsoft recomienda usar la autorización de Azure AD con los recursos de SignalR cuando sea posible para garantizar el acceso con los privilegios mínimos necesarios.

<a id="security-principal"></a>
 *[1]Entidad de seguridad: un grupo de usuarios o recursos, una aplicación o una entidad de servicio, como identidades asignadas por el sistema e identidades asignadas por el usuario.*

## <a name="overview-of-azure-ad-for-signalr"></a>Introducción a Azure AD para SignalR

Cuando una entidad de seguridad intenta acceder a un recurso de SignalR, se debe autorizar la solicitud. Con Azure AD, se necesitan dos pasos para acceder a un recurso. 

1. En primer lugar, la entidad de seguridad se autentica en Azure y se devuelve un token de OAuth 2.0. 
2. Después, el token se pasa como parte de la solicitud al recurso de SignalR y el servicio lo usa para autorizar el acceso al recurso especificado.

### <a name="client-side-authentication-while-using-azure-ad"></a>Autenticación en el lado cliente mientras se usa Azure AD

Cuando se usa la clave de acceso, esta se comparte entre el servidor de aplicaciones (o aplicación de funciones) y el recurso de SignalR, lo que significa que el servicio SignalR podría autenticar la solicitud de conexión de cliente con la clave compartida. Sin embargo, no hay ninguna clave compartida cuando se usa Azure AD para la autorización. 

Para solucionar este problema, se ha introducido una **clave de acceso temporal** que se puede usar para firmar los tokens de las conexiones de cliente. El flujo de trabajo contiene cuatro pasos.

1. En primer lugar, la entidad de seguridad necesita un token de OAuth 2.0 de Azure para autenticarse.
2. En segundo lugar, la entidad de seguridad llama a la API de autenticación de SignalR para obtener una **clave de acceso temporal**.
3. A continuación, la entidad de seguridad firma un token de cliente con la **clave de acceso temporal** para las conexiones de cliente durante la negociación.
4. Por último, el cliente usa este token para conectarse a los recursos de Azure SignalR.

La **clave de acceso temporal** expirará en 90 minutos, lo que significa que se recomienda obtener una nueva y rotar la antigua una vez por hora. 

El flujo de trabajo se basa en nuestro [SDK de servidor](https://github.com/Azure/azure-signalr).

## <a name="assign-azure-roles-for-access-rights"></a>Asignación de roles de Azure para derechos de acceso

Azure Active Directory (Azure AD) autoriza derechos de acceso a recursos protegidos mediante el [control de acceso basado en rol de Azure](../role-based-access-control/overview.md). Azure SignalR define un conjunto de roles integrados de Azure que abarcan conjuntos comunes de permisos utilizados para acceder a recursos de SignalR. También puede definir roles personalizados para el acceso a recursos de SignalR.

### <a name="resource-scope"></a>Ámbito de recursos

Antes de asignar un rol de Azure RBAC a una entidad de seguridad, puede tener que determinar el ámbito de acceso que debería tener la entidad de seguridad. Se recomienda conceder solo el ámbito más limitado posible. Los roles de Azure RBAC definidos en un ámbito más amplio los heredan los recursos que están debajo de ellos.

Puede limitar el acceso a los recursos de Azure SignalR en los niveles siguientes, empezando por el ámbito más reducido:

- **Recurso individual.** 

  En este ámbito, la asignación de roles solo se aplica al recurso de destino.

- **Grupo de recursos.** 

  En este ámbito, la asignación de roles se aplica a todos los recursos del grupo de recursos.

- **Una suscripción.**

  En este ámbito, la asignación de roles se aplica a todos los recursos de todos los grupos de recursos de la suscripción.

- **Un grupo de administración.** 
  
  En este ámbito, la asignación de roles se aplica a todos los recursos de todos los grupos de recursos de todas las suscripciones del grupo de administración.

## <a name="azure-built-in-roles-for-signalr-resources"></a>Roles integrados de Azure para recursos de SignalR

- [Servidor de aplicaciones de SignalR](../role-based-access-control/built-in-roles.md#signalr-app-server-preview)

    Acceda a la API de creación de conexiones de Websocket y a las API de autenticación.
    
    Este es el rol usado más comúnmente para un servidor de aplicaciones.

- [Propietario de SignalR Service](../role-based-access-control/built-in-roles.md#signalr-service-owner)

    Acceso total a todas las API del plano de datos, incluidas todas las API REST, la API de creación de conexiones de WebSocket y las API de autenticación.

    Este rol se debe usar en el **modo sin servidor** para admitir la autorización con Azure AD, ya que se requieren permisos de API REST y permisos de API de autenticación.

- [Propietario de la API REST de SignalR](../role-based-access-control/built-in-roles.md#signalr-rest-api-owner)

    Acceso total a las API REST del plano de datos.

    Normalmente se usa cuando se quiere escribir una herramienta de administración que administre las conexiones y los grupos, pero **NO** realizar conexiones ni llamadas a las API de autenticación.

- [Lector de la API REST de SignalR](../role-based-access-control/built-in-roles.md#signalr-rest-api-reader)

    Acceso de solo lectura a las API REST del plano de datos.

    Normalmente se usa cuando se quiere escribir una herramienta de supervisión que **SOLO** llama a las API REST **READONLY** del plano de datos de SignalR.

## <a name="next-steps"></a>Pasos siguientes

Para descubrir cómo crear una aplicación de Azure y usar la autenticación de Azure AD, consulte:
- [Autorización de la solicitud a recursos de SignalR con Azure AD desde aplicaciones de Azure](signalr-howto-authorize-application.md)

Para información sobre cómo configurar una identidad administrada y usar la autenticación de Azure AD, consulte:
- [Autorización de solicitudes a recursos de SignalR con Azure AD desde identidades administradas](signalr-howto-authorize-managed-identity.md)

Para más información sobre los roles y las asignaciones de roles, consulte: 
- [¿Qué es el control de acceso basado en rol de Azure (RBAC)?](../role-based-access-control/overview.md)

Para aprender a crear roles personalizados, consulte: 
- [Pasos para crear un rol personalizado](../role-based-access-control/custom-roles.md#steps-to-create-a-custom-role)