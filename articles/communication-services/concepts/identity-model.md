---
title: Modelo de identidad
titleSuffix: An Azure Communication Services concept
description: Obtenga información sobre las identidades y los tokens de acceso.
author: tomaschladek
manager: nmurav
services: azure-communication-services
ms.author: tchladek
ms.date: 06/30/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.openlocfilehash: 452fffa58d9845c1655c8b643dcde54f0b6a7d90
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/22/2021
ms.locfileid: "114459268"
---
# <a name="identity-model"></a>Modelo de identidad

Azure Communication Services es un servicio independiente de la identidad. Este diseño ofrece varias ventajas:

- Se reutilizan las identidades existentes de su sistema de administración de identidades.
- Proporciona flexibilidad para escenarios de integración.
- Sus identidades se mantienen privadas en Azure Communication Services.

En lugar de duplicar la información del sistema, conservará la relación de asignaciones que su caso empresarial requiere. Por ejemplo, puede asignar las identidades 1:1, 1:N, N:1 y N:M. Los identificadores externos, como los números de teléfono, los usuarios, los dispositivos, las aplicaciones y los GUID, no se pueden usar para una identidad de Azure Communication Services. Los tokens de acceso que se generan para una identidad de Azure Communication Services se usan para acceder a los primitivos, como el chat o la llamada.

## <a name="identity"></a>Identidad

Puede crear identidades mediante la biblioteca de identidades de Azure Communication Services. Una identidad sirve de identificador en las conversaciones. Se usa para crear tokens de acceso. La misma identidad podría participar en varias sesiones simultáneas en varios dispositivos. Una identidad puede tener varios tokens de acceso activos al mismo tiempo.

La eliminación de una identidad, un recurso o una suscripción invalida todos sus tokens de acceso. Esta acción también elimina todos los datos que se almacenan para la identidad. Una identidad eliminada no puede crear nuevos tokens de acceso ni acceder a los datos previamente almacenados (por ejemplo, mensajes de chat).

No se le cobra por el número de identidades que tenga. En su lugar, se le cobrará por el uso de primitivos. El número de identidades no debe restringir cómo se asignan las identidades de la aplicación a las identidades de Azure Communication Services.

La libertad de asignación implica la responsabilidad de la privacidad. Si un usuario quiere que se le elimine del sistema, debe eliminar todas las identidades asociadas a ese usuario.

Azure Communication Services no proporciona identidades especiales para los usuarios anónimos. No mantiene la asignación entre usuarios e identidades, y no puede determinar si la identidad es anónima. Puede diseñar el concepto de identidad para que se adapte a sus necesidades. Nuestra recomendación es crear una nueva identidad para cada usuario anónimo de cada aplicación.

Cualquier persona que tenga un token de acceso válido puede acceder al contenido de la identidad actual. Por ejemplo, los usuarios pueden acceder a los mensajes de chat que enviaron. El acceso está restringido solo a determinados ámbitos que forman parte del token de acceso. Para más información, vea la sección [Tokens de acceso](#access-tokens) de este artículo.

### <a name="identity-mapping"></a>Asignación de identidades

Azure Communication Services no replican la funcionalidad del sistema de administración de identidades de Azure. No proporciona una manera para que los clientes usen identidades específicas del cliente. Por ejemplo, los clientes no pueden usar un número de teléfono o una dirección de correo electrónico. En su lugar, Azure Communication Services proporciona identificadores únicos. Puede asignar identificadores únicos a las identidades de la aplicación. Azure Communication Services no almacena ningún tipo de información que pueda revelar la identidad real de los usuarios.

Para evitar la duplicación de información en su sistema, planifique cómo asignar los usuarios de su dominio de identidad a identidades de Azure Communication Services. Puede seguir cualquier tipo de patrón. Por ejemplo, puede usar 1:1, 1: N, N:1 o M:N. Decida si un usuario único se asigna a una identidad única o a varias identidades.

Cuando cree una nueva identidad, almacene su asignación al usuario o usuarios de la aplicación. Dado que las identidades requieren tokens de acceso para el uso de primitivos, el usuario o los usuarios de la aplicación deben conocer la identidad.

Si usa una base de datos relacional para almacenar información de usuario, puede ajustar el diseño en función del escenario de asignación. En escenarios que asignan 1:1 o N:1, puede querer agregar la columna `CommunicationServicesId` a la tabla para almacenar la identidad de Azure Communication Services. En escenarios que usan una relación 1:N o N:M, considere la posibilidad de crear una tabla independiente en la base de datos relacional.

## <a name="access-tokens"></a>Tokens de acceso

Un token de acceso es una instancia de JSON Web Token (JWT) que se puede usar para acceder a los primitivos de Azure Communication Services. Un token de acceso que se emite tiene protección de integridad. Es decir, no se pueden cambiar sus notificaciones después de que se emita. Por lo tanto, un cambio manual de propiedades, como la identidad, la expiración o los ámbitos hará que el token de acceso no sea válido. Si se usan primitivos con tokens no válidos, se denegará el acceso a los primitivos.

Las propiedades de un token de acceso son:
* Identidad
* Expiration.
* Ámbitos.

Un token de acceso siempre es válido durante 24 horas. Después de expirar, el token de acceso se invalida y no se puede usar para acceder a ningún primitivo.

La identidad necesita una manera de solicitar un token de acceso nuevo desde un servicio del lado servidor. El parámetro *scope* define un conjunto no vacío de primitivos que se pueden usar. Azure Communication Services admite los siguientes ámbitos para los tokens de acceso.

|Nombre|Descripción|
|---|---|
|Chat|  Ofrece la posibilidad de participar en un chat|
|VoIP|  Concede la capacidad de llamar a identidades y números de teléfono.|


Para revocar un token de acceso antes de su expiración, puede usar la biblioteca de identidades de Azure Communication Services. La revocación del token no es inmediata. Tarda hasta 15 minutos en propagarse. La eliminación de una identidad, recurso o suscripción revocará todos los tokens de acceso.

Si quiere eliminar la capacidad de un usuario para acceder a una funcionalidad específica, revoque todos los tokens de acceso. Después, emita un nuevo token de acceso que tenga un conjunto de ámbitos más limitado.

En Azure Communication Services, una rotación de las claves de acceso revoca todos los tokens de acceso activos creados con la clave de acceso anterior. Todas las identidades pierden el acceso a Azure Communication Services y deben emitir nuevos tokens de acceso.

Se recomienda emitir los tokens de acceso en el servicio del servidor y no en la aplicación del cliente. El motivo es que la emisión requiere una clave de acceso o una identidad administrada. Por motivos de seguridad, no se recomienda compartir las claves de acceso con la aplicación del cliente.

La aplicación cliente debe usar un punto de conexión de servicio de confianza que pueda autenticar a los clientes. El punto de conexión debe emitir tokens de acceso en su nombre. Para más información, vea [Arquitectura de cliente y servidor](./client-and-server-architecture.md).

Si almacena en caché los tokens de acceso en una memoria auxiliar, se recomienda usar cifrado. Un token de acceso es información confidencial. Se puede usar para actividades malintencionadas si no se protege. Alguien que tenga un token de acceso puede iniciar el SDK y acceder a la API. La API accesible solo está restringida en función de los ámbitos que tiene el token de acceso. Se recomienda emitir tokens de acceso que solo tengan los ámbitos necesarios.

## <a name="next-steps"></a>Pasos siguientes

* Para una introducción a la administración de tokens de acceso, consulte [Creación y administración de tokens de acceso](../quickstarts/access-tokens.md).
* Para una introducción a la autenticación, consulte [Autenticación en Azure Communication Services](./authentication.md).
* Para una introducción a la residencia y la privacidad de los datos, consulte [Disponibilidad de regiones y residencia de datos](./privacy.md).
* Para obtener información sobre cómo crear rápidamente identidades para pruebas, consulte el [inicio rápido para crear identidades rápidamente](../quickstarts/identity/quick-create-identity.md).
