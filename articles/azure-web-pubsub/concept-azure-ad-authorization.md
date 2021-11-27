---
title: Autorización del acceso a Azure Web PubSub con Azure Active Directory
description: En este artículo se proporciona información sobre cómo autorizar el acceso a recursos del servicio Azure Web PubSub mediante Azure Active Directory.
author: terencefan
ms.author: tefa
ms.date: 11/08/2021
ms.service: azure-web-pubsub
ms.topic: conceptual
ms.openlocfilehash: 6682328d948d66085799ffe8f6df93adf732c04d
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/17/2021
ms.locfileid: "132710432"
---
# <a name="authorize-access-to-web-pubsub-resources-using-azure-active-directory"></a>Autorización del acceso a recursos de Web PubSub mediante Azure Active Directory
El servicio Azure Web PubSub admite Azure Active Directory (Azure AD) para autorizar las solicitudes a recursos de Web PubSub. Con Azure AD, puede usar el control de acceso basado en rol (RBAC) para conceder permisos a una entidad de seguridad<sup>[<a href="#security-principal">1</a>]</sup>. Azure AD autentica la entidad de seguridad y devuelve un token de OAuth 2.0. El token se puede usar entonces para autorizar una solicitud en el recurso de Web PubSub.

La autorización de solicitudes en Web PubSub con Azure AD proporciona mayor seguridad y facilidad de uso con respecto a la autorización con clave de acceso. Microsoft recomienda usar la autorización de Azure AD con los recursos de Web PubSub cuando sea posible para garantizar el acceso con los privilegios mínimos necesarios.

<a id="security-principal"></a>
 *[1]Entidad de seguridad: un grupo de usuarios o recursos, una aplicación o una entidad de servicio, como identidades asignadas por el sistema e identidades asignadas por el usuario.*

## <a name="overview-of-azure-ad-for-web-pubsub"></a>Introducción a Azure AD para Web PubSub

Cuando una entidad de seguridad intenta acceder a un recurso de Web PubSub, se debe autorizar la solicitud. Con Azure AD, se necesitan dos pasos para acceder a un recurso. 

1. En primer lugar, la entidad de seguridad se autentica en Azure y se devuelve un token de OAuth 2.0. 
2. Después, el token se pasa como parte de una solicitud al recurso de Web PubSub y el servicio lo usa para autorizar el acceso al recurso especificado.

### <a name="client-side-authentication-while-using-azure-ad"></a>Autenticación en el lado cliente mientras se usa Azure AD

Cuando se usa la clave de acceso, esta se comparte entre el servidor de negociación (o aplicación de funciones) y el recurso de Web PubSub, lo que significa que el servicio Web PubSub podría autenticar la solicitud de conexión de cliente con la clave compartida. Sin embargo, no hay ninguna clave de acceso cuando se usa Azure AD para la autorización. 

Para solucionar este problema, se proporciona una API REST para generar el token de cliente que se puede usar para conectarse al servicio Azure Web PubSub.

1. En primer lugar, el servidor de negociación requiere un **token Aad** de Azure para autenticarse.
1. En segundo lugar, el servidor de negociación llama a la API de autenticación de Web PubSub con el **token Aad** para obtener un **token de cliente** y lo devuelve al cliente.
1. Por último, el cliente usa el **token de cliente** para conectarse al servicio Azure Web PubSub.

Se proporcionan funciones auxiliares (por ejemplo, "GenerateClientAccessUri") para los lenguajes de programación compatibles.

## <a name="assign-azure-roles-for-access-rights"></a>Asignación de roles de Azure para derechos de acceso

Azure Active Directory (Azure AD) autoriza derechos de acceso a recursos protegidos mediante el [control de acceso basado en rol de Azure](../role-based-access-control/overview.md). Azure Web PubSub define un conjunto de roles integrados de Azure que abarcan conjuntos comunes de permisos utilizados para acceder a recursos de Web PubSub. También se pueden definir roles personalizados para el acceso a recursos de Web PubSub.

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

## <a name="azure-built-in-roles-for-web-pubsub-resources"></a>Roles integrados de Azure para recursos de Web PubSub.

- `Web PubSub Service Owner`

    Acceso total a los permisos del plano de datos, incluidas las API REST de lectura y escritura y las API de autenticación.

    Este es el rol usado más comúnmente para crear un servidor ascendente.

- `Web PubSub Service Reader`

    Úselo para conceder permisos de API REST de solo lectura a los recursos de Web PubSub.

    Normalmente se usa cuando se quiere escribir una herramienta de supervisión que llama **SOLO** a las API REST **READONLY** del plano de datos de Web PubSub.

## <a name="next-steps"></a>Pasos siguientes

Para descubrir cómo crear una aplicación de Azure y usar la autenticación de AAD, consulte:
- [Autorización de solicitudes a recursos de Web PubSub con Azure AD desde aplicaciones de Azure](howto-authorize-from-application.md)

Para información sobre cómo configurar una identidad administrada y usar la autenticación de AAD, consulte:
- [Autorización de la solicitud a recursos de Web PubSub con Azure AD desde identidades administradas](howto-authorize-from-managed-identity.md)

Para más información sobre los roles y las asignaciones de roles, consulte: 
- [¿Qué es el control de acceso basado en rol de Azure (RBAC)?](../role-based-access-control/overview.md)

Para aprender a crear roles personalizados, consulte: 
- [Pasos para crear un rol personalizado](../role-based-access-control/custom-roles.md#steps-to-create-a-custom-role)