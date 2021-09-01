---
title: Autenticación y autorización en Azure Relay | Microsoft Docs
description: En este artículo se proporciona una introducción sobre la autenticación de firma de acceso compartido (SAS) con el servicio Azure Relay.
ms.topic: article
ms.date: 07/19/2021
ms.openlocfilehash: dbabee7f49e4f905c34a91dcb6095e7eab3faa0f
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121741226"
---
# <a name="azure-relay-authentication-and-authorization"></a>Autenticación y autorización en Azure Relay
Hay dos formas de autenticar y autorizar el acceso a los recursos de Azure Relay: Azure Activity Directory (Azure AD) y firmas de acceso compartido (SAS). En este artículo se proporcionan detalles sobre el uso de estos dos tipos de mecanismos de seguridad.

## <a name="azure-active-directory-preview"></a>Azure Active Directory (versión preliminar)
La integración de Azure AD para recursos de Azure Relay proporciona control de acceso basado en rol de Azure (RBAC de Azure) para el control específico del acceso de los clientes a los recursos. Puede usar Azure RBAC para conceder permisos a una entidad de seguridad, que puede ser un usuario, un grupo o una entidad de servicio de aplicación. Azure AD autentica la entidad de seguridad para devolver un token de OAuth 2.0. El token se puede usar a fin de autorizar una solicitud para acceder a un recurso de Azure Relay.

Consulte los siguientes artículos para más información sobre la autenticación con Azure AD:
- [Autenticación con identidades administradas](authenticate-managed-identity.md)
- [Autenticación desde una aplicación de Azure Active Directory](authenticate-application.md)

> [!IMPORTANT]
> La autorización de usuarios o aplicaciones mediante un token de OAuth 2.0 devuelto por Azure AD proporciona una seguridad superior y facilidad de uso sobre las firmas de acceso compartido (SAS). Con Azure AD, no es preciso almacenar los tokens en el código y arriesgarse a posibles vulnerabilidades en la seguridad. Se recomienda usar Azure AD con las aplicaciones de Azure Relay siempre que sea posible.

### <a name="built-in-roles"></a>Roles integrados
En el caso de Azure Relay, la administración de los espacios de nombres y de todos los recursos relacionados mediante Azure Portal y la API de administración de recursos de Azure, ya se ha protegido mediante el modelo de Azure RBAC. Azure proporciona los siguientes roles integrados de Azure para autorizar el acceso a un espacio de nombres de Relay:

| Rol | Descripción | 
| ---- | ----------- | 
| [Propietario de Azure Relay](../role-based-access-control/built-in-roles.md#azure-relay-owner) | Este rol se usa para conceder acceso **completo** a los recursos de Azure Relay. |
| [Cliente de escucha de Azure Relay](../role-based-access-control/built-in-roles.md#azure-relay-listener) | Este rol se usa para conceder acceso de **escucha y lectura de entidades** a los recursos de Azure Relay. |
| [Emisor de Azure Relay](../role-based-access-control/built-in-roles.md#azure-relay-sender) | Este rol se usa para conceder acceso de **envío y lectura de entidades** a los recursos de Azure Relay. | 


## <a name="shared-access-signature"></a>Firma de acceso compartido
Las aplicaciones pueden autenticarse en Azure Relay mediante la autenticación con Firma de acceso compartido (SAS). La autenticación de SAS permite a las aplicaciones autenticarse en el servicio Azure Relay mediante una clave de acceso configurada en el espacio de nombres de Relay. A continuación, puede usar esta clave para generar un token de Firma de acceso compartido que los clientes pueden usar para autenticarse en el servicio de retransmisión.

La [autenticación de SAS](../service-bus-messaging/service-bus-sas.md) permite conceder a los usuarios acceso a los recursos de Azure Relay con derechos específicos. La autenticación con SAS implica la configuración de una clave criptográfica con derechos asociados en un recurso. Los clientes pueden obtener acceso a ese recurso presentando un token SAS, que consta del URI del recurso al que se tiene acceso y una fecha de expiración firmada con la clave configurada.

Puede configurar claves para SAS en un espacio de nombres de Relay. A diferencia de la mensajería de Service Bus, [Conexiones híbridas de Relay](relay-hybrid-connections-protocol.md) admite remitentes no autorizados o anónimos. Puede habilitar el acceso anónimo para la entidad cuando la cree, tal y como se muestra en la siguiente captura de pantalla del portal:

![Un cuadro de diálogo titulado "Crear conexión híbrida" tiene un cuadro de texto "Nombre" y una casilla denominada "Requiere autenticación de cliente", que está activada.][0]

Para usar SAS, puede configurar un objeto [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) en un espacio de nombres de Relay que conste de las siguientes propiedades:

* *KeyName* que identifica la regla.
* *PrimaryKey* es una clave criptográfica usada para firmar o validar tokens SAS.
* *SecondaryKey* es una clave criptográfica usada para firmar o validar tokens SAS.
* *Rights* representa la recopilación de derechos de escucha, envío o administración concedidos.

Las reglas de autorización configuradas en el nivel de espacio de nombres pueden conceder acceso a todas las conexiones de retransmisión de un espacio de nombres a los clientes con tokens firmados con la clave correspondiente. Se pueden configurar un máximo de 12 reglas de autorización en un espacio de nombres de Relay. De forma predeterminada, se configura un objeto [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) con todos los derechos para cada espacio de nombres la primera vez que se aprovisiona.

Para obtener acceso a una entidad, el cliente requiere un token SAS generado con un objeto [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule)determinado. El token SAS se genera mediante el HMAC-SHA256 de una cadena de recurso que consta del URI del recurso al que se notifica el acceso y una fecha de expiración con una clave criptográfica asociada a la regla de autorización.

La compatibilidad de la autenticación con SAS para Azure Relay se incluye en el SDK .NET de Azure 2.0 y versiones posteriores. SAS incluye compatibilidad con un objeto [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule). Todas las API que aceptan una cadena de conexión como parámetro incluyen compatibilidad con cadenas de conexión SAS.

## <a name="next-steps"></a>Pasos siguientes

- Para obtener más información sobre SAS, siga consultando [Autenticación en Service Bus con Firmas de acceso compartido](../service-bus-messaging/service-bus-sas.md).
- Consulte la guía [Protocolo de conexiones híbridas de Azure Relay](relay-hybrid-connections-protocol.md) para obtener información detallada sobre la funcionalidad Conexiones híbridas.
- Para más información sobre la autenticación de mensajería de Service Bus, consulte [Autenticación y autorización de Service Bus](../service-bus-messaging/service-bus-authentication-and-authorization.md). 

[0]: ./media/relay-authentication-and-authorization/hcanon.png