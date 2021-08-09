---
title: Autenticación con Microsoft Azure Maps
titleSuffix: Azure Maps
description: 'Conozca dos maneras de autenticar solicitudes en Azure Maps: autenticación de clave compartida y autenticación de Azure Active Directory (Azure AD).'
author: anastasia-ms
ms.author: v-stharr
ms.date: 05/25/2021
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: e3f594f910a6645a3a1a0cc8e71afcb65cac8735
ms.sourcegitcommit: 6323442dbe8effb3cbfc76ffdd6db417eab0cef7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/28/2021
ms.locfileid: "110616366"
---
# <a name="authentication-with-azure-maps"></a>Autenticación con Azure Maps

Azure Maps admite dos formas de autenticar las solicitudes: Autenticación de clave compartida y autenticación de [Azure Active Directory (Azure AD)](../active-directory/fundamentals/active-directory-whatis.md). En este artículo se explican ambos métodos de autenticación como guía para implementar los servicios de Azure Maps.

> [!NOTE]
> Para mejorar la comunicación segura con Azure Maps, ahora admitimos Seguridad de la capa de transporte (TLS) 1.2 y retiramos la compatibilidad con TLS 1.0 y 1.1. Si actualmente usa TLS 1.x, evalúe su preparación de TLS 1.2 y desarrolle un plan de migración con las pruebas descritas en [Solución del problema de TLS 1.0](/security/solving-tls1-problem).

## <a name="shared-key-authentication"></a>Autenticación de clave compartida

 Las claves principal y secundaria se generan después de crearse la cuenta de Azure Maps. Se recomienda usar la clave principal como clave de suscripción al llamar a Azure Maps mediante la autenticación de clave compartida. La autenticación de clave compartida pasa las claves generadas por una cuenta de Azure Maps a un servicio de Azure Maps. En solicitud a los servicios de Azure Maps requiere, agregue la *clave de suscripción* como parámetro a la dirección URL. La clave secundaria se puede usar, por ejemplo, para cambios de clave graduales.  

Para obtener información sobre cómo ver sus claves en Azure Portal, consulte [Administración de la autenticación](./how-to-manage-authentication.md#view-authentication-details).

> [!NOTE]
> Las claves principal y secundaria deben tratarse como datos confidenciales. La clave compartida se usa para autenticar todas las API REST de Azure Maps.  Los usuarios que usan una clave compartida deben abstraer la clave de API, bien a través de variables de entorno o del almacenamiento de secretos seguro, donde se puede administrar de forma centralizada.

## <a name="azure-ad-authentication"></a>Autenticación de Azure AD

Se proporcionan suscripciones de Azure con un inquilino de Azure AD para habilitar el control de acceso específico. Azure Maps ofrece autenticación para los servicios de Azure Maps mediante Azure AD. Azure AD proporciona autenticación basada en la identidad para los usuarios y las aplicaciones registradas en el inquilino de Azure AD.

Azure Maps acepta tokens de acceso de **OAuth 2.0** para inquilinos de Azure AD asociados a suscripciones de Azure que contienen una cuenta de Azure Maps. Azure Maps también acepta tokens de:

* Usuarios de Azure AD
* Aplicaciones de asociados que usan permisos delegados por los usuarios
* Identidades administradas de recursos de Azure

Azure Maps genera un *identificador único (Id. de cliente)* para cada cuenta de Azure Maps. Puede solicitar tokens de Azure AD cuando combine este identificador de cliente con otros parámetros.

Para más información sobre cómo configurar Azure AD y solicitar tokens para Azure Maps, consulte [Administración de la autenticación en Azure Maps](./how-to-manage-authentication.md).

Para información general sobre la autenticación con Azure AD, consulte [¿Qué es la autenticación?](../active-directory/develop/authentication-vs-authorization.md).

### <a name="managed-identities-for-azure-resources-and-azure-maps"></a>Identidades administradas para recursos de Azure y Azure Maps

Las [identidades administradas para recursos de Azure](../active-directory/managed-identities-azure-resources/overview.md) proporcionan a los servicios de Azure una entidad de seguridad basada en aplicaciones administrada automáticamente que se puede autenticar con Azure AD. Con el control de acceso basado en rol de Azure (RBAC de Azure), se puede autorizar el acceso de la entidad de seguridad de la identidad administrada a los servicios de Azure Maps. Algunos ejemplos de identidades administradas son: Azure App Service, Azure Functions y Azure Virtual Machines. Para obtener una lista de identidades administradas, consulte [Identidades administradas para recursos de Azure](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md).

### <a name="configuring-application-azure-ad-authentication"></a>Configuración de la aplicación de autenticación de Azure AD

Las aplicaciones se autenticarán con el inquilino de Azure AD con uno o varios escenarios admitidos proporcionados por Azure AD. Cada escenario de aplicación de Azure AD representa requisitos diferentes en función de las necesidades empresariales. Es posible que algunas aplicaciones requieran experiencias de inicio de sesión de usuario y otras aplicaciones requieran una experiencia de inicio de sesión de aplicación. Para más información, consulte [Flujos de autenticación y escenarios de aplicaciones](../active-directory/develop/authentication-flows-app-scenarios.md).

Después de que la aplicación recibe un token de acceso, el SDK o la aplicación envían una solicitud HTTPS con el siguiente conjunto de encabezados HTTP necesarios, además de otros encabezados HTTP de la API REST:

| Nombre de encabezado    | Value               |
| :------------- | :------------------ |
| x-ms-client-id | 30d7cc….9f55        |
| Authorization  | Bearer eyJ0e….HNIVN |

> [!NOTE]
> `x-ms-client-id` es el GUID basado en la cuenta de Azure Maps que aparece en la página de autenticación de Azure Maps.

A continuación se muestra un ejemplo de una solicitud de ruta de Azure Maps que usa un token de portador de OAuth de Azure AD:

```http
GET /route/directions/json?api-version=1.0&query=52.50931,13.42936:52.50274,13.43872
Host: atlas.microsoft.com
x-ms-client-id: 30d7cc….9f55
Authorization: Bearer eyJ0e….HNIVN
```

Para información sobre cómo ver el identificador de cliente, consulte [Visualización de los detalles de la autenticación](./how-to-manage-authentication.md#view-authentication-details).

## <a name="authorization-with-role-based-access-control"></a>Autorización con el control de acceso basado en rol

Azure Maps admite el acceso a todos los tipos de entidad de seguridad para el [control de acceso basado en rol de Azure (RBAC de Azure)](../role-based-access-control/overview.md), por ejemplo, usuarios individuales de Azure AD, grupos, aplicaciones, recursos de Azure e identidades administradas de Azure. A los tipos de entidad de seguridad se les concede un conjunto de permisos, también conocido como definición de roles. Una definición de roles proporciona permisos para las acciones de la API REST. La aplicación del acceso a una o varias cuentas de Azure Maps se conoce como ámbito. Al aplicar una entidad de seguridad, una definición de roles y un ámbito, se crea una asignación de roles.

En las secciones siguientes se habla de los conceptos y componentes de la integración de Azure Maps con RBAC de Azure. Como parte del proceso de configuración de la cuenta de Azure Maps, se asocia un directorio de Azure AD a la suscripción de Azure en la que reside la cuenta de Azure Maps.

Al configurar RBAC de Azure, elija una entidad de seguridad y aplíquela a una asignación de roles. Para más información sobre cómo agregar asignaciones de roles en Azure Portal, consulte [Asignación de roles de Azure](../role-based-access-control/role-assignments-portal.md).

### <a name="picking-a-role-definition"></a>Selección de una definición de roles

Existen los siguientes tipos de definición de roles para admitir los distintos escenarios de aplicación.

| Definición de roles de Azure       | Descripción                                                                                              |
| :-------------------------- | :------------------------------------------------------------------------------------------------------- |
| Azure Maps Data Reader      | Proporciona acceso a las API REST Azure Maps inmutables.                                                       |
| Colaborador de datos de Azure Maps | Proporciona acceso a las API REST de Azure Maps mutables. La mutabilidad se define mediante las acciones: escritura y eliminación. |
| Definición de roles personalizados      | Cree un rol diseñado para permitir un acceso restringido flexible a las API REST de Azure Maps.                      |

Algunos servicios de Azure Maps pueden requerir privilegios elevados para realizar acciones de escritura o eliminación en las API REST de Azure Maps. El rol Colaborador de datos de Azure Maps es necesario para los servicios que proporcionan acciones de escritura o eliminación. En la tabla siguiente se describen los servicios a los que se aplica el rol Colaborador de datos de Azure Maps cuando se usan acciones de escritura o eliminación en un servicio dado. Si solo se usan acciones de lectura en el servicio, se puede usar el rol Lector de datos de Azure Maps en lugar del rol Colaborador de datos de Azure Maps.

| Servicio de Azure Maps | Definición de roles de Azure Maps  |
| :----------------- | :-------------------------- |
| data               | Colaborador de datos de Azure Maps |
| Creador            | Colaborador de datos de Azure Maps |
| Espacial            | Colaborador de datos de Azure Maps |

Para obtener información sobre la visualización de la configuración de RBAC, vea [Configuración de RBAC de Azure para Azure Maps](./how-to-manage-authentication.md).

#### <a name="custom-role-definitions"></a>Definiciones de roles personalizados

Uno de los aspectos de la seguridad de la aplicación es aplicar el principio de privilegios mínimos. Este principio implica que la entidad de seguridad solo debe permitir el acceso necesario y no debe tener acceso adicional. La creación de definiciones de roles personalizados puede admitir casos de uso que requieren mayor granularidad para el control de acceso. Para crear una definición de roles personalizados, puede seleccionar acciones de datos específicas para incluir o excluir de la definición.

La definición de roles personalizados se puede usar en una asignación de roles para cualquier entidad de seguridad. Para más información sobre las definiciones de roles personalizados de Azure, consulte [Roles personalizados de Azure](../role-based-access-control/custom-roles.md).

Estos son algunos escenarios de ejemplo en los que los roles personalizados pueden mejorar la seguridad de la aplicación.

| Escenario                                                                                                                                                                                                                 | Acciones de datos del rol personalizado                                                                                                                  |
| :----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | :------------------------------------------------------------------------------------------------------------------------------------------ |
| Una página web de inicio de sesión interactiva o con orientación al público con mosaicos de mapa base y ninguna otra API REST.                                                                                                                              | `Microsoft.Maps/accounts/services/render/read`                                                                                              |
| Una aplicación que solo requiere geocodificación inversa y ninguna otra API REST.                                                                                                                                             | `Microsoft.Maps/accounts/services/search/read`                                                                                              |
| Un rol para una entidad de seguridad que solicita la lectura de los datos de un mapa basados en Azure Maps Creator y las API REST de mosaicos de mapa base.                                                                                                 | `Microsoft.Maps/accounts/services/data/read`, `Microsoft.Maps/accounts/services/render/read`                                                |
| Un rol para una entidad de seguridad que requiere la lectura, escritura y eliminación de datos de un mapa basado en Creator. Se puede definir como un rol de editor de datos de mapa, pero no permite el acceso a otras API REST, como los mosaicos de mapa base. | `Microsoft.Maps/accounts/services/data/read`, `Microsoft.Maps/accounts/services/data/write`, `Microsoft.Maps/accounts/services/data/delete` |

### <a name="understanding-scope"></a>Descripción del ámbito

Al crear una asignación de roles, esta se define dentro de la jerarquía de recursos de Azure. En la parte superior de la jerarquía hay un [grupo de administración](../governance/management-groups/overview.md) y en la inferior hay un recurso de Azure, como una cuenta de Azure Maps.
Asignar una asignación de roles a un grupo de recursos puede permitir el acceso a varias cuentas de Azure Maps o a los recursos del grupo.

> [!TIP]
> La recomendación general de Microsoft consiste en asignar acceso al ámbito de la cuenta de Azure Maps porque impide **el acceso no deseado a otras cuentas de Azure Maps** existentes en la misma suscripción de Azure.

## <a name="next-steps"></a>Pasos siguientes

Para obtener más información sobre RBAC de Azure, vea
> [!div class="nextstepaction"]
> [Control de acceso basado en rol](../role-based-access-control/overview.md)

Para más información sobre la autenticación de una aplicación con Azure AD y Azure Maps, consulte
> [!div class="nextstepaction"]
> [Administración de la autenticación en Azure Maps](./how-to-manage-authentication.md)

Para más información sobre la autenticación del Control de mapa de Azure Maps con Azure AD, consulte
> [!div class="nextstepaction"]
> [Uso del Control de mapa de Azure Maps](./how-to-use-map-control.md)